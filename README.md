<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Dynamic Arbitrage Stake Calculator</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 40px;
      background-color: #f4f4f4;
    }
    .container {
      max-width: 800px;
      background: #fff;
      padding: 20px;
      margin: 0 auto;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }
    h1, h2 {
      text-align: center;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 20px;
    }
    th, td {
      border: 1px solid #ddd;
      padding: 8px;
      text-align: center;
    }
    th {
      background-color: #f2f2f2;
    }
    .form-group {
      margin-bottom: 15px;
      text-align: center;
    }
    .form-group label {
      margin-right: 10px;
    }
    input[type="number"] {
      width: 100px;
      padding: 5px;
    }
    button {
      padding: 10px 20px;
      font-size: 16px;
      cursor: pointer;
    }
    .results p {
      font-size: 18px;
      text-align: center;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Dynamic Arbitrage Stake Calculator</h1>
    <form id="arbitrageForm">
      <table id="outcomesTable">
        <thead>
          <tr>
            <th>Outcome</th>
            <th>Odds</th>
            <th>Implied Probability</th>
            <th>Stake</th>
          </tr>
        </thead>
        <tbody>
          <!-- Outcome 1 (Row 2) -->
          <tr>
            <td>Outcome 1</td>
            <td>
              <input type="number" step="0.01" value="3.00" required>
            </td>
            <td>
              <span class="probability">0.0000</span>
            </td>
            <td>
              <span class="stake">0.00</span>
            </td>
          </tr>
          <!-- Outcome 2 (Row 3) -->
          <tr>
            <td>Outcome 2</td>
            <td>
              <input type="number" step="0.01" value="4.80" required>
            </td>
            <td>
              <span class="probability">0.0000</span>
            </td>
            <td>
              <span class="stake">0.00</span>
            </td>
          </tr>
          <!-- Outcome 3 (Row 4) -->
          <tr>
            <td>Outcome 3</td>
            <td>
              <input type="number" step="0.01" value="4.60" required>
            </td>
            <td>
              <span class="probability">0.0000</span>
            </td>
            <td>
              <span class="stake">0.00</span>
            </td>
          </tr>
        </tbody>
      </table>

      <div class="form-group">
        <label for="totalFund">Total Fund:</label>
        <input type="number" step="0.01" id="totalFund" value="100" required>
      </div>

      <div class="form-group">
        <button type="button" onclick="calculateArbitrage()">Calculate</button>
      </div>
    </form>

    <div class="results">
      <h2>Results</h2>
      <p id="targetReturn"></p>
      <p id="totalStake"></p>
      <p id="profit"></p>
    </div>
  </div>

  <script>
    function calculateArbitrage() {
      // Get total fund value
      var totalFund = parseFloat(document.getElementById('totalFund').value);
      if (isNaN(totalFund) || totalFund <= 0) {
        alert('Please enter a valid total fund.');
        return;
      }
      
      // Get all outcome rows
      var rows = document.querySelectorAll('#outcomesTable tbody tr');
      var outcomes = [];
      var sumProb = 0;
      
      // Loop through each row, calculating implied probability per outcome
      rows.forEach(function(row, index) {
        var oddsInput = row.querySelector('input[type="number"]');
        var odds = parseFloat(oddsInput.value);
        if (isNaN(odds) || odds <= 0) {
          alert('Please enter valid odds for ' + row.cells[0].textContent + '.');
          return;
        }
        var probability = 1 / odds;
        sumProb += probability;
        outcomes.push({
          row: row,
          odds: odds,
          probability: probability
        });
      });

      // Calculate Target Return: R = Total Fund / Sum of Implied Probabilities (S)
      var targetReturn = totalFund / sumProb;

      // Calculate each outcome's stake and update the display
      outcomes.forEach(function(item) {
        var stake = targetReturn / item.odds;
        item.row.querySelector('.probability').textContent = item.probability.toFixed(4);
        item.row.querySelector('.stake').textContent = stake.toFixed(2);
      });

      // Display overall results
      document.getElementById('targetReturn').textContent = 'Target Return (R): ' + targetReturn.toFixed(2);
      var totalStakes = outcomes.reduce(function(sum, item) {
        return sum + (targetReturn / item.odds);
      }, 0);
      document.getElementById('totalStake').textContent = 'Total Stakes: ' + totalStakes.toFixed(2);
      var profit = targetReturn - totalFund;
      document.getElementById('profit').textContent = 'Guaranteed Profit: ' + profit.toFixed(2);
    }
  </script>
</body>
</html>
