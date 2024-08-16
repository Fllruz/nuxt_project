<template>
  <div>
    <v-app>
      <!--Balance-->
      <div class="all-balance">
        <h1 class="balance">Balance:</h1>
        <h1>${{ balance }}</h1>
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
            <v-card title="Add Income">
              <v-form @submit.prevent ref="form">
                <div class="px-6">
                  <v-text-field
                    v-model="sum"
                    :rules="rules"
                    label="Sum"
                    required
                  />
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
                    @click="addIncome(sum, type, date, isActive)"
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
          <div class="history-all" v-if="list.length > 0">
            <div class="history-item" v-for="n in list" :class="n.income ? 'income' : 'expense'" :key="n">
              <div class="sum">{{ n.sum }}</div>
              <div class="type">{{ n.type }}</div>
              <div class="d-flex align-center ga-2">
                <div class="date">{{ new Date(n.date).getDate() + '/' + (new Date(n.date).getMonth()+1) + '/' + new Date(n.date).getFullYear() }}</div>
                <v-btn icon="mdi-delete" variant="text" color="black" size="regular" class="mb-1" @click="btn_delete(n)">
                </v-btn>
              </div>
              
            </div>
          </div>
          <div class="mt-2" v-else>Nothing here yet...</div>
        </div>
      </div>

      <div class="btn-reset">
        <v-btn color="#d3d3d3" @click="reset"> 
          Reset
        </v-btn>
      </div>
      
      <div class="btn-lang">
        <v-select :items="items" v-model="item" density="compact" variant="outlined">
          <template v-slot:item="{ props, item }">
            <v-list-item v-bind="props">
              <template v-slot:prepend>
                <div>
                  <img v-if="item.value == 'En'" style="width: 25px; height: 15px; margin-right: 10px;" src="https://upload.wikimedia.org/wikipedia/commons/a/a9/Flag_of_the_United_States_%28DoS_ECA_Color_Standard%29.svg" alt="">
                  <img v-if="item.value == 'Ru'" style="width: 25px; height: 15px; margin-right: 10px;" src="https://upload.wikimedia.org/wikipedia/en/thumb/f/f3/Flag_of_Russia.svg/640px-Flag_of_Russia.svg.png" alt="">
                </div>
              </template>
            </v-list-item>
          </template>
        </v-select>
      </div>
    </v-app>
  </div>
  <!--  -->
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

.btn-reset {
  position: absolute;
  right: 20px;
  bottom: 20px;
}

.btn-lang {
  position: absolute;
  right: 20px;
  top: 20px;
}

</style> 

<script setup>

let taskId;
function createtaskId() {
  taskId++
  localStorage.setItem('id', JSON.stringify(taskId))
  return taskId
}

const list = ref([])

onMounted(() => {

  taskId = JSON.parse(localStorage.getItem('id'))

  if(localStorage.getItem('list')) {
    list.value = JSON.parse(localStorage.getItem('list'))
  }

  for (let i=0; i < list.value.length; i++) {
    if(list.value[i].income) {
      balance.value += Number.parseInt(list.value[i].sum) 
    } else {
      balance.value -= Number.parseInt(list.value[i].sum) 
    }
    
  }

})

const form = ref()
const form2 = ref()

async function addIncome(sum, type, date, isActive){
    const valid = await form.value.validate()
    if (!valid.valid) {
      return
    }

    let newIncome = {
      id: createtaskId(),
      sum: sum,
      type: type,
      date: date,
      income: true
    }

    list.value.push(newIncome)

    localStorage.setItem('list', JSON.stringify(list.value))


    balance.value += Number.parseInt(newIncome.sum) 

    resetValues(isActive)
}


async function addExpense(sum, type, date, isActive){
    const valid = await form2.value.validate()
    if (!valid.valid) {
      return
    }

    let date_ts = new Date(date).getTime()

    let newExpense = {
      id: createtaskId(),
      sum: sum,
      type: type,
      date: date_ts,
      income: false
    }

    list.value.push(newExpense)

    localStorage.setItem('list', JSON.stringify(list.value))

    balance.value -= Number.parseInt(newExpense.sum) 

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

const balance = ref(0)

function reset () {
  list.value = []
  localStorage.setItem('list', JSON.stringify(list.value))
  balance.value = 0
}

function btn_delete (inc_exp) {
  const newList = list.value.filter(item => item.id !== inc_exp.id)

  if (inc_exp.income) {
    balance.value -= Number.parseInt(inc_exp.sum)
  } else {
    balance.value += Number.parseInt(inc_exp.sum)
  }

  list.value = newList
  localStorage.setItem('list', JSON.stringify(list.value))
}

const items = ref(['En', 'Ru'])
const item = ref('En')
</script>