<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Reservoir DSS and Sediment Computation</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f4f4f9;
      margin: 0;
      padding: 0;
    }
    header {
      background-color: #0057b7;
      color: #fff;
      padding: 1rem;
      text-align: center;
      box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
    }
    .tabs {
      display: flex;
      justify-content: center;
      background-color: #e6e6e6;
      margin-bottom: 1rem;
    }
    .tabs button {
      flex: 1;
      padding: 1rem;
      background: #d4d4d4;
      border: none;
      cursor: pointer;
      font-weight: bold;
    }
    .tabs button.active {
      background: #0057b7;
      color: #fff;
    }
    .tab-content {
      display: none;
    }
    .tab-content.active {
      display: block;
    }
    .container {
      max-width: 900px;
      margin: 2rem auto;
      padding: 1rem;
      background: #fff;
      border-radius: 8px;
      box-shadow: 0px 2px 5px rgba(0, 0, 0, 0.1);
    }
    .input-group {
      margin-bottom: 1rem;
    }
    .input-group label {
      display: block;
      margin-bottom: 0.5rem;
      font-weight: bold;
    }
    .input-group input {
      width: 100%;
      padding: 0.5rem;
      border: 1px solid #ddd;
      border-radius: 5px;
    }
    button {
      background-color: #0057b7;
      color: #fff;
      padding: 0.7rem 1.5rem;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      font-size: 1rem;
    }
    button:hover {
      background-color: #003f91;
    }
    .chart-container {
      margin-top: 2rem;
    }
  </style>
</head>
<body>

<header>
  <h1>Reservoir Decision Support System</h1>
</header>

<div class="tabs">
  <button class="tab-link active" onclick="openTab('dss')">Reservoir DSS</button>
  <button class="tab-link" onclick="openTab('sediment')">Sediment Computation</button>
</div>

<div id="dss" class="tab-content active">
  <div class="container">
    <h2>Reservoir DSS</h2>
    <div class="input-group">
      <label for="inflow">Inflow (m³/s):</label>
      <input type="number" id="inflow" placeholder="Enter inflow data">
    </div>
    <div class="input-group">
      <label for="outflow">Outflow (m³/s):</label>
      <input type="number" id="outflow" placeholder="Enter outflow data">
    </div>
    <div class="input-group">
      <label for="siltation">Siltation Rate (tonnes/year):</label>
      <input type="number" id="siltation" placeholder="Enter siltation rate">
    </div>
    <button onclick="generateDSSReport()">Generate Report</button>

    <div class="chart-container">
      <canvas id="reservoirChart"></canvas>
    </div>
  </div>
</div>

<div id="sediment" class="tab-content">
  <div class="container">
    <h2>Sediment Computation</h2>
    <div class="input-group">
      <label for="sediment-inflow">Sediment Inflow (tonnes):</label>
      <input type="number" id="sediment-inflow" placeholder="Enter sediment inflow">
    </div>
    <div class="input-group">
      <label for="trap-efficiency">Trap Efficiency (%):</label>
      <input type="number" id="trap-efficiency" placeholder="Enter trap efficiency">
    </div>
    <button onclick="computeSedimentation()">Compute Sedimentation</button>

    <div id="sediment-output" style="margin-top: 1rem;">
      <h3>Results:</h3>
      <p id="sedimentResult">Sediment computation results will appear here.</p>
    </div>
  </div>
</div>

<script>
  function openTab(tabName) {
    const tabs = document.querySelectorAll('.tab-content');
    const tabLinks = document.querySelectorAll('.tab-link');

    tabs.forEach(tab => tab.classList.remove('active'));
    tabLinks.forEach(link => link.classList.remove('active'));

    document.getElementById(tabName).classList.add('active');
    document.querySelector(`.tab-link[onclick="openTab('${tabName}')"]`).classList.add('active');
  }

  function generateDSSReport() {
    const inflow = parseFloat(document.getElementById("inflow").value) || 0;
    const outflow = parseFloat(document.getElementById("outflow").value) || 0;
    const siltation = parseFloat(document.getElementById("siltation").value) || 0;

    const remainingCapacity = inflow - outflow - (siltation / 1e6);

    const data = {
      labels: ["Inflow (m³/s)", "Outflow (m³/s)", "Siltation (MMT)", "Remaining Capacity"],
      datasets: [
        {
          label: "Reservoir Data",
          data: [inflow, outflow, siltation / 1e6, remainingCapacity],
          backgroundColor: ["#007bff", "#28a745", "#ffc107", "#dc3545"],
        },
      ],
    };

    const config = {
      type: "bar",
      data: data,
      options: {
        responsive: true,
        scales: {
          y: {
            beginAtZero: true,
          },
        },
      },
    };

    const ctx = document.getElementById("reservoirChart").getContext("2d");
    new Chart(ctx, config);
  }

  function computeSedimentation() {
    const sedimentInflow = parseFloat(document.getElementById("sediment-inflow").value) || 0;
    const trapEfficiency = parseFloat(document.getElementById("trap-efficiency").value) || 0;

    const sedimentDeposited = (sedimentInflow * trapEfficiency) / 100;
    const resultText = `Sediment Deposited: ${sedimentDeposited.toFixed(2)} tonnes`;

    document.getElementById("sedimentResult").innerText = resultText;
  }
</script>

</body>
</html>
