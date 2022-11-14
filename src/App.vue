<template>
   <div class="container">
     <Header 
      :showAddtask="showAddtask"
      @toggle-add-task='toggleAddTask' 
      title="task tracker">
    </Header>
     <div v-show="showAddtask">
      <AddTask @add-task="addNewTask" />
     </div>
     <Tasks 
        @toggle-reminder="toggleReminder"
        @delete-task="deleteTask" :tasks="tasks" 
      />
   </div>
</template>

<script>
import Header from "./components/Header";
import Tasks from "./components/Tasks.vue";
import AddTask from "./components/AddTask.vue";
import axios from "axios";
export default {
  name: 'App',
  components: { 
    Header,
    Tasks,
    AddTask,
  },
  data(){
    return {
      tasks : [],
      showAddtask : false
    }
  },
  methods : {
    toggleAddTask(){
      this.showAddtask = !this.showAddtask
    },

    async deleteTask(id){
      console.log('task', id)
      let res = await axios.delete(`http://localhost:5000/tasks/${id}`)
      console.log(res.data)
      this.tasks = await this.fetchTask()
    },

    async toggleReminder(id){
      let res = await axios.get(`http://localhost:5000/tasks/${id}`)
      console.log(res.data)
      let obj = res.data
      obj.reminder = !obj.reminder
      let res2 = await axios.patch(`http://localhost:5000/tasks/${id}`,obj)
      console.log(res2.data)
      this.tasks = await this.fetchTask()
    },

    async addNewTask(task){
      let res = await axios.post(`http://localhost:5000/tasks`,task)
      console.log(res.data)
      this.tasks = await this.fetchTask()
    },

    async fetchTask(){
      let res = await axios.get("http://localhost:5000/tasks")
      console.log(res.data)
      return res.data
    }
    
  },
  async created() {
    this.tasks = await this.fetchTask()
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
