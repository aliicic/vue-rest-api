<template>

   <div class="container my-5">
       <div class="row">
          value : {{ userForm }}
          <div v-if="textError">
           
              <p class="alert alert-danger mt-5">{{ textError }}</p>
          </div>
                <form @submit.prevent="login">      
                <div class="form-group my-3">
                    <label for="username">Username</label>
                    <input type="text" class="form-control my-3" id="username" v-model="userForm.identifier">
                    <small id="usernameHelp" class="form-text text-muted">please enter your username</small>
                </div>
                <div class="form-group my-3">
                    <label for="password">Password</label>
                    <input type="password" class="form-control" id="password" v-model="userForm.password">
                </div>
                <button type="submit" class="btn btn-primary">Submit</button>
                </form>
       </div>
       </div>   

</template>

<script>
import { reactive ,ref } from 'vue'
import axios from '../plugins/axios.js'
export default {
 name  : 'Login'
 ,
 setup(props , {emit}){

  const userForm = reactive(
      {
      identifier : 'icic',
      password  : 'Password1234',
      })
  const textError = ref('')    
  
  const login =async ()=> {
  
  try{
     
  const {data} = await axios.post('/auth/local', userForm) 
  //console.log(data);
  localStorage.setItem('token' , data.jwt)
  localStorage.setItem('user' ,  JSON.stringify(data.user) )
  emit('user-logged-in', data.user)
  //console.log(error);
  }catch({response:{data:{error:{message}}}}){
      textError.value = message
  }
//   }catch(error){
//       console.log('error message',error.response);
//   }
   
 
 
 }


  return{
      login,
      userForm,
      textError
      
  }
}
}
</script>

<style>

</style>