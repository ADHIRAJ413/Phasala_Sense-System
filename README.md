# Phasala_Sense-System
Data Driven Crop Advisory System
 <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Crop Recommendation System</title>
    <script src="https://cdn.tailwindcss.com"></script>
<style>
    #disease-list li {
        font-size: 0.9rem;
    }
    #yield-pred, #price-pred {
        font-size: 1.1rem;
    }
</style>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body class="bg-gray-100 min-h-screen">
    <div class="container mx-auto px-4 py-8">
        <h1 class="text-4xl font-bold text-center mb-6">Crop Recommendation System</h1>
        <div class="bg-white p-6 rounded-lg shadow-lg">
            <h2 class="text-2xl font-semibold mb-4">Enter Crop Name</h2>
            <form id="crop-form">
                <div class="mb-4 relative">
                    <label for="crop-name" class="block text-gray-700">Crop Name (e.g. Rice, Wheat):</label>
                    <input type="text" id="crop-name" class="border rounded w-full p-2" required autocomplete="off">
                    <div id="suggestions" class="absolute z-10 w-full bg-white border rounded-b-lg shadow-lg hidden">
                        <ul id="suggestion-list" class="py-1"></ul>
                    </div>
                </div>
                <button type="submit" class="bg-green-600 hover:bg-green-700 text-white font-bold py-2 px-4 rounded">Get Growing Requirements</button>
            </form>
            <div id="result" class="mt-6 hidden">
                <div class="bg-green-50 p-4 rounded-lg border border-green-200">
                    <h3 class="text-xl font-semibold mb-4" id="result-title"></h3>
                    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                        <div class="col-span-1 lg:col-span-2">
                            <h4 class="font-medium mb-2 text-center">Nutrient & Environmental Requirements Overview</h4>
                            <canvas id="overview-chart" height="150"></canvas>
                        </div>
                        <div class="bg-white p-4 rounded-lg shadow">
                            <h4 class="font-medium mb-2">Nutrient Requirements (kg/hectare)</h4>
                            <canvas id="nutrient-chart" height="300"></canvas>
                        </div>
                        <div class="bg-white p-4 rounded-lg shadow">
                            <h4 class="font-medium mb-2">Optimal Ranges</h4>
                            <canvas id="environment-chart" height="300"></canvas>
                        </div>
                        <div class="bg-white p-4 rounded-lg shadow">
                            <h4 class="font-medium mb-2">Temperature Analysis</h4>
                            <canvas id="temp-chart" height="300"></canvas>
                        </div>
                        <div class="bg-white p-4 rounded-lg shadow">
                            <h4 class="font-medium mb-2">Nutrient Balance</h4>
                            <canvas id="balance-chart" height="300"></canvas>
                        </div>
                        <div class="bg-white p-4 rounded-lg shadow">
                            <h4 class="font-medium mb-2">Water Requirements</h4>
                            <div class="flex items-center">
                                <div class="w-full bg-gray-200 rounded-full h-4">
                                    <div id="water-bar" class="bg-blue-500 h-4 rounded-full" style="width: 0%"></div>
                                </div>
                                <span id="water-value" class="ml-2 text-sm font-medium">0mm</span>
                            </div>
                        </div>
                        <div class="bg-white p-4 rounded-lg shadow">
                            <h4 class="font-medium mb-2">Yield & Market Prediction</h4>
                            <div class="grid grid-cols-2 gap-4">
                                <div>
                                    <p class="text-sm">Expected Yield:</p>
                                    <div class="font-bold text-indigo-600" id="yield-pred">Loading...</div>
                                </div>
                                <div>
                                    <p class="text-sm">Market Price:</p>
                                    <div class="font-bold text-green-600" id="price-pred">Loading...</div>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="mt-4">
                        <div class="flex flex-wrap gap-2 mb-4">
                            <button class="graph-btn active bg-green-600 text-white px-3 py-1 rounded" data-chart="nutrient">Nutrients</button>
                            <button class="graph-btn bg-green-600 text-white px-3 py-1 rounded" data-chart="temp">Temperature</button>
                            <button class="graph-btn bg-green-600 text-white px-3 py-1 rounded" data-chart="balance">Nutrient Balance</button>
                            <button class="graph-btn bg-green-600 text-white px-3 py-1 rounded" data-chart="environment">Environment</button>
                        </div>
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                        <div class="bg-white p-4 rounded-lg shadow">
                            <h4 class="font-medium mb-2">Advanced Planting Calendar</h4>
                            <div class="flex items-center justify-between mb-3">
                                <button id="prev-season" class="text-gray-600 hover:text-gray-800">
                                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7"></path>
                                    </svg>
                                </button>
                                <h5 id="season-title" class="font-medium">Main Season</h5>
                                <button id="next-season" class="text-gray-600 hover:text-gray-800">
                                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"></path>
                                    </svg>
                                </button>
                            </div>
                            <div id="calendar" class="grid grid-cols-4 gap-2">
                                <!-- Months will be populated by JS -->
                            </div>
                            <div id="calendar-details" class="mt-3 text-sm hidden">
                                <h6 class="font-medium mb-1">Key Activities:</h6>
                                <ul id="calendar-activities" class="list-disc pl-5"></ul>
                            </div>
                        </div>
                        <div class="bg-white p-4 rounded-lg shadow">
                            <h4 class="font-medium mb-2">Disease & Pest Management</h4>
                            <ul id="disease-list" class="space-y-2"></ul>
                        </div>
                        <div>
                            <h4 class="font-medium mb-2">Key Statistics:</h4>
                            <ul id="stats-list" class="space-y-1 list-disc pl-5"></ul>
                        </div>
                        <div>
                            <h4 class="font-medium mb-2">Optimal Conditions:</h4>
                            <ul id="conditions-list" class="space-y-1 list-disc pl-5"></ul>
                            
                            <h4 class="font-medium mb-2 mt-3">Recommended Varieties</h4>
                            <div id="varieties-list" class="grid grid-cols-2 gap-2 mt-2">
                                <!-- Will be populated by JS -->
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        const cropData = {
            'rice': {
                name: 'Rice', color: '#4CAF50',
                nutrients: { nitrogen: 120, phosphorous: 60, potassium: 70, calcium: 100, magnesium: 50 },
                environment: { temperature: 30, humidity: 85, ph: 6.0, rainfall: 250 },
                yield: '3-6 tonnes/ha',
                price: '₹2,000-2,500/quintal',
                seasons: {
                    'main': {
                        months: ['Jun', 'Jul', 'Aug', 'Sep'],
                        activities: [
                            'Land preparation: Puddling and leveling',
                            'Transplanting 20-25 day old seedlings',
                            'Fertilizer application in 3 splits',
                            'Maintain 3-5cm water depth'
                        ]
                    },
                    'off': {
                        months: ['Nov', 'Dec', 'Jan', 'Feb'],
                        activities: [
                            'Suitable for late varieties',
                            'Requires irrigation support',
                            'Higher pest pressure'
                        ]
                    }
                },
                diseases: [
                    'Bacterial blight (spray Streptocycline)',
                    'Blast (use Tricyclazole)',
                    'Brown spot (apply Carbendazim)'
                ],
                varieties: ['Pusa Basmati', 'IR64', 'Swarna', 'Samba Mahsuri'],
                growthStages: [
                    'Germination: 0-2 weeks',
                    'Vegetative: 2-6 weeks',
                    'Reproductive: 6-10 weeks',
                    'Maturity: 10-14 weeks'
                ]
            },
            'wheat': {
                name: 'Wheat', color: '#FFC107',
                nutrients: { nitrogen: 80, phosphorous: 50, potassium: 60, calcium: 80, magnesium: 25 },
                environment: { temperature: 20, humidity: 65, ph: 6.8, rainfall: 80 }
            },
            'maize': {
                name: 'Maize', color: '#FF9800',
                nutrients: { nitrogen: 100, phosphorous: 40, potassium: 50, calcium: 60, magnesium: 20 },
                environment: { temperature: 27, humidity: 70, ph: 6.5, rainfall: 100 }
            },
            'soybean': {
                name: 'Soybean', color: '#8BC34A',
                nutrients: { nitrogen: 60, phosphorous: 40, potassium: 50, calcium: 40, magnesium: 15 },
                environment: { temperature: 25, humidity: 75, ph: 6.3, rainfall: 90 }
            },
            // Add more crops as needed
        };

        let nutrientChart, environmentChart, tempChart, balanceChart, overviewChart;
        
        // Autocomplete functionality
        document.getElementById('crop-name').addEventListener('input', function() {
            const input = this.value.toLowerCase();
            const suggestions = document.getElementById('suggestion-list');
            suggestions.innerHTML = '';
            
            if (input.length > 1) {
                const matches = Object.values(cropData)
                    .filter(crop => crop.name.toLowerCase().includes(input))
                    .slice(0, 5);
                
                matches.forEach(crop => {
                    const li = document.createElement('li');
                    li.className = 'px-4 py-2 hover:bg-gray-100 cursor-pointer';
                    li.textContent = crop.name;
                    li.addEventListener('click', () => {
                        document.getElementById('crop-name').value = crop.name;
                        document.getElementById('suggestions').classList.add('hidden');
                    });
                    suggestions.appendChild(li);
                });
                
                document.getElementById('suggestions').classList.toggle('hidden', matches.length === 0);
            } else {
                document.getElementById('suggestions').classList.add('hidden');
            }
        });
        
        document.getElementById('crop-form').addEventListener('submit', function(event) {
            event.preventDefault();
            const cropName = document.getElementById('crop-name').value.trim().toLowerCase();
            const crop = Object.values(cropData).find(c => c.name.toLowerCase() === cropName);
            
            if (!crop) {
                alert('Crop not found. Please try one of these: ' + 
                    Object.values(cropData).map(c => c.name).join(', '));
                return;
            }

            document.getElementById('result-title').innerHTML = `
                <span class="inline-block w-4 h-4 rounded-full mr-2" style="background:${crop.color}"></span>
                ${crop.name} Growing Requirements`;
            
            // Destroy existing charts
            if (nutrientChart) nutrientChart.destroy();
            if (environmentChart) environmentChart.destroy();
            if (tempChart) tempChart.destroy();
            if (balanceChart) balanceChart.destroy();

            // Overview Chart
            overviewChart = new Chart(
                document.getElementById('overview-chart').getContext('2d'),
                {
                    type: 'bar',
                    data: {
                        labels: [...Object.keys(crop.nutrients), ...Object.keys(crop.environment)],
                        datasets: [{
                            label: 'Values',
                            data: [...Object.values(crop.nutrients), ...Object.values(crop.environment)],
                            backgroundColor: [
                                ...Array(Object.keys(crop.nutrients).length).fill(crop.color),
                                ...Array(Object.keys(crop.environment).length).fill('#607D8B')
                            ],
                            borderWidth: 1
                        }]
                    },
                    options: {
                        responsive: true,
                        scales: {
                            y: { beginAtZero: true }
                        },
                        plugins: {
                            legend: { display: false }
                        }
                    }
                }
            );

            // Nutrient Bar Chart
            nutrientChart = new Chart(
                document.getElementById('nutrient-chart').getContext('2d'),
                {
                    type: 'bar',
                    data: {
                        labels: Object.keys(crop.nutrients),
                        datasets: [{
                            label: 'kg per hectare',
                            data: Object.values(crop.nutrients),
                            backgroundColor: Array(Object.keys(crop.nutrients).length)
                                .fill(crop.color.replace(')', ',0.7)').replace('rgb', 'rgba')),
                            borderColor: crop.color,
                            borderWidth: 1
                        }]
                    },
                    options: {
                        responsive: true,
                        scales: { y: { beginAtZero: true } }
                    }
                }
            );

            // Environment Radar Chart
            environmentChart = new Chart(
                document.getElementById('environment-chart').getContext('2d'),
                {
                    type: 'radar',
                    data: {
                        labels: Object.keys(crop.environment)
                            .map(label => label.charAt(0).toUpperCase() + label.slice(1)),
                        datasets: [{
                            label: 'Optimal Range',
                            data: Object.values(crop.environment),
                            backgroundColor: `${crop.color}33`,
                            borderColor: crop.color,
                            borderWidth: 2
                        }]
                    },
                    options: {
                        scales: {
                            r: {
                                angleLines: { display: true },
                                suggestedMin: 0
                            }
                        }
                    }
                }
            );

            // Temperature Line Chart
            tempChart = new Chart(
                document.getElementById('temp-chart').getContext('2d'),
                {
                    type: 'line',
                    data: {
                        labels: ['Min', 'Ideal', 'Max'],
                        datasets: [{
                            label: 'Temperature (°C)',
                            data: [
                                crop.environment.temperature * 0.8,
                                crop.environment.temperature,
                                crop.environment.temperature * 1.2
                            ],
                            borderColor: crop.color,
                            backgroundColor: `${crop.color}33`,
                            tension: 0.3,
                            fill: true
                        }]
                    },
                    options: {
                        responsive: true
                    }
                }
            );

            // Nutrient Balance Pie Chart
            balanceChart = new Chart(
                document.getElementById('balance-chart').getContext('2d'),
                {
                    type: 'doughnut',
                    data: {
                        labels: Object.keys(crop.nutrients),
                        datasets: [{
                            data: Object.values(crop.nutrients),
                            backgroundColor: [
                                '#4CAF50', '#2196F3', '#FFC107', 
                                '#FF5722', '#9C27B0', '#607D8B'
                            ].slice(0, Object.keys(crop.nutrients).length),
                            borderWidth: 1
                        }]
                    },
                    options: {
                        cutout: '70%',
                        plugins: {
                            legend: { position: 'right' }
                        }
                    }
                }
            );
            
            // Populate stats lists
            const statsList = document.getElementById('stats-list');
            statsList.innerHTML = Object.entries(crop.nutrients).map(
                ([k,v]) => `<li><span class="font-medium">${k}:</span> ${v} kg/ha</li>`).join('');
                
            const conditionsList = document.getElementById('conditions-list');
            conditionsList.innerHTML = Object.entries(crop.environment).map(
                ([k,v]) => `<li><span class="font-medium">${k}:</span> ${
                    k === 'temperature' ? v+'°C' : 
                    k === 'humidity' ? v+'%' : 
                    k === 'rainfall' ? v+'mm' : v
                }</li>`).join('');
            
            // Update water requirements
            const waterBar = document.getElementById('water-bar');
            const waterValue = document.getElementById('water-value');
            waterBar.style.width = `${(crop.environment.rainfall / 300) * 100}%`;
            waterValue.textContent = `${crop.environment.rainfall}mm`;
            
            // Update planting calendar
            let currentSeason = 'main';
            const updateCalendar = () => {
                const calendar = document.getElementById('calendar');
                calendar.innerHTML = '';
                ['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'].forEach(mont
