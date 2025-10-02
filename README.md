<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora de Prazos | Verde Esmeralda</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Inter', sans-serif; }
        /* Remove o spinner de inputs numéricos */
        input[type=number]::-webkit-inner-spin-button, 
        input[type=number]::-webkit-outer-spin-button { -webkit-appearance: none; margin: 0; }
        input[type=number] { -moz-appearance: textfield; }
        /* Seta customizada para o select */
        select {
             -webkit-appearance: none;
            -moz-appearance: none;
            appearance: none;
            background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='currentColor' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3e%3cpolyline points='6 9 12 15 18 9'%3e%3c/polyline%3e%3c/svg%3e");
            background-repeat: no-repeat;
            background-position: right 1rem center;
            background-size: 1em;
        }
        /* Animação para o resultado */
        .fade-in {
            animation: fadeIn 0.5s ease-in-out;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body class="bg-gradient-to-br from-emerald-50 to-green-100 flex items-center justify-center min-h-screen p-4">

    <div class="w-full max-w-2xl bg-white rounded-2xl shadow-2xl p-8 sm:p-10 space-y-8">
        <!-- Cabeçalho -->
        <div class="text-center">
            <div class="flex justify-center items-center mb-4">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 text-emerald-500" viewBox="0 0 20 20" fill="currentColor">
                  <path fill-rule="evenodd" d="M6 2a1 1 0 00-1 1v1H4a2 2 0 00-2 2v10a2 2 0 002 2h12a2 2 0 002-2V6a2 2 0 00-2-2h-1V3a1 1 0 10-2 0v1H7V3a1 1 0 00-1-1zm0 5a1 1 0 000 2h8a1 1 0 100-2H6z" clip-rule="evenodd" />
                </svg>
            </div>
            <h1 class="text-4xl font-bold text-gray-800">Calculadora de Prazos</h1>
            <p class="text-gray-500 mt-2">Calcule datas com precisão e elegância.</p>
        </div>
    
        <!-- Formulário Principal -->
        <div class="space-y-6">
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <div>
                    <label for="startDate" class="block text-sm font-medium text-gray-700 mb-2">Data de Início</label>
                    <div class="flex items-center space-x-2">
                        <input type="date" id="startDate" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 transition">
                        <button id="todayBtn" class="p-3 bg-gray-100 text-gray-700 font-semibold rounded-lg hover:bg-emerald-100 hover:text-emerald-700 transition" title="Usar data de hoje">Hoje</button>
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div>
                        <label for="daysToProcess" class="block text-sm font-medium text-gray-700 mb-2">Quantidade</label>
                        <input type="number" id="daysToProcess" placeholder="Ex: 15" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 transition">
                    </div>
                    <div>
                        <label for="dayType" class="block text-sm font-medium text-gray-700 mb-2">Tipo</label>
                        <select id="dayType" class="w-full p-3 bg-gray-50 border border-gray-300 rounded-lg focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 transition">
                            <option value="business">Dias Úteis</option>
                            <option value="calendar">Dias Corridos</option>
                        </select>
                    </div>
                </div>
            </div>
            <div id="includeStartDateWrapper" class="flex items-center justify-center pt-2">
                <input type="checkbox" id="includeStartDate" class="h-4 w-4 text-emerald-600 border-gray-300 rounded focus:ring-emerald-500">
                <label for="includeStartDate" class="ml-3 block text-sm text-gray-800">Incluir data de início no cálculo</label>
            </div>
        </div>
        
        <!-- Mensagem de Erro -->
        <div id="error-message" class="text-center text-red-600 bg-red-50 p-3 rounded-lg font-medium hidden"></div>
    
        <!-- Botões de Ação -->
        <div class="flex flex-col sm:flex-row items-center justify-center gap-4 pt-4">
            <button id="calculateBtn" class="w-full sm:w-auto bg-emerald-600 text-white font-semibold py-3 px-8 rounded-lg shadow-md hover:bg-emerald-700 transition-transform transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-emerald-500">
                Calcular Data Final
            </button>
            <button id="clearBtn" class="w-full sm:w-auto bg-transparent text-gray-700 font-semibold py-3 px-8 rounded-lg hover:bg-gray-100 transition focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-gray-400">
                Limpar
            </button>
        </div>
    
        <!-- Área de Resultado -->
        <div id="result" class="text-center bg-emerald-50 p-6 rounded-lg mt-6 hidden">
            <p class="text-emerald-900/80">A data final será:</p>
            <p id="result-date" class="text-5xl font-bold text-emerald-600 my-2"></p>
            <div class="mt-4 pt-4 border-t border-emerald-200 text-sm text-gray-700">
                <h3 class="font-semibold text-gray-800 mb-3">Detalhes do Período</h3>
                <div id="result-summary"></div>
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
        // Feriados móveis atualizados até 2028
        const nationalHolidays = [
            '01-01', '04-21', '05-01', '09-07', '10-12', '11-02', '11-15', '12-25',
            '2024-02-12', '2024-02-13', '2024-03-29', '2024-05-30', // 2024
            '2025-03-03', '2025-03-04', '2025-04-18', '2025-06-19', // 2025
            '2026-02-16', '2026-02-17', '2026-04-03', '2026-06-04', // 2026
            '2027-02-08', '2027-02-09', '2027-03-26', '2027-05-27', // 2027
            '2028-02-28', '2028-02-29', '2028-04-14', '2028-06-01', // 2028
        ];
    
        // --- FUNÇÕES AUXILIARES ---
        const formatDate = (date) => {
            const day = String(date.getDate()).padStart(2, '0');
            const month = String(date.getMonth() + 1).padStart(2, '0');
            return `${day}/${month}/${date.getFullYear()}`;
        };
    
        const isBusinessDay = (date) => {
            const dayOfWeek = date.getDay();
            if (dayOfWeek === 0 || dayOfWeek === 6) return false; // Domingo ou Sábado
            
            const year = date.getFullYear();
            const monthDay = `${String(date.getMonth() + 1).padStart(2, '0')}-${String(date.getDate()).padStart(2, '0')}`;
            const fullDate = `${year}-${monthDay}`;
            
            return !nationalHolidays.includes(monthDay) && !nationalHolidays.includes(fullDate);
        };
    
        const showError = (message) => {
            errorMessage.textContent = message;
            errorMessage.classList.remove('hidden');
            resultDiv.classList.add('hidden');
        };
    
        const hideError = () => errorMessage.classList.add('hidden');
        
        const showResult = () => {
            resultDiv.classList.remove('hidden');
            resultDiv.classList.add('fade-in');
            resultDiv.addEventListener('animationend', () => resultDiv.classList.remove('fade-in'), { once: true });
        }

        // --- FUNÇÕES DE CÁLCULO ---
        function calculate() {
            const startDateValue = startDateInput.value;
            const daysToProcess = parseInt(daysToProcessInput.value, 10);
            if (!startDateValue || isNaN(daysToProcess)) {
                showError("Por favor, preencha a data de início e a quantidade de dias.");
                return;
            }
            if (daysToProcess === 0) {
                showError("A quantidade de dias deve ser diferente de zero.");
                return;
            }
    
            hideError();
            const startDate = new Date(startDateValue + 'T12:00:00');
            dayTypeSelect.value === 'business' ? calculateBusinessDays(startDate, daysToProcess) : calculateCalendarDays(startDate, daysToProcess);
        }
    
        function calculateCalendarDays(startDate, daysToProcess) {
            let finalDate = new Date(startDate);
            finalDate.setDate(finalDate.getDate() + daysToProcess);
            resultDate.textContent = formatDate(finalDate);
            resultSummary.innerHTML = `
                <ul class="text-left w-full max-w-sm mx-auto space-y-2">
                    <li class="flex justify-between"><span>Data Inicial:</span> <strong>${formatDate(startDate)}</strong></li>
                    <li class="flex justify-between"><span>Data Final:</span> <strong>${formatDate(finalDate)}</strong></li>
                    <li class="flex justify-between border-t border-emerald-200 pt-2 mt-2"><span>Total de Dias Corridos:</span> <strong>${Math.abs(daysToProcess)}</strong></li>
                </ul>`;
            showResult();
        }
    
        function calculateBusinessDays(startDate, daysToProcess) {
            let currentDate = new Date(startDate);
            let businessDaysCount = 0;
            const direction = daysToProcess > 0 ? 1 : -1;
            const targetBusinessDays = Math.abs(daysToProcess);
    
            if (includeStartDateInput.checked && isBusinessDay(new Date(currentDate))) {
                businessDaysCount = 1;
            }
    
            while (businessDaysCount < targetBusinessDays) {
                currentDate.setDate(currentDate.getDate() + direction);
                if (isBusinessDay(new Date(currentDate))) businessDaysCount++;
            }
            
            let holidaysCount = 0, weekendsCount = 0, totalDays = 0;
            const [startSummary, endSummary] = startDate < currentDate ? [new Date(startDate), new Date(currentDate)] : [new Date(currentDate), new Date(startDate)];
    
            while(startSummary <= endSummary) {
                if (!isBusinessDay(startSummary)) {
                    const dayOfWeek = startSummary.getDay();
                    dayOfWeek === 0 || dayOfWeek === 6 ? weekendsCount++ : holidaysCount++;
                }
                totalDays++;
                startSummary.setDate(startSummary.getDate() + 1);
            }
    
            resultDate.textContent = formatDate(currentDate);
            resultSummary.innerHTML = `
                <ul class="text-left w-full max-w-sm mx-auto space-y-2">
                    <li class="flex justify-between"><span>Dias Úteis Calculados:</span> <strong>${businessDaysCount}</strong></li>
                    <li class="flex justify-between"><span>Fins de Semana no Período:</span> <strong>${weekendsCount}</strong></li>
                    <li class="flex justify-between"><span>Feriados no Período:</span> <strong>${holidaysCount}</strong></li>
                    <li class="flex justify-between border-t border-emerald-200 pt-2 mt-2"><span>Total de Dias Corridos:</span> <strong>${totalDays - 1}</strong></li>
                </ul>`;
            showResult();
        }
    
        function clearFields() {
            startDateInput.value = '';
            daysToProcessInput.value = '';
            includeStartDateInput.checked = false;
            dayTypeSelect.value = 'business';
            includeStartDateWrapper.style.display = 'flex';
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
            includeStartDateWrapper.style.display = e.target.value === 'calendar' ? 'none' : 'flex';
        });
        document.addEventListener('DOMContentLoaded', () => {
            includeStartDateWrapper.style.display = dayTypeSelect.value === 'calendar' ? 'none' : 'flex';
        });
    </script>
</body>
</html>

