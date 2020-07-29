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
      this.renderChart(
        {
          labels: [this.currentPercent, this.balancePercent],
          // labels: ['Январь', 'Февраль', 'Март'],
          datasets: [
            {
              label: this.globalSumm,
              backgroundColor: [
                "rgba(255, 99, 132, 0.7)",
                "rgba(75, 192, 192, 0.7)",
              ],
              borderColor: ["rgb(255, 99, 132)", "rgb(75, 192, 192)"],
              data: [1, 2],
              // data: [40, 20, 12]
            },
          ],
        },
        {
          legend: {
            display: true,
            position: "top",
            labels: {
              boxWidth: 80,
              fontColor: "black",
            },
          },
          scales: {
            yAxes: [
              {
                barPercentage: 0.5,
                gridLines: {
                  display: false,
                },
              },
            ],
            xAxes: [
              {
                gridLines: {
                  zeroLineColor: "black",
                  zeroLineWidth: 2,
                },
                ticks: {
                  min: 0,
                  max: 6500,
                  stepSize: 1300,
                },
                scaleLabel: {
                  display: true,
                  labelString: "Density in kg/m3",
                },
              },
            ],
          },
        }
      );
    }, 2000);
  },
};
</script>
