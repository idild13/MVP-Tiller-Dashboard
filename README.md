<!DOCTYPE html>
<html lang="en" class="scroll-smooth">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tiller Restaurant Dashboard</title>
    <!-- Chosen Palette: Warm Neutral Palette -->
    <!-- Application Structure Plan: A single-page dashboard with a top navigation bar linking to four thematic sections: Seasonality, Staff, Service Type, and Menu. This structure is task-oriented for restaurant owners, allowing them to intuitively explore key business areas. Interactions include toggling chart metrics and switching between different views within a section to provide layered information without clutter. -->
    <!-- Visualization & Content Choices: Seasonality -> Line/Bar Chart (Goal: Show change over time) with toggle for Revenue/Orders. Staff -> Bar Chart (Goal: Compare workload) + Info Card (Goal: Inform on ML forecast). Service Type -> Donut Chart (Goal: Show composition). Menu -> Bar Chart/List (Goal: Compare/Organize) with view toggle. All visualizations use Chart.js on Canvas, chosen for interactivity and adherence to requirements. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f8f7f4;
            color: #4a4a4a;
        }
        .chart-container {
            position: relative;
            width: 100%;
            height: 300px;
            max-height: 400px;
        }
        @media (min-width: 768px) {
            .chart-container {
                height: 350px;
            }
        }
        .nav-link {
            transition: color 0.3s, border-color 0.3s;
        }
        .active-link {
            color: #c084fc;
            border-bottom-color: #c084fc;
        }
        .stat-card {
            background-color: white;
            border-radius: 0.75rem;
            padding: 1.5rem;
            box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1), 0 2px 4px -2px rgb(0 0 0 / 0.1);
            transition: transform 0.3s, box-shadow 0.3s;
        }
        .stat-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 15px -3px rgb(0 0 0 / 0.1), 0 4px 6px -4px rgb(0 0 0 / 0.1);
        }
        .btn-toggle {
            transition: background-color 0.3s, color 0.3s;
        }
        .btn-active {
            background-color: #c084fc;
            color: white;
        }
    </style>
