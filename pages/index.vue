<template>
  <div>
    <v-app>

      <!--Balance-->
      <div class="all-balance">
        <h1 class="balance">{{ $t('balance') }}:</h1>
        <h1>${{ balance }}</h1>
      </div>


      <div class="btn_inc_and_exp">
        <v-btn
          color="success"
          size="x-large"
          :text="$t('income')"
          variant="flat"
          @click="isediting = false; form_open = true; isIncome = true"
        ></v-btn>
        <v-btn
          color="error"
          size="x-large"
          :text="$t('expense')"
          variant="flat"
          @click="isediting = false; form_open = true; isIncome = false"
        ></v-btn>

        <v-dialog @afterLeave="form.reset()" v-model="form_open" max-width="500">
          <template v-slot:default="{ isActive }">
            <v-card :title="!isediting ? (isIncome ? $t('add_income') : $t('add_expense')) : (isIncome ? 'Edit income' : 'Edit expense')" >
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
                          <v-text-field
                          v-bind="props"
                          v-model="date"
                          :rules="dateRules"
                          :label="$t('date')" 
                          required
                          readonly
                          class="mr-4"
                          >
                          <template v-slot:prepend>
                            <v-icon
                              icon="mdi-calendar"
                            />
                          </template>
                          </v-text-field>
                        </div>
                      </template>
                      <v-locale-provider :locale="locale">
                        <v-date-picker
                          required
                          v-model="pickerDate"
                          hide-header
                          header=""
                          elevation="5"
                          @update:model-value="date = pickerDate.toLocaleDateString('en', {year: 'numeric', month: '2-digit', day: '2-digit'})">
                        </v-date-picker>
                      </v-locale-provider>
                    </v-menu>

                    <div class="flex-shrink-1 d-flex ga-1">
                      <v-text-field
                        v-model="hours"
                        width="70"
                        :label="$t('hh')"
                        :rules="[checkhours, checkLength]"
                      ></v-text-field>
                      <span style="padding: 17px 0px">:</span>
                      <v-text-field
                        v-model="minutes"
                        width="70"
                        :label="$t('mm')"
                        :rules="[checkminutes, checkLength]"
                      ></v-text-field>
                    </div>

                  </div>
                  <v-text-field
                    v-model="type"
                    :label="$t('type')" 
                  />
                </div>

                <v-card-actions> 
                  <v-spacer></v-spacer>

                  <v-btn
                    v-if="!isediting"
                    :text="$t('addcontinue')" 
                    @click="add_continue(isActive)"
                  ></v-btn>
                  <v-btn
                    v-if="!isediting"
                    :text="$t('add')" 
                    @click="add(isActive)"
                  ></v-btn>
                  <v-btn
                    v-if="isediting"
                    :text="$t('edit')"  
                    @click="edit(isActive)"
                  ></v-btn>
                  <v-btn
                    :text="$t('cancel')" 
                    @click="form_open = false; form.reset()"
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
                      <v-list-item-title>{{ $t(item.title) }}</v-list-item-title>
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
        <v-dialog max-width="500">
          <template v-slot:activator="{ props: activatorProps }">
            <v-btn v-bind="activatorProps" color="#d3d3d3"> 
              {{ $t('reset') }}
            </v-btn>
          </template>
          
          <template v-slot:default="{ isActive }">
            <v-card :title="$t('reset')">
              <v-card-text>
                {{$t('sure') }}
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                
                <v-btn
                  :text="$t('reset')"
                  @click="reset(); isActive.value = false"
                ></v-btn>
                <v-btn
                  :text="$t('cancel')"
                  @click="isActive.value = false"
                ></v-btn>
              </v-card-actions>
            </v-card>
          </template>
        </v-dialog>
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
    { rel: 'icon', type: 'image/x-icon', href: 'https://www.svgrepo.com/show/158640/monument-of-ismoil-somoni-tajikistan.svg' }
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

  updateBalance()

})

