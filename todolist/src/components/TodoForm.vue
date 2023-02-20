<template>
    <div class="container">
        <div v-if="loading">Loading...</div>
        <form v-else @submit.prevent="onSave">
            <div class="row1">
                <div>
                    <div class="form-group">
                        <label>todo subject</label>
                        <input type="text" class="form-control" v-model="todo.subject">
                        <div v-if="subjectError">{{ subjectError }}</div>
                    </div>
                </div>
                <div v-if="editing">
                    <div class="form-group">
                        <label>status</label>
                      <div>
                        <button @click="toggleTodoStatus" type="button" class="ing" :class=" todo.completed ? 'completed' : 'ing'">{{ todo.completed ? 'completed' : 'ing...'}}</button>
                        </div>
                    </div>
                </div>
            </div>
            <div class="bodyWrap">
                <div class="form-group">
                    <label >Body</label>
                    <textarea v-model="todo.body" cols="30" rows="10"></textarea>
                </div>
            </div>
            <button class="btn btnmt" type ="submit" :disabled="!todoUpdated">{{ editing ? 'save' : 'create' }}</button>
            <button class="btn btnW btnL" @click = "moveTodoListPage">cancel</button>
        </form>
    </div>
</template>

<script>
import {useRoute, useRouter} from 'vue-router';
import axios from 'axios';
import { ref,computed } from 'vue';
import _ from 'lodash';
export default {
    props:{
        editing :{
            type :Boolean,
            default : false
        }
    },
    setup(props){
        const route =useRoute();
        const router =useRouter();
        const subjectError = ref('');
        const todo =ref({
            subject : '',
            completed : false,
            bodt : ''
        })
        const loading = ref(true);
        const todoId =route.params.id;
        const originalTodo = ref(' ')
        const getTodo = async () =>{
            const res = await axios.get(`http://localhost:3000/toods/${todoId}`);
            todo.value=res.data;
            originalTodo.value={...res.data}
            loading.value =false;
        }
        const toggleTodoStatus = () =>{
            todo.value.completed = !todo.value.completed
        }
        const moveTodoListPage = () =>{
            router.push({
                name : 'Todos'
            })
        }
        if(props.editing){
             getTodo();
        }
       

        const onSave = async () =>{
            if(!todo.value.subject){
                subjectError.value='Please add list'
                return;
            }
            try{
                let res;
                const data={
                    subject : todo.value.subject,
                    completed : todo.value.completed,
                    body : todo.value.body,
                }
                if(props.editing){
                     res = await axios.put(`http://localhost:3000/toods/${todoId}`,data)
                }else{
                    res = await axios.post(`http://localhost:3000/toods`,data);
                    todo.value.subject= "";
                    todo.value.body ="";
                }
                 originalTodo.value={...res.data}
            }catch(error){
                console.log(error)
            }
        }

        const todoUpdated = computed(()=>{
            return !_.isEqual(todo.value, originalTodo.value)
        })
        return{
            todo,
            toggleTodoStatus,
            moveTodoListPage,
            onSave,
            todoUpdated,
            subjectError,   
        }
    }
}
</script>
<style>
h1{margin-top: 30px; margin-bottom: 20px;}
.form-group{
    display: flex;
    flex-direction: column;
}
.form-group label{font-weight: bold; margin-bottom: 5px;}
.form-group .form-control{padding: 10px 20px;}
.btnmt{margin-top: 10px;}
.row1{display: flex; justify-content: space-between;}
.row1>div{flex-basis: 49%;}
.ing{padding: 10px 30px; background: #979f8a; border: none;  color: #fff;}
.completed{background: #3f4f23;}
.btnW{background: #fff; color: #3f4f23;}
.btnL{margin-left: 10px;}
</style>
<style scoped>
    .row1{margin-bottom: 20px;}
</style>
