<template>
  <div>
    <v-app>
      <!--Balance-->
      <div class="all-balance">
        <h1 class="balance">Balance:</h1>
        <h1>$0</h1>
      </div>

      <div class="btn_inc_and_exp">
        <!-- income button -->
        <v-dialog max-width="500">
          <template v-slot:activator="{ props: activatorProps }">
            <v-btn
              color="success"
              size="x-large"
              v-bind="activatorProps"
              text="Income"
              variant="flat"
            ></v-btn>
          </template>

          <template v-slot:default="{ isActive }">
            <v-card title="Add income" >
              <v-form @submit.prevent ref="form">
                <div class="px-6">
                  <v-text-field
                    v-model="sum"
                    :rules="rules"
                    label="Sum"
                    required
                  ></v-text-field>
                  <v-text-field
                    v-model="date"
                    :rules="rules"
                    label="Date"
                    required
                    ></v-text-field>
                  <v-text-field
                    v-model="type"
                    :rules="rules"
                    label="Type"
                    required
                  ></v-text-field>
                </div>

                <v-card-actions>
                  <v-spacer></v-spacer>

                  <v-btn
                    text="Add"
                    @click="addIncome(sum, type, date, isActive);"
                  ></v-btn>
                  <v-btn
                    text="Cancel"
                    @click="resetValues(isActive)"
                  ></v-btn>

                </v-card-actions>
              </v-form>
            </v-card>
          </template>
        </v-dialog>


        <!-- Expense button-->
        <v-dialog max-width="500">
          <template v-slot:activator="{ props: activatorProps }">
            <v-btn
              color="error"
              size="x-large"
              v-bind="activatorProps"
              text="Expense"
              variant="flat"
            ></v-btn>
          </template>

          <template v-slot:default="{ isActive }">
            <v-card title="Add Expense">
              <v-form @submit.prevent ref="form2">
                <div class="px-6">
                  <v-text-field
                    v-model="sum"
                    :rules="rules"
                    label="Sum"
                    required
                  />
                  <!-- <v-date-input
                    v-model="date"
                    :rules="dateRules"
                    label="Date input"
                    required
                  /> -->
                  <v-menu v-model="dialogOpen" :close-on-content-click="false">
                    <template v-slot:activator="{ props }">
                      <div class="date-input">
                        <v-btn class="mt-1" icon="mdi-calendar" flat v-bind="props"></v-btn>
                        <v-text-field
                          v-model="date"
                          :rules="dateRules"
                          label="Date"
                          required
                          disabled
                        />
                      </div>
                    </template>
                    <v-date-picker required v-model="date"></v-date-picker>
                  </v-menu>
                  <v-text-field
                    v-model="type"
                    :rules="rules"
                    label="Type"
                    required
                  />
                </div>

                <v-card-actions> 
                  <v-spacer></v-spacer>

                  <v-btn
                    text="Add"
                    @click="addExpense(sum, type, date, isActive)"
                  ></v-btn>
                  <v-btn
                    text="Cancel"
                    @click="resetValues(isActive)"
                  ></v-btn>

                </v-card-actions>
              </v-form>
            </v-card>
          </template>
        </v-dialog>
      </div>
    
      <div class="history-container">
        <div class="history">
          <h2>
            History:
          </h2>
          <div class="history-all">
            <div class="history-item" v-for="n in list" :class="n.income ? 'income' : 'expense'" :key="n">
              <div class="sum">{{ n.sum }}</div>
              <div class="type">{{ n.type }}</div>
              <div class="date">{{ new Date(n.date).getDate() + '/' + (new Date(n.date).getMonth()+1) + '/' + new Date(n.date).getFullYear() }}</div>
            </div>
          </div>
        </div>
      </div>
    </v-app>
  </div>
</template>



<style>
.all-balance {
  margin: 40px 0 20px 0;
  display: flex;
  gap: 10px;
  justify-content: center;
  align-content: center;
}

.all-balance h1 {
  font-size: 80px;
}

.btn_inc_and_exp {
  width: 100%;
  margin-top: 10px;
  display: flex;
  justify-content: center;
  gap: 100px;
}

.date-input {
  display: flex;
}

.history-container {
  margin: 40px 0;
  display: flex;
  justify-content: center;
}

.history {
  padding: 10px 20px;
  width: 700px;
  background-color: lightgrey;
  height: 600px;
}

.history-all {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.history-item {
  display: flex;
  justify-content: space-between;
  
}

.income {
  color: green;
}
.expense {
  color: red;
}

</style> 

<script setup>
const list = ref([
  {
    sum: 1000,
    type: "salary",
    date: 1722798000000,
    income: true
  },
  {
    sum: 2000,
    type: "levak",
    date: 1722798000000,
    income: true
  },
  {
    sum: 5000,
    type: "salary",
    date: 1722798000000,
    income: true
  },
  {
    sum: 1000,
    type: "lunch",
    date: 1722798000000,
    income: false
  },
  {
    sum: 4000,
    type: "oil",
    date: 1722798000000,
    income: false
  },
  {
    sum: 500,
    type: "shopping",
    date: 1722798000000,
    income: false
  }
])

const form = ref()
const form2 = ref()

async function addIncome(sum, type, date, isActive){
    const valid = await form.value.validate()
    if (!valid.valid) {
      return
    }

    let newIncome = {
      sum: sum,
      type: type,
      date: date,
      income: true
    }

    list.value.push(newIncome)
    resetValues()
    isActive.value = false
}

async function addExpense(sum, type, date, isActive){
    const valid = await form2.value.validate()
    if (!valid.valid) {
      return
    }

    let date_ts = new Date(date).getTime()

    let newExpense = {
      sum: sum,
      type: type,
      date: date_ts,
      income: false
    }

    list.value.push(newExpense)
    resetValues(isActive)
}

const dialogOpen = ref(false)
const sum = ref(null)
const type = ref(null)
const date = ref(null)

function resetValues(isActive){
  sum.value = null
  date.value = null
  type.value = null
  isActive.value = false
}

const rules = ref([
  v => !!v || 'Required',
])

const dateRules = ref([
  v => !!v || 'Date is required',
])

watch(date, async(newDate, oldDate) => {
  dialogOpen.value = false
})
</script>