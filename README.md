<body class="flex items-center justify-center min-h-screen p-4">

    <div class="w-full max-w-4xl bg-gray-100 rounded-2xl shadow-2xl shadow-lime-500/10 overflow-hidden border-t-4 border-lime-400">
        <div class="md:grid md:grid-cols-2">
            <!-- Coluna da Esquerda: Formulário -->
            <div class="p-8 sm:p-10 space-y-8">
                <!-- Cabeçalho -->
                <div class="text-center md:text-left">
                    <div class="flex justify-center md:justify-start items-center gap-3">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-9 w-9 text-lime-500" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                          <path stroke-linecap="round" stroke-linejoin="round" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
                        </svg>
                        <h1 class="text-4xl font-bold text-gray-800">Calculadora de Prazos</h1>
                    </div>
                    <p class="text-gray-500 mt-3">Calcule datas futuras com precisão e agilidade.</p>
                </div>
            
                <!-- Formulário Principal -->
                <div class="space-y-6">
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-6 items-end">
                        <div class="sm:col-span-2">
                            <label for="startDate" class="block text-sm font-medium text-gray-700 mb-2">Data de Início</label>
                            <input type="date" id="startDate" class="w-full p-3 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-lime-500 focus:border-lime-500 transition text-gray-900">
                        </div>
                        <div>
                            <label for="daysToProcess" class="block text-sm font-medium text-gray-700 mb-2">Quantidade de Dias</label>
                            <input type="number" id="daysToProcess" placeholder="15" class="w-full p-3 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-lime-500 focus:border-lime-500 transition text-gray-900">
                        </div>
                        <div>
                            <label for="dayType" class="block text-sm font-medium text-gray-700 mb-2">Tipo</label>
                            <select id="dayType" class="w-full p-3 bg-white border border-gray-300 rounded-lg focus:ring-2 focus:ring-lime-500 focus:border-lime-500 transition h-[50px] text-gray-900">
                                <option value="business">Dias Úteis</option>
                                <option value="calendar">Dias Corridos</option>
                            </select>
                        </div>
                    </div>
                    <div id="includeStartDateWrapper" class="flex items-center justify-center pt-2">
                        <input type="checkbox" id="includeStartDate" class="h-4 w-4 text-lime-600 bg-gray-200 border-gray-300 rounded focus:ring-lime-500">
                        <label for="includeStartDate" class="ml-3 block text-sm text-gray-800">Incluir data de início no cálculo</label>
                    </div>
                </div>
                
                <div class="pt-4">
                    <button id="calculateBtn" class="w-full bg-lime-500 text-slate-900 font-semibold py-3 px-8 rounded-lg shadow-md shadow-lime-500/20 hover:bg-lime-400 transition-all transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-lime-500">
                        Calcular Data Final
                    </button>
                </div>
            </div>

            <!-- Coluna da Direita: Resultado -->
            <div id="result-column" class="p-8 sm:p-10 bg-gray-200/50 rounded-r-2xl">
                <div id="error-message" class="text-center text-red-700 bg-red-100 p-4 rounded-lg font-medium hidden"></div>

                <div id="result-placeholder" class="h-full flex flex-col items-center justify-center text-center text-gray-500">
                    <svg class="w-16 h-16 mb-4" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" d="M7.5 8.25h9m-9 3H12m-9.75 1.51c0 1.6 1.123 2.994 2.707 3.227 1.129.166 2.27.293 3.423.379.35.026.67.21.865.501L12 21l2.755-4.133a1.14 1.14 0 01.865-.501 48.17 48.17 0 003.423-.379c1.584-.233 2.707-1.626 2.707-3.228V6.741c0-1.602-1.123-2.995-2.707-3.228A48.394 48.394 0 0012 3c-2.392 0-4.744.175-7.043.513C3.373 3.746 2.25 5.14 2.25 6.741v6.018z" /></svg>
                    <h3 class="text-xl font-semibold text-gray-600">Seu resultado aparecerá aqui</h3>
                    <p class="mt-1">Preencha os dados e clique em calcular.</p>
                </div>

                <div id="result" class="text-center hidden space-y-6">
                    <div class="bg-lime-100/80 border border-lime-200/80 p-6 rounded-lg">
                        <p class="text-lime-900/80">A data final será:</p>
                        <p id="result-date" class="text-5xl font-bold text-lime-600 my-2"></p>
                    </div>
                    <div id="result-summary" class="grid grid-cols-2 gap-4"></div>
                    <div id="holiday-list" class="hidden">
                        <h3 class="font-semibold text-gray-700 text-left mb-2">Feriados no período:</h3>
                        <ul id="holiday-list-items" class="text-left text-sm text-gray-600 bg-white p-3 rounded-lg space-y-1"></ul>
                    </div>
                </div>
            </div>
        </div>
    </div>
    
    <script>
        // --- DOM Elements ---
        const calculateBtn = document.getElementById('calculateBtn');
        const resultPlaceholder = document.getElementById('result-placeholder');
        const resultDiv = document.getElementById('result');
        const errorMessage = document.getElementById('error-message');
        const resultDate = document.getElementById('result-date');
        const resultSummary = document.getElementById('result-summary');
        const startDateInput = document.getElementById('startDate');
        const daysToProcessInput = document.getElementById('daysToProcess');
        const includeStartDateInput = document.getElementById('includeStartDate');
        const dayTypeSelect = document.getElementById('dayType');
        const includeStartDateWrapper = document.getElementById('includeStartDateWrapper');
        const holidayListDiv = document.getElementById('holiday-list');
        const holidayListItems = document.getElementById('holiday-list-items');

        // --- Holiday Data ---
        const allHolidays = {
            br: [
                { date: '01-01', name: 'Confraternização Universal' }, { date: '04-21', name: 'Tiradentes' }, 
                { date: '05-01', name: 'Dia do Trabalho' }, { date: '09-07', name: 'Independência do Brasil' },
                { date: '10-12', name: 'Nossa Senhora Aparecida' }, { date: '11-02', name: 'Finados' },
                { date: '11-15', name: 'Proclamação da República' }, { date: '12-25', name: 'Natal' },
                { date: '2024-02-12', name: 'Carnaval' }, { date: '2024-02-13', name: 'Carnaval' },
                { date: '2024-03-29', name: 'Sexta-feira Santa' }, { date: '2024-05-30', name: 'Corpus Christi' },
                { date: '2025-03-03', name: 'Carnaval' }, { date: '2025-03-04', name: 'Carnaval' },
                { date: '2025-04-18', name: 'Sexta-feira Santa' }, { date: '2025-06-19', name: 'Corpus Christi' },
                { date: '2026-02-16', name: 'Carnaval' }, { date: '2026-02-17', name: 'Carnaval' },
                { date: '2026-04-03', name: 'Sexta-feira Santa' }, { date: '2026-06-04', name: 'Corpus Christi' },
                { date: '2027-02-08', name: 'Carnaval' }, { date: '2027-02-09', name: 'Carnaval' },
                { date: '2027-03-26', name: 'Sexta-feira Santa' }, { date: '2027-05-27', name: 'Corpus Christi' },
            ],
            us: [
                { date: '01-01', name: "New Year's Day" }, { date: '06-19', name: 'Juneteenth' },
                { date: '07-04', name: 'Independence Day' }, { date: '12-25', name: 'Christmas Day' },
                { date: '2024-01-15', name: 'Martin Luther King, Jr. Day' }, { date: '2024-02-19', name: "Presidents' Day" },
                { date: '2024-05-27', name: 'Memorial Day' }, { date: '2024-09-02', name: 'Labor Day' },
                { date: '2024-11-28', name: 'Thanksgiving Day' },
                { date: '2025-01-20', name: 'Martin Luther King, Jr. Day' }, { date: '2025-02-17', name: "Presidents' Day" },
                { date: '2025-05-26', name: 'Memorial Day' }, { date: '2025-09-01', name: 'Labor Day' },
                { date: '2025-11-27', name: 'Thanksgiving Day' },
                { date: '2026-01-19', name: 'Martin Luther King, Jr. Day' }, { date: '2026-02-16', name: "Presidents' Day" },
                { date: '2026-05-25', name: 'Memorial Day' }, { date: '2026-09-07', name: 'Labor Day' },
                { date: '2026-11-26', name: 'Thanksgiving Day' },
                { date: '2027-01-18', name: 'Martin Luther King, Jr. Day' }, { date: '2027-02-15', name: "Presidents' Day" },
                { date: '2027-05-31', name: 'Memorial Day' }, { date: '2027-09-06', name: 'Labor Day' },
                { date: '2027-11-25', name: 'Thanksgiving Day' },
            ]
        };
    
        // --- Helper Functions ---
        const formatDate = (date) => {
            const day = String(date.getDate()).padStart(2, '0');
            const month = String(date.getMonth() + 1).padStart(2, '0');
            return `${day}/${month}/${date.getFullYear()}`;
        };

        const getHoliday = (date, country) => {
            const holidays = allHolidays[country] || [];
            const monthDay = `${String(date.getMonth() + 1).padStart(2, '0')}-${String(date.getDate()).padStart(2, '0')}`;
            const fullDate = `${date.getFullYear()}-${monthDay}`;
            return holidays.find(h => h.date === monthDay || h.date === fullDate);
        };
    
        const isBusinessDay = (date, country) => {
            const dayOfWeek = date.getDay();
            if (dayOfWeek === 0 || dayOfWeek === 6) return false;
            return !getHoliday(date, country);
        };
    
        const showError = (message) => {
            errorMessage.textContent = message;
            errorMessage.classList.remove('hidden');
            resultDiv.classList.add('hidden');
            resultPlaceholder.classList.remove('hidden');
        };
    
        const hideError = () => errorMessage.classList.add('hidden');
        
        const showResult = () => {
            resultPlaceholder.classList.add('hidden');
            resultDiv.classList.remove('hidden');
            resultDiv.classList.add('fade-in');
            resultDiv.addEventListener('animationend', () => resultDiv.classList.remove('fade-in'), { once: true });
        }

        const createSummaryCard = (label, value) => {
            return `
                <div class="bg-white p-3 rounded-lg border border-gray-200">
                    <p class="text-xs font-semibold uppercase text-gray-500">${label}</p>
                    <p class="text-2xl font-bold text-lime-600">${value}</p>
                </div>`;
        };
    
        // --- Main Calculation Function ---
        function calculate() {
            const startDateValue = startDateInput.value;
            const daysToProcess = parseInt(daysToProcessInput.value, 10);
            if (!startDateValue || isNaN(daysToProcess)) {
                showError("Por favor, preencha a data e a quantidade de dias."); return;
            }
            if (daysToProcess === 0) {
                showError("A quantidade de dias deve ser diferente de zero."); return;
            }
    
            hideError();
            const startDate = new Date(startDateValue + 'T12:00:00');
            calculateFinalDate(startDate, daysToProcess, dayTypeSelect.value);
        }
    
        function calculateFinalDate(startDate, daysToProcess, type) {
            let currentDate = new Date(startDate);
            const direction = daysToProcess > 0 ? 1 : -1;

            if (type === 'business') {
                let businessDaysCount = 0;
                const targetBusinessDays = Math.abs(daysToProcess);
                if (includeStartDateInput.checked && isBusinessDay(new Date(currentDate), 'br')) {
                    businessDaysCount = 1;
                }
                while (businessDaysCount < targetBusinessDays) {
                    currentDate.setDate(currentDate.getDate() + direction);
                    if (isBusinessDay(new Date(currentDate), 'br')) businessDaysCount++;
                }
            } else {
                currentDate.setDate(currentDate.getDate() + daysToProcess);
            }
            
            let holidaysBRCount = 0, weekendsCount = 0, businessDaysCountResult = 0, totalDays = 0;
            let holidayDetails = [];
            const [startSummary, endSummary] = startDate < currentDate ? [new Date(startDate), new Date(currentDate)] : [new Date(currentDate), new Date(startDate)];

            for (let d = new Date(startSummary); d <= endSummary; d.setDate(d.getDate() + 1)) {
                const holidayBR = getHoliday(d, 'br');
                const holidayUS = getHoliday(d, 'us');

                if (holidayBR) {
                    holidayDetails.push(`🇧🇷 ${holidayBR.name} (${formatDate(d)})`);
                }
                if (holidayUS) {
                    holidayDetails.push(`🇺🇸 ${holidayUS.name} (${formatDate(d)})`);
                }
                
                // Summary counts are based on Brazilian calendar
                if (holidayBR) {
                    holidaysBRCount++;
                } else if (d.getDay() === 0 || d.getDay() === 6) {
                    weekendsCount++;
                } else {
                    businessDaysCountResult++;
                }
                totalDays++;
            }
    
            resultDate.textContent = formatDate(currentDate);
            resultSummary.innerHTML = createSummaryCard('Dias Úteis', businessDaysCountResult);
            resultSummary.innerHTML += createSummaryCard('Fins de Semana', weekendsCount);
            resultSummary.innerHTML += createSummaryCard('Feriados (BR)', holidaysBRCount);
            resultSummary.innerHTML += createSummaryCard('Total Corridos', totalDays - 1);
            
            if (holidayDetails.length > 0) {
                const uniqueHolidays = [...new Set(holidayDetails)];
                uniqueHolidays.sort((a, b) => {
                    const dateA = new Date(a.match(/\(([^)]+)\)/)[1].split('/').reverse().join('-'));
                    const dateB = new Date(b.match(/\(([^)]+)\)/)[1].split('/').reverse().join('-'));
                    return dateA - dateB;
                });
                holidayListItems.innerHTML = uniqueHolidays.map(h => `<li>${h}</li>`).join('');
                holidayListDiv.classList.remove('hidden');
            } else {
                holidayListDiv.classList.add('hidden');
            }
            
            showResult();
        }
    
        // --- Event Listeners ---
        calculateBtn.addEventListener('click', calculate);

        dayTypeSelect.addEventListener('change', (e) => {
            includeStartDateWrapper.style.display = e.target.value === 'calendar' ? 'none' : 'flex';
        });
        
        document.addEventListener('DOMContentLoaded', () => {
            startDateInput.value = new Date().toISOString().split('T')[0];
            includeStartDateWrapper.style.display = dayTypeSelect.value === 'calendar' ? 'none' : 'flex';
        });
    </script>
</body>
