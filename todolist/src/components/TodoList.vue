<template>
    <div>
         <div v-for="(todo, index) in todos" :key="todo.id" class="card">
                <div class="card-body" @click="moveToPage(todo.id)">
                <div class="form-check">
                    <input type="checkbox" class="form-check-input" :checked="todo.completed" @change="toggleTodo(index,$event)" @click.stop>
                    <label class="form-check-label" :class="{todoStyle:todo.completed}">
                        {{ todo.subject }}
                    </label>
                </div>
                <div>
                    <button class="btnG" @click.stop="deleteTodo(index)">X</button>
                </div>
                </div>
          </div>
    </div>
</template>

<script>
import{useRouter} from 'vue-router'
    export default {
        props : {
           todos:{
             type : Array,
            required : true
           }
        },
        emits : ['toggle-todo','delete-todo'],
         setup(props,{emit}){
            const router =useRouter();
            const toggleTodo = (index, event) =>{
               emit('toggle-todo', index, event.target.checked)
            }
            const deleteTodo = (index) =>{
                emit('delete-todo',index)
            }
            const moveToPage = (todoId) =>{
                console.log(todoId)
                router.push({
                    name : 'Todo',
                    params :{
                        id: todoId
                    }
                })
            }
            return {
                deleteTodo,
                toggleTodo,
                moveToPage,
            }
    }
    }
</script>
