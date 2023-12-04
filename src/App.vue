<template>
  <div class="wraper" >
    <div>
      <h1>ToDo List</h1>
      <input class="input_name" type="text" placeholder="Add a task..." v-model="title" @keyup.enter="onAddTask">
      <button @click="onAddTask" class="AddButton">Add</button>
      <div class="input_buttons" v-if="tasks.length > 1">  
        <button @click="removeAllTasks">Delete all</button>
        <button @click="completeAllTasks">Complete all</button>
      </div>
    </div>
    <div v-if="tasks.length > 0">  
      <div v-for="(task, index) in tasks" :key="task.id" >
        <div class="task-info">
          <div class="task-title">
            <h3 :style="{ textDecoration: task.status ? 'line-through' : 'none', color: task.status ? 'grey' : 'black' }">
              <label>
                <input type="checkbox" :checked="task.status" @change="toggleTaskStatus(index)"/>
                <span></span>
              </label> 
              {{ task.title }}
            </h3>
            <p class="description">{{ task.description }}</p>
            <input type="text" v-model="tasks[index].description" @keyup.enter="editDescription" placeholder="Add a description" v-if="showInput === index"/>          
          </div>
          <div class="options-popup">
            <button  @click="toggleInput(index)">Edit</button>
            <button @click="removeTask(task.id)">Delete</button>
          </div>
        </div> 
      </div>
    </div>
    <div v-else>
      <p>No tasks</p>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  emits: ['onAddTask', 'onDone', 'onRemove'],
  props: {
    model: {
      required: true,
      default: {
        id: 0,
        title: 'Task 1',
        description: 'Congratulations! This is your first task',
        status: false
      }
    }
  },
  setup(props, { emit }) {
    const title = ref('');
    const description = ref('');
    const tasks = ref([props.model]);

    const onAddTask = () => {
      if (title.value) {
        const newTask = {
          id: tasks.value.length + 1,
          title: title.value,
          description: description.value || '', 
          status: false,
          pinned: false
        };
        tasks.value.push(newTask);
        emit('onAddTask', newTask);
        title.value = '';
        description.value = '';
        } else {
        alert('Please, fill in the title field!');
        }
      };

    const toggleTaskStatus = (index) => {
      tasks.value[index].status = !tasks.value[index].status;
      emit('onDone', tasks.value[index].id);
    };

    const removeTask = (taskId) => {
      const taskIndex = tasks.value.findIndex(task => task.id === taskId);
      if (taskIndex !== -1) {
        tasks.value.splice(taskIndex, 1);
        emit('onRemove', taskId);
      }
    };

    const removeAllTasks = () => {
      tasks.value = [];
    };

    const completeAllTasks = () => {
      tasks.value.forEach(task => {
        task.status = true;
        emit('onDone', task.id);
      });
    };

    const showInput = ref(null);

    const toggleInput = (index) => {
      showInput.value = showInput.value === index ? null : index;
    };

    const editDescription = (index) => {
      showInput.value = index;
    };

    return {
      title,
      description,
      tasks,
      showInput,
      onAddTask,
      toggleTaskStatus,
      removeTask,
      removeAllTasks,
      completeAllTasks,
      toggleInput,
      editDescription,
    };
  }
};
</script>

<style scoped>
.wraper{
  width: 700px;
  overflow-y: auto;
  height: 600px;
  border: 3px solid rgb(0, 0, 100);
  border-radius: 25px;
  background-color: #fff;
  padding: 30px;
  z-index: 0;
  direction: ltr;
  scrollbar-color: #d4aa70 #e4e4e4;
  scrollbar-width: thin;
}
.wraper::-webkit-scrollbar {
  width: 6px;
}

.wraper::-webkit-scrollbar-track {
  background-color: #fff;
  border-radius: 100px;
  margin: 25px;
}

.wraper::-webkit-scrollbar-thumb {
  border-radius: 100px;
  background-image: linear-gradient(180deg, rgb(66, 10, 139) 0%, #5067a7 99%);
}

.wraper h1{
  color: rgb(66, 10, 139);
  font-weight: 700;
  font-size: 35px;
  margin-bottom: 20px;
  text-align: center;
}
input{
  margin-bottom: 15px;
  padding: 5px;
  width: 420px;
  border: none;
  border-bottom: 2px solid rgb(66, 10, 139);
  border-radius: 0;
}
input:focus{
  outline: none;
  border-bottom: 2px solid rgb(102, 102, 195);
}
.input_name{
  font-size: 16px;
  font-weight: 600;
}
.AddButton{
  width: 140px;
  height: 38px;
  color: #fff;
  background-color: rgb(66, 10, 139);
  border: none;
  border-radius: 15px;
  cursor: pointer;
  font-size: 15px;
  font-weight: bold;
  float: inline-end;
}
.AddButton:hover{
  color: rgb(66, 10, 139);
  background-color: rgb(255, 255, 255);
  border: 2px solid rgb(66, 10, 139);
}
.input_buttons button{
  width: 140px;
  height: 37px;
  border-radius: 15px;
  cursor: pointer;
  font-size: 15px;
  color: rgb(66, 10, 139);
  background-color: rgb(255, 255, 255);
  border: 2px solid rgb(66, 10, 139);
  margin: 5px 0px 30px 55px;
  font-weight: 600;
}
.input_buttons button:hover{
  border: 2px solid rgb(125, 125, 225);
}
label input {
  display: none;
}
label span {
  height: 17px;
  width: 17px;
  margin-right: 5px;
  border: 1px solid gray;
  display: inline-block;
  position: relative;
  background-color:#FFF;
  border-radius:2px;
}
[type=checkbox]:checked + span:before {
  content: '\2714';
  position: absolute;
  top: -6px;
  left: 2px;
  font-size:16px;
  color:rgb(66, 10, 139);
}
.description{
  border-bottom: 1px solid rgb(66, 10, 139);
  width: 86%;
  padding: 5px 5px 15px 0px;
  color: #5e5e5e;
}
.task-info{
  padding: 10px 0px;
  z-index: 0;
  display: flex;
}
.task-title{
  width: 76%;
}
.options-popup{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: flex-start;
}
.options-popup button{
  background-color: #fff;
  border: none;
  border-left: 3px solid rgb(66, 10, 139);
  cursor: pointer;
  font-size: 16px;
  margin-left: 15px;
  height: 20px;
  width: 80px;
  text-align: start;
  padding: 5px;
  color: #5e5e5e;
}
.options-popup button:hover{
  color: #000;
}
</style>


