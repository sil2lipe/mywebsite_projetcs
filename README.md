<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dados Climáticos para Indaiatuba</title>
  <style>
    /* Estilo para o corpo e fonte */
    body {
      font-family: 'Roboto', Arial, sans-serif;
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;
      min-height: 100vh;
      background-image: url('/mnt/data/image_TW-CV4--_1718834236416_raw.jpg');
      background-size: cover;
      background-position: center;
      background-repeat: no-repeat;
      color: #333;
    }

    /* Estilo para o cabeçalho */
    header {
      background-color: rgba(206, 194, 221, 0.9);
      color: #333;
      padding: 1em;
      text-align: center;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    /* Estilo para a barra de navegação */
    nav {
      background-color: rgba(211, 224, 221, 0.9);
      padding: 10px 0;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }

    nav ul {
      list-style-type: none;
      padding: 0;
      text-align: center;
    }

    nav ul li {
      display: inline-block;
      margin: 0 15px;
    }

    nav ul li a {
      color: #333;
      font-size: 16px;
      text-decoration: none;
      padding: 10px 15px;
      border-radius: 5px;
      transition: background-color 0.3s;
    }

    nav ul li a:hover {
      background-color: rgba(207, 46, 46, 0.1);
    }

    /* Estilo para o conteúdo principal */
    main {
      flex: 1;
      padding: 20px;
      background-color: rgba(46, 116, 77, 0.8);
      margin: 20px;
      border-radius: 8px;
      box-shadow: 0 4px 8px rgba(37, 23, 23, 0.1);
    }

    /* Estilo para seções */
    section {
      margin-bottom: 20px;
    }

    /* Estilo para o rodapé */
    footer {
      background-color: rgba(43, 41, 41, 0.8);
      color: rgb(15, 230, 194)b5c;
      text-align: center;
      padding: 1em;
      width: 100%;
      bottom: 0;
    }

    /* Estilo para botões */
    button {
      background-color: #616848;
      color: white;
      border: none;
      padding: 10px 20px;
      text-align: center;
      text-decoration: none;
      display: inline-block;
      font-size: 16px;
      margin: 10px 0;
      cursor: pointer;
      border-radius: 5px;
      transition: background-color 0.3s;
    }

    button:hover {
      background-color: #45a049;
    }

    /* Estilo para cards de dados climáticos */
    .card {
      background-color: rgba(199, 128, 128, 0.9);
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      padding: 15px;
      border-radius: 8px;
      margin-bottom: 20px;
    }

    .card h3 {
      color: #555;
      font-size: 18px;
      margin-bottom: 10px;
    }

    .card p {
      color: #777;
      font-size: 16px;
    }
  </style>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body>
  <header>
    <h1>Dados Climáticos para Indaiatuba</h1>
    <nav>
      <ul>
        <li><a href="#Início">Início</a></li>
        <li><a href="#data">Dados Climáticos</a></li>
        <li><a href="#analysis">Análise de Tendência</a></li>
      </ul>
    </nav>
  </header>
  <main>
    <section id="Início">
      <h2>Início</h2>
      <p>Bem-vindo à aplicação de análise de dados climáticos para a cidade de Indaiatuba.</p>
    </section>
    <section id="data">
      <h2>Dados Climáticos</h2>
      <div class="form-group">
        <label for="start-date">Data de Início:</label>
        <input type="date" id="start-date">
      </div>
      <div class="form-group">
        <label for="end-date">Data Final:</label>
        <input type="date" id="end-date">
      </div>
      <div class="form-group">
        <label for="data-type">Tipo de Dados:</label>
        <select id="data-type">
          <option value="temperature">Temperatura</option>
          <option value="humidity">Umidade</option>
          <option value="precipitation">Precipitação</option>
        </select>
      </div>
      <button id="fetch-data">Buscar Dados</button>
      <button id="export-btn">Exportar Dados</button>
      <div id="climate-data" class="card">
        <h3>Dados Detalhados</h3>
        <div id="detailed-data"></div>
      </div>
    </section>
    <section id="analysis">
      <h2>Análise de Tendência</h2>
      <canvas id="temperature-chart" class="card"></canvas>
      <canvas id="precipitation-chart" class="card"></canvas>
    </section>
  </main>
  <footer>
    <p>&copy; 2024 Climate Data Application</p>
  </footer>
  <script>
    document.addEventListener("DOMContentLoaded", function() {
      const fetchDataButton = document.getElementById('fetch-data');
      const exportButton = document.getElementById('export-btn');
      fetchDataButton.addEventListener('click', fetchClimateData);
      exportButton.addEventListener('click', exportClimateData);
    });

    async function fetchClimateData() {
      const startDate = document.getElementById('start-date').value;
      const endDate = document.getElementById('end-date').value;
      const dataType = document.getElementById('data-type').value;

      if (!startDate || !endDate) {
        alert('Por favor, selecione as datas de início e fim.');
        return;
      }

      try {
        const response = await fetch(`https://api.weatherapi.com/v1/history.json?key=9ec96893a526ffb761a4f1456e22915d&q=Indaiatuba&dt=${startDate}&end_dt=${endDate}`);
        if (!response.ok) {
          throw new Error(`Erro ao buscar dados: ${response.statusText}`);
        }

        const data = await response.json();
        if (!data.forecast) {
          throw new Error('Dados de previsão não encontrados.');
        }

        displayClimateData(data, dataType);
      } catch (error) {
        alert(`Erro ao buscar dados climáticos: ${error.message}`);
      }
    }

    function displayClimateData(data, dataType) {
      const detailedDataDiv = document.getElementById('detailed-data');
      detailedDataDiv.innerHTML = '';

      const dates = data.forecast.forecastday.map(day => day.date);
      let dataValues;

      if (dataType === 'temperature') {
        const temps = data.forecast.forecastday.map(day => ({
          avg: day.day.avgtemp_c,
          max: day.day.maxtemp_c,
          min: day.day.mintemp_c
        }));
        dataValues = {
          avg: temps.map(t => t.avg),
          max: temps.map(t => t.max),
          min: temps.map(t => t.min)
        };
        detailedDataDiv.innerHTML = `<p>Temperatura Média: ${dataValues.avg.join(', ')}</p>
                                     <p>Temperatura Máxima: ${dataValues.max.join(', ')}</p>
                                     <p>Temperatura Mínima: ${dataValues.min.join(', ')}</p>`;
      } else if (dataType === 'humidity') {
        dataValues = data.forecast.forecastday.map(day => day.day.avghumidity);
        detailedDataDiv.innerHTML = `<p>Umidade Média: ${dataValues.join(', ')}</p>`;
      } else if (dataType === 'precipitation') {
        dataValues = data.forecast.forecastday.map(day => day.day.totalprecip_mm);
        detailedDataDiv.innerHTML = `<p>Precipitação: ${dataValues.join(', ')} mm</p>`;
      }

      updateChart(dates, dataValues, dataType);
    }

    function updateChart(labels, data, dataType) {
      const chartCanvas = dataType === 'temperature' ? 'temperature-chart' : 'precipitation-chart';
      const ctx = document.getElementById(chartCanvas).getContext('2d');
      const chartType = dataType === 'temperature' ? 'line' : 'bar';

      new Chart(ctx, {
        type: chartType,
        data: {
          labels: labels,
          datasets: [{
            label: `Dados de ${dataType}`,
            data: data.avg || data,
            borderColor: 'rgba(75, 192, 192, 1)',
            backgroundColor: 'rgba(75, 192, 192, 0.2)',
            borderWidth: 1
          }]
        },
        options: {
          scales: {
            y: {
              beginAtZero: true
            }
          }
        }
      });
    }

    function exportClimateData() {
      const data = document.getElementById('detailed-data').innerText;
      const blob = new Blob([data], { type: 'text/csv' });
      const url = window.URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.setAttribute('href', url);
      a.setAttribute('download', 'climate_data.csv');
      a.click();
    }
  </script>
</body>
</html>
