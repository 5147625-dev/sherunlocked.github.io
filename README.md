# sherunlocked.github.io
<html lang="cs">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Dešifrování zprávy</title>

<style>
* {
    box-sizing: border-box;
}

body {
    margin: 0;
    padding: 25px 15px;
    background: #f2f4f8;
    color: #202638;
    font-family: Arial, sans-serif;
}

.container {
    max-width: 950px;
    margin: auto;
    background: white;
    padding: 30px;
    border-radius: 14px;
    box-shadow: 0 4px 20px rgba(0,0,0,0.08);
}

h1 {
    text-align: center;
    margin-top: 0;
    color: #243b64;
}

h2 {
    margin-top: 30px;
    color: #243b64;
    font-size: 21px;
}

.description {
    text-align: center;
    color: #687187;
    line-height: 1.6;
    margin-bottom: 30px;
}

label {
    display: block;
    font-weight: bold;
    margin: 18px 0 7px;
}

input, select, textarea {
    width: 100%;
    padding: 12px;
    border: 1px solid #cbd2df;
    border-radius: 7px;
    font-size: 16px;
    font-family: Arial, sans-serif;
}

textarea {
    min-height: 110px;
    resize: vertical;
}

input:focus, select:focus, textarea:focus {
    outline: 2px solid #789bd5;
    border-color: #789bd5;
}

.dimensions {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 15px;
}

button {
    width: 100%;
    margin-top: 25px;
    padding: 15px;
    border: none;
    border-radius: 8px;
    background: #294b80;
    color: white;
    font-size: 17px;
    font-weight: bold;
    cursor: pointer;
}

button:hover {
    background: #19365f;
}

.secondary {
    background: #687187;
}

.secondary:hover {
    background: #4c566b;
}

.error {
    display: none;
    background: #ffe7e7;
    color: #a51f1f;
    padding: 14px;
    border-radius: 7px;
    margin-top: 20px;
    font-weight: bold;
    line-height: 1.5;
}

.result {
    display: none;
    margin-top: 30px;
}

.final-message {
    background: #e9f5ec;
    border: 1px solid #b9ddc1;
    padding: 20px;
    border-radius: 9px;
    margin: 20px 0;
}

.final-message h2 {
    margin-top: 0;
    color: #24663a;
}

.final-text {
    font-size: 23px;
    font-weight: bold;
    overflow-wrap: anywhere;
    letter-spacing: 1px;
}

.step {
    background: #f6f7fa;
    border: 1px solid #dce1e9;
    border-radius: 9px;
    padding: 18px;
    margin: 18px 0;
    overflow-x: auto;
}

.step h3 {
    margin-top: 0;
    color: #294b80;
}

.step p {
    line-height: 1.6;
}

.matrix {
    border-collapse: collapse;
    margin: 12px 0;
}

.matrix td {
    border: 1px solid #9da9ba;
    min-width: 38px;
    height: 38px;
    text-align: center;
    font-weight: bold;
    font-size: 17px;
    padding: 5px;
    background: white;
}

.matrix td.empty {
    background: #eceff4;
    color: #aab1bd;
}

.matrix-group {
    margin-bottom: 25px;
}

.matrix-group h4 {
    color: #374b6a;
}

.small-note {
    color: #687187;
    font-size: 13px;
    line-height: 1.5;
}

@media (max-width: 600px) {
    .container {
        padding: 20px 15px;
    }

    .dimensions {
        grid-template-columns: 1fr;
    }

    .matrix td {
        min-width: 31px;
        height: 33px;
    }
}
</style>
</head>

<body>

<div class="container">

<h1>Dešifrování zprávy</h1>

<p class="description">
Zadejte zašifrovanou zprávu, klíč, osobní číslo,
pohlaví příjemce a Fibonacciho velikost matice.
Systém následně obnoví původní zprávu a zobrazí
jednotlivé kroky dešifrování.
</p>

<label for="cipher">1. Zašifrovaná zpráva</label>
<textarea id="cipher"
placeholder="Vložte zašifrovanou zprávu..."></textarea>

<label for="key">2. Klíč k šifře</label>
<input id="key" type="text"
placeholder="Zadejte klíč...">

<label for="personal">3. Osobní číslo (přesně 8 číslic)</label>
<input id="personal" type="text"
inputmode="numeric"
maxlength="8"
placeholder="Např. 58120547">