</head>
<body class="antialiased">

    <header class="bg-white/80 backdrop-blur-md sticky top-0 z-50 shadow-sm">
        <div class="container mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-16">
                <div class="flex items-center">
                    <span class="text-2xl font-bold text-purple-500">🍴</span>
                    <h1 class="text-xl font-bold ml-2 text-gray-800">Tiller Restaurant Dashboard</h1>
                </div>
                <nav class="hidden md:flex space-x-8">
                    <a href="#seasonality" class="nav-link text-gray-500 hover:text-purple-500 border-b-2 border-transparent pb-1">Seasonality</a>
                    <a href="#staff" class="nav-link text-gray-500 hover:text-purple-500 border-b-2 border-transparent pb-1">Staff</a>
                    <a href="#service" class="nav-link text-gray-500 hover:text-purple-500 border-b-2 border-transparent pb-1">Service Type</a>
                    <a href="#menu" class="nav-link text-gray-500 hover:text-purple-500 border-b-2 border-transparent pb-1">Menu</a>
                </nav>
            </div>
        </div>
    </header>

    <main class="container mx-auto px-4 sm:px-6 lg:px-8 py-8">
        <section id="overview" class="mb-12">
            <h2 class="text-3xl font-bold text-gray-800 mb-2">Project Overview</h2>
            <p class="text-lg text-gray-600 mb-6">A Minimum Viable Product to help restaurant owners in Paris leverage their data for growth. Analysis based on data from 2015-2020.</p>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
                <div class="stat-card">
                    <h3 class="text-gray-500 text-sm font-medium">Total Orders Analyzed</h3>
                    <p class="text-3xl font-bold text-purple-600 mt-1">1.28M</p>
                </div>
                <div class="stat-card">
                    <h3 class="text-gray-500 text-sm font-medium">Restaurants</h3>
                    <p class="text-3xl font-bold text-purple-600 mt-1">21</p>
                </div>
                <div class="stat-card">
                    <h3 class="text-gray-500 text-sm font-medium">Waiters</h3>
                    <p class="text-3xl font-bold text-purple-600 mt-1">61</p>
                </div>
                <div class="stat-card">
                    <h3 class="text-gray-500 text-sm font-medium">Date Range</h3>
                    <p class="text-3xl font-bold text-purple-600 mt-1">5 Years</p>
                </div>
            </div>
        </section>

        <div class="space-y-16">
            <section id="seasonality" class="pt-20 -mt-16">
                <h2 class="text-3xl font-bold text-gray-800 mb-2">Seasonality Insights</h2>
                <p class="text-lg text-gray-600 mb-6">Understand the daily rhythm of your business to optimize for peak hours and manage slow periods.</p>
                <div class="bg-white p-6 rounded-lg shadow-lg">
                    <div class="flex justify-center mb-4">
                        <div class="bg-gray-100 rounded-full p-1 flex space-x-1">
                            <button id="showRevenue" class="btn-toggle btn-active px-4 py-2 text-sm font-medium rounded-full">Revenue by Hour</button>
                            <button id="showOrders" class="btn-toggle px-4 py-2 text-sm font-medium rounded-full">Orders by Hour</button>
                        </div>
                    </div>
                    <div class="chart-container">
                        <canvas id="seasonalityChart"></canvas>
                    </div>
                </div>
            </section>

            <section id="staff" class="pt-20 -mt-16">
                <h2 class="text-3xl font-bold text-gray-800 mb-2">Staff & Customer Management</h2>
                <p class="text-lg text-gray-600 mb-6">Analyze staff workload and forecast customer attendance to improve service and efficiency.</p>
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                    <div class="lg:col-span-2 bg-white p-6 rounded-lg shadow-lg">
                        <h3 class="font-semibold text-lg mb-4">Workload per Waiter (Sample)</h3>
                         <div class="chart-container">
                            <canvas id="staffChart"></canvas>
                        </div>
                    </div>
                    <div class="stat-card flex flex-col justify-center items-center text-center">
                         <h3 class="text-gray-500 text-lg font-medium">Attendance Forecast</h3>
                         <p class="text-purple-600 text-sm mb-4">(Machine Learning Model)</p>
                         <p class="text-5xl font-bold text-purple-600">~1,250</p>
                         <p class="text-gray-600 mt-2">Customers expected next week</p>
                         <p class="mt-4 text-xs text-gray-500">This forecast helps in planning staff schedules and inventory for the upcoming week, reducing costs and preventing shortages.</p>
                    </div>
                </div>
            </section>
            
            <section id="service" class="pt-20 -mt-16">
                <h2 class="text-3xl font-bold text-gray-800 mb-2">Service Type Performance</h2>
                <p class="text-lg text-gray-600 mb-6">Discover how your customers prefer to dine and strategize your operations accordingly.</p>
                 <div class="bg-white p-6 rounded-lg shadow-lg">
                    <h3 class="font-semibold text-center text-lg mb-4">Order Breakdown by Service Type</h3>
                    <div class="chart-container max-w-sm mx-auto">
                        <canvas id="serviceChart"></canvas>
                    </div>
                </div>
            </section>

            <section id="menu" class="pt-20 -mt-16">
                <h2 class="text-3xl font-bold text-gray-800 mb-2">Menu Category Analysis</h2>
                <p class="text-lg text-gray-600 mb-6">Identify your most popular products and winning combinations to drive sales and upsell opportunities.</p>
                <div class="bg-white p-6 rounded-lg shadow-lg">
                    <div class="flex justify-center mb-4">
                        <div class="bg-gray-100 rounded-full p-1 flex space-x-1">
                            <button id="showSellers" class="btn-toggle btn-active px-4 py-2 text-sm font-medium rounded-full">Top/Bottom Sellers</button>
                            <button id="showCombos" class="btn-toggle px-4 py-2 text-sm font-medium rounded-full">Winning Combos</button>
                        </div>
                    </div>
                    <div id="sellersView">
                         <div class="chart-container">
                            <canvas id="menuChart"></canvas>
                        </div>
                    </div>
                    <div id="combosView" class="hidden">
                        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 text-center">
                            <div class="bg-purple-50 p-4 rounded-lg">
                                <p class="font-bold text-purple-800 text-lg">#1 Combo</p>
                                <p class="text-gray-700">Main Course & Wine</p>
                            </div>
                            <div class="bg-purple-50 p-4 rounded-lg">
                                <p class="font-bold text-purple-800 text-lg">#2 Combo</p>
                                <p class="text-gray-700">Coffee & Dessert</p>
                            </div>
                            <div class="bg-purple-50 p-4 rounded-lg">
                                <p class="font-bold text-purple-800 text-lg">#3 Combo</p>
                                <p class="text-gray-700">Appetizer & Cocktail</p>
                            </div>
                             <div class="bg-purple-50 p-4 rounded-lg">
                                <p class="font-bold text-purple-800 text-lg">#4 Combo</p>
                                <p class="text-gray-700">Burger & Beer</p>
                            </div>
                             <div class="bg-purple-50 p-4 rounded-lg">
                                <p class="font-bold text-purple-800 text-lg">#5 Combo</p>
                                <p class="text-gray-700">Salad & Water</p>
                            </div>
                        </div>
                        <p class="text-center mt-6 text-gray-600">Use these popular pairings to train staff on upselling and create profitable new combo deals.</p>
                    </div>
                </div>
            </section>
        </div>
    </main>
