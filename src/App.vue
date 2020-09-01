<template>

    <div id = "toDo">
      <h3 style="text-align: center"> MY TO DO LIST </h3>
      <ol>
        <li 
          v-for="(myToDo, index) in toDo" :key="index">
          <button @click="delToDo(index)" style="margin-top: 2px"> Выполено </button>
          <!-- {{ myToDo }} -->
          {{ myToDo.task }}
        </li>
      </ol>
      <div>
        <button @click="newToDo" :class="none" style="margin-right: 3px"> Добавить задачу </button>
        <h2 v-if="x" style = "background-color: red; text-align: center"> !!!  Слишком много задач, выполни что-то  !!! </h2>
        <input v-model = "addToDo" type = "text" style="width: 350px" placeholder = "Новая задача">
      </div>
    </div>

</template>

<script>

export default {
  name: 'App',
  
  data() {
    return {
      message: 'Test message',
      // toDo: ['Отжаться от пола 100 раз за день','Сделать домашку по vue', 
      //       'Посмотреть пропущенный урок английского','Пробежать 5 км вечером' ],
      // toDo: [
      //   {task: 'Отжаться от пола 100 раз за день'},
      //   {task: 'Сделать домашку по vue'}, 
      //   {task: 'Посмотреть пропущенный урок английского'},
      //   {task: 'Пробежать 5 км вечером'}],
      toDo: [],
      addToDo: '',
      x: false,
    }
  },

  beforeMount() {
    this.$http.get('https://hw-3-vue-firebase.firebaseio.com/myToDoList.json')
      .then((res) => {
        return res.json()
      }).then((res) => {
        Object.values(res).forEach((event) => this.toDo.push(event))
      })
  },

  methods: {

    // delToDo (index)
    // {
    //   this.toDo.splice (index, 1)
    //   if (this.toDo.length < 10) 
    //   this.x = false;
    // },

    delToDo(index) {
      this.toDo.splice(index, 1)
      this.$http.get('https://hw-3-vue-firebase.firebaseio.com/myToDoList.json')
      .then((res) => {
        return res.json()
      }).then((res) => {
        this.$http.delete(`https://hw-3-vue-firebase.firebaseio.com/myToDoList/${Object.keys(res)[index]}.json`)
      })
      if (this.toDo.length < 10) 
      this.x = false;
    },
    
    // newToDo() {
    //   if (this.addToDo.length > 0)
    //     {
    //       this.toDo.push (this.addToDo)
    //       this.addToDo = '';
    //     }
    //   if (this.toDo.length >= 10) 
    //   this.x = true;
    // }

    newToDo() {
      console.log(this.toDo, 'BEFORE')
      // this.toDo.push(this.addToDo);
      this.toDo.push({task: this.addToDo})
      console.log(this.toDo, 'AFTER')
      this.$http.post('https://hw-3-vue-firebase.firebaseio.com/myToDoList.json', {task:this.addToDo})
      // this.$http.post('https://hw-3-vue-firebase.firebaseio.com/myToDoList.json', this.addToDo)
      // this.$http.post('https://hw-3-vue-firebase.firebaseio.com/myToDoList.json', this.toDo)
      this.addToDo = ''
      if (this.toDo.length >= 10) 
      this.x = true;
    },
  },

  computed: {

    none() {
      if (this.toDo.length >= 10)
        {
          return 'none'; 
        }
          else 
          { return ''}
    }
  }
}

</script>

<style>

.none {display: none}

#toDo {
  width: 500px;
  box-shadow: 0 3px 20px #0f0f0f23;
  padding: 10px;
  margin: 20px 0 0 20px;
}

</style>