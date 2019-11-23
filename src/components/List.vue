<template>
  <div id="todo-list" class="container">
    <div class="row">
      <div class="col-md-6 mx-auto">
        <h1 class="text-center">Todo List App</h1>
        <form onSubmit="return false;">
          <label for="tasknameinput">Task Name</label>
          <input v-model="taskname" id="tasknameinput"
            type="text" class="form-control" autocomplete="false"
            placeholder="Add new task" />
          <button v-if="this.isEdit==false" type="button" v-on:click="addNewTask()"
            class="btn btn-sucess btn-block mt-3">Salvar</button>
          <button v-else type="button" v-on:click="updateTask()"
          class="btn btn-primary btn-block mt-3">Alterar</button>
        </form>

        <table class="table">
          <tr v-for="(todo) in todos" v-bind:key="todo.id" v-bind:title="todo.title">
            <td class="text-left">{{todo.title}}</td>
            <td class="text-right">
              <button v-on:click="editTask(todo.title, todo.id)" class="btn btn-info">Edit</button>
              <button v-on:click="deleteTask(todo.id)" class="btn btn-danger">Delete</button>
            </td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      todos: [],
      id: '',
      taskname: '',
      isEdit: false
    }
  },
  mounted () {
    this.getTasks()
  },
  methods: {
    getTasks () {
      axios({method: 'GET', url: '/api/tasks'}).then(
        result => {
          console.log(result.data)
          this.todos = result.data
        },
        error => {
          console.error(error)
        }
      )
    },
    addNewTask () {
      axios.post('/api/tasks', {title: this.taskname}).then(res => {
        this.taskname = ''
        this.getTasks()
        console.log(this.taskname)
        console.log(res)
      })
        .catch(err => {
          console.log(err)
        })
    },
    editTask (title, id) {
      this.id = id
      this.taskname = title
      this.isEdit = true
    },
    updateTask () {
      axios.put('/api/tasks/' + this.id,
        {title: this.taskname})
        .then(res => {
          this.taskname = ''
          this.isEdit = false
          this.getTasks()
          console.log(res)
        })
        .catch(err => {
          console.log(err)
        })
    },
    deleteTask (id) {
      if (confirm('Deseja excluir permanentemnte?')) {
        axios.delete('/api/tasks/' + id)
          .then(res => {
            this.taskname = ''
            this.getTasks()
            console.log(res)
          })
          .catch(err => {
            console.log(err)
          })
      }
    }
  }
}
</script>
