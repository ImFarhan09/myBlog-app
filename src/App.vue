<script>
import "bootstrap/dist/css/bootstrap.min.css";
import "font-awesome/css/font-awesome.min.css";
import "animate.css";
import axios from "axios";

export default {
  name: 'App',
  data(){
    return{
      id:"",
      cardIndex:"",
      save: false,
      userId:'',
      title:'',
      body:'',
      response: [],
      loader: false
    }
  },
  methods: {
    async post(e){
      e.preventDefault();
      this.loader = true;
      try {
        const response = await axios({
        method:"post",
        url:"https://jsonplaceholder.typicode.com/posts/",
        data: {
          userId:this.userId,
          title: this.title,
          body: this.body
        }
      });
      this.loader= false;
      this.response.push(response.data);
      this.userId="";
      this.title="";
      this.body="";
      }
      catch(err){
        this.loader= false;
        alert('Something is not right');
      }
    },
    async deleteClick(data,index){
      this.loader = true;
      let string = JSON.stringify(data);
      let deleteData = JSON.parse(string);
      let id = deleteData.id;
      try {
        await axios({
        method:"delete",
        url:"https://jsonplaceholder.typicode.com/posts/"+ id,
      });
      this.loader= false;
      this.response.splice(index,1);
      }
      catch(err){
        this.loader= false;
        alert('Something is not right');
      }
    },
    edit(data,index){
      this.save = true;
      let string = JSON.stringify(data);
      const editData = JSON.parse(string);
      this.userId=editData.userId;
      this.title=editData.title;
      this.body=editData.body;
      this.cardIndex=index;
      this.id=editData.id;
    },
    reset(){
      this.userId="";
      this.title="";
      this.body="";
      this.save = false;
    },
    async update(e){
      e.preventDefault();
      this.loader = true;
      try {
        const response = await axios({
        method:"put",
        url:"https://jsonplaceholder.typicode.com/posts/1",
        data: {
          id:this.id,
          userId:this.userId,
          title: this.title,
          body: this.body
        }
      });
      this.loader= false;
      this.response[this.cardIndex] = response.data;
      this.userId="";
      this.title="";
      this.body="";
      this.save = false;
      }
      catch(err){
        this.loader= false;
        alert('Something is not right');
      }
    }
  }
}
</script>

<template>
  <div class="container-fluid bg-light min-vh-100">
    <div class="row">
      <div class="col-md-3 bg-dark min-vh-100 p-4">
        <h4 class="text-light mb-4">VueJS Blog App</h4>
        <form @submit="(event) => save? update(event):post(event)">
            <input type="number" placeholder="User Id" class="form-control mb-3" v-model="userId" />
            <input type="text" placeholder="Title" class="form-control mb-3" v-model="title" />
            <textarea placeholder="Post" class="form-control mb-3" v-model="body"></textarea>
            <button type="submit" class="btn btn-success" v-if="!save">CREATE</button>
            <button type="submit" class="btn btn-primary me-2" v-if="save">SAVE</button>
            <button type="button" class="btn btn-warning" v-if="save" @click="reset">RESET</button>
        </form>
      </div>
      <div class="col-md-9 p-4 overflow-hidden">
        <div class="card shadow-sm mb-4 animate__animated animate__backInRight animate__faster" v-for="(data,index) in response" :key="index">
          <div class="card-header d-flex justify-content-between"> 
            {{ data.title }}
            <div class="d-flex align-items-center">
              <button class="btn" @click="edit(data,index)"><i class="fa fa-edit text-info"></i></button>
              <button class="btn" @click="deleteClick(data,index)"><i class="fa fa-trash text-danger"></i></button>
            </div>
          </div>
          <div class="card-body"> {{ data.body }}</div>
        </div>
        <i v-if="loader" class="fa fa-spinner fa-spin" style="font-size:40px"></i>
      </div>
    </div>
  </div>
</template>