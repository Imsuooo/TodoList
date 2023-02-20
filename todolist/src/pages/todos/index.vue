<template>
  <div class="container">
    <h2>TODO-List</h2>
    <button class="btn btnB" @click="moveToCreatePage">create todo</button>
    <input type="text" placeholder="search" class="form-control" v-model="searchText">
   <!--  <TodoSimpleForm @addTodo="addTodo" /> -->
    <div v-if="!todos.length" style="color:#7c8173">
      Not Found
    </div>
  <TodoList :todos="todos" @delete-todo="deleteTodo" @toggle-todo="toggleTodo"/>
  <br>
  <nav class="nav">
    <ul class="pagenation">
      <li v-if="currentpage!==1" class="page-item"><a href="#" class="page-link" @click="getTodos(currentpage-1)">👈</a></li>
      <li v-for="page in numberofpages" :key="page" class="page-item"><a href="#" class="page-link"  :class="currentpage===page ? 'active' : ''" @click="getTodos(page)">{{ page }}</a></li>
      <li v-if="numberofpages!==currentpage" class="page-item"><a href="#" class="page-link" @click="getTodos(currentpage+1)">👉</a></li>
    </ul>
  </nav>
  </div>
</template>

<script>
import { computed, ref, watch } from 'vue';
/* import TodoSimpleForm from '@/components/TodoSimpleForm.vue'; */
import TodoList from '@/components/TodoList.vue';
import axios from 'axios';
import { useRouter } from 'vue-router';
export default {
  components : {
    /* TodoSimpleForm, */
    TodoList,
  },
  setup(){
    const router = useRouter();
    const todos = ref([ ]);
    const searchText =ref('');
    const error =ref('');
    const limit =5;
    const numberoftodos = ref(0);
    const currentpage = ref(1)
    const numberofpages = computed(()=>{
      return Math.ceil(numberoftodos.value/limit)
     });

    const getTodos = async (page = currentpage.value) => {
        currentpage.value =page;
      try{
         const res=await axios.get(`http://localhost:3000/toods?_sort=id&_order=desc&subject_like=${searchText.value}&_page=${page}&_limit=${limit}`);

         numberoftodos.value=res.headers['x-total-count'];
          todos.value=res.data;
      }catch(err){
          console.log(err);
          error.value="Not Found"
      }

    };
    getTodos();
    const moveToCreatePage =() =>{
      router.push({
        name : 'TodoCreate',
      })
    }
    const addTodo = async(todo)=>{
      error.value='';
      try{
        await axios.post('http://localhost:3000/toods',{
           subject:todo.subject,
          completed : todo.completed,
        });
        getTodos(1);
      }catch (err){
        console.log(err)
        error.value="error"
      }
    }
    watch(searchText, () =>{
      getTodos(1);
    })
    const deleteTodo = async (index)=>{
       error.value=" ";
      const id = todos.value[index].id;
      try{
        await axios.delete('http://localhost:3000/toods/' + id);
       getTodos(1);
      }catch(err){
         console.log(err)
         error.value="error"
      }
    };
    const toggleTodo = async (index, checked) =>{
      error.value=" ";
      const id = todos.value[index].id;
      try{
        await axios.patch('http://localhost:3000/toods/' + id, {
          completed : !todos.value[index].completed
        });
        todos.value[index].completed = checked
      }catch(err){
        console.log(err);
        error.value="Not Found"
      }
      todos.value[index].completed = checked
    }
    return{
      todos,
      deleteTodo,
      addTodo,
      toggleTodo,
      searchText,
      getTodos,
      numberofpages,
      currentpage,
      numberoftodos,
      moveToCreatePage,
    }
  }
}
</script>

<style>
  *{margin: 0; padding: 0; box-sizing: border-box;}
  body{ background: #f2f1e9;}
  .container{widows: 100%; max-width: 1024px; margin: 0 auto; padding: 0 20px;}
  h2{text-align: center; color: #3f4f23; margin-bottom: 50px; margin-top: 50px;}
  .d-flex{display: flex;}
  .flex-grow-1{flex-grow: 1;}
  .flex-grow-1 input{width: 95%; padding: 10px 20px;}
  .btn{padding: 10px 30px; border: none; background: #3c501a; color: #fff; cursor: pointer;}
  form{margin-bottom: 20px;}
  .card{margin: 10px 0;}
  .card-body{border:  1px solid #c0cdab; padding: 10px 20px; background: rgba(255,255,255,.5); display: flex; align-items: center; justify-content: space-between;}
  .form-check-input{margin-right: 10px;}
  .todoStyle{text-decoration: line-through; color: #c0cdab;}
  .btnG{padding: 8px 10px; background: #3f4f23; color: #fff; font-weight: bold; border: none; cursor: pointer;}
  .form-control{width: 100%; border: 1px solid #ddd; margin-bottom: 10px; padding: 10px 20px;}
  .pagenation{list-style: none; display: flex; align-items: center; justify-content: center;}
  .page-link{text-decoration: none; font-size: 20px; color: #3c501a; padding: 4px 10px; margin: 5px 10px; border: 1px solid #c0cdab;}
  .active{background: #3f4f23; color: #fff;}
  .btnB{margin-bottom: 10px;}
</style>
