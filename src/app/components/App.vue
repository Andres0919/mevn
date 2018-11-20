<template>
    <div>
        <nav class="navbar navbar-light bg-light">
            <a href="/" class="navbar-brand">MEVN Stack</a>
        </nav>
        <div class="container">
            <div class="row pt-5"> 
                <div class="col-md-5">
                    <div class="card">
                        <div class="card-body">
                            <form @submit.prevent="sendTask">
                                <div class="form-group">
                                    <input type="text" v-model="task.title" placeholder="Insert A task" class="form-control">
                                </div>
                                <div class="form-group">
                                    <textarea class="form-control" v-model="task.description" cols="30" rows="10" placeholder="Insert description"></textarea>
                                </div>
                                <template v-if="isUpdate === false">
                                    <button class="btn btn-primary btn-block">Send</button>
                                </template>
                                <template v-else>
                                    <button class="btn btn-primary btn-block">Update</button>                                    
                                </template>
                            </form>
                        </div>
                    </div>
                </div>
                <div class="col-md-7">
                    <table class="table table-bordered">
                        <thead>
                            <tr>
                                <th>Task</th>
                                <th>description</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="task of tasks" :key="task._id">
                                <td>{{ task.title }}</td>
                                <td>{{ task.description }}</td>
                                <td>
                                    <button @click="updateTask(task._id)" class="btn btn-outline-secondary">Update</button>
                                    <button @click="deleteTask(task._id)" class="btn btn-outline-danger">Delete</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    class Task {
        constructor(title, description) {
            this.title = title;
            this.description = description;
        }
    }
    export default {
        data() {
            return {
                task: new Task(),
                tasks: [],
                isUpdate: false,
                taskUpdate: ''
            }
        },
        created() {
            this.getTasks();
        },
        methods:{
            getTasks() {
                fetch('/api/task')
                    .then(res => res.json())
                    .then(data => {
                        this.tasks = data;
                    }) 
            },
            sendTask() {
                if(!this.isUpdate) {
                    fetch('/api/task', {
                        method: 'POST',
                        body: JSON.stringify(this.task),
                        headers: {
                            'Accept': 'application/json',
                            'Content-type': 'application/json'
                        }
                    })
                    .then(res => res.json())
                    .then(data => {
                        this.getTasks();
                    })
                }else{
                    fetch(`/api/task/${this.taskUpdate}`,{
                        method: 'PUT',
                        body: JSON.stringify(this.task),
                        headers: {
                            'Accept': 'application/json',
                            'Content-type': 'application/json'
                        }
                    })
                    .then(res => res.json())
                    .then(data => {
                        this.getTasks();
                        this.isUpdate = false;
                    })
                }
 
                this.task = new Task();
            },
            deleteTask(id){
                fetch(`/api/task/${id}`, {
                    method: 'DELETE',
                    headers: {
                        'Accept': 'application/json',
                        'Content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => {
                    this.getTasks();
                });
            },
            updateTask(id){
                fetch(`/api/task/${id}`)
                .then(res => res.json())
                .then(data => {
                    this.task = new Task(data.title, data.description);
                    this.isUpdate = true;
                    this.taskUpdate = data._id;
                })
            }
        }
    }
</script>