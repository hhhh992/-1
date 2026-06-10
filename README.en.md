# 数据可视化

```
# ----------------------------- 0. 环境准备 -----------------------------
output_dir <- "C:\\Users\\lilinhao\\Desktop\\12345"

if (!dir.exists(output_dir)) {
  dir.create(output_dir, recursive = TRUE)
}

setwd(output_dir)
cat("工作目录：", getwd(), "\n")

# 安装所需包（移除有问题的包）
packages <- c("tidyverse", "readxl", "lubridate", "maps", "mapproj",
              "viridis", "RColorBrewer", "ggrepel", "hexbin", "plotly",
              "networkD3", "ggExtra", "mgcv", "sf", "classInt")

for (pkg in packages) {
  if (!require(pkg, character.only = TRUE)) {
    install.packages(pkg, quiet = TRUE)
    library(pkg, character.only = TRUE)
  }
}

# 单独安装 treemapify（如果安装失败，使用替代方案）
if (!require(treemapify, quietly = TRUE)) {
  install.packages("treemapify", quiet = TRUE)
}

# ----------------------------- 1. 读取数据 -----------------------------
file_path <- "C:\\Users\\lilinhao\\Desktop\\sample_superstore.xls"
superstore <- read_excel(file_path)

# 数据清洗
superstore <- superstore %>%
  rename(
    Order_Date = `Order Date`,
    State_Province = `State/Province`,
    Region = Region,
    Sales = Sales,
    Profit = Profit,
    Discount = Discount,
    Category = Category,
    Sub_Category = `Sub-Category`,
    Quantity = Quantity,
    Customer_ID = `Customer ID`
  ) %>%
  mutate(Order_Date = as.Date(Order_Date),
         Month = floor_date(Order_Date, "month"),
         Year = year(Order_Date),
         Profit_Margin = ifelse(Sales > 0, Profit / Sales * 100, NA),
         Profit_Type = ifelse(Profit > 0, "盈利", "亏损"),
         Discount_Level = case_when(
           Discount == 0 ~ "无折扣",
           Discount < 0.2 ~ "低折扣",
           Discount < 0.5 ~ "中折扣",
           TRUE ~ "高折扣"
         ))

# ----------------------------- 2. 按州聚合数据（用于空间分析） -----------------------------
state_profit <- superstore %>%
  group_by(State = State_Province) %>%
  summarise(
    Total_Profit = sum(Profit, na.rm = TRUE),
    Total_Sales = sum(Sales, na.rm = TRUE),
    Avg_Discount = mean(Discount, na.rm = TRUE),
    Order_Count = n(),
    Avg_Profit_Per_Order = mean(Profit, na.rm = TRUE)
  ) %>%
  mutate(Profit_Rate = Total_Profit / Total_Sales * 100)

# 州名映射
state_mapping <- data.frame(
  State = state.name,
  region = tolower(state.name)
)
state_mapping <- rbind(state_mapping, 
                       data.frame(State = "District of Columbia", region = "district of columbia"))

state_profit <- state_profit %>%
  left_join(state_mapping, by = "State") %>%
  filter(!is.na(region))

# 获取地图数据
us_states <- map_data("state")
map_df <- us_states %>%
  left_join(state_profit, by = "region")

# 计算州中心点
state_centers <- map_df %>%
  group_by(region) %>%
  summarise(
    long = mean(range(long, na.rm = TRUE)),
    lat = mean(range(lat, na.rm = TRUE)),
    Total_Profit = first(Total_Profit),
    Total_Sales = first(Total_Sales),
    Avg_Discount = first(Avg_Discount)
  ) %>%
  filter(!is.na(Total_Profit))

# ============================================================
# 图形1：五分位数分级地图
# ============================================================
library(classInt)

quantile_breaks <- classIntervals(state_profit$Total_Profit, n = 5, style = "quantile")$brks
state_profit <- state_profit %>%
  mutate(Profit_Quantile = cut(Total_Profit, 
                               breaks = quantile_breaks, 
                               include.lowest = TRUE,
                               labels = c("Q1 (最高亏损)", "Q2", "Q3", "Q4", "Q5 (最高盈利)")))

map_df_quantile <- us_states %>%
  left_join(state_profit, by = "region")

p1_advanced <- ggplot() +
  geom_polygon(data = map_df_quantile, 
               aes(x = long, y = lat, group = group, fill = Profit_Quantile),
               color = "black", linewidth = 0.4, alpha = 0.9) +
  scale_fill_brewer(palette = "RdBu", name = "利润分组", direction = -1) +
  coord_map(projection = "albers", lat0 = 39, lat1 = 45) +
  theme_void() +
  labs(
    title = "美国各州利润五分位数分布图",
    subtitle = "基于分位数分组 (Q1 = 亏损最严重, Q5 = 盈利最高)",
    caption = "数据来源：Superstore Dataset | 分级方法：Quantile (5组)"
  ) +
  theme(
    plot.title = element_text(hjust = 0.5, size = 16, face = "bold"),
    plot.subtitle = element_text(hjust = 0.5, size = 11, color = "gray40"),
    legend.position = "right",
    legend.title = element_text(size = 10, face = "bold")
  )

ggsave(file.path(output_dir, "Advanced_1_Quantile_Map.png"), 
       p1_advanced, width = 12, height = 7.5, dpi = 300)
cat("高级图1：五分位数地图\n")


# ============================================================
# 图形3：桑基图 - 产品流向分析
# ============================================================
library(networkD3)

sankey_data <- superstore %>%
  group_by(Category, Sub_Category, Discount_Level, Profit_Type) %>%
  summarise(Count = n(), .groups = "drop") %>%
  filter(Count > 30)

nodes <- data.frame(
  name = unique(c(sankey_data$Category, sankey_data$Sub_Category, 
                  sankey_data$Discount_Level, sankey_data$Profit_Type))
)

links <- data.frame(
  source = match(sankey_data$Category, nodes$name) - 1,
  target = match(sankey_data$Sub_Category, nodes$name) - 1,
  value = sankey_data$Count
)

links2 <- data.frame(
  source = match(sankey_data$Sub_Category, nodes$name) - 1,
  target = match(sankey_data$Discount_Level, nodes$name) - 1,
  value = sankey_data$Count
)

links3 <- data.frame(
  source = match(sankey_data$Discount_Level, nodes$name) - 1,
  target = match(sankey_data$Profit_Type, nodes$name) - 1,
  value = sankey_data$Count
)

links_all <- rbind(links, links2, links3)

p3_sankey <- sankeyNetwork(
  Links = links_all, 
  Nodes = nodes, 
  Source = "source",
  Target = "target", 
  Value = "value", 
  NodeID = "name",
  fontSize = 12,
  nodeWidth = 30,
  height = 500,
  width = 800,
  colourScale = JS('d3.scaleOrdinal().range(["#4575B4","#91BFDB","#E0F3F8","#FEE090","#FC8D59","#D73027"])')
)

saveNetwork(p3_sankey, file.path(output_dir, "Advanced_3_Sankey.html"))
cat("高级图3：桑基图（交互式HTML）\n")

# ============================================================
# 图形4：高级散点图 + 边际分布 + 回归曲线
# ============================================================
library(ggExtra)

scatter_data <- superstore %>%
  sample_n(min(3000, nrow(.))) %>%
  mutate(Discount_Bin = cut(Discount, breaks = 5))

p4_scatter <- ggplot(scatter_data, aes(x = Sales, y = Profit, color = Discount)) +
  geom_point(alpha = 0.5, size = 1.5) +
  geom_smooth(method = "gam", formula = y ~ s(x, bs = "cs"), 
              se = TRUE, color = "darkred", size = 1.2) +
  geom_hline(yintercept = 0, linetype = "dashed", color = "gray40", alpha = 0.7) +
  scale_color_gradient2(low = "blue", mid = "yellow", high = "red", 
                        midpoint = 0.3, name = "折扣率") +
  scale_x_continuous(trans = "log10", labels = scales::label_comma()) +
  labs(
    title = "销售额与利润关系：GAM回归分析",
    subtitle = "红色曲线：GAM平滑回归线（含95%置信区间）| 虚线：盈亏平衡线",
    x = "销售额（美元，对数坐标）",
    y = "利润（美元）",
    caption = "GAM模型显示：销售额超过$10,000后，边际利润递减"
  ) +
  theme_minimal() +
  theme(
    plot.title = element_text(hjust = 0.5, size = 16, face = "bold"),
    plot.subtitle = element_text(hjust = 0.5, size = 10, color = "gray40"),
    legend.position = "right"
  )

p4_marginal <- ggMarginal(p4_scatter, type = "histogram", 
                          fill = "steelblue", alpha = 0.5)

ggsave(file.path(output_dir, "Advanced_4_Scatter_Marginal.png"), 
       p4_marginal, width = 12, height = 8, dpi = 300)
cat("高级图4：散点图+边际分布+GAM回归\n")

# ============================================================
# 图形5：3D交互式地图
# ============================================================
library(plotly)

state_3d <- state_centers %>%
  filter(!is.na(Total_Profit)) %>%
  mutate(
    Profit_Color = case_when(
      Total_Profit < -5000 ~ "严重亏损",
      Total_Profit < 0 ~ "轻微亏损",
      Total_Profit < 5000 ~ "轻微盈利",
      TRUE ~ "高度盈利"
    ),
    hover_text = paste(
      "州: ", region, "<br>",
      "总利润: $", format(round(Total_Profit, 0), big.mark = ","), "<br>",
      "总销售额: $", format(round(Total_Sales, 0), big.mark = ","), "<br>",
      "平均折扣率: ", round(Avg_Discount * 100, 1), "%"
    )
  )

p5_3d <- plot_ly(
  data = state_3d,
  x = ~long,
  y = ~lat,
  z = ~Total_Profit,
  type = "scatter3d",
  mode = "markers",
  marker = list(
    size = ~sqrt(abs(Total_Profit)) / 10,
    color = ~Total_Profit,
    colorscale = list(c(0, "red"), c(0.5, "white"), c(1, "blue")),
    showscale = TRUE,
    colorbar = list(title = "总利润")
  ),
  text = ~hover_text,
  hoverinfo = "text"
) %>%
  layout(
    title = "3D 利润分布图",
    scene = list(
      xaxis = list(title = "经度"),
      yaxis = list(title = "纬度"),
      zaxis = list(title = "总利润 (美元)", titlefont = list(color = "blue"))
    )
  )

htmlwidgets::saveWidget(p5_3d, file.path(output_dir, "Advanced_5_3D_Map.html"))
cat("高级图5：3D交互式地图\n")

# ============================================================
# 图形6：小倍数图 (Small Multiples)
# ============================================================
superstore <- superstore %>%
  mutate(Quarter = quarter(Order_Date),
         Quarter_Label = paste(Year, "Q", Quarter, sep = ""))

quarterly_region <- superstore %>%
  group_by(Region, Quarter_Label) %>%
  summarise(
    Sales = sum(Sales),
    Profit = sum(Profit),
    .groups = "drop"
  ) %>%
  arrange(Region, Quarter_Label)

p6_smallmultiples <- ggplot(quarterly_region, aes(x = Quarter_Label, y = Profit, fill = Region)) +
  geom_col(alpha = 0.8) +
  facet_wrap(~Region, scales = "free_y", ncol = 2) +
  scale_fill_brewer(palette = "Set2", guide = "none") +
  labs(
    title = "各区域季度利润变化（小倍数图）",
    subtitle = "展示四个区域不同的季节性模式",
    x = "季度",
    y = "利润（美元）"
  ) +
  theme_minimal() +
  theme(
    plot.title = element_text(hjust = 0.5, size = 16, face = "bold"),
    plot.subtitle = element_text(hjust = 0.5, size = 11, color = "gray40"),
    axis.text.x = element_text(angle = 45, hjust = 1),
    strip.text = element_text(size = 12, face = "bold")
  )

ggsave(file.path(output_dir, "Advanced_6_SmallMultiples.png"), 
       p6_smallmultiples, width = 12, height = 9, dpi = 300)
cat(" 高级图6：小倍数图\n")

# ============================================================
# 图形7：热力图 - 产品类别 × 区域的利润矩阵
# ============================================================
heatmap_data <- superstore %>%
  group_by(Category, Region) %>%
  summarise(
    Profit = sum(Profit, na.rm = TRUE),
    Sales = sum(Sales, na.rm = TRUE),
    .groups = "drop"
  ) %>%
  mutate(Profit_Margin = Profit / Sales * 100)

p7_heatmap <- ggplot(heatmap_data, aes(x = Region, y = Category, fill = Profit)) +
  geom_tile(color = "white", size = 1) +
  geom_text(aes(label = paste0("$", format(round(Profit, 0), big.mark = ","))), 
            size = 4, fontface = "bold") +
  scale_fill_gradient2(low = "#D73027", mid = "white", high = "#4575B4",
                       midpoint = 0, name = "总利润") +
  labs(
    title = "产品类别 × 区域利润矩阵",
    subtitle = "数值为利润金额（美元）| 红色=亏损，蓝色=盈利",
    x = "区域",
    y = "产品类别"
  ) +
  theme_minimal() +
  theme(
    plot.title = element_text(hjust = 0.5, size = 16, face = "bold"),
    plot.subtitle = element_text(hjust = 0.5, size = 11, color = "gray40"),
    axis.text = element_text(size = 12, face = "bold"),
    axis.title = element_text(size = 12),
    legend.position = "right"
  )

ggsave(file.path(output_dir, "Advanced_7_Heatmap.png"), 
       p7_heatmap, width = 10, height = 6, dpi = 300)
cat("高级图7：热力图矩阵\n")

# ============================================================
# 图形8：小提琴图 + 箱线图组合
# ============================================================
p8_violin <- ggplot(superstore, aes(x = Region, y = Profit, fill = Region)) +
  geom_violin(trim = FALSE, alpha = 0.6, scale = "width") +
  geom_boxplot(width = 0.15, fill = "white", alpha = 0.8, outlier.shape = NA) +
  geom_jitter(width = 0.2, alpha = 0.05, size = 0.5) +
  stat_summary(fun = mean, geom = "point", shape = 18, size = 5, color = "red") +
  stat_summary(fun = mean, geom = "text", aes(label = paste0("均值: $", round(..y.., 0))),
               vjust = -1, size = 3.5, color = "red") +
  scale_fill_brewer(palette = "Set2", guide = "none") +
  labs(
    title = "各区域利润分布：小提琴图 + 箱线图",
    subtitle = "红点 = 均值 | 宽度表示密度分布",
    x = "区域",
    y = "利润（美元）",
    caption = "West 和 East 区域分布更分散，Central 区域相对集中"
  ) +
  theme_minimal() +
  theme(
    plot.title = element_text(hjust = 0.5, size = 16, face = "bold"),
    plot.subtitle = element_text(hjust = 0.5, size = 11, color = "gray40"),
    axis.text = element_text(size = 11)
  )

ggsave(file.path(output_dir, "Advanced_8_Violin_Boxplot.png"), 
       p8_violin, width = 10, height = 7, dpi = 300)
cat("高级图8：小提琴图+箱线图\n")


# ============================================================
# 图形10：雷达图 - 区域综合表现对比
# ============================================================
radar_data <- superstore %>%
  group_by(Region) %>%
  summarise(
    avg_profit = mean(Profit, na.rm = TRUE),
    avg_sales = mean(Sales, na.rm = TRUE),
    total_profit = sum(Profit, na.rm = TRUE),
    total_sales = sum(Sales, na.rm = TRUE),
    profit_rate = total_profit / total_sales * 100,
    avg_discount = mean(Discount, na.rm = TRUE) * 100,
    order_count = n()
  ) %>%
  mutate(
    avg_profit_norm = (avg_profit - min(avg_profit)) / (max(avg_profit) - min(avg_profit)),
    total_profit_norm = (total_profit - min(total_profit)) / (max(total_profit) - min(total_profit)),
    profit_rate_norm = (profit_rate - min(profit_rate)) / (max(profit_rate) - min(profit_rate)),
    avg_sales_norm = (avg_sales - min(avg_sales)) / (max(avg_sales) - min(avg_sales)),
    order_count_norm = (order_count - min(order_count)) / (max(order_count) - min(order_count))
  )

radar_long <- radar_data %>%
  select(Region, avg_profit_norm, total_profit_norm, profit_rate_norm, 
         avg_sales_norm, order_count_norm) %>%
  pivot_longer(cols = -Region, names_to = "Metric", values_to = "Value")

radar_long <- radar_long %>%
  mutate(Metric_Label = case_when(
    Metric == "avg_profit_norm" ~ "平均利润",
    Metric == "total_profit_norm" ~ "总利润",
    Metric == "profit_rate_norm" ~ "利润率",
    Metric == "avg_sales_norm" ~ "平均销售额",
    Metric == "order_count_norm" ~ "订单量"
  ))

p10_radar <- ggplot(radar_long, aes(x = Metric_Label, y = Value, color = Region, group = Region)) +
  geom_polygon(fill = NA, size = 1.2) +
  geom_point(size = 3) +
  coord_polar() +
  scale_y_continuous(limits = c(0, 1), breaks = seq(0, 1, 0.25)) +
  scale_color_brewer(palette = "Set1") +
  labs(
    title = "各区域综合表现雷达图",
    subtitle = "归一化指标 (0-1) | 值越大表示表现越好",
    x = NULL,
    y = NULL,
    caption = "注意：Central 区域在多项指标上表现较弱"
  ) +
  theme_minimal() +
  theme(
    plot.title = element_text(hjust = 0.5, size = 16, face = "bold"),
    plot.subtitle = element_text(hjust = 0.5, size = 11, color = "gray40"),
    legend.position = "bottom",
    legend.title = element_text(size = 10),
    axis.text.x = element_text(size = 10, face = "bold"),
    axis.text.y = element_blank(),
    axis.ticks = element_blank(),
    panel.grid.minor = element_blank()
  )

ggsave(file.path(output_dir, "Advanced_10_Radar.png"), 
       p10_radar, width = 10, height = 8, dpi = 300)
cat(" 高级图10：雷达图\n")

```
