<template>
  <div id="app">
    <h4 class="bg-primary text-white text-center p-2">
      {{ name }}'s To Do List
    </h4>
    <div class="container-fluid p-4">
      <template>
        <div class="row py-2">
          <div class="col-4">
            <input
              v-model="searchItemText"
              placeholder="Search a Task"
              class="form-control"
            />
          </div>
        </div>
        <div class="row">
          <div class="col font-weight-bold">Task</div>
          <div class="col-2 font-weight-bold">Done</div>
        </div>
        <div class="row" v-for="task in filteredTasks" :key="task.id">
          <div class="col">{{ task.content }}</div>
          <div class="col-2">
            <input
              type="checkbox"
              v-model="task.done"
              class="form-check-input" style="margin-left: 0.75rem;"
              @change="markAsCompleted(task)"
            />
          </div>
        </div>
        <div class="row" v-if="filteredTasks.length == 0">
          <div class="col text-center">
            <b>Nothing to do. Hurrah!</b>
          </div>
        </div>
        <div class="row py-2">
          <div class="col">
            <input
              v-model="newItemText"
              placeholder="Add new Task"
              class="form-control"
            />
          </div>
          <div class="col-2">
            <button
              class="btn btn-primary"
              @click="addNewTodo"
              :disabled="newItemText === ''"
            >
              Add
            </button>
          </div>
        </div>
        <div class="row bg-secondary py-2 mt-2 text-white">
          <div class="col text-center">
            <input
              type="checkbox"
              v-model="hideCompleted"
              class="form-check-input" 
            />
            <label class="form-check-label font-weight-bold">
              Hide completed tasks
            </label>
          </div>
          <div class="col text-center">
            <button class="btn btn-sm btn-warning" v-on:click="deleteCompleted" :disabled="disableCompleted">
              Delete Completed
            </button>
          </div>
        </div>
      </template>
    </div>
  </div>
</template>
<script>
import axios from "axios";
const API_URL = "http://localhost:3000/notes";

export default {
  name: "app",
  data: () => ({
    name: "Shradha",
    tasks: [],
    hideCompleted: false,
    newItemText: "",
    searchItemText: "",
  }),

  computed: {
    disableCompleted(){
     return !this.tasks.find(t => t.done);
    },
    filteredTasks() {
      if (this.searchItemText !== "" && this.hideCompleted)
        return this.tasks.filter(
          (t) =>
            t.action
              .toLowerCase()
              .includes(this.searchItemText.toLowerCase()) && !t.done
        );
      else if (this.searchItemText !== "")
        return this.tasks.filter((t) =>
          t.action.toLowerCase().includes(this.searchItemText.toLowerCase())
        );
      else if (this.hideCompleted) return this.tasks.filter((t) => !t.done);
      else return this.tasks;
    },
  },

  methods: {
    addNewTodo() {
      var newTask = { content: this.newItemText, done: false };

      axios.post(API_URL, newTask).then((res) => {
        this.tasks.push(res.data);
        this.newItemText = "";
      });
    },

    deleteCompleted() {
      var ids = this.tasks.filter((t) => t.done).map((t) => t._id);
      axios
        .post(`${API_URL}/delete`, { ids: ids })
        .then((res) => (this.tasks = this.tasks.filter((t) => !t.done)));
    },
    
    markAsCompleted(task) {
      axios.put(API_URL, task);
    },
  },

  created() {
    axios.get(API_URL).then((res) => {
      if (res.data != null) {
        this.tasks = res.data;
      }
    });
  },
};
</script>