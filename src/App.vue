<template>

<div class="container my-5">
<div class="alert alert-success" v-if="user" >
{{user.username}} is logged in 
{{ postForm.data.title }} {{ postForm.data.body }}
   <form @submit.prevent="savePost">      
                <div class="form-group my-3">
                    <label for="title">title</label>
                    <input type="text" class="form-control my-3" id="title" v-model="postForm.data.title">
                    <small id="titleHelp" class="form-text text-muted">please enter your title</small>
                </div>
                <div class="form-group my-3">
                    <label for="body">body</label>
                  
                    <textarea name="" class="form-control" id="" cols="30" v-model="postForm.data.body" rows="10"></textarea>
                </div>
                <button type="submit" class="btn btn-primary">Submit</button>
                <template  v-if="errorsText" >
                  <p class="alert alert-danger" v-for="error in errorsText" :key="error">{{ error.message }}</p>
                </template>
                
   </form>
</div>
<Login
@user-logged-in = "userLoggedIn"
 v-else />
<Posts
 :posts="state.posts"
 @delete-item = "deletePost"
  />
  
</div>


</template>

<script>
// https://docs.strapi.io/developer-docs/latest/guides/auth-request.html#login-as-a-reader
import Posts from './components/Posts.vue'
import Login from './components/Login.vue'
import axios from './plugins/axios.js'
import {  reactive, ref } from 'vue'
 
export default {

name : "App" ,
components : {
      Posts,
      Login
    },

setup(){
  const token = ref('')
  const user = ref(JSON.parse(localStorage.getItem('user'))) 
  const postForm = reactive({data:{title : null ,body: null }}) 
  const errorsText  = ref(null)
  const state =  reactive({posts : []})
  //const  posts  = reactive([''])
  //const data = ref('')
  token.value = localStorage.getItem('token')
 
  const getPost = async () =>{
        const {data:{data}} = await axios.get('/posts')
        
        console.log('raw array',data);
        //state.posts.push(...data)
        state.posts = data 
        //console.log(data);      
        console.log('with push',state.posts);      
  }
  const userLoggedIn = (userInfo)=>{
     user.value = userInfo 
  }
  const savePost = async() => {

   try{
     const { data:{data} } = await axios.post('/posts' , postForm)
     
     state.posts.push(data)
     postForm.data.title = ""
     postForm.data.body = ""
    // getPost();
   }catch({response:{data:{error:{details:{errors}}}}}){
      console.log(errors);
      errorsText.value = errors
   }


  }
  const sendUserInfo = async() => {
  
  try{
   const { data } = await axios.get('/users/me')
  }catch(error){
     console.log('we have error');
     localStorage.removeItem('token')
     localStorage.removeItem('user')
     delete axios.defaults.headers['Authorization']
     user.value = null
  }
   
  }

  const deletePost = async(id,index) => {
    
    axios.delete(`/posts/${id}`)
    state.posts.splice(index,1)
  }
 

  sendUserInfo()
  getPost();
  return{
    user,
    token,
    sendUserInfo,
    userLoggedIn,
    savePost,
    postForm,
    state,
    errorsText,
    deletePost
    
  }
  
}    


}


</script>
