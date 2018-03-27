<template>
  <div class="container">
    <div class="navbar navbar-light">
      <a class="navbar-brand" href="#">Go Kanban</a>
      <!-- Button trigger modal -->
      <button type="button" class="btn btn-success" data-toggle="modal" data-target="#exampleModalCenter">
      Add Task
      </button>
      <div class="modal fade" id="exampleModalCenter" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered" role="document">
          <div class="modal-content">
            <div class="modal-body">
            <div class = "form-group">
              <label for="tiles">Title</label>
              <input type="text" v-model ="title" class="form-control" placeholder="Enter title">
            </div>
            <div class="form-group">
              <label for="exampleInputPassword1">Description</label>
              <textarea class="form-control" v-model="description" placeholder="Description" rows="3"></textarea>
            </div>
            <div class="form-group">
              <label for="tile">Point</label>
              <input type="number" v-model="point" class="form-control">
            </div>
            <div class="form-group">
              <label for="tile">Assigned To</label>
              <input type="text" class="form-control" v-model="assign" placeholder="Assigned To">
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
            <button type="button" @click="addTask" class="btn btn-primary" data-dismiss="modal" >Save changes</button>
          </div>
        </div>
      </div>
    </div>
  </div>
    <div class="row">
      <div class ="col-md 3">
        <div class="card text-white mb-3" style="max-width: 18rem;">
          <div class="card-header bg-danger" style="text-align:left;">Back-log</div>
            <div class="card-body">
             <div class="card mb-3" v-for="(task,index) in backlog" :key="index" style="max-width: 18rem; color:black;">
              <div class="card-header">{{task.title}}</div>
              <div class="card-body">
                <p class="card-text">{{task.description}}</p>
                <p>Assign to : {{task.assign}}</p>
                <p>Point : {{task.point}}</p>
                <hr>
                <div class="row">
                  <div class="col-md-6">
                  </div>
                  <div class="col-md-6">
                    <button class="float-right" @click="next(task['.key'],task.status)">Todo</button>
                  </div>
                </div>
            </div>
          </div>
          </div>
        </div> 
      </div>
      <div class ="col-md 3">
      <div class="card text-white mb-3" style="max-width: 18rem;">
          <div class="card-header bg-warning" style="text-align:left;">To-do</div>
            <div class="card-body">
             <div class="card mb-3" v-for="(task,index) in todo" :key="index" style="max-width: 18rem; color:black;">
              <div class="card-header">{{task.title}}</div>
              <div class="card-body">
                <p class="card-text">{{task.description}}</p>
                <p>Assign to : {{task.assign}}</p>
                <p>Point : {{task.point}}</p>
                <hr>
                <div class="row">
                  <div class="col-md-6">
                    <button class="float-right" @click="back(task['.key'],task.status)">Back</button>
                  </div>
                  <div class="col-md-6">
                    <button class="float-right" @click="next(task['.key'],task.status)">Doing</button>
                  </div>
                </div>
            </div>
          </div>
          </div>
        </div> 
      </div>
      <div class ="col-md 3">
       <div class="card text-white mb-3" style="max-width: 18rem;">
          <div class="card-header bg-primary" style="text-align:left;">Doing</div>
            <div class="card-body">
             <div class="card mb-3" v-for="(task,index) in doing" :key="index" style="max-width: 18rem; color:black;">
              <div class="card-header">{{task.title}}</div>
              <div class="card-body">
                <p class="card-text">{{task.description}}</p>
                <p>Assign to : {{task.assign}}</p>
                <p>Point : {{task.point}}</p>
                <hr>
                <div class="row">
                  <div class="col-md-6">
                    <button class="float-right" @click="back(task['.key'],task.status)">Todo</button>
                  </div>
                  <div class="col-md-6">
                    <button class="float-right" @click="next(task['.key'],task.status)">Done</button>
                  </div>
                </div>
            </div>
          </div>
          </div>
        </div>
      </div>
      <div class ="col-md 3">
        <div class="card text-white mb-3" style="max-width: 18rem;">
          <div class="card-header bg-success" style="text-align:left;">Done</div>
            <div class="card-body">
             <div class="card mb-3" v-for="(task,index) in done" :key="index"  style="max-width: 18rem; color:black;">
              <div class="card-header">{{task.title}}</div>
              <div class="card-body">
                <p class="card-text">{{task.description}}</p>
                <p>Assign to : {{task.assign}}</p>
                <p>Point : {{task.point}}</p>
                <hr>
                <div class="row">
                  <div class="col-md-6">
                    <button class="float-right" @click="back(task['.key'],task.status)">Doing</button>
                  </div>
                  <div class="col-md-6">
                  </div>
                </div>
            </div>
          </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import * as firebase from 'firebase'

 var config = {
    databaseURL: "https://nth-cumulus-197904.firebaseio.com",
    projectId: "nth-cumulus-197904",
  };

  firebase.initializeApp(config);
  var db = firebase.database()

export default {
 data: function(){
   return {
      title: '',
      description: '',
      point: '',
      assign: ''
    }
   },
   firebase :{
     items: db.ref('kanban')
   },
   computed: {
     backlog(){
       return this.items.filter(data=> data.status === 0)
     },
     todo(){
       return this.items.filter(data => data.status === 1)
     },
     doing(){
       return this.items.filter(data => data.status === 2)
     },
     done(){
       return this.items.filter(data => data.status === 3)
     }
   },
   methods:{
     addTask (){
       db.ref('kanban').push({
         title : this.title, 
         description: this.description,
         point : this.point,
         assign: this.assign,
         status : 0
       })
       this.title = ''
       this.description = ''
       this.point = ''
       this.assign = ''
     },
     next(index,status){
       db.ref('kanban').child(index)
       .child('status')
       .set(status+1)
     },
     back(index,status){
       db.ref('kanban').child(index)
       .child('status')
       .set(status-1)
     }
   }
 }

</script>

<style>

</style>
