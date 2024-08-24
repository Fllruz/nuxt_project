<template>
  <div>
    <v-app>

      <!--Balance-->
      <div class="all-balance">
        <h1 class="balance">{{ $t('balance') }}:</h1>
        <h1>${{ balance }}</h1>
      </div>

      <div class="btn_inc_and_exp">
        <!-- income button -->
        <v-dialog @afterLeave="form.reset()" v-model="income_open" max-width="500">
          <template v-slot:activator="{ props: activatorProps }">
            <v-btn
              color="success"
              size="x-large"
              v-bind="activatorProps"
              :text=" $t('income') "
              variant="flat"
              @click="isediting = false"
            ></v-btn>
          </template>

          <template v-slot:default="{ isActive }">
            <v-card :title="!isediting ? $t('add_income') : 'Edit income'" >
              <v-form @submit.prevent ref="form">
                <div class="px-6">
                  <v-text-field
                    v-model="sum"
                    :rules="[checkValue, checkNumber]"
                    :label="$t('sum')" 
                    required
                  />
                  <div class="d-flex">
                    <v-menu v-model="dialogOpen" :close-on-content-click="false">
                      <template v-slot:activator="{ props }">
                        <div class="date-input flex-grow-1">
                          <v-btn class="mt-1" icon="mdi-calendar" flat v-bind="props"></v-btn>
                          <v-text-field
                          v-model="date"
                          :rules="dateRules"
                          :label="$t('date')" 
                          required
                          disabled
                          />
                        </div>
                      </template>
                      <v-locale-provider :locale="locale">
                        <v-date-picker required v-model="date" hide-header elevation="5"></v-date-picker>
                      </v-locale-provider>
                    </v-menu>

                    <div class="flex-shrink-1 d-flex ga-1">
                      <v-text-field
                        v-model="hours"
                        width="70"
                        label="hh"
                        :rules="[checkhours, checkLength]"
                      ></v-text-field>
                      <span style="padding: 17px 0px">:</span>
                      <v-text-field
                        v-model="minutes"
                        width="70"
                        label="mm"
                        :rules="[checkminutes, checkLength]"
                      ></v-text-field>
                    </div>

                  </div>
                  <v-text-field
                    v-model="type"
                    :rules="rules"
                    :label="$t('type')" 
                    required
                  />
                </div>

                <v-card-actions> 
                  <v-spacer></v-spacer>

                  <v-btn
                    v-if="!isediting"
                    :text="$t('add')" 
                    @click="addIncome(sum, type, date, hours, minutes, isActive)"
                  ></v-btn>
                  <v-btn
                    v-if="isediting"
                    text="Edit" 
                    @click="edit(sum, type, date, hours, minutes, false, isActive)"
                  ></v-btn>
                  <v-btn
                    :text="$t('cancel')" 
                    @click="resetValues(isActive)"
                  ></v-btn>

                </v-card-actions>
              </v-form>
            </v-card>
          </template>
        </v-dialog>


        <!-- Expense button-->
        <v-dialog @afterLeave="form2.reset()" v-model="expense_open" max-width="500">
          <template v-slot:activator="{ props: activatorProps }">
            <v-btn
              color="error"
              size="x-large"
              v-bind="activatorProps"
              :text=" $t('expense') "
              variant="flat"
              @click="isediting = false"
            ></v-btn>
          </template>

          <template v-slot:default="{ isActive }">
            <v-card :title="!isediting ? $t('add_expense') : 'Edit expense'" >
              <v-form @submit.prevent ref="form2">
                <div class="px-6">
                  <v-text-field
                    v-model="sum"
                    :rules="[checkValue, checkNumber]"
                    :label="$t('sum')" 
                    required
                  />
                  <div class="d-flex">
                    <v-menu v-model="dialogOpen" :close-on-content-click="false">
                      <template v-slot:activator="{ props }">
                        <div class="date-input flex-grow-1">
                          <v-btn class="mt-1" icon="mdi-calendar" flat v-bind="props"></v-btn>
                          <v-text-field
                          v-model="date"
                          :rules="dateRules"
                          :label="$t('date')" 
                          required
                          disabled
                          />
                        </div>
                      </template>
                      <v-locale-provider :locale="locale">
                        <v-date-picker required v-model="date" hide-header elevation="5"></v-date-picker>
                      </v-locale-provider>
                    </v-menu>

                    <div class="flex-shrink-1 d-flex ga-1">
                      <v-text-field
                        v-model="hours"
                        width="70"
                        label="hh"
                        :rules="[checkhours, checkLength]"
                      ></v-text-field>
                      <span style="padding: 17px 0px">:</span>
                      <v-text-field
                        v-model="minutes"
                        width="70"
                        label="mm"
                        :rules="[checkminutes, checkLength]"
                      ></v-text-field>
                    </div>

                  </div>
                  <v-text-field
                    v-model="type"
                    :rules="rules"
                    :label="$t('type')" 
                    required
                  />
                </div>

                <v-card-actions> 
                  <v-spacer></v-spacer>

                  <v-btn
                    v-if="!isediting"
                    :text="$t('add')" 
                    @click="addExpense(sum, type, date, isActive)"
                  ></v-btn>
                  <v-btn
                    v-if="isediting"
                    text="Edit" 
                    @click="edit(sum, type, date, hours, minutes, true, isActive)"
                  ></v-btn>
                  <v-btn
                    :text="$t('cancel')" 
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
            {{ $t('history') }}:
          </h2>
          <div class="history-all" v-if="list.length > 0">
            <div class="history-item" v-for="n in list" :class="n.income ? 'income' : 'expense'" :key="n">
              <div class="sum">{{ n.sum }}</div>
              <div class="type">{{ n.type }}</div>
              <div class="d-flex align-center ga-2">
                <div class="date">
                  {{ new Date(n.date).toLocaleDateString('ru', {year: 'numeric', month:'2-digit', day:'2-digit'}) }}
                  •
                  {{ new Date(n.date).toLocaleString('ru', {hour: '2-digit', minute: '2-digit'}) }}
                </div>
                <!-- <v-btn icon="mdi-delete" variant="text" color="black" size="regular" class="mb-1" @click="btn_delete(n)"> -->
              
                <v-menu>
                  <template v-slot:activator="{ props }">
                    <v-btn icon="mdi-dots-vertical" variant="text" color="black" size="regular" class="mb-1" v-bind="props"></v-btn>
                  </template>
                  <v-list>
                    <v-list-item
                      v-for="(item, index) in menuItems"
                      :key="index"
                      :value="index"
                      @click="item.func(n)"
                    >
                      <v-list-item-title>{{ item.title }}</v-list-item-title>
                    </v-list-item>
                  </v-list>
                </v-menu>
              
              </div>
              
            </div>
          </div>
          <div class="mt-2" v-else>{{ $t('nothing') }}</div>
        </div>
      </div>

      <div class="btn-reset">
        <v-btn color="#d3d3d3" @click="reset"> 
          {{ $t('reset') }}
        </v-btn>
      </div>
      
      <div class="btn-lang">
        <v-select :items="items" v-model="item" density="compact" variant="outlined">
          <template v-slot:item="{ props, item }">
            <v-list-item v-bind="props" @click="item.value == 'En' ? setLocale('en') : setLocale('ru')">
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
useHead({
  title: 'Money calendar',
  link: [
    { rel: 'icon', type: 'image/x-icon', href: 'https://e7.pngegg.com/pngimages/753/938/png-clipart-dollar-sign-united-states-dollar-dollar-saving-text-thumbnail.png' }
  ]
})