<script>
document.addEventListener('DOMContentLoaded', () => {
    let seasonalityChart, staffChart, serviceChart, menuChart;

    const navLinks = document.querySelectorAll('.nav-link');
    const sections = document.querySelectorAll('main section');

    const observer = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
            if (entry.isIntersecting) {
                navLinks.forEach(link => {
                    link.classList.remove('active-link');
                    if (link.getAttribute('href').substring(1) === entry.target.id) {
                        link.classList.add('active-link');
                    }
                });
            }
        });
    }, { threshold: 0.5 });

    sections.forEach(section => observer.observe(section));

    const seasonalityData = {
        revenue: [120, 200, 450, 700, 900, 1500, 2200, 3000, 3500, 4200, 5000, 6100, 7500, 8000, 7200, 6500, 6800, 7300, 8200, 9000, 8500, 7000, 5000, 3000],
        orders: [10, 15, 30, 50, 60, 100, 150, 200, 230, 280, 350, 420, 500, 550, 510, 480, 520, 560, 620, 680, 650, 550, 400, 250]
    };
    const hours = Array.from({ length: 24 }, (_, i) => `${String(i).padStart(2, '0')}:00`);

    const seasonalityConfig = {
        type: 'line',
        data: {
            labels: hours,
            datasets: [{
                label: 'Revenue (€)',
                data: seasonalityData.revenue,
                borderColor: '#c084fc',
                backgroundColor: 'rgba(192, 132, 252, 0.2)',
                fill: true,
                tension: 0.4,
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: { y: { beginAtZero: true } }
        }
    };
    seasonalityChart = new Chart(document.getElementById('seasonalityChart'), seasonalityConfig);

    document.getElementById('showRevenue').addEventListener('click', (e) => {
        seasonalityChart.data.datasets[0].data = seasonalityData.revenue;
        seasonalityChart.data.datasets[0].label = 'Revenue (€)';
        seasonalityChart.update();
        document.getElementById('showOrders').classList.remove('btn-active');
        e.target.classList.add('btn-active');
    });

    document.getElementById('showOrders').addEventListener('click', (e) => {
        seasonalityChart.data.datasets[0].data = seasonalityData.orders;
        seasonalityChart.data.datasets[0].label = 'Number of Orders';
        seasonalityChart.update();
        document.getElementById('showRevenue').classList.remove('btn-active');
        e.target.classList.add('btn-active');
    });

    const staffConfig = {
        type: 'bar',
        data: {
            labels: ['Waiter A', 'Waiter B', 'Waiter C', 'Waiter D', 'Waiter E'],
            datasets: [{
                label: 'Orders Taken',
                data: [250, 220, 190, 180, 150],
                backgroundColor: ['#d8b4fe', '#c084fc', '#a855f7', '#9333ea', '#7e22ce'],
            }]
        },
        options: {
            indexAxis: 'y',
            responsive: true,
            maintainAspectRatio: false,
            plugins: { legend: { display: false } }
        }
    };
    staffChart = new Chart(document.getElementById('staffChart'), staffConfig);
    
    const serviceConfig = {
        type: 'doughnut',
        data: {
            labels: ['Dine-In', 'Take-Away', 'Delivery'],
            datasets: [{
                label: 'Service Type',
                data: [75, 15, 10],
                backgroundColor: ['#a855f7', '#d8b4fe', '#e9d5ff'],
                hoverOffset: 4
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
        }
    };
    serviceChart = new Chart(document.getElementById('serviceChart'), serviceConfig);

    const menuConfig = {
        type: 'bar',
        data: {
            labels: ['Main Courses', 'Wine', 'Desserts', 'Appetizers', 'Beer', 'Coffee', 'Juices', 'Pastries', 'Cocktails', 'Salads'],
            datasets: [{
                label: 'Total Revenue',
                data: [15000, 12000, 9500, 8000, 7500, -3000, -2500, -2000, -1500, -1000].reverse(),
                 backgroundColor: (context) => {
                    const value = context.raw;
                    return value >= 0 ? 'rgba(168, 85, 247, 0.8)' : 'rgba(239, 68, 68, 0.8)';
                },
            }]
        },
        options: {
            indexAxis: 'y',
            responsive: true,
            maintainAspectRatio: false,
            scales: {
                y: { ticks: { autoSkip: false } },
                x: {
                    ticks: {
                        callback: function(value) {
                            return Math.abs(value);
                        }
                    }
                }
            },
            plugins: {
                legend: { display: false },
                tooltip: {
                    callbacks: {
                        label: function(context) {
                            return `Revenue: ${Math.abs(context.raw)}€`;
                        }
                    }
                }
            }
        }
    };
    menuChart = new Chart(document.getElementById('menuChart'), menuConfig);
    
    document.getElementById('showSellers').addEventListener('click', (e) => {
        document.getElementById('sellersView').classList.remove('hidden');
        document.getElementById('combosView').classList.add('hidden');
        document.getElementById('showCombos').classList.remove('btn-active');
        e.target.classList.add('btn-active');
    });

    document.getElementById('showCombos').addEventListener('click', (e) => {
        document.getElementById('sellersView').classList.add('hidden');
        document.getElementById('combosView').classList.remove('hidden');
        document.getElementById('showSellers').classList.remove('btn-active');
        e.target.classList.add('btn-active');
    });
});
</script>
</body>
</html>
