<script>
import { Bar } from "vue-chartjs";
const axios = require("axios");

export default {
  extends: Bar,
  data() {
    return {
      currentPercent: 0,
      balancePercent: 0,
      globalSumm: 20000000,

      chartdata: {
        labels: [this.currentPercent, this.balancePercent],
        datasets: [
          {
            label: this.globalSumm,
            backgroundColor: [
              "rgba(255, 99, 132, 0.7)",
              "rgba(75, 192, 192, 0.7)",
            ],
            borderColor: ["rgb(255, 99, 132)", "rgb(75, 192, 192)"],
            data: [1, 2],
          },
        ],
      },
      options: {
        responsive: true,
        legend: {
          labels: {
            // This more specific font property overrides the global property
            fontColor: "white",
            fontSize: 28,
          },
        },
        title: {
          display: false,
        },
        tooltips: false,
        hover: false,
        animation: {
          duration: 1,
          onComplete: function () {
            let chartInstance = this.chart;
            ctx = chartInstance.ctx;
            //Chart.defaults.global.defaultFontSize
            ctx.font = Chart.helpers.fontString(
              20,
              Chart.defaults.global.defaultFontStyle,
              Chart.defaults.global.defaultFontFamily
            );
            ctx.fillStyle = "white";
            ctx.textAlign = "left";
            ctx.textBaseline = "bottom";

            // this.data.datasets.forEach(function (dataset, i) {
            //   var meta = chartInstance.controller.getDatasetMeta(i);
            //   meta.data.forEach(function (bar, index) {
            //     var data = dataset.data[index];
            //     if (index === 0) {
            //       data = "Осталось " + data + "%";
            //     } else if (index === 1) {
            //       data = "Выполнено " + data + "%";
            //     }
            //     // console.log(data)
            //     ctx.fillText(data, bar._model.x - 75, bar._model.y - 5);
            //   });
            // });
          },
        },
        scales: {
          yAxes: [
            {
              ticks: {
                fontColor: "white",
                min: 0,
                max: 100, // Your absolute max value
                callback: function (value) {
                  return ((value / this.max) * 100).toFixed(0) + "%"; // convert it to percentage
                },
                beginAtZero: true,
              },
            },
          ],
          xAxes: [
            {
              ticks: {
                fontColor: "white",
                fontSize: 28,
              },
            },
          ],
        },
      },
    };
  },
  methods: {
    async fetchManagerInfo() {
      axios
        .get("http://display.prime-connect.ru/?type=megaplan")
        .then((response) => {
          this.manager_total = response.data.manager_total;
          this.currentBalance(response.data.manager_total);
        })
        .catch((error) => {
          if (error) throw new Error(error);
        });
    },
    currentBalance(data) {
      this.currentPercent = data.reduce((a, b) => a + b.total, 0).toFixed();
      this.balancePercent = this.globalSumm - this.currentPercent;
    },
  },
  created() {
    this.fetchManagerInfo();
  },
  async mounted() {
    await this.fetchManagerInfo();
    setTimeout(() => {
      this.renderChart(this.chartdata, this.options);
    }, 2000);
  },
};
</script>
