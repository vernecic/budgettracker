<template>
  <div class="font-open text-grayish p-6 shadow-lg bg-white rounded-md">
    <div class="flex justify-between">
      <h1 class="text-4xl font-semibold">BudgetTracker</h1>
      <button
        class="bg-grayish text-white font-semibold py-1 px-2 rounded-md cursor-pointer shadow-md hover:shadow-lg hover:bg-zinc-800"
        @click="reset"
      >
        Reset
      </button>
    </div>

    <div v-if="isError">
      <h3 class="text-red-500 text-lg">Error: {{ errorMessage }}</h3>
    </div>
    <div
      class="flex flex-col space-y-4 xl:space-y-0 xl:grid xl:grid-cols-2 xl:gap-8"
    >
      <div class="mt-5 space-y-4">
        <form action="" class="flex flex-col" @submit.prevent="addExpense()">
          <label for="expense" class="text-xl font-semibold">Expense (€)</label>
          <input
            type="number"
            v-model="expense"
            placeholder="Enter your expenses"
            class="bg-zinc-100 focus:outline-none p-1 text-lg rounded-md placeholder:text-base placeholder:text-zinc-500"
          />
        </form>
        <div class="mt-2 relative">
          <h2 class="text-xl font-semibold">Expense type</h2>
          <div
            class="border border-slate-300 flex justify-between text-base p-1 rounded-md cursor-pointer"
            @click="showModal"
          >
            <span>{{ trenutnaKategorija }}</span>
            <img
              src="../images/arrowdown.svg"
              alt=""
              class="transition-transform duration-100"
              :class="{ 'rotate-180': isModal }"
            />
          </div>
          <div
            class="absolute z-50 bg-white flex flex-col text-base border border-slate-300 p-1 rounded-md mt-2"
            v-if="isModal"
          >
            <span
              v-for="(kategorija, index) in kategorije"
              :key="index"
              class="cursor-pointer hover:bg-slate-100 rounded px-1"
              @click="selectKategorija(kategorija)"
            >
              {{ kategorija }}
            </span>
          </div>
        </div>
        <div class="mt-2">
          <textarea
            name=""
            placeholder="Enter a description(optional)"
            id=""
            cols="30"
            rows="10"
            class="bg-zinc-100 focus:outline-none p-1 text-lg rounded-md resize-none h-16 placeholder:text-base placeholder:text-zinc-500"
            v-model="exDescription"
          ></textarea>
        </div>
        <div class="flex xl:justify-center items-center my-5">
          <button
            class="bg-grayish text-base font-semibold text-white rounded-md p-2 cursor-pointer shadow-md hover:shadow-lg hover:bg-zinc-800"
            @click="addExpense()"
          >
            Add expense
          </button>
        </div>
      </div>
      <div class="mt-5 flex flex-col justify-between">
        <form action="" class="flex flex-col" @submit.prevent="addIncome()">
          <label for="expense" class="text-xl font-semibold">Income (€)</label>
          <input
            type="number"
            v-model="income"
            placeholder="Enter your income"
            class="bg-zinc-100 focus:outline-none p-1 text-lg rounded-md placeholder:text-base placeholder:text-zinc-500"
          />
        </form>
        <div>
          <textarea
            name=""
            placeholder="Enter a description(optional)"
            id=""
            cols="30"
            rows="10"
            class="mt-5 xl:mt-0 bg-zinc-100 focus:outline-none p-1 text-lg rounded-md resize-none h-16 placeholder:text-base placeholder:text-zinc-500"
            v-model="inDescription"
          ></textarea>
          <div class="flex xl:justify-center items-center my-5">
            <button
              class="bg-white text-grayish border-grayish border font-semibold rounded-md p-2 cursor-pointer shadow-md hover:shadow-lg hover:bg-zinc-100"
              @click="addIncome()"
            >
              Add income
            </button>
          </div>
        </div>
      </div>
    </div>
    <div class="">
      <div
        class="flex flex-col space-y-4 xl:space-y-0 xl:grid xl:grid-cols-2 xl:gap-8"
      >
        <!-- expenses -->
        <div class="p-2 rounded-md bg-zinc-100">
          <h2 class="text-2xl font-semibold">📉 Expenses</h2>

          <div
            v-for="(expense, index) in expenses"
            :key="index"
            class="flex justify-between mt-2 items-center cursor-pointer"
            @click="showExpenseDesc(index)"
          >
            <div class="flex flex-col flex-1">
              <div class="flex justify-between">
                <span class="text-lg"
                  ><span class="text-red-500 font-semibold">-</span>
                  {{ expense.amount }}€</span
                >
                <div>
                  <div class="flex gap-2">
                    <span class="font-medium">{{ expense.type }}</span>

                    <img
                      src="../images/trash.svg"
                      alt=""
                      @click="removeExpense(index)"
                      class="cursor-pointer"
                    />
                  </div>
                </div>
              </div>

              <div v-if="expenseDescIndex === index">
                <span class="text-zinc-500 italic">{{ expense.desc }}</span>
              </div>
            </div>
          </div>
        </div>
        <div class="p-2 rounded-md bg-zinc-100">
          <h2 class="text-2xl font-semibold">📈 Incomes</h2>
          <div
            v-for="(income, index) in incomes"
            :key="index"
            class="flex mt-2 items-center cursor-pointer"
            @click="showIncomeDesc(index)"
          >
            <div class="flex flex-col flex-1">
              <div class="flex justify-between">
                <span class="text-lg"
                  ><span class="text-green-500 font-semibold">+</span>
                  {{ income.amount }}€</span
                >
                <img
                  src="../images/trash.svg"
                  alt=""
                  class="cursor-pointer"
                  @click="removeIncome(index)"
                />
              </div>
              <div v-if="incomeDescIndex === index">
                <span class="text-zinc-500 italic">{{ income.desc }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="grid grid-cols-2 gap-8">
        <div class="mt-2 p-2 pt-2 bg-white rounded-md">
          <h1>
            <span class="font-semibold">Total expense: </span>
            {{ fullexpenses }} €
          </h1>
        </div>
        <div class="mt-2 p-2 pt-2 bg-white rounded-md">
          <h1>
            <span class="font-semibold">Total income:</span>
            {{ fullincomes }} €
          </h1>
        </div>
      </div>
    </div>
    <div class="mt-8">
      <h2 class="text-2xl font-semibold mb-4">📊 Expense Chart</h2>
      <div class="bg-white p-4 rounded-md shadow-sm">
        <Pie :data="chartData" :options="chartOptions" class="cursor-pointer" />
      </div>
    </div>

    <div>
      <h2 class="mt-5 text-xl">
        💰 Current budget:
        <span
          :class="{
            'text-red-500': currentBudget < 0,
            'font-bold': currentBudget < 0,
          }"
          >{{ currentBudget }}€</span
        >
      </h2>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";

