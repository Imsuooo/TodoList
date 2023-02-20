<template>
     <form @submit.prevent="onSubmit">
        <div class="d-flex">
            <div class="flex-grow-1">
            <input type="text" placeholder="Todo" v-model="todo" style="backgroundColor : #fbfbf8">
            </div>
            <div class="b-btn">
            <button class="btn" type="submit">ADD</button>
            </div>
        </div>
        <div style="color:#7c8173" v-show="hasError">
        Please Add 
        </div>
     </form>
</template>

<script>
import { ref } from 'vue';
export default {
    emits:['addTodo'],
    setup(props,context){
        const todo = ref('');
        const hasError = ref(false);
        /* const todoStyle ={
        color : '#c0cdab',
        textDecoration : 'line-through'
        } */
        const onSubmit = ()=>{
        if(todo.value===""){
            hasError.value=true;
        }else{
            context.emit('addTodo',{
                id: Date.now(),
                subject: todo.value,
                completed : false,
                }
            )
            hasError.value=false;
            todo.value=""
         }
        };
        return{
            todo,
            hasError,
            onSubmit,
        }
    }
}
</script>

<style>

</style>