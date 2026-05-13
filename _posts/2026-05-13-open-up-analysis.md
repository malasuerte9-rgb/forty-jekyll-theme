<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>オープンアップグループ (2154) 決算分析ボード</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Chosen Palette: Warm Sand & Sage (暖かなベージュと洗練されたセージグリーンのパレット) -->
    <!-- Application Structure Plan: 
        1. ヘッダー: 投資判断を強調し、ユーザーに結論を即座に提示。
        2. メインダッシュボード: 進捗率を視覚化するプログレスバーを配置。
        3. 詳細タブ: 「財務分析」と「配当・将来性」を切り替え可能にし、情報過多を防ぐ。
        4. インタラクティブ・チャート: 3Q時点での進捗を可視化し、通期目標との距離感を直感的に理解させる。
        5. カードUI: 成長ドライバーを整理して表示。
    -->
    <!-- Visualization & Content Choices: 
        - 進捗状況 -> 目標達成度 -> Tailwind CSS プログレスバー -> 直感的な進捗確認。
        - セグメント変化 -> 収益性向上 -> Chart.js 棒グラフ -> 国内外の収益構造の変化を提示。
        - 配当利回り -> 投資魅力 -> 大きな数値表示とバッジ -> インパクトを最大化。
        - 成長性 -> 定性分析 -> アイコン付きリスト -> 読みやすさを重視。
        - NO SVG/Mermaid: 全てのグラフィックはCanvas(Chart.js)またはCSSで構築。
    -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@300;400;700&display=swap');
        body { font-family: 'Noto Sans JP', sans-serif; background-color: #fdfbf7; color: #3d3935; }
        .chart-container { position: relative; width: 100%; max-width: 600px; margin-left: auto; margin-right: auto; height: 350px; max-height: 400px; }
        .nav-active { border-bottom: 2px solid #8ba88e; color: #5a735c; }
        .card-shadow { box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.05); }
    </style>
</head>
<body class="min-h-screen">

    <header class="bg-white border-b border-stone-200 sticky top-0 z-50">
        <div class="max-w-5xl mx-auto px-4 py-4 flex justify-between items-center">
            <div>
                <h1 class="text-xl font-bold tracking-tight">Open Up Group <span class="text-stone-400 font-light">2154</span></h1>
                <p class="text-xs text-stone-500 uppercase tracking-widest">FY2026 Q3 Analysis</p>
            </div>
            <div class="flex items-center gap-3">
                <span class="text-sm font-medium text-stone-400">判定:</span>
                <span class="bg-[#8ba88e] text-white px-3 py-1 rounded-full text-sm font-bold">積増 (Buy More)</span>
            </div>
        </div>
    </header>

    <main class="max-w-5xl mx-auto px-4 py-8">
        
        <section class="mb-12">
            <div class="bg-white rounded-2xl p-8 card-shadow border border-stone-100">
                <h2 class="text-2xl font-bold mb-4">筋肉質な体質への変貌</h2>
                <p class="text-stone-600 leading-relaxed max-w-3xl">
                    英国事業の売却という「負の遺産」の処理を完了。今回の決算は、見かけの売上減に惑わされず、国内の高収益な技術者派遣に特化した「新・オープンアップ」の収益力に注目すべき内容です。
                </p>
            </div>
        </section>

        <div class="flex gap-8 border-b border-stone-200 mb-8 overflow-x-auto">
            <button onclick="switchTab('overview')" id="tab-btn-overview" class="pb-3 px-2 font-bold nav-active transition-all">進捗サマリー</button>
            <button onclick="switchTab('finance')" id="tab-btn-finance" class="pb-3 px-2 font-bold text-stone-400 hover:text-stone-600 transition-all">財務・利益分析</button>
            <button onclick="switchTab('yield')" id="tab-btn-yield" class="pb-3 px-2 font-bold text-stone-400 hover:text-stone-600 transition-all">配当・将来性</button>
        </div>

        <div id="tab-overview" class="space-y-8">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                <div class="bg-white p-6 rounded-2xl card-shadow border border-stone-50">
                    <h3 class="font-bold text-stone-400 text-sm mb-6 uppercase tracking-wider">売上収益 進捗率</h3>
                    <div class="flex items-end justify-between mb-2">
                        <span class="text-4xl font-bold">75.0<span class="text-xl">%</span></span>
                        <span class="text-stone-400 text-sm">目標 1,670億円</span>
                    </div>
                    <div class="w-full bg-stone-100 h-3 rounded-full overflow-hidden">
                        <div id="bar-revenue" class="bg-stone-800 h-full transition-all duration-1000 ease-out" style="width: 0%"></div>
                    </div>
                    <p class="mt-4 text-sm text-stone-500 italic">※英国事業売却の影響を含みますが、計画通りの推移です。</p>
                </div>

                <div class="bg-white p-6 rounded-2xl card-shadow border border-stone-50">
                    <h3 class="font-bold text-stone-400 text-sm mb-6 uppercase tracking-wider">EPS (1株利益) 進捗率</h3>
                    <div class="flex items-end justify-between mb-2">
                        <span class="text-4xl font-bold text-[#8ba88e]">77.3<span class="text-xl">%</span></span>
                        <span class="text-stone-400 text-sm">3Q実績 111.95円</span>
                    </div>
                    <div class="w-full bg-stone-100 h-3 rounded-full overflow-hidden">
                        <div id="bar-eps" class="bg-[#8ba88e] h-full transition-all duration-1000 ease-out" style="width: 0%"></div>
                    </div>
                    <p class="mt-4 text-sm text-stone-500 italic">※予想比 103.1% と、収益性が大幅に改善しています。</p>
                </div>
            </div>

            <div class="bg-[#3d3935] text-white p-8 rounded-3xl flex flex-col md:flex-row items-center justify-between gap-6">
                <div>
                    <h3 class="text-xl font-bold mb-2">通期ガイダンス判定</h3>
                    <p class="opacity-80">利益ベースで75%を超えており、上方修正の期待も持てる着地です。</p>
                </div>
                <div class="text-5xl">⭕️</div>
            </div>
        </div>

        <div id="tab-finance" class="hidden space-y-8">
            <div class="bg-white p-8 rounded-2xl card-shadow border border-stone-100">
                <h3 class="text-lg font-bold mb-6">売上 vs 利益のギャップ分析</h3>
                <p class="text-stone-600 mb-8 text-sm">「売上が減ったのに利益が増えた」要因は、低利益な海外事業の切り離しにあります。以下のグラフは、進捗ベースでの健全性を示しています。</p>
                <div class="chart-container">
                    <canvas id="progressChart"></canvas>
                </div>
            </div>
        </div>

        <div id="tab-yield" class="hidden space-y-8">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <div class="md:col-span-1 bg-[#f0ede9] p-8 rounded-2xl text-center flex flex-col justify-center">
                    <h3 class="text-stone-500 font-bold text-sm mb-4">予想配当利回り</h3>
                    <div class="text-6xl font-bold text-[#5a735c]">4.83<span class="text-2xl">%</span></div>
                    <p class="mt-4 text-sm text-stone-400">配当性向50%以上を維持</p>
                </div>
                <div class="md:col-span-2 bg-white p-8 rounded-2xl card-shadow border border-stone-50">
                    <h3 class="text-lg font-bold mb-6">成長を支える3つの柱</h3>
                    <div class="space-y-4">
                        <div class="flex items-start gap-4">
                            <div class="w-8 h-8 bg-[#8ba88e] rounded-lg flex-shrink-0 flex items-center justify-center text-white font-bold">1</div>
                            <div>
                                <h4 class="font-bold">国内IT・建設派遣の堅調</h4>
                                <p class="text-sm text-stone-500">エンジニア不足は構造的。稼働率・単価ともに上昇傾向。</p>
                            </div>
                        </div>
                        <div class="flex items-start gap-4">
                            <div class="w-8 h-8 bg-stone-800 rounded-lg flex-shrink-0 flex items-center justify-center text-white font-bold">2</div>
                            <div>
                                <h4 class="font-bold">資本効率 (ROE) の向上</h4>
                                <p class="text-sm text-stone-500">海外撤退によりROICが改善。利益重視の経営へシフト。</p>
                            </div>
                        </div>
                        <div class="flex items-start gap-4">
                            <div class="w-8 h-8 bg-stone-400 rounded-lg flex-shrink-0 flex items-center justify-center text-white font-bold">3</div>
                            <div>
                                <h4 class="font-bold">強固な財務基盤</h4>
                                <p class="text-sm text-stone-500">キャッシュが蓄積。増配や自社株買いの余地も大きい。</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </main>

    <footer class="bg-stone-100 border-t border-stone-200 mt-20 py-12 text-center text-stone-400 text-sm">
        <p class="mb-4 max-w-xl mx-auto px-4">
            本アプリは2026年5月12日発表の決算短信に基づき、投資家への情報提供を目的として設計されています。
        </p>
        <div class="flex justify-center gap-6">
            <a href="https://www.openupgroup.co.jp/ir/library/results/" target="_blank" class="hover:text-stone-800 transition-colors">公式IR資料</a>
            <span>次期決算: 2026年8月7日</span>
        </div>
    </footer>

    <script>
        const state = {
            activeTab: 'overview',
            revenueProgress: 75.0,
            epsProgress: 77.3
        };

        function switchTab(tabId) {
            document.querySelectorAll('[id^="tab-"]').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('[id^="tab-btn-"]').forEach(el => {
                el.classList.remove('nav-active', 'text-stone-800');
                el.classList.add('text-stone-400');
            });

            document.getElementById(`tab-${tabId}`).classList.remove('hidden');
            const activeBtn = document.getElementById(`tab-btn-${tabId}`);
            activeBtn.classList.add('nav-active', 'text-stone-800');
            activeBtn.classList.remove('text-stone-400');

            if (tabId === 'finance') {
                renderFinanceChart();
            }
        }

        function initAnimations() {
            setTimeout(() => {
                document.getElementById('bar-revenue').style.width = `${state.revenueProgress}%`;
                document.getElementById('bar-eps').style.width = `${state.epsProgress}%`;
            }, 300);
        }

        let financeChart = null;
        function renderFinanceChart() {
            const ctx = document.getElementById('progressChart').getContext('2d');
            
            if (financeChart) financeChart.destroy();

            financeChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['売上収益', '純利益'],
                    datasets: [
                        {
                            label: '3Q実績 (進捗)',
                            data: [1252.6, 96.9],
                            backgroundColor: ['#3d3935', '#8ba88e'],
                            borderRadius: 8
                        },
                        {
                            label: '通期目標',
                            data: [1670, 125.5],
                            backgroundColor: ['#e2e0dd', '#e2e0dd'],
                            borderRadius: 8
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { position: 'bottom', labels: { boxWidth: 12, font: { size: 10 } } },
                        tooltip: { backgroundColor: '#3d3935' }
                    },
                    scales: {
                        y: {
                            beginAtZero: true,
                            grid: { color: '#f0f0f0' },
                            title: { display: true, text: '単位: 億円', font: { size: 10 } }
                        },
                        x: { grid: { display: false } }
                    }
                }
            });
        }

        document.addEventListener('DOMContentLoaded', initAnimations);
    </script>
</body>
</html>