<label for="gender">4. Pohlaví příjemce</label>
<select id="gender">
    <option value="male">Muž</option>
    <option value="female">Žena</option>
    <option value="nonbinary">Nebinární</option>
</select>

<label>5. Fibonacciho velikost matice</label>

<div class="dimensions">
    <div>
        <label for="rows">Počet řádků</label>
        <select id="rows"></select>
    </div>

    <div>
        <label for="cols">Počet sloupců</label>
        <select id="cols"></select>
    </div>
</div>

<p class="small-note">
Zadávají se Fibonacciho čísla použitá při šifrování.
Například 8 řádků znamená skutečně 5 řádků,
zatímco 3 sloupce zůstávají 3 sloupci.
</p>

<button onclick="decrypt()">DEŠIFROVAT ZPRÁVU</button>

<button class="secondary" onclick="clearAll()">
VYMAZAT FORMULÁŘ
</button>

<div id="error" class="error"></div>

<div id="result" class="result">

    <div class="final-message">
        <h2>Výsledná rozšifrovaná zpráva</h2>
        <div id="finalText" class="final-text"></div>
    </div>

    <h2>Podrobný průběh dešifrování</h2>

    <div id="details"></div>

</div>

</div>

<script>

// ============================================
// 1. ZÁKLADNÍ NASTAVENÍ
// ============================================

const alphabet = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";

// Vytvoření Fibonacciho posloupnosti:
// 1, 2, 3, 5, 8, 13, 21, 34...

const fibonacci = [1, 2];

while (fibonacci[fibonacci.length - 1] < 1000000) {
    const n = fibonacci.length;
    fibonacci.push(
        fibonacci[n - 1] + fibonacci[n - 2]
    );
}

// Vytvoření potřebného množství prvočísel.

function generatePrimes(count) {

    const result = [];

    let number = 2;

    while (result.length < count) {

        let prime = true;

        for (let i = 2; i * i <= number; i++) {

            if (number % i === 0) {
                prime = false;
                break;
            }
        }

        if (prime) {
            result.push(number);
        }

        number++;
    }

    return result;
}

const primes = generatePrimes(100);

// ============================================
// 2. NASTAVENÍ FIBONACCIHO ROZMĚRŮ
// ============================================

function fillDimensions() {

    const rowSelect = document.getElementById("rows");
    const colSelect = document.getElementById("cols");

    fibonacci.forEach(n => {

        const option1 = document.createElement("option");
        option1.value = n;
        option1.textContent = n;

        rowSelect.appendChild(option1);

        const option2 = document.createElement("option");
        option2.value = n;
        option2.textContent = n;

        colSelect.appendChild(option2);
    });

    rowSelect.value = "8";
    colSelect.value = "3";
}

fillDimensions();

// Fibonacciho číslo převedeme na jeho pořadí
// v posloupnosti.
//
// 1 -> 1
// 2 -> 2
// 3 -> 3
// 5 -> 4
// 8 -> 5
// 13 -> 6
// 21 -> 7
// 34 -> 8
// 55 -> 9

function actualDimension(value) {

    const index = fibonacci.indexOf(Number(value));

    if (index === -1) {
        throw new Error(
            "ERROR: Neplatná Fibonacciho velikost matice."
        );
    }

    return index + 1;
}

// ============================================
// 3. NORMALIZACE TEXTU
// ============================================

function normalizeText(text) {

    return text
        .normalize("NFD")
        .replace(/[\u0300-\u036f]/g, "")
        .replace(/[^a-zA-Z]/g, "");
}

// Převedení písmene na číselnou hodnotu.
// A = 1, B = 2 ... Z = 26.

function letterNumber(letter) {

    return alphabet.indexOf(letter.toUpperCase()) + 1;
}

// Převedení čísla na písmeno.
// Po Z následuje A.

function numberLetter(number) {

    return alphabet[
        ((number - 1) % 26 + 26) % 26
    ];
}

// ============================================
// 4. OBRÁCENÍ PROSTŘEDNÍHO PÍSMENE
// ============================================

