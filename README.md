<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Property Tax Calculator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
        }
        .calculator {
            max-width: 400px;
            margin: 0 auto;
            padding: 20px;
            border: 1px solid #ccc;
            border-radius: 10px;
            background-color: #f9f9f9;
        }
        .calculator h2 {
            text-align: center;
        }
        .calculator label {
            display: block;
            margin: 10px 0 5px;
        }
        .calculator input, .calculator select {
            width: 100%;
            padding: 8px;
            margin-bottom: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        .calculator button {
            width: 100%;
            padding: 10px;
            background-color: #28a745;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .calculator button:hover {
            background-color: #218838;
        }
        .result {
            margin-top: 20px;
            text-align: center;
            font-size: 1.2em;
        }
        .calculation-steps {
            margin-top: 20px;
            padding: 10px;
            background-color: #e9ecef;
            border-radius: 5px;
            font-family: monospace;
        }
    </style>
</head>
<body>

<div class="calculator">
    <h2>Property Tax Calculator</h2>
    <label for="taxClass">Tax Classification:</label>
    <select id="taxClass">
        <option value="ownerOccupied">Owner-Occupied</option>
        <option value="nonOwnerOccupied">Non-Owner-Occupied</option>
        <option value="tvrStrh">TVR-STRH</option>
        <option value="longTermRental">Long-Term Rental</option>
        <option value="commercializedResidential">Commercialized Residential</option>
        <option value="hotelResort">Hotel and Resort</option>
        <option value="timeShare">Time Share</option>
        <option value="agriculture">Agriculture</option>
        <option value="conservation">Conservation</option>
        <option value="commercial">Commercial</option>
        <option value="industrial">Industrial</option>
    </select>

    <label for="assessmentValue">Assessment Value ($):</label>
    <input type="number" id="assessmentValue" placeholder="Enter assessment value">

    <button onclick="calculateTax()">Calculate Tax</button>

    <div class="result" id="result"></div>
    <div class="calculation-steps" id="calculationSteps"></div>
</div>

<script>
    const taxRates = {
        ownerOccupied: [
            { min: 0, max: 1000000, rate: 1.80 },
            { min: 1000001, max: 3000000, rate: 2.00 },
            { min: 3000001, max: Infinity, rate: 3.25 }
        ],
        nonOwnerOccupied: [
            { min: 0, max: 1000000, rate: 5.87 },
            { min: 1000001, max: 3000000, rate: 8.50 },
            { min: 3000001, max: Infinity, rate: 14.00 }
        ],
        tvrStrh: [
            { min: 0, max: 1000000, rate: 12.50 },
            { min: 1000001, max: 3000000, rate: 13.50 },
            { min: 3000001, max: Infinity, rate: 15.00 }
        ],
        longTermRental: [
            { min: 0, max: 1000000, rate: 3.00 },
            { min: 1000001, max: 3000000, rate: 5.00 },
            { min: 3000001, max: Infinity, rate: 8.00 }
        ],
        commercializedResidential: [
            { min: 0, max: 1000000, rate: 4.00 },
            { min: 1000001, max: 3000000, rate: 5.00 },
            { min: 3000001, max: Infinity, rate: 8.00 }
        ],
        hotelResort: 11.75,
        timeShare: 13.50,
        agriculture: 3.00,
        conservation: 8.00,
        commercial: 6.05,
        industrial: 7.05
    };

    function calculateTax() {
        const taxClass = document.getElementById('taxClass').value;
        const assessmentValue = parseFloat(document.getElementById('assessmentValue').value);

        if (isNaN(assessmentValue) || assessmentValue <= 0) {
            document.getElementById('result').innerText = "Please enter a valid assessment value.";
            document.getElementById('calculationSteps').innerText = "";
            return;
        }

        let taxOwed = 0;
        let calculationSteps = "";

        if (Array.isArray(taxRates[taxClass])) {
            // Tiered tax calculation
            calculationSteps = "Calculation Steps:\n";
            for (const tier of taxRates[taxClass]) {
                if (assessmentValue > tier.min) {
                    const taxableAmount = Math.min(assessmentValue, tier.max) - tier.min;
                    const tierTax = (taxableAmount / 1000) * tier.rate;
                    taxOwed += tierTax;

                    calculationSteps += `- Tier ($${tier.min} - $${tier.max === Infinity ? "∞" : tier.max}): $${taxableAmount.toLocaleString()} * $${tier.rate}/1000 = $${tierTax.toFixed(2)}\n`;
                }
            }
        } else {
            // Flat tax calculation
            taxOwed = (assessmentValue / 1000) * taxRates[taxClass];
            calculationSteps = `Calculation Steps:\n- Flat Rate: $${assessmentValue.toLocaleString()} * $${taxRates[taxClass]}/1000 = $${taxOwed.toFixed(2)}`;
        }

        // Apply minimum tax if applicable
        if (taxOwed < 300) {
            calculationSteps += `\n- Minimum Tax Applied: $${taxOwed.toFixed(2)} → $300.00`;
            taxOwed = 300;
        }

        document.getElementById('result').innerText = `Annual Property Tax: $${taxOwed.toFixed(2)}`;
        document.getElementById('calculationSteps').innerText = calculationSteps;
    }
</script>

</body>
</html>
