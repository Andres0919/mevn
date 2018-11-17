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
                            <form @submit.prevent="addTask">
                                <div class="form-group">
                                    <input type="text" v-model="task.title" placeholder="Insert A task" class="form-control">
                                </div>
                                <div class="form-group">
                                    <textarea class="form-control" v-model="task.description" cols="30" rows="10" placeholder="Insert description"></textarea>
                                </div>
                                <button class="btn btn-primary btn-block">Send</button>
                            </form>
                        </div>
                    </div>
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
                tasks: []
            }
        },
        created() {
            this.getTasks();
        },
        methods:{
            getTasks() {
                fetch('/api/task')
                    .then(res => res.json())
                    .then(data => console.log(data)) 
            },
            addTask() {
                fetch('/api/task', {
                    method: 'POST',
                    body: JSON.stringify(this.task),
                    headers: {
                        'Accept': 'application/json',
                        'Content-type': 'application/json'
                    }
                })
                .then(res => res.json())
                .then(data => console.log(data))
                this.task = new Task();
            }
        }
    }
</script>