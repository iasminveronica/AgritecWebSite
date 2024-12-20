<script>
import * as echarts from "echarts";

export default {
  name: "EChart",
  data() {
    return {
      chart: null,
    };
  },
  methods: {
    async fetchData() {
      try {
        const response = await fetch(
          "https://script.google.com/macros/s/AKfycbwWU67248H0oOCBwgiMRPp6rzd7JX8QqlxigftEfcOC9KIol5eNl9Opa0aI65E7pBOAwg/exec?data_inicial=23/08/2024&data_final=28/08/2024"
        );
        const data = await response.json();
        const entries = data.retornodaSaida;

        // Extraindo as horas e as temperaturas desejadas
        const horas = entries.map((entry) => entry.Hora);
        const temperatura1 = entries.map((entry) => parseFloat(entry.Temperatura1));
        const temperatura2 = entries.map((entry) => parseFloat(entry.Temperatura2));
        const temperatura3 = entries.map((entry) => parseFloat(entry.Temperatura3));
        const temperatura4 = entries.map((entry) => parseFloat(entry.Temperatura4));

        // Calcular os valores mínimo e máximo das temperaturas
        const todasTempraturas = [...temperatura1, ...temperatura2, ...temperatura3, ...temperatura4];
        const minTemp = Math.min(...todasTempraturas);
        const maxTemp = Math.max(...todasTempraturas);

        console.log("Mínima temperatura:", minTemp);
        console.log("Máxima temperatura:", maxTemp);

        // Renderizando o gráfico com os dados e intervalo calculado
        this.renderChart(horas, { temperature1: temperatura1, temperature2: temperatura2, temperature3: temperatura3, temperature4: temperatura4 }, minTemp, maxTemp);
      } catch (error) {
        console.error("Erro ao buscar dados:", error);
      }
    },
    renderChart(horas, temperaturas, minTemp, maxTemp) {
      const option = {
        title: {
          text: "Temperaturas ",
        },
        tooltip: {
          trigger: "axis",
        },
        legend: {
          data: ["Temperatura 1", "Temperatura 2", "Temperatura 3", "Temperatura 4"],
        },
        xAxis: {
          type: "category",
          data: horas,
          name: "Hora",
        },
        yAxis: {
          type: "value",
          name: "Temperatura (°C)",
          min: minTemp - 1, // Ajuste para garantir espaçamento
          max: maxTemp + 1, // Ajuste para garantir espaçamento
        },
        series: [
          {
            name: "Temperatura 1",
            type: "line",
            data: temperaturas.temperature1,
          },
          {
            name: "Temperatura 2",
            type: "line",
            data: temperaturas.temperature2,
          },
          {
            name: "Temperatura 3",
            type: "line",
            data: temperaturas.temperature3,
          },
          {
            name: "Temperatura 4",
            type: "line",
            data: temperaturas.temperature4,
          },
        ],
      };
      this.chart.setOption(option);
    },
  },
  mounted() {
    this.chart = echarts.init(this.$refs.chartRef);
    this.fetchData();
    window.addEventListener("resize", () => {
      if (this.chart) {
        this.chart.resize();
      }
    });
  },
};
</script>

<template>
  <div id="app">
    <div class="flex items-center justify-between w-full h-screen bg-blue-200">
      <!-- Barra Lateral ainda em desenvolvimento-->
      <div class="flex flex-col items-start justify-center w-30 bg-red-300 h-full">
        <div class="flex-1 items-start bg-red-700">
          <p>BARRA LATERAL</p>
        </div>
        <div class="items-start w-30 bg-blue-700">
          <div>
            <p>
              Configurações
              <svg
                xmlns="http://www.w3.org/2000/svg"
                x="0px"
                y="0px"
                width="20"
                height="10"
                viewBox="0 0 24 24"
              >
                <path d="..."></path>
              </svg>
            </p>
          </div>
          <router-link to="/">Sair</router-link>
        </div>
      </div>
      <!-- Tela do Dashboard -->
      <div class="flex items-center justify-center flex-1 bg-white h-full">
      <div>
      <div ref="chartRef" style="width: 600px; height: 400px; border: 1px solid red;">
      </div>
  </div>
      </div>
    </div>
  </div>
</template>