function reverseMiddle(resultLetter, keyLetter) {

    const V = letterNumber(resultLetter);
    const K = letterNumber(keyLetter);

    let original;

    if (resultLetter === resultLetter.toLowerCase()) {

        // Malé písmeno:
        // Použilo se dřívější ze dvou prostředních písmen.

        original = 2 * V + 1 - K;

    } else {

        // Velké písmeno:
        // Existovalo pouze jedno prostřední písmeno.

        original = 2 * V - K;
    }

    if (original < 1 || original > 26) {

        throw new Error(
            "ERROR: Nelze matematicky obnovit původní písmeno. " +
            "Zkontrolujte zprávu a klíč."
        );
    }

    const recovered = numberLetter(original);

    // Kontrola zpětného výpočtu.
    // Znovu vypočítáme prostřední písmeno
    // a porovnáme jej s původním vstupem.

    const distance = Math.abs(original - K);

    let expected;

    if (distance % 2 === 0) {

        expected = numberLetter(
            (original + K) / 2
        );

    } else {

        expected = numberLetter(
            Math.floor((original + K) / 2)
        ).toLowerCase();
    }

    if (expected !== resultLetter) {

        throw new Error(
            "ERROR: Nesouhlasí zpětný výpočet prostředního písmene."
        );
    }

    return recovered;
}

// ============================================
// 5. OBRÁCENÍ PRVOČÍSELNÉHO POSUNU
// ============================================

function reversePrimeShift(matrix, rows, cols) {

    for (let c = 0; c < cols; c++) {

        const prime = primes[c];

        if (prime === undefined) {

            throw new Error(
                "ERROR: Pro tento počet sloupců není připraveno prvočíslo."
            );
        }

        // Původní šifrování:
        // 1. sloupec dolů (+2)
        // 2. sloupec nahoru (-3)
        // 3. sloupec dolů (+5)
        // 4. sloupec nahoru (-7)

        const encryptionDirection =
            (c % 2 === 0) ? 1 : -1;

        // Dešifrování používá opačný směr.

        const decryptionShift =
            -encryptionDirection * prime;

        for (let r = 0; r < rows; r++) {

            if (matrix[r][c] !== "") {

                // Zjistíme původní velikost písmene.
                const wasLower =
                    matrix[r][c] ===
                    matrix[r][c].toLowerCase();

                const value = letterNumber(matrix[r][c]);

                let shifted = numberLetter(
                    value + decryptionShift
                );

                // DŮLEŽITÉ:
                // Zachováváme velikost písmena.
                // Malá písmena jsou nezbytná
                // pro zpětný výpočet prostředního písmene.

                if (wasLower) {
                    shifted = shifted.toLowerCase();
                }

                matrix[r][c] = shifted;
            }
        }
    }

    return matrix;
}

// ============================================
// 6. OBRÁCENÍ OSOBNÍHO ABECEDNÍHO POSUNU
// ============================================

function reversePersonalShift(text, personal, gender) {

    let output = "";

    for (let i = 0; i < text.length; i++) {

        const letter = text[i];

        const digit = Number(personal[i % 8]);

        let shift;

        if (gender === "male") {

            // Male:
            // Při šifrování směrem k A.
            // Dešifrování směrem k Z.

            shift = digit;

        } else if (gender === "female") {

            // Female:
            // Při šifrování směrem k Z.
            // Dešifrování směrem k A.

            shift = -digit;

        } else {

            // Nonbinary:
            // Šifrování začíná směrem k Z.
            // Následně se směr střídá.

            if (i % 2 === 0) {
                shift = -digit;
            } else {
                shift = digit;
            }
        }

        const value = letterNumber(letter);

        output += numberLetter(value + shift);
    }

    return output;
}

// ============================================
// 7. VYKRESLENÍ MATICE
// ============================================

function matrixHTML(matrix, rows, cols) {

    let html = '<table class="matrix">';

    for (let r = 0; r < rows; r++) {

        html += "<tr>";

        for (let c = 0; c < cols; c++) {

            const value = matrix[r][c] || "";

            if (value !== "") {

                html += `<td>${value}</td>`;

            } else {

                html += '<td class="empty">·</td>';
            }
        }

        html += "</tr>";
    }

    html += "</table>";

    return html;
}

// Vytvoření prázdné matice.

function emptyMatrix(rows, cols) {

    return Array.from(
        { length: rows },
        () => Array(cols).fill("")
    );
}

