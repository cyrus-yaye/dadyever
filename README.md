<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>清朝人口统计互动图表</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Microsoft YaHei', Arial, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            color: #333;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }

        .header {
            text-align: center;
            margin-bottom: 30px;
            color: white;
        }

        .header h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .header p {
            font-size: 1.1rem;
            opacity: 0.9;
        }

        .controls {
            background: rgba(255,255,255,0.95);
            padding: 20px;
            border-radius: 15px;
            margin-bottom: 20px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.1);
            backdrop-filter: blur(10px);
        }

        .control-group {
            display: flex;
            flex-wrap: wrap;
            gap: 15px;
            align-items: center;
            justify-content: center;
        }

        .control-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 5px;
        }

        .control-item label {
            font-weight: bold;
            color: #555;
        }

        select, button {
            padding: 8px 15px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 14px;
            transition: all 0.3s ease;
        }

        select:focus, button:hover {
            border-color: #667eea;
            outline: none;
        }

        button {
            background: linear-gradient(45deg, #667eea, #764ba2);
            color: white;
            border: none;
            cursor: pointer;
            font-weight: bold;
        }

        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
        }

        .chart-container {
            background: rgba(255,255,255,0.95);
            padding: 30px;
            border-radius: 15px;
            margin-bottom: 20px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.1);
            backdrop-filter: blur(10px);
        }

        .chart {
            width: 100%;
            height: 400px;
            position: relative;
            overflow: hidden;
        }

        .bar {
            position: absolute;
            bottom: 0;
            background: linear-gradient(to top, #667eea, #764ba2);
            border-radius: 4px 4px 0 0;
            transition: all 0.3s ease;
            cursor: pointer;
        }

        .bar:hover {
            transform: scaleY(1.05);
            box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
        }

        .bar-label {
            position: absolute;
            bottom: -25px;
            left: 50%;
            transform: translateX(-50%);
            font-size: 12px;
            color: #666;
            text-align: center;
        }

        .bar-value {
            position: absolute;
            top: -25px;
            left: 50%;
            transform: translateX(-50%);
            font-size: 11px;
            color: #333;
            font-weight: bold;
            background: rgba(255,255,255,0.9);
            padding: 2px 6px;
            border-radius: 4px;
        }

        .info-panel {
            background: rgba(255,255,255,0.95);
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.1);
            backdrop-filter: blur(10px);
        }

        .info-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
        }

        .info-card {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            transition: transform 0.3s ease;
        }

        .info-card:hover {
            transform: translateY(-5px);
        }

        .info-card h3 {
            margin-bottom: 10px;
            font-size: 1.2rem;
        }

        .info-card p {
            font-size: 0.9rem;
            opacity: 0.9;
        }

        .game-link {
            position: fixed;
            top: 20px;
            right: 20px;
            background: linear-gradient(45deg, #ff6b6b, #ee5a24);
            color: white;
            padding: 15px 25px;
            border-radius: 25px;
            text-decoration: none;
            font-weight: bold;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            transition: all 0.3s ease;
            z-index: 1000;
        }

        .game-link:hover {
            transform: translateY(-3px);
            box-shadow: 0 6px 20px rgba(0,0,0,0.3);
        }

        @media (max-width: 768px) {
            .container {
                padding: 10px;
            }
            
            .header h1 {
                font-size: 2rem;
            }
            
            .control-group {
                flex-direction: column;
            }
            
            .chart {
                height: 300px;
            }
            
            .game-link {
                position: relative;
                top: auto;
                right: auto;
                display: block;
                text-align: center;
                margin: 20px auto;
                width: fit-content;
            }
        }

        .loading {
            display: none;
            text-align: center;
            padding: 20px;
            color: white;
        }

        .spinner {
            border: 4px solid rgba(255,255,255,0.3);
            border-radius: 50%;
            border-top: 4px solid white;
            width: 40px;
            height: 40px;
            animation: spin 1s linear infinite;
            margin: 0 auto 10px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
    </style>
</head>
<body>
    <a href="https://michael1982.my.canva.site/dag11jrnr94" class="game-link" target="_blank">🎮 人口知識遊戲</a>
    
    <div class="container">
        <div class="header">
            <h1>清朝人口統計互動圖表</h1>
            <p>探索清朝社會發展對人口增減的影響（順治至宣統年間）</p>
        </div>

        <div class="controls">
            <div class="control-group">
                <div class="control-item">
                    <label>統計類型</label>
                    <select id="chartType">
                        <option value="population">人口數量</option>
                        <option value="growth">增長率</option>
                        <option value="comparison">朝代對比</option>
                    </select>
                </div>
                <div class="control-item">
                    <label>時間範圍</label>
                    <select id="timeRange">
                        <option value="all">全部時期</option>
                        <option value="early">順治-康熙</option>
                        <option value="middle">雍正-乾隆</option>
                        <option value="late">嘉慶-宣統</option>
                    </select>
                </div>
                <div class="control-item">
                    <label>顯示模式</label>
                    <select id="displayMode">
                        <option value="bars">柱狀圖</option>
                        <option value="lines">折線圖</option>
                        <option value="pie">餅圖</option>
                    </select>
                </div>
                <button onclick="updateChart()">更新圖表</button>
                <button onclick="resetChart()">重置</button>
            </div>
        </div>

        <div class="loading" id="loading">
            <div class="spinner"></div>
            <p>正在加載數據...</p>
        </div>

        <div class="chart-container">
            <div class="chart" id="chart">
                <!-- 圖表將在這裡動態生成 -->
            </div>
        </div>

        <div class="info-panel">
            <div class="info-grid">
                <div class="info-card">
                    <h3>人口峰值</h3>
                    <p>道光二十九年達到4.13億，為清朝官方統計最高值</p>
                </div>
                <div class="info-card">
                    <h3>增長最快</h3>
                    <p>乾隆年間年均增長超過1000萬，被稱為"人口爆炸"時期</p>
                </div>
                <div class="info-card">
                    <h3>戰亂影響</h3>
                    <p>咸豐-同治年間因太平天國等戰亂，人口銳減約9000萬</p>
                </div>
                <div class="info-card">
                    <h3>統計變革</h3>
                    <p>乾隆六年(1741)開始由"人丁"統計改為"人口"統計，數據更準確</p>
                </div>
            </div>
        </div>
    </div>

    <script>
        // 清朝人口数据
        const populationData = [
            { year: '順治十八年(1661)', population: 19203233, growth: 0, description: '明末戰亂後恢復期' },
            { year: '康熙五十年(1711)', population: 24621324, growth: 28.2, description: '三藩之亂後穩定增長' },
            { year: '雍正十二年(1734)', population: 27355462, growth: 11.1, description: '攤丁入畝政策實施' },
            { year: '乾隆六年(1741)', population: 143411559, growth: 424.4, description: '統計方法改革' },
            { year: '乾隆二十九年(1764)', population: 205591017, growth: 43.4, description: '人口快速增長期' },
            { year: '乾隆六十年(1795)', population: 296965545, growth: 44.4, description: '乾嘉盛世頂峰' },
            { year: '嘉慶二十四年(1819)', population: 301260545, growth: 1.4, description: '白蓮教亂後恢復' },
            { year: '道光二十九年(1850)', population: 412986649, growth: 37.1, description: '清朝人口峰值' },
            { year: '光緒元年(1875)', population: 322655781, growth: -21.9, description: '太平天國戰亂影響' },
            { year: '宣統三年(1911)', population: 340000000, growth: 5.4, description: '清末緩慢恢復' }
        ];

        let currentData = [...populationData];

        function showLoading() {
            document.getElementById('loading').style.display = 'block';
            document.getElementById('chart').style.opacity = '0.5';
        }

        function hideLoading() {
            document.getElementById('loading').style.display = 'none';
            document.getElementById('chart').style.opacity = '1';
        }

        function createBarsChart(data) {
            const chart = document.getElementById('chart');
            chart.innerHTML = '';
            
            const maxValue = Math.max(...data.map(d => d.population));
            const chartWidth = chart.offsetWidth;
            const barWidth = Math.max(60, (chartWidth - 100) / data.length);
            const maxHeight = 350;

            data.forEach((item, index) => {
                const bar = document.createElement('div');
                bar.className = 'bar';
                bar.style.left = `${50 + index * (barWidth + 10)}px`;
                bar.style.width = `${barWidth}px`;
                bar.style.height = `${(item.population / maxValue) * maxHeight}px`;
                
                const label = document.createElement('div');
                label.className = 'bar-label';
                label.textContent = item.year.split('(')[0];
                bar.appendChild(label);
                
                const value = document.createElement('div');
                value.className = 'bar-value';
                value.textContent = `${(item.population / 10000).toFixed(0)}萬`;
                bar.appendChild(value);
                
                bar.addEventListener('click', () => {
                    alert(`${item.year}\n人口: ${item.population.toLocaleString()}\n增長率: ${item.growth}%\n${item.description}`);
                });
                
                chart.appendChild(bar);
            });
        }

        function createLinesChart(data) {
            const chart = document.getElementById('chart');
            chart.innerHTML = '';
            
            const maxValue = Math.max(...data.map(d => d.population));
            const chartWidth = chart.offsetWidth - 100;
            const chartHeight = 350;
            const stepX = chartWidth / (data.length - 1);

            let pathData = '';
            const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
            svg.setAttribute('width', chartWidth + 100);
            svg.setAttribute('height', chartHeight + 50);
            svg.style.position = 'absolute';
            svg.style.left = '0';
            svg.style.top = '0';

            data.forEach((item, index) => {
                const x = 50 + index * stepX;
                const y = chartHeight - (item.population / maxValue) * chartHeight + 20;
                
                if (index === 0) {
                    pathData += `M ${x} ${y}`;
                } else {
                    pathData += ` L ${x} ${y}`;
                }

                // 添加数据点
                const circle = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
                circle.setAttribute('cx', x);
                circle.setAttribute('cy', y);
                circle.setAttribute('r', '6');
                circle.setAttribute('fill', '#667eea');
                circle.setAttribute('stroke', 'white');
                circle.setAttribute('stroke-width', '2');
                circle.style.cursor = 'pointer';
                
                circle.addEventListener('click', () => {
                    alert(`${item.year}\n人口: ${item.population.toLocaleString()}\n增長率: ${item.growth}%\n${item.description}`);
                });
                
                svg.appendChild(circle);
            });

            const path = document.createElementNS('http://www.w3.org/2000/svg', 'path');
            path.setAttribute('d', pathData);
            path.setAttribute('stroke', 'url(#gradient)');
            path.setAttribute('stroke-width', '3');
            path.setAttribute('fill', 'none');
            path.setAttribute('stroke-linecap', 'round');

            const defs = document.createElementNS('http://www.w3.org/2000/svg', 'defs');
            const gradient = document.createElementNS('http://www.w3.org/2000/svg', 'linearGradient');
            gradient.setAttribute('id', 'gradient');
            gradient.setAttribute('x1', '0%');
            gradient.setAttribute('y1', '0%');
            gradient.setAttribute('x2', '100%');
            gradient.setAttribute('y2', '0%');

            const stop1 = document.createElementNS('http://www.w3.org/2000/svg', 'stop');
            stop1.setAttribute('offset', '0%');
            stop1.setAttribute('stop-color', '#667eea');

            const stop2 = document.createElementNS('http://www.w3.org/2000/svg', 'stop');
            stop2.setAttribute('offset', '100%');
            stop2.setAttribute('stop-color', '#764ba2');

            gradient.appendChild(stop1);
            gradient.appendChild(stop2);
            defs.appendChild(gradient);
            svg.appendChild(defs);
            svg.appendChild(path);
            chart.appendChild(svg);
        }

        function createPieChart(data) {
            const chart = document.getElementById('chart');
            chart.innerHTML = '';
            
            const total = data.reduce((sum, item) => sum + item.population, 0);
            const colors = ['#667eea', '#764ba2', '#f093fb', '#f5576c', '#4facfe', '#00f2fe', '#43e97b', '#38f9d7', '#ffecd2', '#fcb69f'];
            
            let currentAngle = 0;
            const centerX = chart.offsetWidth / 2;
            const centerY = 175;
            const radius = 120;

            const svg = document.createElementNS('http://www.w3.org/2000/svg', 'svg');
            svg.setAttribute('width', chart.offsetWidth);
            svg.setAttribute('height', '350');
            svg.style.position = 'absolute';
            svg.style.left = '0';
            svg.style.top = '0';

            data.forEach((item, index) => {
                const percentage = (item.population / total) * 100;
                const angle = (percentage / 100) * 360;
                const endAngle = currentAngle + angle;

                const x1 = centerX + radius * Math.cos((currentAngle * Math.PI) / 180);
                const y1 = centerY + radius * Math.sin((currentAngle * Math.PI) / 180);
                const x2 = centerX + radius * Math.cos((endAngle * Math.PI) / 180);
                const y2 = centerY + radius * Math.sin((endAngle * Math.PI) / 180);

                const largeArcFlag = angle > 180 ? 1 : 0;

                const pathData = [
                    `M ${centerX} ${centerY}`,
                    `L ${x1} ${y1}`,
                    `A ${radius} ${radius} 0 ${largeArcFlag} 1 ${x2} ${y2}`,
                    'Z'
                ].join(' ');

                const path = document.createElementNS('http://www.w3.org/2000/svg', 'path');
                path.setAttribute('d', pathData);
                path.setAttribute('fill', colors[index % colors.length]);
                path.setAttribute('stroke', 'white');
                path.setAttribute('stroke-width', '2');
                path.style.cursor = 'pointer';
                
                path.addEventListener('click', () => {
                    alert(`${item.year}\n人口: ${item.population.toLocaleString()}\n占比: ${percentage.toFixed(1)}%\n${item.description}`);
                });

                // 添加标签
                const labelAngle = currentAngle + angle / 2;
                const labelRadius = radius + 30;
                const labelX = centerX + labelRadius * Math.cos((labelAngle * Math.PI) / 180);
                const labelY = centerY + labelRadius * Math.sin((labelAngle * Math.PI) / 180);

                const text = document.createElementNS('http://www.w3.org/2000/svg', 'text');
                text.setAttribute('x', labelX);
                text.setAttribute('y', labelY);
                text.setAttribute('text-anchor', 'middle');
                text.setAttribute('dominant-baseline', 'middle');
                text.setAttribute('font-size', '12');
                text.setAttribute('fill', '#333');
                text.textContent = item.year.split('(')[0];

                svg.appendChild(path);
                svg.appendChild(text);
                currentAngle = endAngle;
            });

            chart.appendChild(svg);
        }

        function updateChart() {
            showLoading();
            
            setTimeout(() => {
                const chartType = document.getElementById('chartType').value;
                const timeRange = document.getElementById('timeRange').value;
                const displayMode = document.getElementById('displayMode').value;

                // 篩選數據
                let filteredData = [...populationData];
                if (timeRange === 'early') {
                    filteredData = filteredData.slice(0, 3);
                } else if (timeRange === 'middle') {
                    filteredData = filteredData.slice(2, 6);
                } else if (timeRange === 'late') {
                    filteredData = filteredData.slice(6);
                }

                // 根據圖表類型調整數據
                if (chartType === 'growth') {
                    filteredData = filteredData.map(d => ({...d, population: Math.abs(d.growth) * 1000000}));
                }

                currentData = filteredData;

                // 創建圖表
                if (displayMode === 'bars') {
                    createBarsChart(filteredData);
                } else if (displayMode === 'lines') {
                    createLinesChart(filteredData);
                } else if (displayMode === 'pie') {
                    createPieChart(filteredData);
                }

                hideLoading();
            }, 500);
        }

        function resetChart() {
            document.getElementById('chartType').value = 'population';
            document.getElementById('timeRange').value = 'all';
            document.getElementById('displayMode').value = 'bars';
            updateChart();
        }

        // 初始化圖表
        window.addEventListener('load', () => {
            updateChart();
        });

        // 響應式處理
        window.addEventListener('resize', () => {
            setTimeout(updateChart, 100);
        });
    </script>
</body>
</html>