const { locale, setLocale } = useI18n()

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

async function addIncome(sum, type, date, hours, minutes, isActive){
    const valid = await form.value.validate()
    
    if (!valid.valid) {
      return
    }
    
    let newDate = new Date(new Date(date).getTime() + Number.parseInt(hours ? hours : 0)*3600*1000 + Number.parseInt(minutes ? minutes : 0)*60*1000)

    let newIncome = {
      id: createtaskId(),
      sum: sum,
      type: type,
      date: newDate,
      income: true
    }

    list.value.push(newIncome)

    localStorage.setItem('list', JSON.stringify(list.value))


    balance.value += Number.parseInt(newIncome.sum) 

    resetValues(isActive)
}


async function addExpense(sum, type, date, hours, minutes, isActive){
    const valid = await form2.value.validate()
    if (!valid.valid) {
      return
    }

    let newDate = new Date(new Date(date).getTime() + Number.parseInt(hours ? hours : 0)*3600*1000 + Number.parseInt(minutes ? minutes : 0)*60*1000)


    let newExpense = {
      id: createtaskId(),
      sum: sum,
      type: type,
      date: newDate,
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
  if(isActive) {
    isActive.value = false
  }
}

const checkValue = v => !!v || 'Required'

const rules = ref([
  checkValue
])

const checkNumber = v => !isNaN(parseFloat(v)) && isFinite(v) || 'Incorrect number'

const dateRules = ref([
  v => !!v || 'Date is required',
])

const hours = ref('')
const minutes = ref('')
const checkhours = v => v >= 0 && v < 25 || "Invalid"
const checkminutes = v => v >= 0 && v < 61 || "Invalid"
const checkLength = v => {
  if(v && v.length > 2) {
    return 'Invalid'
  }
  return true
}

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

const menuItems = ref([
  {
    title: 'edit',
    func: editClick
  },
  {
    title: 'delete',
    func: btn_delete
  },
])

const income_open = ref (false)
const expense_open = ref (false)
const isediting = ref(false)
const currentId = ref(null)

function editClick(inc_exp) {
  isediting.value = true
  if (inc_exp.income) {
    income_open.value = true
  } else {
    expense_open.value = true
  }
  sum.value = inc_exp.sum
  type.value = inc_exp.type
  date.value = new Date(inc_exp.date)
  hours.value = new Date(inc_exp.date).getHours()
  minutes.value = new Date(inc_exp.date).getMinutes()
  currentId.value = inc_exp.id
}

async function edit(sum, type, date, hours, minutes, expense, isActive) {
  if(!expense) {
    const valid = await form.value.validate()
    if (!valid.valid) return
  }
  if(expense) {
    const valid = await form2.value.validate()
    if (!valid.valid) return
  }
    

  let hm = new Date(date).getHours()*3600*1000 + new Date(date).getMinutes()*60*1000
  let newDate = new Date(new Date(date).getTime() + Number.parseInt(hours ? hours : 0)*3600*1000 + Number.parseInt(minutes ? minutes : 0)*60*1000 - hm)


  let index = list.value.findIndex((el) => el.id == currentId.value)
  list.value[index].sum = sum
  list.value[index].type = type
  list.value[index].date = newDate
  
  balance.value = 0
  for (let i=0; i < list.value.length; i++) {
    if(list.value[i].income) {
      balance.value += Number.parseInt(list.value[i].sum) 
    } else {
      balance.value -= Number.parseInt(list.value[i].sum) 
    }
  }

  localStorage.setItem('list', JSON.stringify(list.value))
  isActive.value = false
}
</script>