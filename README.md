[数据可视化.html](https://github.com/user-attachments/files/28794199/default.html)

<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>数据可视化绘图技能 · 多层次分析引擎 | 美国数据专版</title>
    <!-- 引入现代字体 & 简洁重置 -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:opsz,wght@14..32,300;14..32,400;14..32,500;14..32,600;14..32,700;14..32,800&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <!-- 动态图表核心库 -->
    <script src="https://cdn.plot.ly/plotly-3.0.1.min.js" charset="utf-8"></script>
    <script src="https://cdn.sheetjs.com/xlsx-0.20.2/package/dist/xlsx.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Inter', sans-serif;
            background: #f5f7fb;
            color: #1a2a3f;
            line-height: 1.4;
            scroll-behavior: smooth;
        }

        .poster-container {
            max-width: 1300px;
            margin: 2rem auto;
            background: white;
            border-radius: 2rem;
            box-shadow: 0 25px 45px -12px rgba(0, 0, 0, 0.25);
            overflow: hidden;
        }

        .section {
            padding: 2rem 2.5rem;
            border-bottom: 1px solid #eef2f8;
        }
        .section:last-child { border-bottom: none; }

        .hero {
            background: linear-gradient(135deg, #0b2b40 0%, #1c4e6f 100%);
            color: white;
            padding: 3rem 2.5rem;
            text-align: center;
        }
        .hero h1 {
            font-size: 3.2rem;
            font-weight: 800;
            letter-spacing: -0.02em;
            margin-bottom: 0.75rem;
            background: linear-gradient(130deg, #FFFFFF, #CFE9FF);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
        }
        .hero .tagline {
            font-size: 1.25rem;
            opacity: 0.9;
            margin-bottom: 1.5rem;
            font-weight: 500;
        }
        .badge-group {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 0.8rem;
            margin-top: 1rem;
        }
        .badge {
            background: rgba(255,255,255,0.18);
            backdrop-filter: blur(4px);
            padding: 0.4rem 1.2rem;
            border-radius: 40px;
            font-size: 0.85rem;
            font-weight: 500;
        }

        .features-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(290px, 1fr));
            gap: 2rem;
            margin-top: 1rem;
        }
        .feature-card {
            background: #ffffff;
            border-radius: 1.5rem;
            padding: 1.5rem;
            box-shadow: 0 5px 18px rgba(0,0,0,0.03);
            border: 1px solid #e9edf2;
            transition: transform 0.2s;
        }
        .feature-card:hover {
            transform: translateY(-4px);
            border-color: #cbdae9;
        }
        .feature-icon {
            font-size: 2.2rem;
            color: #1f6e8c;
            margin-bottom: 1rem;
        }
        .feature-card h3 {
            font-size: 1.4rem;
            font-weight: 700;
            margin-bottom: 0.75rem;
        }
        .feature-card p {
            color: #2c475e;
            line-height: 1.45;
            font-size: 0.95rem;
        }
        .tech-tag {
            display: inline-block;
            background: #eef2fa;
            padding: 0.2rem 0.7rem;
            border-radius: 20px;
            font-size: 0.7rem;
            font-weight: 600;
            margin-top: 0.8rem;
            color: #1f6e8c;
        }

        .gallery-title {
            font-size: 1.9rem;
            font-weight: 700;
            margin-bottom: 0.5rem;
            display: flex;
            align-items: center;
            gap: 12px;
        }
        .gallery-sub {
            color: #4a627a;
            margin-bottom: 2rem;
            border-left: 4px solid #1f6e8c;
            padding-left: 1rem;
            font-weight: 500;
        }
        .chart-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
            gap: 1.8rem;
            margin: 1.5rem 0 0.5rem;
        }
        .chart-card {
            background: #ffffff;
            border-radius: 1.2rem;
            border: 1px solid #eef2fa;
            overflow: hidden;
            transition: all 0.2s;
            box-shadow: 0 2px 8px rgba(0,0,0,0.03);
        }
        .chart-img-container {
            width: 100%;
            height: 200px;
            overflow: hidden;
            display: flex;
            align-items: center;
            justify-content: center;
            background: #f5f7fb;
            border-bottom: 1px solid #e2e8f0;
        }
        .chart-img-container img {
            width: 100%;
            height: 100%;
            object-fit: contain;
            transition: transform 0.2s;
        }
        .chart-img-container img:hover {
            transform: scale(1.02);
        }
        .chart-info {
            padding: 1rem 1.2rem 1.2rem;
        }
        .chart-info h4 {
            font-weight: 700;
            font-size: 1.1rem;
            margin-bottom: 0.4rem;
        }
        .chart-info p {
            font-size: 0.85rem;
            color: #4a5b6e;
        }
        .code-block {
            background: #0f172a;
            border-radius: 1.2rem;
            padding: 1.2rem;
            margin: 1.2rem 0 0.5rem;
            font-family: 'Monaco', 'Menlo', monospace;
            font-size: 0.75rem;
            overflow-x: auto;
            color: #e2e8f0;
        }
        .two-columns {
            display: flex;
            flex-wrap: wrap;
            gap: 2rem;
            margin-top: 1rem;
        }
        .col {
            flex: 1;
            background: #f8fafc;
            border-radius: 1.2rem;
            padding: 1.5rem;
        }
        .data-req-list {
            list-style: none;
            padding-left: 0;
        }
        .data-req-list li {
            margin-bottom: 0.6rem;
            display: flex;
            align-items: center;
            gap: 0.7rem;
        }
        .data-req-list i {
            color: #1f6e8c;
            width: 1.4rem;
        }
        .btn-outline-skill {
            display: inline-flex;
            align-items: center;
            gap: 8px;
            background: transparent;
            border: 1.5px solid #1f6e8c;
            padding: 0.5rem 1.4rem;
            border-radius: 40px;
            font-weight: 600;
            color: #1f6e8c;
            transition: 0.2s;
            text-decoration: none;
            font-size: 0.9rem;
        }
        footer {
            background: #f8fafd;
            text-align: center;
            padding: 1.5rem;
            font-size: 0.8rem;
            color: #5f7f9e;
        }
        hr {
            margin: 1rem 0;
            border: 0;
            height: 1px;
            background: #e2e8f0;
        }
        .interactive-panel {
            background: #fafcff;
            border-radius: 1.8rem;
            border: 1px solid #e2edf7;
            padding: 1.5rem;
            margin-top: 1rem;
            box-shadow: 0 8px 20px rgba(0,0,0,0.02);
        }
        .control-bar {
            display: flex;
            flex-wrap: wrap;
            gap: 1.2rem;
            align-items: flex-end;
            margin-bottom: 1.8rem;
            background: #f1f5f9;
            padding: 1.2rem;
            border-radius: 1.2rem;
        }
        .ctrl-group {
            display: flex;
            flex-direction: column;
            gap: 0.4rem;
            min-width: 160px;
        }
        .ctrl-group label {
            font-weight: 600;
            font-size: 0.8rem;
            color: #2c5a74;
        }
        select, input, button {
            padding: 0.55rem 1rem;
            border-radius: 40px;
            border: 1px solid #cfdfed;
            background: white;
            font-family: 'Inter', sans-serif;
            font-size: 0.85rem;
            cursor: pointer;
        }
        button {
            background: #1f6e8c;
            color: white;
            border: none;
            font-weight: 600;
            transition: 0.2s;
        }
        button:hover {
            background: #0e4e66;
            transform: scale(0.97);
        }
        .chart-container {
            width: 100%;
            min-height: 500px;
            background: #ffffff;
            border-radius: 1.2rem;
            border: 1px solid #e2edf7;
            margin-top: 0.8rem;
            padding: 0.5rem;
        }
        .data-notice {
            font-size: 0.75rem;
            color: #5e7e9e;
            margin-top: 0.6rem;
            text-align: center;
        }
        @media (max-width: 760px) {
            .section { padding: 1.5rem; }
            .hero h1 { font-size: 2.2rem; }
            .chart-grid { grid-template-columns: 1fr; }
            .control-bar { flex-direction: column; align-items: stretch; }
        }
    </style>