// pie chart
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from "chart.js";
import { Pie } from "vue-chartjs";

ChartJS.register(ArcElement, Tooltip, Legend);

const chartData = computed(() => {
  const categorySum = {};

  expenses.value.forEach((expense) => {
    const category = expense.type;
    categorySum[category] = (categorySum[category] || 0) + expense.amount;
  });

  const labels = Object.keys(categorySum);
  const data = Object.values(categorySum);

  return {
    labels: labels,
    datasets: [
      {
        data: data,
        backgroundColor: [
          "#fcba03",
          "#fc8003",
          "#fc4a03",
          "#0fbd32",
          "#19d496",
          "#0dccde",
          "#1fb8f0",
          "#1f84f0",
          "#0e47e3",
          "#7852e3",
        ],
        borderWidth: 0,
        borderColor: "transparent",
      },
    ],
  };
});

//chartOptions

// refs
const income = ref("");
const expense = ref("");
const isModal = ref(false);
const trenutnaKategorija = ref("Namirnice");
const expenses = ref([]);
const incomes = ref([]);
const currentBudget = computed(() => {
  return fullincomes.value - fullexpenses.value;
});
const isError = ref(false);
const errorMessage = ref("");
const exDescription = ref("");
const inDescription = ref("");
const incomeDescIndex = ref(null);
const expenseDescIndex = ref(null);

// total expense/income
const fullexpenses = computed(() => {
  return expenses.value.reduce((acc, curr) => acc + curr.amount, 0);
});
const fullincomes = computed(() => {
  return incomes.value.reduce((acc, curr) => acc + curr.amount, 0);
});

// kategorije
const kategorije = [
  "Namirnice",
  "Šišanje",
  "Pretplate",
  "Mobitel",
  "Auto",
  "Fast food & sl.",
  "Investicije",
  "Štednja",
  "Stan + režije",
  "Ostalo",
];

// FUNKCIJE
function selectKategorija(kategorija) {
  trenutnaKategorija.value = kategorija;
  isModal.value = false;
}

function showModal() {
  isModal.value = !isModal.value;
}
function addExpense() {
  isError.value = false;
  errorMessage.value = "";
  if (expense.value !== "" && expense.value > 0) {
    expenses.value.push({
      amount: parseFloat(expense.value),
      type: trenutnaKategorija.value,
      desc: exDescription.value,
    });

    expense.value = "";
    exDescription.value = "";
    saveData();
  } else if (expense.value <= 0) {
    isError.value = true;
    errorMessage.value = `Expense has to be greater than 0`;
    expense.value = "";
  }
}
function addIncome() {
  isError.value = false;
  errorMessage.value = "";
  if (income.value !== "" && income.value > 0) {
    incomes.value.push({
      amount: parseFloat(income.value),
      desc: inDescription.value,
    });

    income.value = "";
    inDescription.value = "";
    saveData();
  } else if (income.value <= 0) {
    isError.value = true;
    errorMessage.value = `Income has to be greater than 0`;
    income.value = "";
  }
}

function removeIncome(index) {
  incomes.value.splice(index, 1);
  saveData();
}

function removeExpense(index) {
  expenses.value.splice(index, 1);
  saveData();
}

function reset() {
  const expensesLength = expenses.value.length;
  expenses.value.splice(0, expensesLength);
  const incomesLength = incomes.value.length;
  incomes.value.splice(0, incomesLength);

  trenutnaKategorija.value = "Namirnice";
  saveData();
}

function showExpenseDesc(index) {
  expenseDescIndex.value = expenseDescIndex.value === index ? null : index;
}
function showIncomeDesc(index) {
  incomeDescIndex.value = incomeDescIndex.value === index ? null : index;
}

function saveData() {
  localStorage.setItem("expenses", JSON.stringify(expenses.value));
  localStorage.setItem("incomes", JSON.stringify(incomes.value));
}

// onMounted
onMounted(() => {
  const savedExpenses = localStorage.getItem("expenses");
  const savedIncomes = localStorage.getItem("incomes");

  if (savedExpenses) {
    expenses.value = JSON.parse(savedExpenses);
  }
  if (savedIncomes) {
    incomes.value = JSON.parse(savedIncomes);
  }
});
</script>

<style scoped>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
</style>
