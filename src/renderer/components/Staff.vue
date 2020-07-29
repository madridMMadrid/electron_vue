<template>
  <div>
    <!-- <div>
      <div>
        <button @click="fetchTodos()" class="btn btn-primary">Fetch Todos</button>
      </div>
    </div>
    <div>
      <div>
        <ul>
          <li v-for="(value, i) in todos" :key="i">{{value.name}}</li>
        </ul>
      </div>
    </div>-->
    <div class="container-fluid bg-dark">
      <div class="row pt-3">
        <div class="col-sm-4">
          <table class="table table-bordered table-dark table-striped table-hover table-sm">
            <thead>
              <tr>
                <th scope="col">#</th>
                <th scope="col" colspan="2">Выполнение плана за Июль</th>
              </tr>
            </thead>
            <tbody id="table_A">
              <tr
                class="text-dark"
                :class="{ 'table-success': manager_total_norma < value.total.toFixed(), 'table-danger': manager_total_norma > value.total.toFixed()}"
                v-for="(value, i) in manager_total"
                :key="i"
              >
                <th scope="row">{{ i + 1 }}</th>
                <td>{{ value.name }}</td>
                <td>{{ value.total.toFixed() }} ₽</td>
              </tr>
            </tbody>
          </table>
          <table class="table table-bordered table-dark table-striped table-hover table-sm">
            <thead>
              <tr>
                <th scope="col">#</th>
                <th scope="col">Необработанные заявки</th>
                <th scope="col">Кол-во</th>
              </tr>
            </thead>
            <tbody id="table_B">
              <tr
                style="color: #fff !important"
                class="text-dark"
                v-for="(value, i) in manager_total"
                :key="i"
              >
                <th scope="row">{{ i + 1 }}</th>
                <td>{{ value.name }}</td>
                <td>{{ value.not_processed }}</td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="col-sm-4">
          <div class="d-flex flex-column" style="height: 100%;" id="js_column_2">
            <table class="table table-bordered table-dark table-striped table-hover table-sm">
              <thead>
                <tr>
                  <th scope="col">#</th>
                  <th scope="col">Сумма оплат в день</th>
                  <th scope="col">29.07.2020</th>
                </tr>
              </thead>
              <tbody id="table_D">
                <tr
                  class="text-dark"
                  v-for="(value, i) in manager_day_total"
                  :class="{ 'table-success': manager_day_total_norma < value.current_day_total, 'table-danger': manager_day_total_norma > value.current_day_total}"
                  :key="i"
                >
                  <th scope="row">{{ i + 1 }}</th>
                  <td>{{ value.name }}</td>
                  <td>{{ value.current_day_total }} ₽</td>
                </tr>
              </tbody>
            </table>
            <div>
              <div>
                <VueCharts/>
              </div>
              <!-- <canvas id="chart_month" width="100" height="70"></canvas> -->
            </div>
            <div>
              <span id="change_time">{{ change_file }}</span>
            </div>
          </div>
        </div>
        <div class="col-sm-4">
          <table class="table table-bordered table-dark table-striped table-hover table-sm">
            <thead>
              <tr>
                <th scope="col">#</th>
                <th scope="col">Исходящие звонки в день</th>
                <th scope="col">Кол-во</th>
              </tr>
            </thead>
            <tbody id="table_C">
              <tr
                class="table-success text-dark"
                v-for="(value, i) in manager_call"
                :class="{ 'table-success': manager_call_normal < value.count_calls, 'table-danger': manager_call_normal > value.count_calls}"
                :key="i"
              >
                <th scope="row">{{ i + 1 }}</th>
                <td>{{ value.name }}</td>
                <td>{{ value.count_calls }}</td>
              </tr>
            </tbody>
          </table>
          <table class="table table-bordered table-dark table-striped table-hover table-sm">
            <thead>
              <tr>
                <th scope="col">#</th>
                <th scope="col" colspan="2">KPD сотрудников</th>
              </tr>
            </thead>
            <tbody id="table_E">
              <tr
                style="color: #fff !important"
                class="text-dark"
                v-for="(value, i) in deals_to_manager"
                :key="i"
              >
                <th scope="row">{{ i + 1 }}</th>
                <td>{{ value.name }}</td>
                <td>{{ value.total }} %</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
      <div class="d-flex justify-content-center" id="js_error"></div>
    </div>
  </div>
</template>
<script>
import VueCharts from "./VueCharts";
const axios = require("axios");
export default {
  name: "Staff",
  components: {
    VueCharts,
  },
  data: () => {
    return {
      manager_total_norma: 2500000,
      manager_day_total_norma: 80000,
      manager_call_normal: 20,
      change_file: "",
      deals_to_manager: [],
      manager_day_total: [],
      manager_total: [],
      manager_call: [],
    };
  },
  methods: {
    async fetchManagerInfo() {
      axios
        .get("http://display.prime-connect.ru/?type=megaplan")
        .then((response) => {
          this.change_file = response.data.change_file;
          this.deals_to_manager = response.data.deals_to_manager;
          this.manager_day_total = response.data.manager_day_total;
          this.manager_total = response.data.manager_total;
        })
        .catch((error) => {
          if (error) throw new Error(error);
        });
    },
    async fetchManagerCall() {
      axios
        .get("http://display.prime-connect.ru/?type=mangooffice")
        .then((response) => {
          this.manager_call = response.data.users;
        })
        .catch((error) => {
          if (error) throw new Error(error);
        });
    },
  },
  computed: {},
  async mounted() {
    this.fetchManagerInfo();
    this.fetchManagerCall();
  },
};
</script>
<style lang="scss">
li {
  color: #fff;
}
:-webkit-full-screen {
  background: #343a40 !important;
}

:-moz-full-screen {
  background: #343a40 !important;
}

body {
  // font-size: 178% !important;
  overflow: hidden;
}

.container-fluid {
  height: 100vh;
}

#js_fullscreen {
  position: fixed;
  bottom: 1rem;
  left: 50%;
  transform: translateX(-50%);
  z-index: 10;
}

.manager_1000043 {
  display: none;
}

span#change_time {
  color: #ffffff6b;
  font-size: 1rem;
  display: block;
  width: 100%;
  text-align: center;
  padding: 1rem 0;
  border-top: 0.1rem solid #607d8bab;
  margin-top: 1rem;
}
</style>