</head>
<body>
<div class="poster-container">
    <!-- 头部：技能名称 -->
    <div class="hero">
        <h1>📊 数据可视化绘图技能</h1>
        <div class="tagline">基于R语言 · 多层次分析引擎 | 专为美国区域/销售数据设计</div>
        <div class="badge-group">
            <span class="badge"><i class="fas fa-chart-line"></i> ggplot2 / tidyverse</span>
            <span class="badge"><i class="fas fa-globe-americas"></i> 空间统计 & 3D地图</span>
            <span class="badge"><i class="fas fa-chart-network"></i> 桑基图 / 热力图 / 雷达图</span>
            <span class="badge"><i class="fas fa-layer-group"></i> 州级 · 区域级 · 产品级 · 时间级</span>
        </div>
    </div>

    <!-- 纯代码功能介绍区 (不含结论) -->
    <div class="section">
        <h2 style="font-size: 1.8rem; font-weight: 700; margin-bottom: 1rem;"><i class="fas fa-code" style="color:#1f6e8c;"></i> 代码功能 · 图形构建能力</h2>
        <div class="features-grid">
            <div class="feature-card">
                <div class="feature-icon"><i class="fas fa-map-marked-alt"></i></div>
                <h3>空间统计图 (Choropleth)</h3>
                <p>基于 sf / maps 包绘制美国州级分级统计图，支持等频分位数、自然断点法分组，发散色阶（红-白-蓝）自动映射利润/销售额，可叠加州名标签和Albers投影。</p>
                <div class="tech-tag">geom_polygon + classInt + coord_map</div>
            </div>
            <div class="feature-card">
                <div class="feature-icon"><i class="fas fa-project-diagram"></i></div>
                <h3>交互式桑基图 (Sankey)</h3>
                <p>networkD3构建多层级流向图，支持自定义节点（大类→子类→折扣档位→盈亏状态），自动聚合频次并生成HTML交互小部件，鼠标悬停查看流量值。</p>
                <div class="tech-tag">sankeyNetwork + 数据聚合管道</div>
            </div>
            <div class="feature-card">
                <div class="feature-icon"><i class="fas fa-chart-scatter"></i></div>
                <h3>GAM回归散点图 + 边际分布</h3>
                <p>mgcv::gam 拟合广义加性模型，绘制平滑回归曲线及置信带；ggExtra::ggMarginal 添加边际直方图/箱线图，支持对数坐标轴转换，颜色编码第三维度（如折扣率）。</p>
                <div class="tech-tag">geom_smooth(method="gam") + ggMarginal</div>
            </div>
            <div class="feature-card">
                <div class="feature-icon"><i class="fas fa-chart-simple"></i></div>
                <h3>矩阵热力图 & 数值标注</h3>
                <p>geom_tile 构建二维聚合矩阵（产品类别×区域/州），fill映射利润/销售额，自动添加文本数值标签；发散色阶强调正负差异，支持自定义中点。</p>
                <div class="tech-tag">geom_tile + geom_text + scale_fill_gradient2</div>
            </div>
            <div class="feature-card">
                <div class="feature-icon"><i class="fas fa-chart-line"></i></div>
                <h3>小倍数图 (Small Multiples)</h3>
                <p>facet_wrap / facet_grid 分面绘制多子图，统一坐标轴便于横向对比（如各区域季度利润变化），支持自定义时间聚合（季度/月度）和条形/折线图。</p>
                <div class="tech-tag">facet_wrap + 时间序列聚合</div>
            </div>
            <div class="feature-card">
                <div class="feature-icon"><i class="fas fa-chart-pie"></i></div>
                <h3>雷达图 + 小提琴图组合</h3>
                <p>coord_polar 生成多维度归一化雷达图（平均利润/总利润/利润率/订单量等）；geom_violin + geom_boxplot 展示分布形态，支持均值点标注与抖动点叠加。</p>
                <div class="tech-tag">coord_polar + geom_violin + stat_summary</div>
            </div>
            <div class="feature-card">
                <div class="feature-icon"><i class="fas fa-cube"></i></div>
                <h3>3D 交互式散点图</h3>
                <p>plotly::plot_ly 绘制三维散点图，X/Y/Z轴映射经度/纬度/利润，点大小与颜色编码额外指标，生成可拖拽、缩放的HTML交互地图。</p>
                <div class="tech-tag">plotly scatter3d + 颜色标尺</div>
            </div>
            <div class="feature-card">
                <div class="feature-icon"><i class="fas fa-chart-bar"></i></div>
                <h3>聚合统计与数据转换</h3>
                <p>dplyr 链式操作：group_by + summarise 完成州级、区域级、产品类别或时间粒度的利润/销售额/折扣率聚合；支持自定义盈亏标识、折扣分层等特征工程。</p>
                <div class="tech-tag">dplyr / tidyr / lubridate</div>
            </div>
        </div>
    </div>

    <!-- 实际图表展示区：使用用户提供的本地图片路径（已修正） -->
    <div class="section" style="background: #fdfdfd;">
        <div class="gallery-title">
            <i class="fas fa-images" style="color:#1f6e8c; font-size: 2rem;"></i> 
            <span>实际可视化成果 · 代码输出展示（本地图片）</span>
        </div>
        <div class="gallery-sub">
            🖼️ 以下为基于 Superstore 美国销售数据生成的8类核心图表（R语言ggplot2输出）
        </div>
        <div class="chart-grid">
            <!-- 1. 地图 -->
            <div class="chart-card">
                <div class="chart-img-container">
                    <img src="file:///C:/Users/lilinhao/Desktop/图片/地图.png" alt="美国各州利润分级地图" onerror="this.onerror=null;this.src='data:image/svg+xml,%3Csvg xmlns=%22http://www.w3.org/2000/svg%22 width=%22320%22 height=%22200%22 viewBox=%220 0 320 200%22%3E%3Crect width=%22100%25%22 height=%22100%25%22 fill=%22%23f0f4fa%22/%3E%3Ctext x=%22160%22 y=%22100%22 font-family=%22sans-serif%22 font-size=%2212%22 fill=%22%237c9ebf%22 text-anchor=%22middle%22%3E图片加载失败，请检查路径%3C/text%3E%3C/svg%3E';">
                </div>
                <div class="chart-info">
                    <h4>🌎 美国各州利润五分位数地图</h4>
                    <p>ggplot2 + 州边界，分位数分组（红-白-蓝发散色阶），直观展示东西海岸高盈利、中西部亏损空间格局。</p>
                    <span class="tech-tag">Choropleth · 空间异质性</span>
                </div>
            </div>
            <!-- 2. 桑基图 -->
            <div class="chart-card">
                <div class="chart-img-container">
                    <img src="file:///C:/Users/lilinhao/Desktop/图片/桑基图.png" alt="产品流向桑基图" onerror="this.onerror=null;this.src='data:image/svg+xml,%3Csvg xmlns=%22http://www.w3.org/2000/svg%22 width=%22320%22 height=%22200%22 viewBox=%220 0 320 200%22%3E%3Crect width=%22100%25%22 height=%22100%25%22 fill=%22%23f0f4fa%22/%3E%3Ctext x=%22160%22 y=%22100%22 font-family=%22sans-serif%22 font-size=%2212%22 fill=%22%237c9ebf%22 text-anchor=%22middle%22%3E图片加载失败，请检查路径%3C/text%3E%3C/svg%3E';">
                </div>
                <div class="chart-info">
                    <h4>🔁 产品流向桑基图 (Sankey)</h4>
                    <p>networkD3 交互式桑基图，展示产品大类→子类→折扣水平→盈亏结果，高折扣路径导向亏损。</p>
                    <span class="tech-tag">层级流向 · 折扣归因</span>
                </div>
            </div>
            <!-- 3. GAM散点图 -->
            <div class="chart-card">
                <div class="chart-img-container">
                    <img src="file:///C:/Users/lilinhao/Desktop/图片/散点图.png" alt="GAM回归散点图" onerror="this.onerror=null;this.src='data:image/svg+xml,%3Csvg xmlns=%22http://www.w3.org/2000/svg%22 width=%22320%22 height=%22200%22 viewBox=%220 0 320 200%22%3E%3Crect width=%22100%25%22 height=%22100%25%22 fill=%22%23f0f4fa%22/%3E%3Ctext x=%22160%22 y=%22100%22 font-family=%22sans-serif%22 font-size=%2212%22 fill=%22%237c9ebf%22 text-anchor=%22middle%22%3E图片加载失败，请检查路径%3C/text%3E%3C/svg%3E';">
                </div>
                <div class="chart-info">
                    <h4>📈 GAM回归散点图</h4>
                    <p>销售额 vs 利润，GAM平滑曲线揭示边际利润递减规律，颜色映射折扣率（红=高折扣）。</p>
                    <span class="tech-tag">非线性拟合 · 折扣侵蚀</span>
                </div>
            </div>
            <!-- 4. 边际直方图（对应直方图.png） -->
            <div class="chart-card">
                <div class="chart-img-container">
                    <img src="file:///C:/Users/lilinhao/Desktop/图片/直方图.png" alt="边际分布直方图" onerror="this.onerror=null;this.src='data:image/svg+xml,%3Csvg xmlns=%22http://www.w3.org/2000/svg%22 width=%22320%22 height=%22200%22 viewBox=%220 0 320 200%22%3E%3Crect width=%22100%25%22 height=%22100%25%22 fill=%22%23f0f4fa%22/%3E%3Ctext x=%22160%22 y=%22100%22 font-family=%22sans-serif%22 font-size=%2212%22 fill=%22%237c9ebf%22 text-anchor=%22middle%22%3E图片加载失败，请检查路径%3C/text%3E%3C/svg%3E';">
                </div>
                <div class="chart-info">
                    <h4>📊 边际分布直方图</h4>
                    <p>ggExtra 添加的边际直方图，展示销售额与利润的分布密度，辅助理解数据集中趋势。</p>
                    <span class="tech-tag">边际分布 · 密度补充</span>
                </div>
            </div>
            <!-- 5. 热力图 -->
            <div class="chart-card">
                <div class="chart-img-container">
                    <img src="file:///C:/Users/lilinhao/Desktop/图片/热力图.png" alt="产品类别×区域利润热力图" onerror="this.onerror=null;this.src='data:image/svg+xml,%3Csvg xmlns=%22http://www.w3.org/2000/svg%22 width=%22320%22 height=%22200%22 viewBox=%220 0 320 200%22%3E%3Crect width=%22100%25%22 height=%22100%25%22 fill=%22%23f0f4fa%22/%3E%3Ctext x=%22160%22 y=%22100%22 font-family=%22sans-serif%22 font-size=%2212%22 fill=%22%237c9ebf%22 text-anchor=%22middle%22%3E图片加载失败，请检查路径%3C/text%3E%3C/svg%3E';">
                </div>
                <div class="chart-info">
                    <h4>🔥 矩阵热力图</h4>
                    <p>产品类别 × 区域利润矩阵，格内标注具体金额，蓝色盈利红色亏损，快速识别高/低效组合。</p>
                    <span class="tech-tag">二维聚合 · 数值标注</span>
                </div>
            </div>
            <!-- 6. 箱线图+小提琴（箱线图.png） -->
            <div class="chart-card">
                <div class="chart-img-container">
                    <img src="file:///C:/Users/lilinhao/Desktop/图片/箱线图.png" alt="区域利润分布箱线图" onerror="this.onerror=null;this.src='data:image/svg+xml,%3Csvg xmlns=%22http://www.w3.org/2000/svg%22 width=%22320%22 height=%22200%22 viewBox=%220 0 320 200%22%3E%3Crect width=%22100%25%22 height=%22100%25%22 fill=%22%23f0f4fa%22/%3E%3Ctext x=%22160%22 y=%22100%22 font-family=%22sans-serif%22 font-size=%2212%22 fill=%22%237c9ebf%22 text-anchor=%22middle%22%3E图片加载失败，请检查路径%3C/text%3E%3C/svg%3E';">
                </div>
                <div class="chart-info">
                    <h4>📦 小提琴图 + 箱线图组合</h4>
                    <p>展示各区域利润分布形态、中位数、均值（红点）及异常值，West/East波动大，Central集中且偏低。</p>
                    <span class="tech-tag">分布对比 · 离群检测</span>
                </div>
            </div>
            <!-- 7. 雷达图 -->
            <div class="chart-card">
                <div class="chart-img-container">
                    <img src="file:///C:/Users/lilinhao/Desktop/图片/雷达图.png" alt="区域综合表现雷达图" onerror="this.onerror=null;this.src='data:image/svg+xml,%3Csvg xmlns=%22http://www.w3.org/2000/svg%22 width=%22320%22 height=%22200%22 viewBox=%220 0 320 200%22%3E%3Crect width=%22100%25%22 height=%22100%25%22 fill=%22%23f0f4fa%22/%3E%3Ctext x=%22160%22 y=%22100%22 font-family=%22sans-serif%22 font-size=%2212%22 fill=%22%237c9ebf%22 text-anchor=%22middle%22%3E图片加载失败，请检查路径%3C/text%3E%3C/svg%3E';">
                </div>
                <div class="chart-info">
                    <h4>🎯 区域综合表现雷达图</h4>
                    <p>归一化指标（总利润/平均利润/利润率/平均销售额/订单量），West多边形最大，Central全面垫底。</p>
                    <span class="tech-tag">多维对比 · 策略优先级</span>
                </div>
            </div>
        </div>
        <div style="text-align: center; margin-top: 0.5rem; font-size: 0.75rem; color: #6c86a3;">
            <i class="fas fa-info-circle"></i> 图片路径为您的本地绝对路径，若仍无法显示请确认文件是否存在：<br>
            C:\Users\lilinhao\Desktop\图片\地图.png , 桑基图.png , 散点图.png , 直方图.png , 热力图.png , 箱线图.png , 雷达图.png
        </div>
    </div>

    <!-- 多层次分析 + 数据要求 + 适合人群（保持不变） -->
    <div class="section">
        <div class="two-columns">
            <div class="col">
                <i class="fas fa-chart-simple" style="font-size: 1.6rem; color:#1f6e8c;"></i>
                <h3 style="margin: 0.6rem 0 1rem 0;">📐 多层次分析支持</h3>
                <p style="margin-bottom: 1rem;">本技能专为嵌套结构数据设计，轻松实现：</p>
                <ul style="list-style: none; padding-left: 0;">
                    <li style="margin-bottom: 0.7rem;"><i class="fas fa-flag-usa" style="width: 1.6rem; color:#1f6e8c;"></i> <strong>空间层次</strong> – 州 (State) / 区域 (Region) / 邮政编码 聚合对比</li>
                    <li style="margin-bottom: 0.7rem;"><i class="fas fa-boxes"></i> <strong>产品层次</strong> – 大类(Category) → 子类(Sub-Category) → 单品名称，支持逐级钻取</li>
                    <li style="margin-bottom: 0.7rem;"><i class="fas fa-calendar-alt"></i> <strong>时间层次</strong> – 年/季度/月度趋势分析，窗口聚合与同比环比</li>
                    <li style="margin-bottom: 0.7rem;"><i class="fas fa-chart-line"></i> <strong>交易层次</strong> – 订单级利润/折扣分布，识别异常交易</li>
                    <li style="margin-bottom: 0.7rem;"><i class="fas fa-layer-group"></i> <strong>交叉层次</strong> – 区域×产品类别矩阵热力图、时间×空间小倍数图等复合视图</li>
                </ul>
                <div class="tech-tag">group_by 多变量组合 | 分面 | 透视图层</div>
            </div>
            <div class="col">
                <i class="fas fa-database" style="font-size: 1.6rem; color:#1f6e8c;"></i>
                <h3 style="margin: 0.6rem 0 1rem 0;">📁 数据要求（适配美国地区数据）</h3>
                <ul class="data-req-list">
                    <li><i class="fas fa-check-circle"></i> 必需字段：<strong>地理标识</strong>（州名/State、Region、经度/纬度 或 邮政编码）</li>
                    <li><i class="fas fa-check-circle"></i> 必需数值：<strong>销售额(Sales)、利润(Profit)、折扣(Discount)</strong></li>
                    <li><i class="fas fa-check-circle"></i> 推荐分类：产品类别(Category/Sub-Category)、客户细分(Segment)、运输模式(Ship Mode)</li>
                    <li><i class="fas fa-check-circle"></i> 时间字段：订单日期(Order Date) 用于时间序列 & 季节性分析</li>
                    <li><i class="fas fa-check-circle"></i> 数据规模：建议 ≥ 1000 条订单记录，以保证统计稳定性</li>
                    <li><i class="fas fa-check-circle"></i> 格式：.xlsx / .csv / .rds，支持缺失值过滤与类型转换</li>
                </ul>
                <hr>
                <i class="fas fa-users" style="font-size: 1.6rem; color:#1f6e8c;"></i>
                <h3 style="margin: 0.6rem 0 0.8rem 0;">👥 适合什么人用？</h3>
                <p><strong>📊 美国市场数据分析师</strong> – 需要分析美国各州/区域利润表现、折扣策略效果、地理销售异质性。<br>
                <strong>📚 应用统计/数据科学专业学生</strong> – 完成课程期末报告（如《数据可视化技术》），需生成多种高级图表并编写R代码。<br>
                <strong>📈 商业智能分析师</strong> – 基于美国销售数据（如Superstore）进行多层次诊断，为区域运营、产品优化提供可视化佐证。<br>
                <strong>🎓 科研人员</strong> – 研究空间经济、区域不平等或零售利润驱动因素，需要可复现的可视化流水线。<br>
                <strong>🛠️ R语言开发者</strong> – 希望复用现成的图形模板（地图/桑基图/雷达图）并快速适配自己的数据集。</p>
            </div>
        </div>
    </div>

    <!-- 核心交互区：动态数据绘图（新增功能，放在所有原有内容之后） -->
    <div class="section" style="background: #fefef9;">
        <div class="gallery-title">
            <i class="fas fa-chalkboard-user" style="color:#1f6e8c;"></i>
            <span>📌 交互式数据工坊 · 上传数据即时绘制专业图表</span>
        </div>
        <div class="interactive-panel">
            <div class="control-bar">
                <div class="ctrl-group">
                    <label><i class="fas fa-upload"></i> 上传数据 (CSV / Excel)</label>
                    <input type="file" id="fileUpload" accept=".csv, .xlsx, .xls">
                </div>
                <div class="ctrl-group">
                    <label><i class="fas fa-chart-bar"></i> 图表类型</label>
                    <select id="chartType">
                        <option value="map">🗺️ 美国各州利润地图 (Choropleth)</option>
                        <option value="heatmap">🔥 产品类别×区域利润热力图</option>
                        <option value="scatter">📈 销售额 vs 利润 (GAM趋势模拟)</option>
                        <option value="radar">📡 区域综合表现雷达图 (5指标归一化)</option>
                        <option value="violin">🎻 区域利润分布小提琴+箱线图</option>
                    </select>
                </div>
                <div class="ctrl-group">
                    <label><i class="fas fa-database"></i> 示例数据</label>
                    <button id="loadSampleBtn" style="background:#3a6b5b;"><i class="fas fa-table"></i> 加载Superstore示例</button>
                </div>
                <div class="ctrl-group">
                    <button id="drawChartBtn"><i class="fas fa-chart-line"></i> 绘制图表</button>
                </div>
            </div>
            <div id="plotlyChart" class="chart-container"></div>
            <div class="data-notice">
                <i class="fas fa-info-circle"></i> 支持字段映射：State（州全名）, Region, Category, Sales, Profit, Discount。地图需包含State字段，热力图需Region+Category，雷达图需Region+利润销售额等。示例数据基于Superstore风格，点击“加载示例”体验。
            </div>
        </div>
    </div>

    <!-- 代码片段展示（保持不变） -->
    <div class="section">
        <h3><i class="fas fa-terminal"></i> 核心代码示例 · 一键生成热力图矩阵</h3>
        <div class="code-block">
            <pre style="margin:0;">
