<template>
  <div class="home">
    <h1 class="title">Vuengo</h1>
  
    <hr>

    <div class="columns">
      <div class="column is-3 is-offset-3">
        
        <!-- ADDTASK -->
        <form v-on:submit.prevent="addTask">   <!-- aqui el methdo agregar -->
          <h2 class="subtitle">Add task</h2> 

          <div class="field">
            <label class="label">Description</label>
            <div class="control">
              <input class="input" type="text" v-model="description">  <!-- DESCRIPTCION -->
            </div>
          </div>

          <div class="field">
            <label class="label">Status</label>
            <div class="control">
              <div class="select">
                <select v-model="status">   <!-- aqui el ESTATUS agregar -->

                <!-- LAS OPCIONES SIMPLES STRINGS -->
                  <option value="todo">To do</option>   
                  <option value="done">Done</option>
                </select>
              </div>
            </div>
          </div>

          <div class="field">
            <div class="control">
              <!-- BOTTON PARA SUBMIT DEL FORMULARIO -->
              <button class="button is-link">Submit</button>  
            </div>
          </div>
        </form>
      </div>
    </div>

  <!-- / las tareas  -->
      <div class="columns">
        <div class="column is-6">
          <h2 class="subtitle">To do</h2>

          <div class="todo">
            <div class="card" v-for="task in tasks" v-if="task.status === 'todo'" v-bind:key="task.id">
              <div class="card-content">{{ task.description }}</div>

              <footer class="card-footer">
                <a class="card-footer-item" @click="setStatus(task.id, 'done')">Done</a>
              </footer>
            </div>
          </div>
        </div>
        <div class="column is-6">
          <h2 class="subtitle">Done</h2>

          <div class="done">
            <div class="card" v-for="task in tasks" v-if="task.status === 'done'" v-bind:key="task.id">
              <div class="card-content">{{ task.description }}</div>

              <footer class="card-footer">
                <a class="card-footer-item" @click="setStatus(task.id, 'todo')">To do</a>
              </footer>
            </div>
          </div>
        </div>
      <!-- / -->
    </div>
  </div>
</template>

<script>
const API_URL = 'http://127.0.0.1:8000/'
import axios from 'axios'
export default {
  name: 'Home',
  data () {
    return {
      tasks: [],
      description: '',
      status: 'todo'
    }
  },
  mounted () {
    this.getTasks()
  },
  methods: {
    getTasks() {
      axios({
        method: 'get',
        url: API_URL + 'tasks/',
      }).then(response => this.tasks = response.data)
    },
    addTask() {
      if (this.description) {
        axios({
          method: 'post',
          url: API_URL + 'tasks/',
          data: {
            description: this.description,
            status: this.status
          },
        }).then((response) => {
          let newTask = {
            'id': response.data.id,
            'description': this.description,
            'status': this.status
          }
          this.tasks.push(newTask)
          this.description = ''
          this.status = 'todo'
        }).catch((error) => {
          console.log(error)
        })
      }
    },
    setStatus(task_id, status) {
      const task = this.tasks.filter(task => task.id === task_id)[0]
      const description = task.description
      axios({
        method: 'put',
        url: API_URL + 'tasks/' + task_id + '/',
        headers: {
          'Content-Type': 'application/json',
        },
        data: {
          status: status,
          description: description
        },
      }).then(() => {
        task.status = status
      })

    },
  }}
</script>

<style lang="scss">
.select, select {
  width: 100%;
}
.card {
  margin-bottom: 20px;
}
.done {
  opacity: 0.3;
}
</style>