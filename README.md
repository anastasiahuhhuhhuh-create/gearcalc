
<!DOCTYPE html>
<html lang="uk">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<title>GearCalc — Калькулятор 3D-шестерень</title>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: Inter, Arial, sans-serif;
    background: #0b0f19;
    color: #e5e7eb;
    min-height: 100vh;
    padding: 40px 20px;
}

.container {
    max-width: 850px;
    margin: auto;
}

header {
    text-align: center;
    margin-bottom: 35px;
}

.logo {
    font-size: 34px;
    font-weight: 800;
    color: #60a5fa;
    margin-bottom: 12px;
}

header p {
    color: #94a3b8;
    font-size: 15px;
}

.calculator {
    background: #151c2b;
    border: 1px solid #263248;
    border-radius: 18px;
    padding: 30px;
    box-shadow: 0 20px 60px #0005;
}

h2 {
    font-size: 21px;
    margin-bottom: 25px;
}

.inputs {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
}

.field label {
    display: block;
    color: #aab6ca;
    font-size: 14px;
    margin-bottom: 9px;
}

.field input {
    width: 100%;
    padding: 14px;
    background: #0c1322;
    border: 1px solid #344158;
    border-radius: 9px;
    color: white;
    font-size: 16px;
    outline: none;
}

.field input:focus {
    border-color: #60a5fa;
    box-shadow: 0 0 0 3px #3b82f633;
}

.hint {
    color: #718096;
    font-size: 12px;
    margin-top: 7px;
}

button {
    width: 100%;
    margin-top: 27px;
    padding: 15px;
    border: none;
    border-radius: 10px;
    background: #3b82f6;
    color: white;
    font-size: 16px;
    font-weight: 700;
    cursor: pointer;
    transition: 0.2s;
}

button:hover {
    background: #2563eb;
    transform: translateY(-2px);
}

.results {
    display: none;
    margin-top: 30px;
}

.results h2 {
    margin-bottom: 18px;
}

.result-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 14px;
}

.result-card {
    background: #0d1525;
    border: 1px solid #293750;
    border-radius: 12px;
    padding: 18px;
}

.result-card span {
    display: block;
    color: #94a3b8;
    font-size: 13px;
    margin-bottom: 10px;
}

.result-card strong {
    font-size: 25px;
    color: #60a5fa;
}

.result-card small {
    color: #64748b;
    margin-left: 4px;
}

.advice {
    margin-top: 20px;
    background: #172b42;
    border-left: 4px solid #3b82f6;
    padding: 20px;
    border-radius: 9px;
    line-height: 1.6;
}

.advice h3 {
    color: #60a5fa;
    margin-bottom: 8px;
}

.error {
    color: #f87171;
    margin-top: 15px;
    font-size: 14px;
}

footer {
    text-align: center;
    color: #64748b;
    font-size: 12px;
    margin-top: 30px;
}

@media(max-width: 600px) {
    body {
        padding: 25px 14px;
    }

    .calculator {
        padding: 22px 17px;
    }

    .inputs,
    .result-grid {
        grid-template-columns: 1fr;
    }

    .logo {
        font-size: 28px;
    }
}
</style>
</head>

<body>

<div class="container">

<header>
    <div class="logo">⚙ GearCalc</div>
    <p>Калькулятор параметрів шестерень для 3D-друку</p>
</header>

<div class="calculator">

<h2>Параметри шестерні</h2>

<div class="inputs">

    <div class="field">
        <label for="module">Модуль зубців (мм)</label>
        <input
            type="number"
            id="module"
            min="0.1"
            max="20"
            step="0.1"
            value="2"
        >
        <div class="hint">Наприклад: 1, 1.5, 2 або 3</div>
    </div>

    <div class="field">
        <label for="teeth">Кількість зубців</label>
        <input
            type="number"
            id="teeth"
            min="6"
            max="300"
            step="1"
            value="30"
        >
        <div class="hint">Ціле число від 6 до 300</div>
    </div>