# 载入核心包
library(tidyverse)

# 数据聚合：产品类别 × 区域 利润求和
heatmap_data <- superstore %>%
  group_by(Category, Region) %>%
  summarise(Total_Profit = sum(Profit, na.rm = TRUE), .groups = "drop")

# 绘制热力图 + 数值标签
ggplot(heatmap_data, aes(x = Region, y = Category, fill = Total_Profit)) +
  geom_tile(color = "white", size = 1.2) +
  geom_text(aes(label = scales::dollar(Total_Profit)), size = 4, fontface = "bold") +
  scale_fill_gradient2(low = "#D73027", mid = "white", high = "#4575B4",
                       midpoint = 0, name = "总利润") +
  labs(title = "产品类别 × 区域利润矩阵", x = "区域", y = "产品大类") +
  theme_minimal()</pre>
        </div>
        <div style="margin-top: 1rem;">
            <span class="tech-tag"><i class="fab fa-r-project"></i> 完整R脚本可导出，支持一键生成8+图形并保存为PNG/HTML</span>
        </div>
    </div>

    <footer>
        <i class="fas fa-chart-line"></i> 数据可视化绘图技能 · 聚焦代码实现与多层次分析 | 适配美国销售/经济数据 | 新增动态绘图工坊：上传自有数据，一键复现论文级图表<br>
        💡 静态展示区已使用您本地的7张图片，若仍无法显示请确认图片路径正确。
    </footer>
