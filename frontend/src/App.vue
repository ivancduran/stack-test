<template>
      <div id="app">
        <div class="container">

          <div class="title">
            <h1>Todo App</h1>
          </div>

          <div class="new-task">
            <div class="add-new" v-on:click="addTask">
              <i class="fa fa-plus"></i>
            </div>
            <input id="todo-text" placeholder="New task" v-model="addInput" v-on:keyup.enter="addTask"></input>
          </div>

          <ul>
            <li v-for="(task, index) in tasks" class="row">
              <a class="remove" href="#" v-on:click.stop.prevent="removeThis(task, index)">
                <i class="fa fa-trash-o"></i>
              </a>
              <a class="completed" href="#" v-on:click.stop.prevent="completeThis(task)">
                <i class="fa fa-check"></i>
              </a>
              <span v-bind:class="{ finish : task.completed }">
              {{task.name}}
              </span>
            </li>
          </ul>

        </div>
      </div>
</template>

<script>
    export default {
      name: 'app',
      data () {
        return {
          addInput: '',
          tasks: []
        }
      },
      methods: {
        addTask: function () {
          if (this.addInput.trim() !== '') {
            var task = this.addInput.trim()

            this.axios.post(`/api/v1/tasks`, {
              name: task
            }).then((response) => {
              console.log(response.data.id)
              this.tasks.push({
                id: response.data.id,
                name: task,
                completed: false
              })
            }, (response) => {
              console.log(response)
            })

            this.addInput = ''
          }
        },
        removeThis: function (el, index) {
          this.axios.delete(`/api/v1/tasks/${el.id}`).then((response) => {
            this.tasks.splice(index, 1)
          }, (response) => {
            console.log(response)
          })
        },
        completeThis: function (task) {
          this.axios.put(`/api/v1/tasks/${task.id}`, {
            completed: !task.completed
          }).then((response) => {
            task.completed = !task.completed
          }, (response) => {
            console.log(response)
          })
        }
      },
      created: function () {
        const inBrowser = typeof window !== 'undefined'
        if (inBrowser) {
          this.axios.get('/api/v1/tasks').then((response) => {
            this.tasks = response.data
          }, (response) => {
            console.log(response)
          })
        }
      }
    }
</script>

<style>
</style>
