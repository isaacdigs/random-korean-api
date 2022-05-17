<template>
  <div class="container">
    <HeaderComponent @toggle-add-task="toggleAddTask" title="Task Tracker" :showAddTask="showAddTask"/>
    <div v-if="showAddTask">
      <AddTask @add-task="addTask"/>
    </div>
    <TasksComponent 
      @toggle-task="toggleTask" 
      @delete-task="deleteTask" :tasks="tasks" 
    />
  </div>
  <div></div>
</template>

<script>
import HeaderComponent from './components/Header'
import TasksComponent from './components/Tasks'
import AddTask from './components/AddTask'

export default {
  name: 'App',
  components: {
    HeaderComponent,
    TasksComponent,
    AddTask
  },
  data() {
    return {
      tasks: [],
      showAddTask: false,
    }
  },
  methods: {
    async deleteTask(id) {
      if(confirm('Are you sure you want to delete this task?')) {
        const response = await fetch(`api/tasks/${id}`, {
          method: 'DELETE',
        });
        response.status === 200 ? this.tasks = this.tasks.filter(task => task.id !== id) : alert('Error deleting task');
      }
    },
    async toggleTask(id) {
      const taskToToggle = await this.fetchTask(id);
      const updateTask = {
        ...taskToToggle,
        reminder: !taskToToggle.reminder,
      }

      const response = await fetch(`api/tasks/${id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(updateTask)
      });

      const data = await response.json();

      if(response.status === 200) {
        this.tasks = this.tasks.map(task => {
          task.id === id ? {...task, reminder: data.reminder} : task;
        })
      }
    },
    async addTask(task) {
      const response = await fetch('api/tasks', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(task)
        });
      const data = await response.json();
      this.tasks.push(data);
    },
    toggleAddTask() {
      this.showAddTask = !this.showAddTask
    },
    async fetchTasks() {
      const response = await fetch('api/tasks');
      const data = await response.json();
      return data;
    },
    async fetchtask(id) {
      const response = await fetch(`api/${id}`);
      const data = await response.json();
      return data;
    },
  },
  async created() {
    this.tasks = await this.fetchTasks();
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: 'Poppins', sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}
.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}
.btn:focus {
  outline: none;
}
.btn:active {
  transform: scale(0.98);
}
.btn-block {
  display: block;
  width: 100%;
}
</style>
