# Egg-opener-13
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Egg Opener Simulator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background-color: #f7f7f7;
            padding: 20px;
            overflow: hidden;
        }
        .button {
            padding: 15px 25px;
            font-size: 20px;
            color: #fff;
            background-color: #007BFF;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin: 5px;
        }
        .button:hover {
            background-color: #0056b3;
        }
        .output {
            margin-top: 20px;
            font-size: 24px;
            color: #333;
        }
        .animation-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.8);
            display: none;
            align-items: center;
            justify-content: center;
            z-index: 1000;
            overflow: hidden;
        }
        .zoom-effect {
            animation: fullZoomOut 1s forwards, fullZoomIn 1.5s 1s forwards;
        }
        .side-effects {
            position: absolute;
            width: 100%;
            height: 100%;
            pointer-events: none;
        }
        .side-line {
            position: absolute;
            width: 10px;
            height: 100%;
            background: linear-gradient(to bottom, rgba(255, 0, 0, 0), red, rgba(255, 0, 0, 0));
            animation: sideLineMove 1s infinite ease-in-out;
        }
        .side-line:nth-child(1) {
            left: 10%;
        }
        .side-line:nth-child(2) {
            right: 10%;
        }
        .side-line:nth-child(3) {
            left: 20%;
        }
        .side-line:nth-child(4) {
            right: 20%;
        }
        @keyframes fullZoomOut {
            from {
                transform: scale(1);
            }
            to {
                transform: scale(0.5);
            }
        }
        @keyframes fullZoomIn {
            from {
                transform: scale(0.5);
            }
            to {
                transform: scale(1);
            }
        }
        @keyframes sideLineMove {
            from {
                opacity: 0;
            }
            to {
                opacity: 1;
            }
        }
        .pet-popup {
            position: absolute;
            background-color: white;
            border: 2px solid #ccc;
            border-radius: 10px;
            padding: 20px;
            text-align: center;
            opacity: 0;
            animation: fadeIn 1s 2s forwards;
            font-size: 36px;
            font-weight: bold;
            color: #333;
        }
        @keyframes fadeIn {
            from {
                opacity: 0;
            }
            to {
                opacity: 1;
            }
        }
        .inventory {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: white;
            display: none;
            flex-wrap: wrap;
            align-items: center;
            justify-content: center;
            overflow-y: auto;
            z-index: 1000;
        }
        .inventory.active {
            display: flex;
        }
        .pet-box {
            width: 120px;
            height: 150px;
            margin: 10px;
            background-color: #fff;
            border-radius: 10px;
            box-shadow: 0 2px 8px rgba(0, 0, 0, 0.3);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            position: relative;
        }
        .pet-box .count {
            position: absolute;
            top: 5px;
            right: 5px;
            background-color: #007BFF;
            color: white;
            font-size: 12px;
            padding: 3px 6px;
            border-radius: 50%;
        }
        .pet-box .name {
            font-size: 14px;
            font-weight: bold;
            margin-top: 10px;
        }
        .close-button {
            position: absolute;
            top: 10px;
            right: 10px;
            background-color: #ff0000;
            color: white;
            border: none;
            border-radius: 50%;
            width: 30px;
            height: 30px;
            font-size: 18px;
            cursor: pointer;
        }
        .table-container {
            margin-top: 40px;
        }
        .pet-list {
            text-align: left;
            margin-left: auto;
            margin-right: auto;
            max-width: 600px;
        }
        .pet-list table {
            width: 100%;
            border-collapse: collapse;
        }
        .pet-list th, .pet-list td {
            border: 1px solid #ccc;
            padding: 8px;
            text-align: center;
        }
        .pet-list th {
            background-color: #007BFF;
            color: white;
        }
    </style>
</head>
<body>
    <h1>Egg Opener Simulator</h1>
    <div>
        <button id="inventoryButton" class="button" style="margin-right: auto; float: left;" onclick="toggleInventory()">Inventar</button>
        <button id="openButton" class="button" onclick="openEgg()">Open Egg</button>
    </div>
    <div class="output" id="output">Click the button to open an egg!</div>
    <div id="popupContainer"></div>

    <!-- Animation Container -->
    <div class="animation-container" id="animationContainer">
        <div class="side-effects">
            <div class="side-line"></div>
            <div class="side-line"></div>
            <div class="side-line"></div>
            <div class="side-line"></div>
        </div>
        <div class="pet-popup" id="petPopup">Rare Fox!</div>
    </div>

    <!-- Inventory Overlay -->
    <div class="inventory" id="inventory">
        <button class="close-button" onclick="toggleInventory()">×</button>
    </div>

    <!-- Pet List Table -->
    <div class="table-container">
        <div class="pet-list">
            <h2>Pet Collection</h2>
            <table>
                <thead>
                    <tr>
                        <th>Pet</th>
                        <th>Probability</th>
                        <th>Count</th>
                    </tr>
                </thead>
                <tbody id="petTableBody"></tbody>
            </table>
        </div>
    </div>

    <script>
        const pets = [
            { name: "Common Cat", chance: 60.0, count: 0 },
            { name: "Common Dog", chance: 20.0, count: 0 },
            { name: "Uncommon Bird", chance: 10.0, count: 0 },
            { name: "Rare Fox", chance: 5.0, count: 0 },
            { name: "Epic Dragon", chance: 2.5, count: 0 },
            { name: "Legendary Unicorn", chance: 1.0, count: 0 },
            { name: "Mythic Phoenix", chance: 0.5, count: 0 },
            { name: "Divine Tiger", chance: 0.25, count: 0 },
            { name: "Godly Wolf", chance: 0.1, count: 0 },
            { name: "Celestial Whale", chance: 0.05, count: 0 },
        ];
        const totalChance = pets.reduce((sum, pet) => sum + pet.chance, 0);

        function openEgg() {
            const random = Math.random() * totalChance;
            let cumulativeChance = 0;

            for (const pet of pets) {
                cumulativeChance += pet.chance;
                if (random <= cumulativeChance) {
                    pet.count++;
                    updateTable();
                    if (pet.name === "Rare Fox") playRareAnimation(pet.name);
                    else showPopup(pet.name);
                    return;
                }
            }
        }

        function playRareAnimation(petName) {
            const container = document.getElementById("animationContainer");
            const popup = document.getElementById("petPopup");

            popup.innerText = petName;
            container.style.display = "flex";
            container.classList.add("zoom-effect");
            document.getElementById("openButton").disabled = true;

            setTimeout(() => {
                container.style.display = "none";
                container.classList.remove("zoom-effect");
                document.getElementById("openButton").disabled = false;
            }, 2500);
        }

        function toggleInventory() {
            const inventory = document.getElementById("inventory");
            inventory.classList.toggle("active");

            inventory.innerHTML = `<button class="close-button" onclick="toggleInventory()">×</button>`;
            pets.forEach(pet => {
                const petBox = document.createElement("div");
                petBox.className = "pet-box";
                petBox.innerHTML = `<div class="count">${pet.count}</div><div class="name">${pet.name}</div>`;
                inventory.appendChild(petBox);
            });
        }

        function updateTable() {
            const tableBody = document.getElementById("petTableBody");
            tableBody.innerHTML = "";
            pets.forEach(pet => {
                const row = `<tr><td>${pet.name}</td><td>${pet.chance}%</td><td>${pet.count}</td></tr>`;
                tableBody.innerHTML += row;
            });
        }

        function showPopup(petName) {
            const output = document.getElementById("output");
            output.innerText = `You got a ${petName}!`;
        }

        updateTable();
    </script>
</body>
</html>