// balance
const balance = ref(0)
function updateBalance() {
  balance.value = 0

  for (let i=0; i < list.value.length; i++) {
    if(list.value[i].income) {
      balance.value += Number.parseInt(list.value[i].sum) 
    } else {
      balance.value -= Number.parseInt(list.value[i].sum) 
    }
  }
}

// forms
const form = ref()
const isIncome = ref(true)
const form_open = ref (false)
const isediting = ref(false)

// form values
const dialogOpen = ref(false)
const sum = ref('')
const type = ref('')
const date = ref('')
const hours = ref('')
const minutes = ref('')
const pickerDate = ref(null)

// 
watch(date, async(newDate, oldDate) => {
  dialogOpen.value = false
})

// rules
const checkValue = v => !!v || 'Required'

const rules = ref([
  checkValue
])

const checkNumber = v => !isNaN(parseFloat(v)) && isFinite(v) || 'Incorrect number'

const dateRules = ref([
  v => !!v || 'Date is required',
])

const checkhours = v => v >= 0 && v < 25 || "Invalid"
const checkminutes = v => v >= 0 && v < 61 || "Invalid"
const checkLength = v => {
  if(v && v.length > 2) {
    return 'Invalid'
  }
  return true
}

// add & continue
async function add_continue(isActive){
  const valid = await form.value.validate()
  
  if (!valid.valid) {
    return
  }
  
  let newDate = new Date(new Date(date.value).getTime() + Number.parseInt(hours.value ? hours.value : 0)*3600*1000 + Number.parseInt(minutes.value ? minutes.value : 0)*60*1000)

  let newIncome = {
    id: createtaskId(),
    sum: sum.value,
    type: type.value,
    date: newDate,
    income: isIncome.value,
  }
  
  list.value.push(newIncome)

  localStorage.setItem('list', JSON.stringify(list.value))


  updateBalance()

  form.value.reset()
}

// add
async function add(isActive){
  const valid = await form.value.validate()
  
  if (!valid.valid) {
    return
  }
  
  let newDate = new Date(new Date(date.value).getTime() + Number.parseInt(hours.value ? hours.value : 0)*3600*1000 + Number.parseInt(minutes.value ? minutes.value : 0)*60*1000)

  let newIncome = {
    id: createtaskId(),
    sum: sum.value,
    type: type.value,
    date: newDate,
    income: isIncome.value,
  }
  
  list.value.push(newIncome)

  localStorage.setItem('list', JSON.stringify(list.value))


  updateBalance()

  form.value.reset()
  form_open.value = false
}

// delete
function btn_delete (inc_exp) {
  const newList = list.value.filter(item => item.id !== inc_exp.id)

  updateBalance()

  list.value = newList
  localStorage.setItem('list', JSON.stringify(list.value))
}

// edit
const currentId = ref(null)

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

function editClick(inc_exp) {
  isediting.value = true
  form_open.value = true
  
  sum.value = inc_exp.sum
  type.value = inc_exp.type
  date.value = new Date(inc_exp.date).toLocaleDateString('en', {year: 'numeric', month: '2-digit', day: '2-digit'})
  hours.value = new Date(inc_exp.date).getHours()
  minutes.value = new Date(inc_exp.date).getMinutes()
  currentId.value = inc_exp.id
}
async function edit(isActive) {
  const valid = await form.value.validate()
  if (!valid.valid) return

  let hm = new Date(date.value).getHours()*3600*1000 + new Date(date.value).getMinutes()*60*1000
  let newDate = new Date(new Date(date.value).getTime() + Number.parseInt(hours.value ? hours.value : 0)*3600*1000 + Number.parseInt(minutes.value ? minutes.value : 0)*60*1000 - hm)


  let index = list.value.findIndex((el) => el.id == currentId.value)
  list.value[index].sum = sum.value
  list.value[index].type = type.value
  list.value[index].date = newDate
  
  updateBalance()

  localStorage.setItem('list', JSON.stringify(list.value))
  isActive.value = false
}

// reset
function reset () {
  list.value = []
  localStorage.setItem('list', JSON.stringify(list.value))
  updateBalance()
}

// locales
const items = ref(['En', 'Ru'])
const item = ref('En')
</script>