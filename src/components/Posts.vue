<template>
  <div class="container my-5">
    <div class="row">
           <div class="card" style="width: 18rem;" v-for="(post , index ) in posts" :key="post.id ">
       
        <div class="card-body">
                   <h5 class="card-title">{{ post.id }}</h5>
                   <p class="card-text">{{ post.attributes.title }}</p>
                   <p class="card-text">{{ post.attributes.body }}</p>
                   <button href="#" @click="deleteItem(post.id , index)"  class="btn btn-primary">delete</button>
                   

        </div>
      </div>
    </div>
  

  </div>
</template>

<script>
export default {
name:"Posts",

props :{
  posts : {
    type: Object,
    required: true ,
  }
}
,
setup(props, {emit}) {
  
 const deleteItem = (id , index) =>{
   
   emit('delete-item' , id , index )

 } 


 return{
   deleteItem
 }
 
}


   
}
</script>

<style>

</style>