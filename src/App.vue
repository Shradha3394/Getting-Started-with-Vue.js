<template>
    <div id="app">
        <h4 class="bg-primary text-white text-center p-2">
            {{name}}'s To Do List
        </h4>
        <div class="container-fluid p-4">
            <div class="row" v-if="filteredTasks.length == 0">
                <div class="col text-center">
                    <b>Nothing to do. Hurrah!</b>
                </div>
            </div>
            <template v-else>
                <div class="row">
                    <div class="col font-weight-bold">Task</div>
                    <div class="col-2 font-weight-bold">Done</div>
                </div>
                <div class="row" v-for="task in filteredTasks" :key="task.action">

                    <div class="col">{{task.action}}</div>
                    <div class="col-2">
                        <input type="checkbox" v-model="task.done" class="form-check-input" />
                        {{task.done}}
                    </div>
                </div>
                <div class="row py-2">
                    <div class="col">
                        <input v-model="newItemText" class="form-control" />
                    </div>
                    <div class="col-2">
                        <button class="btn btn-primary" v-on:click="addNewTodo" :disabled="newItemText === ''">Add</button>
                    </div>
                </div>
                <div class="row bg-secondary py-2 mt-2 text-white">
                    <div class="col text-center">
                        <input type="checkbox" v-model="hideCompleted" class="form-check-input" />
                        <label class="form-check-label font-weight-bold">
                            Hide completed tasks
                        </label>
                    </div>
                    <div class="col text-center">
                        <button class="btn btn-sm btn-warning"
                                v-on:click="deleteCompleted">
                            Delete Completed
                        </button>
                    </div>
                </div>

            </template>
        </div>
    </div>
</template>
<script>
    export default {
        name: 'app',
        data() {
            return {
                name: "Shradha",
                tasks: [],
                hideCompleted: false,
                newItemText: ""
            }
        },

        computed: {
            filteredTasks() {
                return this.hideCompleted ? this.tasks.filter(t => !t.done) : this.tasks;
            }
        },

        methods: {
            addNewTodo() {
                var newTask = { action: this.newItemText, done: false };
                this.tasks.push(newTask);
                this.storeData();
                this.newItemText = ""
            },
            storeData() {
                localStorage.setItem("todos", JSON.stringify(this.tasks));
            },
            deleteCompleted() {
                this.tasks = this.tasks.filter(t => !t.done);
                this.storeData();
            }
        },

        created() {
            let data = localStorage.getItem("todos");
            if (data != null) {
                this.tasks = JSON.parse(data);
            }
        }
    }
</script>