</div>

<button id="calculateButton">Розрахувати параметри</button>

<div class="error" id="error" role="alert"></div>

<div class="results" id="results">

    <h2>Результати розрахунку</h2>

    <div class="result-grid">

        <div class="result-card">
            <span>Ділильний діаметр</span>
            <strong id="pitch"></strong>
            <small>мм</small>
        </div>

        <div class="result-card">
            <span>Зовнішній діаметр</span>
            <strong id="outside"></strong>
            <small>мм</small>
        </div>

        <div class="result-card">
            <span>Діаметр западин</span>
            <strong id="root"></strong>
            <small>мм</small>
        </div>

        <div class="result-card">
            <span>Висота зубця</span>
            <strong id="height"></strong>
            <small>мм</small>
        </div>

    </div>

    <div class="advice">
        <h3>💡 Рекомендація</h3>
        <p id="recommendation"></p>
    </div>

</div>
</div>

<footer>
    GearCalc © 2026 | Інструмент для проєктування 3D-шестерень
</footer>

</div>

<script>

// Заборона від'ємних значень
const moduleInput = document.getElementById("module");
const teethInput = document.getElementById("teeth");

[moduleInput, teethInput].forEach(input => {

    input.addEventListener("input", function() {

        if (this.value !== "" && Number(this.value) < 0) {
            this.value = "";
        }

        document.getElementById("results").style.display = "none";
        document.getElementById("error").textContent = "";
    });

});

// Розрахунок параметрів
function calculate() {

    const module = Number(moduleInput.value);
    const teeth = Number(teethInput.value);

    const error = document.getElementById("error");
    const results = document.getElementById("results");

    error.textContent = "";
    results.style.display = "none";

    // Перевірка модуля
    if (
        moduleInput.value === "" ||
        !Number.isFinite(module) ||
        module <= 0 ||
        module > 20
    ) {
        error.textContent =
            "Помилка: введіть модуль більше 0 та не більше 20 мм.";
        return;
    }

    // Перевірка кількості зубців
    if (
        teethInput.value === "" ||
        !Number.isFinite(teeth) ||
        !Number.isInteger(teeth) ||
        teeth < 6 ||
        teeth > 300
    ) {
        error.textContent =
            "Помилка: кількість зубців має бути цілим числом від 6 до 300.";
        return;
    }

    // Основні формули стандартної прямозубої шестерні
    const pitchDiameter = module * teeth;
    const outsideDiameter = module * (teeth + 2);
    const rootDiameter = module * (teeth - 2.5);
    const toothHeight = 2.25 * module;

    // Виведення результатів
    document.getElementById("pitch").textContent =
        pitchDiameter.toFixed(2);

    document.getElementById("outside").textContent =
        outsideDiameter.toFixed(2);

    document.getElementById("root").textContent =
        rootDiameter.toFixed(2);

    document.getElementById("height").textContent =
        toothHeight.toFixed(2);

    // Порада щодо типу шестерні
    let recommendation = "";

    if (teeth < 12) {

        recommendation =
            "Рекомендується прямозуба шестерня з корекцією профілю. " +
            "Мала кількість зубців може спричинити підрізання профілю.";

    } else if (module <= 1) {

        recommendation =
            "Підійде компактна прямозуба шестерня. " +
            "Рекомендується точне калібрування 3D-принтера.";

    } else if (module <= 2.5) {

        recommendation =
            "Хороший універсальний варіант — прямозуба шестерня. " +
            "Вона проста у моделюванні та 3D-друці.";

    } else {

        recommendation =
            "Для великих механізмів підійде прямозуба шестерня. " +
            "Якщо важлива плавність роботи, розгляньте косозубу конструкцію.";

    }

    document.getElementById("recommendation").textContent =
        recommendation;

    results.style.display = "block";
}

// Обробка натискання кнопки
document.getElementById("calculateButton")
    .addEventListener("click", calculate);

</script>

</body>
</html>

