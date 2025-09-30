<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora de Datas Avançada</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
        input[type="date"]::-webkit-calendar-picker-indicator { cursor: pointer; opacity: 0.6; transition: opacity 0.2s; }
        input[type="date"]::-webkit-calendar-picker-indicator:hover { opacity: 1; }
        input[type=number]::-webkit-inner-spin-button, 
        input[type=number]::-webkit-outer-spin-button { -webkit-appearance: none; margin: 0; }
        input[type=number] { -moz-appearance: textfield; }
        select {
             -webkit-appearance: none;
            -moz-appearance: none;
            appearance: none;
            background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='currentColor' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3e%3cpolyline points='6 9 12 15 18 9'%3e%3c/polyline%3e%3c/svg%3e");
            background-repeat: no-repeat;
            background-position: right 1rem center;
            background-size: 1em;
            padding-right: 2.5rem;
        }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen p-4">

    <div class="w-full max-w-2xl bg-white rounded-2xl shadow-lg p-8 space-y-6">
        <!-- Cabeçalho -->
        <div class="text-center">
            <h1 class="text-3xl font-bold text-gray-800">Calculadora de Prazos</h1>
            <p class="text-gray-500 mt-2">Descubra a data final somando ou subtraindo dias de uma data inicial.</p>
        </div>

        <!-- Formulário Principal -->
        <div class="pt-4 space-y-6">
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
                <div>
                    <label for="startDate" class="block text-sm font-medium text-gray-700 mb-1">Data de Início</label>
                    <div class="flex items-center space-x-2">
                        <input type="date" id="startDate" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
                        <button id="todayBtn" class="p-3 bg-gray-200 text-gray-700 font-semibold rounded-lg hover:bg-gray-300 transition" title="Usar data de hoje">Hoje</button>
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-2">
                    <div>
                        <label for="daysToProcess" class="block text-sm font-medium text-gray-700 mb-1">Quantidade</label>
                        <input type="number" id="daysToProcess" placeholder="Ex: 15" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition">
                    </div>
                    <div>
                        <label for="dayType" class="block text-sm font-medium text-gray-700 mb-1">Tipo de Dia</label>
                        <select id="dayType" class="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition h-[50px]">
                            <option value="business">Dias Úteis</option>
                            <option value="calendar">Dias Corridos</option>
                        </select>
                    </div>
                </div>
            </div>
            <div id="includeStartDateWrapper" class="flex items-center justify-center pt-2">
                <input type="checkbox" id="includeStartDate" class="h-4 w-4 text-indigo-600 border-gray-300 rounded focus:ring-indigo-500">
                <label for="includeStartDate" class="ml-2 block text-sm text-gray-900">Incluir data de início no cálculo</label>
            </div>
        </div>
        
        <!-- Mensagem de Erro -->
        <div id="error-message" class="text-center text-red-500 font-medium hidden"></div>

        <!-- Botões de Ação -->
        <div class="flex flex-col sm:flex-row items-center justify-center gap-4 pt-4">
            <button id="calculateBtn" class="w-full sm:w-auto bg-indigo-600 text-white font-semibold py-3 px-8 rounded-lg shadow-md hover:bg-indigo-700 transition-transform transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                Calcular Data Final
            </button>
            <button id="clearBtn" class="w-full sm:w-auto bg-gray-200 text-gray-700 font-semibold py-3 px-8 rounded-lg hover:bg-gray-300 transition focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-400">
                Limpar
            </button>
        </div>

        <!-- Área de Resultado -->
        <div id="result" class="text-center bg-gray-50 p-6 rounded-lg mt-6 hidden">
            <p class="text-gray-600">A data final é:</p>
            <p id="result-date" class="text-5xl font-bold text-indigo-600 mt-2"></p>
            <div class="mt-4 pt-4 border-t border-gray-200 text-sm text-gray-600 space-y-1">
                <p id="result-summary"></p>
            </div>
        </div>
    </div>

    <script>
        // --- ELEMENTOS DO DOM ---
        const calculateBtn = document.getElementById('calculateBtn');
        const clearBtn = document.getElementById('clearBtn');
        const todayBtn = document.getElementById('todayBtn');
        const resultDiv = document.getElementById('result');
        const errorMessage = document.getElementById('error-message');
        const resultDate = document.getElementById('result-date');
        const resultSummary = document.getElementById('result-summary');
        const startDateInput = document.getElementById('startDate');
        const daysToProcessInput = document.getElementById('daysToProcess');
        const includeStartDateInput = document.getElementById('includeStartDate');
        const dayTypeSelect = document.getElementById('dayType');
        const includeStartDateWrapper = document.getElementById('includeStartDateWrapper');

        // --- FERIADOS NACIONAIS (YYYY-MM-DD para móveis, MM-DD para fixos) ---
        const nationalHolidays = [
            '01-01', '04-21', '05-01', '09-07', '10-12', '11-02', '11-15', '12-25',
            '2024-02-12', '2024-02-13', '2024-03-29', '2024-05-30',
            '2025-03-03', '2025-03-04', '2025-04-18', '2025-06-19',
            '2026-02-16', '2026-02-17', '2026-04-03', '2026-06-04',
            '2027-02-08', '2027-02-09', '2027-03-26', '2027-05-27',
            '2028-02-28', '2028-02-29', '2028-04-14', '2028-06-01',
        ];

        // --- FUNÇÕES AUXILIARES ---
        const formatDate = (date) => {
            const day = ('0' + date.getDate()).slice(-2);
            const month = ('0' + (date.getMonth() + 1)).slice(-2);
            return `${day}/${month}/${date.getFullYear()}`;
        };

        const isBusinessDay = (date) => {
            const dayOfWeek = date.getDay();
            const isWeekend = dayOfWeek === 0 || dayOfWeek === 6;
            const year = date.getFullYear();
            const monthDay = ('0' + (date.getMonth() + 1)).slice(-2) + '-' + ('0' + date.getDate()).slice(-2);
            const fullDate = year + '-' + monthDay;
            const isHoliday = nationalHolidays.includes(monthDay) || nationalHolidays.includes(fullDate);
            return !isWeekend && !isHoliday;
        };

        const showError = (message) => {
            errorMessage.textContent = message;
            errorMessage.classList.remove('hidden');
            resultDiv.classList.add('hidden');
        };

        const hideError = () => errorMessage.classList.add('hidden');
        
        // --- FUNÇÃO PRINCIPAL DE CÁLCULO ---
        function calculate() {
            const startDateValue = startDateInput.value;
            const daysToProcess = parseInt(daysToProcessInput.value, 10);
            const dayType = dayTypeSelect.value;

            if (!startDateValue || isNaN(daysToProcess)) {
                showError("Preencha a data de início e a quantidade de dias.");
                return;
            }
             if (daysToProcess === 0) {
                showError("A quantidade de dias deve ser diferente de zero.");
                return;
            }

            hideError();
            
            const startDate = new Date(startDateValue + 'T00:00:00');
            let finalDate = new Date(startDate);

            if (dayType === 'business') {
                calculateBusinessDays(startDate, daysToProcess);
            } else {
                calculateCalendarDays(startDate, daysToProcess);
            }
        }

        function calculateCalendarDays(startDate, daysToProcess) {
            let finalDate = new Date(startDate);
            finalDate.setDate(finalDate.getDate() + daysToProcess);
            
            resultDate.textContent = formatDate(finalDate);
            resultSummary.innerHTML = `
                <strong>Período:</strong> ${formatDate(startDate)} a ${formatDate(finalDate)}<br>
                <strong>Resumo:</strong> ${Math.abs(daysToProcess)} dias corridos foram ${daysToProcess > 0 ? 'adicionados' : 'subtraídos'}.
            `;
            resultDiv.classList.remove('hidden');
        }

        function calculateBusinessDays(startDate, daysToProcess) {
            const includeStartDate = includeStartDateInput.checked;
            let currentDate = new Date(startDate);
            let businessDaysCount = 0;
            let holidaysCount = 0;
            let weekendsCount = 0;
            
            const direction = daysToProcess > 0 ? 1 : -1;
            const targetBusinessDays = Math.abs(daysToProcess);

            if (includeStartDate && isBusinessDay(startDate)) {
                businessDaysCount = 1;
            }

            while (businessDaysCount < targetBusinessDays) {
                currentDate.setDate(currentDate.getDate() + direction);
                if (isBusinessDay(currentDate)) {
                    businessDaysCount++;
                }
            }
            
            let tempDate = new Date(startDate);
            let totalDays = 0;
            let finalDate = new Date(currentDate);

            const [startSummary, endSummary] = startDate < finalDate ? [tempDate, finalDate] : [finalDate, tempDate];

            while(startSummary <= endSummary) {
                const dayOfWeek = startSummary.getDay();
                if (isBusinessDay(startSummary)) {
                    // Já calculado
                } else if (dayOfWeek === 0 || dayOfWeek === 6) {
                    weekendsCount++;
                } else {
                    holidaysCount++;
                }
                totalDays++;
                startSummary.setDate(startSummary.getDate() + 1);
            }

            resultDate.textContent = formatDate(currentDate);
            resultSummary.innerHTML = `
                <strong>Período:</strong> ${formatDate(startDate)} a ${formatDate(currentDate)}<br>
                <strong>Resumo:</strong> ${businessDaysCount} dias úteis, ${weekendsCount} dias de fim de semana e ${holidaysCount} feriados, totalizando ${totalDays - 1} dias corridos.
            `;
            resultDiv.classList.remove('hidden');
        }

        function clearFields() {
            startDateInput.value = '';
            daysToProcessInput.value = '';
            includeStartDateInput.checked = false;
            dayTypeSelect.value = 'business';
            includeStartDateWrapper.classList.remove('hidden');
            resultDiv.classList.add('hidden');
            hideError();
        }

        // --- EVENT LISTENERS ---
        calculateBtn.addEventListener('click', calculate);
        clearBtn.addEventListener('click', clearFields);
        todayBtn.addEventListener('click', () => {
            startDateInput.value = new Date().toISOString().split('T')[0];
        });
        dayTypeSelect.addEventListener('change', (e) => {
            if (e.target.value === 'calendar') {
                includeStartDateWrapper.classList.add('hidden');
            } else {
                includeStartDateWrapper.classList.remove('hidden');
            }
        });
    </script>
</body>
</html>