// ============================================
// 8. HLAVNÍ FUNKCE DEŠIFROVÁNÍ
// ============================================

function decrypt() {

    const errorBox = document.getElementById("error");
    const resultBox = document.getElementById("result");
    const details = document.getElementById("details");

    errorBox.style.display = "none";
    resultBox.style.display = "none";
    details.innerHTML = "";

    try {

        // ------------------------------------
        // ZÍSKÁNÍ VSTUPNÍCH HODNOT
        // ------------------------------------

        const cipher = normalizeText(
            document.getElementById("cipher").value
        );

        const key = normalizeText(
            document.getElementById("key").value
        ).toUpperCase();

        const personal =
            document.getElementById("personal").value;

        const gender =
            document.getElementById("gender").value;

        const fibonacciRows = Number(
            document.getElementById("rows").value
        );

        const fibonacciCols = Number(
            document.getElementById("cols").value
        );

        // ------------------------------------
        // KONTROLA VSTUPŮ
        // ------------------------------------

        if (!/^\d{8}$/.test(personal)) {

            throw new Error(
                "ERROR: Osobní číslo musí obsahovat přesně 8 číslic."
            );
        }

        if (!cipher.length) {

            throw new Error(
                "ERROR: Zadejte zašifrovanou zprávu."
            );
        }

        if (!key.length) {

            throw new Error(
                "ERROR: Zadejte klíč k šifře."
            );
        }

        // ------------------------------------
        // PŘEVOD FIBONACCIHO ROZMĚRŮ
        // ------------------------------------

        const rows = actualDimension(fibonacciRows);
        const cols = actualDimension(fibonacciCols);

        const capacity = rows * cols;

        const matrixCount = Math.ceil(
            cipher.length / capacity
        );

        let recoveredFirstShift = "";

        let log = "";

        // ------------------------------------
        // INFORMACE O DEŠIFROVÁNÍ
        // ------------------------------------

        log += `
        <div class="step">

            <h3>Vstupní parametry</h3>

            <p>
                Fibonacciho rozměry:
                <strong>
                    ${fibonacciRows} × ${fibonacciCols}
                </strong>
            </p>

            <p>
                Skutečná velikost matice:
                <strong>
                    ${rows} × ${cols}
                </strong>
            </p>

            <p>
                Kapacita jedné matice:
                <strong>${capacity} znaků</strong>
            </p>

            <p>
                Délka zašifrované zprávy:
                <strong>${cipher.length} znaků</strong>
            </p>

            <p>
                Počet použitých matic:
                <strong>${matrixCount}</strong>
            </p>

            <p>
                Pohlaví:
                <strong>${gender.toUpperCase()}</strong>
            </p>

        </div>
        `;

        // ====================================
        // ZPRACOVÁNÍ JEDNOTLIVÝCH MATIC
        // ====================================

        for (let block = 0; block < matrixCount; block++) {

            const start = block * capacity;

            const blockText = cipher.slice(
                start,
                start + capacity
            );

            const matrix = emptyMatrix(rows, cols);

            // --------------------------------
            // A. ZÁPIS ŠIFROVANÉHO TEXTU
            // PO ŘÁDCÍCH
            // --------------------------------

            for (let i = 0; i < blockText.length; i++) {

                const r = Math.floor(i / cols);
                const c = i % cols;

                matrix[r][c] = blockText[i];
            }

            const cipherMatrix =
                matrix.map(row => [...row]);

            // --------------------------------
            // B. ZRUŠENÍ PRVOČÍSELNÉHO POSUNU
            // --------------------------------

            reversePrimeShift(matrix, rows, cols);

            const afterPrime =
                matrix.map(row => [...row]);

            // --------------------------------
            // C. VYTVOŘENÍ MATICE KLÍČE
            // KLÍČ SE ZAPISUJE PO SLOUPCÍCH
            // --------------------------------

            const keyMatrix = emptyMatrix(rows, cols);

            for (let c = 0; c < cols; c++) {

                for (let r = 0; r < rows; r++) {

                    const keyIndex = block * rows * cols + c * rows + r;

                    // Pokud je klíč kratší než zpráva,
                    // opakuje se od začátku.

                    keyMatrix[r][c] =
                        key[keyIndex % key.length];
                }
            }

            // --------------------------------
            // D. OBRÁCENÍ PROSTŘEDNÍHO PÍSMENE
            // --------------------------------

            const recoveredMatrix =
                emptyMatrix(rows, cols);

            for (let r = 0; r < rows; r++) {

                for (let c = 0; c < cols; c++) {

                    const encoded = matrix[r][c];

                    // Prázdné buňky se přeskočí.

                    if (encoded === "") {
                        continue;
                    }

                    const keyLetter = keyMatrix[r][c];

                    recoveredMatrix[r][c] =
                        reverseMiddle(encoded, keyLetter);
                }
            }

            // --------------------------------
            // E. ČTENÍ MATICE PO ŘÁDCÍCH
            // --------------------------------

            let blockRecovered = "";

            for (let r = 0; r < rows; r++) {

                for (let c = 0; c < cols; c++) {

                    if (recoveredMatrix[r][c] !== "") {

                        blockRecovered +=
                            recoveredMatrix[r][c];
                    }
                }
            }

            recoveredFirstShift += blockRecovered;

            // --------------------------------
            // F. ZOBRAZENÍ MEZIKROKŮ
            // --------------------------------

            log += `

            <div class="step matrix-group">

                <h3>Matice č. ${block + 1}</h3>

                <h4>A. Původní zašifrovaná matice</h4>

                <p>
                    Zašifrovaný text zapsaný po řádcích.
                </p>

                ${matrixHTML(cipherMatrix, rows, cols)}

                <h4>B. Matice po zrušení prvočíselného posunu</h4>

                <p>
                    Byly obráceny posuny podle prvočísel
                    2, 3, 5, 7, 11, 13...
                </p>

                ${matrixHTML(afterPrime, rows, cols)}

                <h4>C. Matice klíče</h4>

                <p>
                    Klíč je zapsán po sloupcích.
                    Kratší klíč se opakuje.
                </p>

                ${matrixHTML(keyMatrix, rows, cols)}

                <h4>D. Matice po obrácení prostředních písmen</h4>

                <p>
                    Původní písmena byla obnovena
                    matematicky pomocí známého klíče.
                </p>

                ${matrixHTML(recoveredMatrix, rows, cols)}

                <h4>E. Obnovený text před osobním posunem</h4>

                <p>
                    <strong>${blockRecovered}</strong>
                </p>

            </div>

            `;
        }

        // ====================================
        // 9. ZRUŠENÍ OSOBNÍHO POSUNU
        // ====================================

        const originalMessage = reversePersonalShift(
            recoveredFirstShift,
            personal,
            gender
        );

        // ====================================
        // 10. ZOBRAZENÍ POSLEDNÍHO MEZIKROKU
        // ====================================

        log += `

        <div class="step">

            <h3>Poslední krok: Obrácení osobního posunu</h3>

            <p>
                Použité osobní číslo:
                <strong>${personal}</strong>
            </p>

            <p>
                Pohlaví:
                <strong>${gender.toUpperCase()}</strong>
            </p>

            <p>
                Text před obrácením osobního posunu:
                <strong>${recoveredFirstShift}</strong>
            </p>

            <p>
                Výsledný původní text:
                <strong>${originalMessage}</strong>
            </p>

        </div>

        `;

        // ====================================
        // 11. VÝSLEDNÁ ZPRÁVA
        // ====================================

        document.getElementById("finalText").textContent =
            originalMessage;

        details.innerHTML = log;

        resultBox.style.display = "block";

        resultBox.scrollIntoView({
            behavior: "smooth",
            block: "start"
        });

    } catch (error) {

        errorBox.textContent = error.message.startsWith("ERROR")
            ? error.message
            : "ERROR: " + error.message;

        errorBox.style.display = "block";
    }
}

// ============================================
// 12. VYMAZÁNÍ FORMULÁŘE
// ============================================

function clearAll() {

    document.getElementById("cipher").value = "";
    document.getElementById("key").value = "";
    document.getElementById("personal").value = "";

    document.getElementById("gender").value = "male";

    document.getElementById("rows").value = "8";
    document.getElementById("cols").value = "3";

    document.getElementById("error").style.display = "none";
    document.getElementById("result").style.display = "none";

    document.getElementById("details").innerHTML = "";
}

</script>

</body>
</html>