</div>

<script>
    // 动态绘图逻辑（与之前相同，保证交互功能完整）
    let currentDataset = null;

    const sampleRawData = [
        {State:"California", Region:"West", Category:"Technology", Sub_Category:"Phones", Sales:1200, Profit:450, Discount:0.05},
        {State:"California", Region:"West", Category:"Technology", Sub_Category:"Machines", Sales:800, Profit:200, Discount:0.1},
        {State:"Texas", Region:"Central", Category:"Furniture", Sub_Category:"Tables", Sales:500, Profit:-120, Discount:0.4},
        {State:"Texas", Region:"Central", Category:"Office Supplies", Sub_Category:"Paper", Sales:300, Profit:30, Discount:0.0},
        {State:"New York", Region:"East", Category:"Furniture", Sub_Category:"Chairs", Sales:950, Profit:80, Discount:0.2},
        {State:"New York", Region:"East", Category:"Technology", Sub_Category:"Phones", Sales:2100, Profit:890, Discount:0.03},
        {State:"Ohio", Region:"Central", Category:"Furniture", Sub_Category:"Tables", Sales:620, Profit:-200, Discount:0.5},
        {State:"Florida", Region:"South", Category:"Technology", Sub_Category:"Machines", Sales:440, Profit:30, Discount:0.2},
        {State:"Florida", Region:"South", Category:"Office Supplies", Sub_Category:"Binders", Sales:290, Profit:55, Discount:0.0},
        {State:"Washington", Region:"West", Category:"Technology", Sub_Category:"Phones", Sales:1100, Profit:410, Discount:0.02},
        {State:"Illinois", Region:"Central", Category:"Furniture", Sub_Category:"Chairs", Sales:780, Profit:-45, Discount:0.3},
        {State:"Massachusetts", Region:"East", Category:"Office Supplies", Sub_Category:"Paper", Sales:320, Profit:80, Discount:0.0},
        {State:"Texas", Region:"Central", Category:"Technology", Sub_Category:"Accessories", Sales:430, Profit:-10, Discount:0.25},
        {State:"California", Region:"West", Category:"Furniture", Sub_Category:"Tables", Sales:900, Profit:-300, Discount:0.45},
        {State:"Nevada", Region:"West", Category:"Technology", Sub_Category:"Phones", Sales:300, Profit:85, Discount:0.05},
        {State:"Arizona", Region:"West", Category:"Office Supplies", Sub_Category:"Paper", Sales:210, Profit:22, Discount:0.0},
        {State:"Colorado", Region:"Central", Category:"Technology", Sub_Category:"Machines", Sales:470, Profit:68, Discount:0.1},
        {State:"Georgia", Region:"South", Category:"Furniture", Sub_Category:"Chairs", Sales:540, Profit:-70, Discount:0.35},
        {State:"Pennsylvania", Region:"East", Category:"Technology", Sub_Category:"Phones", Sales:780, Profit:210, Discount:0.04},
    ];

    function loadSampleData() {
        currentDataset = [...sampleRawData];
        document.getElementById("plotlyChart").innerHTML = "<div style='text-align:center;padding:2rem;color:#1f6e8c'>✅ 已加载Superstore示例数据 (包含加州/德州等)，选择图表类型并点击“绘制图表”。</div>";
    }

    function aggregateStateProfit(data) {
        const map = new Map();
        data.forEach(row => {
            let state = row.State;
            if(!state) return;
            let profit = Number(row.Profit) || 0;
            map.set(state, (map.get(state) || 0) + profit);
        });
        return Array.from(map.entries()).map(([State, TotalProfit]) => ({State, TotalProfit}));
    }

    function aggregateCategoryRegionProfit(data) {
        const map = new Map();
        data.forEach(row => {
            let cat = row.Category, reg = row.Region;
            if(!cat || !reg) return;
            let profit = Number(row.Profit) || 0;
            let key = `${cat}|${reg}`;
            map.set(key, (map.get(key) || 0) + profit);
        });
        const result = [];
        for(let [key, val] of map.entries()) {
            let [Category, Region] = key.split("|");
            result.push({Category, Region, Profit: val});
        }
        return result;
    }

    function aggregateRegionRadar(data) {
        const regionStats = new Map();
        data.forEach(row => {
            let reg = row.Region;
            if(!reg) return;
            let profit = Number(row.Profit)||0, sales=Number(row.Sales)||0;
            if(!regionStats.has(reg)) regionStats.set(reg, {totalProfit:0, totalSales:0, count:0});
            let stat = regionStats.get(reg);
            stat.totalProfit += profit;
            stat.totalSales += sales;
            stat.count += 1;
        });
        let regions = Array.from(regionStats.keys());
        let rawVals = [];
        for(let reg of regions) {
            let s = regionStats.get(reg);
            let avgProfit = s.totalProfit / s.count;
            let profitRate = s.totalSales !== 0 ? (s.totalProfit / s.totalSales)*100 : 0;
            let avgSales = s.totalSales / s.count;
            let orderCount = s.count;
            rawVals.push({region:reg, totalProfit: s.totalProfit, avgProfit, profitRate, avgSales, orderCount});
        }
        const getNorm = (arr) => arr.map(v => (v - Math.min(...arr)) / (Math.max(...arr)-Math.min(...arr)+1e-9));
        let tpArr = rawVals.map(v=>v.totalProfit);
        let apArr = rawVals.map(v=>v.avgProfit);
        let prArr = rawVals.map(v=>v.profitRate);
        let asArr = rawVals.map(v=>v.avgSales);
        let ocArr = rawVals.map(v=>v.orderCount);
        let normTP = getNorm(tpArr), normAP = getNorm(apArr), normPR = getNorm(prArr), normAS = getNorm(asArr), normOC = getNorm(ocArr);
        let radarData = [];
        for(let i=0;i<rawVals.length;i++) {
            radarData.push({region: rawVals[i].region, values: [normTP[i], normAP[i], normPR[i], normAS[i], normOC[i]]});
        }
        return {radarData, metrics:['总利润','平均利润','利润率','平均销售额','订单量']};
    }

    function regionViolinData(data) {
        let groups = {};
        data.forEach(row=>{
            let reg = row.Region;
            if(!reg) return;
            let profit = Number(row.Profit)||0;
            if(!groups[reg]) groups[reg]=[];
            groups[reg].push(profit);
        });
        return groups;
    }

    async function renderChart() {
        let chartType = document.getElementById("chartType").value;
        let container = document.getElementById("plotlyChart");
        if(!currentDataset || currentDataset.length===0) {
            container.innerHTML = "<div style='padding:2rem;text-align:center'>⚠️ 请先上传数据或点击「加载Superstore示例」</div>";
            return;
        }
        let data = currentDataset;
        if(chartType === "map") {
            let stateProfit = aggregateStateProfit(data);
            if(stateProfit.length===0) { container.innerHTML="<div>错误：缺少State字段或利润无效</div>"; return; }
            let states = stateProfit.map(s=>s.State);
            let values = stateProfit.map(s=>s.TotalProfit);
            let fig = {
                type: 'choropleth',
                locationmode: 'USA-states',
                locations: states,
                z: values,
                colorscale: [[0,'#D73027'],[0.5,'#FEE090'],[1,'#4575B4']],
                colorbar: {title: "总利润 (美元)"},
                text: states,
                hoverinfo: 'text+z'
            };
            let layout = { title: '美国各州总利润空间分布 (红亏蓝盈)', geo: { scope: 'usa', projection: { type: 'albers usa' } }, height: 550 };
            Plotly.newPlot(container, [fig], layout);
        } else if(chartType === "heatmap") {
            let matrix = aggregateCategoryRegionProfit(data);
            let categories = [...new Set(matrix.map(m=>m.Category))];
            let regions = [...new Set(matrix.map(m=>m.Region))];
            let zMatrix = categories.map(cat => regions.map(reg => {
                let item = matrix.find(m=>m.Category===cat && m.Region===reg);
                return item ? item.Profit : 0;
            }));
            let heatData = { x: regions, y: categories, z: zMatrix, type: 'heatmap', colorscale: [[0,'#D73027'],[0.5,'#F9F9F9'],[1,'#4575B4']], text: zMatrix.map(row=>row.map(v=>`$${Math.round(v)}`)), hoverinfo: 'text' };
            let layout = { title: '产品类别 × 区域利润矩阵热力图', xaxis: {title:'区域'}, yaxis: {title:'产品大类'}, height: 500 };
            Plotly.newPlot(container, [heatData], layout);
        } else if(chartType === "scatter") {
            let scatterData = data.filter(d=> !isNaN(Number(d.Sales)) && !isNaN(Number(d.Profit))).map(d=>({sales: Number(d.Sales), profit: Number(d.Profit), discount: Number(d.Discount)||0}));
            let trace = { x: scatterData.map(s=>s.sales), y: scatterData.map(s=>s.profit), mode: 'markers', type: 'scatter', marker: { color: scatterData.map(s=>s.discount), colorscale: 'Viridis', colorbar: {title:'折扣率'}, showscale: true }, name: '订单' };
            let xsSorted = [...scatterData.map(s=>s.sales)].sort((a,b)=>a-b);
            let trendY = xsSorted.map(x => x<10000 ? x*0.08 : 600 + Math.log(x)*60);
            let traceLine = { x: xsSorted, y: trendY, mode: 'lines', type: 'scatter', line: { color: 'red', width: 2 }, name: '非线性趋势(模拟GAM)' };
            let layout = { title: '销售额 vs 利润 (颜色=折扣率)', xaxis: { title: '销售额 (美元)', type: 'log' }, yaxis: { title: '利润' }, height: 500 };
            Plotly.newPlot(container, [trace, traceLine], layout);
        } else if(chartType === "radar") {
            let {radarData, metrics} = aggregateRegionRadar(data);
            let traces = radarData.map(r => ({ type: 'scatterpolar', r: r.values, theta: metrics, fill: 'toself', name: r.region, line: { width: 2 } }));
            let layout = { polar: { radialaxis: { visible: true, range: [0,1] } }, title: '区域综合表现雷达图 (归一化指标)', height: 550 };
            Plotly.newPlot(container, traces, layout);
        } else if(chartType === "violin") {
            let groupMap = regionViolinData(data);
            let traces = [];
            for(let [region, profits] of Object.entries(groupMap)) {
                traces.push({ y: profits, type: 'violin', name: region, box: { visible: true }, meanline: { visible: true }, points: 'all', jitter: 0.3 });
            }
            let layout = { title: '各区域利润分布 (小提琴+箱线+均值)', yaxis: { title: '利润 (美元)' }, height: 550 };
            Plotly.newPlot(container, traces, layout);
        }
    }

    function handleFileUpload(file) {
        const ext = file.name.split('.').pop().toLowerCase();
        if(ext === 'csv') {
            Papa.parse(file, { header: true, dynamicTyping: true, complete: function(results) {
                if(results.data && results.data.length) currentDataset = results.data;
                else currentDataset = [];
                document.getElementById("plotlyChart").innerHTML = "<div style='padding:1rem;text-align:center'>✅ 数据加载成功，共"+currentDataset.length+"条记录</div>";
            }});
        } else if(ext === 'xlsx' || ext === 'xls') {
            const reader = new FileReader();
            reader.onload = function(e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, {type: 'array'});
                const sheet = workbook.Sheets[workbook.SheetNames[0]];
                const json = XLSX.utils.sheet_to_json(sheet);
                currentDataset = json;
                document.getElementById("plotlyChart").innerHTML = "<div style='padding:1rem;text-align:center'>✅ Excel 加载成功，共"+currentDataset.length+"条</div>";
            };
            reader.readAsArrayBuffer(file);
        } else {
            alert("仅支持 CSV 或 Excel 文件");
        }
    }

    document.getElementById("loadSampleBtn").addEventListener("click", ()=>{ loadSampleData(); });
    document.getElementById("drawChartBtn").addEventListener("click", ()=>{ if(!currentDataset) loadSampleData(); renderChart(); });
    document.getElementById("fileUpload").addEventListener("change", (e)=>{ if(e.target.files.length) handleFileUpload(e.target.files[0]); });
    loadSampleData();
</script>
</body>
</html>
