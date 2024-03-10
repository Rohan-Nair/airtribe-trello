<template>
    <v-app>
        <v-main :style="board.color == '' ? `background-color:white` : `background-color:${board.color}`" >
            <v-toolbar class="bg-green lighten-3"  :elevation="5" app> 
                <v-toolbar-title class="headline">
                    <p class="text-black font-bold text-h4">Trello</p>
                </v-toolbar-title>
            </v-toolbar>
            <v-container class="h-screen">
                <v-form ref="form">
                    <div class="d-flex flex-column">
                        <v-text-field
                            label="Board title"
                            name="title"
                            type="text"
                            :rules="[(title) => !!title || 'Board title is required']"
                            required  
                        />
                        <v-container class="d-flex flex-row justify-center">
                            <v-btn class="bg-black w-50 mx-5" @click="dialogopen=true">Change Board Colour</v-btn>
                                <v-dialog v-model="dialogopen" class="w-50">
                                    <v-container class="d-flex flex-column justify-center align-center w-50 bg-black">
                                        <p class="text-h4  text-white rounded px-2">
                                            Choose board color
                                        </p>
                                
                                        <v-color-picker
                                            dot-size="20"
                                            hide-inputs
                                            swatches-max-height="100"
                                            :elevation="4"
                                            class="bg-black align-self-center mb-5 mt-2"
                                            v-model="board.color"
                                        />
                                        <v-btn @click="snackbaron=true, dialogopen=false" class="bg-black text-white" :elevation="4"> 
                                        Submit
                                        </v-btn>
                                    </v-container>
                                </v-dialog>
                            <v-btn class="bg-black w-50 mx-5" @click="addtaskdialog=true">Add Task</v-btn>
                                <v-dialog v-model="addtaskdialog" class="w-50">
                                    <v-container class="d-flex flex-column justify-center align-center w-100" :style="board.color == '' ? `background-color:white` : `background-color:${board.color}`" >
                                        <p class="text-h4 rounded px-2">
                                            Add a task
                                        </p>
                                        <v-text-field
                                            label="Task title"
                                            name="title"
                                            type="text"
                                            :rules="[(title) => !!title || 'Task title is required']"
                                            required  
                                            v-model="task.tasktitle"
                                            class="w-50"
                                            
                                        />
                                        <v-text-field
                                            label="Task description"
                                            name="description"
                                            type="text"
                                            :rules="[(description) => !!description || 'Task description is required']"
                                            required  
                                            class="w-50"
                                            v-model="task.description"
                                            auto-grow
                                        />
                                        <v-select
                                            label="Status"
                                            name="status"
                                            class="w-50"
                                            :items="['To Do', 'In Progress', 'Done']"
                                            v-model="task.status"
                                            required
                                        />
                                        <v-color-picker
                                            dot-size="20"
                                            hide-inputs
                                            swatches-max-height="100"
                                            :elevation="4"
                                            class="bg-black align-self-center mb-5 mt-2"
                                            v-model="task.color"
                                        />
                                        <v-btn @click="addtask" class="bg-black text-white" :elevation="4">
                                            Submit
                                        </v-btn>
                                    </v-container>
                                </v-dialog>
                        </v-container>
                    </div>
                </v-form>
                <v-container class="d-flex flex-row justify-space-evenly align-start">
                    <!-- Column for tasks with status "To Do" -->
                    <v-col cols="12" md="4" lg="3" class="d-flex flex-column align-center">
                        <h2
                        @drop="drop($event, 'To Do')"
                        @dragover="allowDrop($event)"
                        >To Do</h2> <!-- Heading for "To Do" column -->
                        <v-row>
                            <v-col
                                v-for="task in alltasks.filter(t => t.status === 'To Do')"
                                :key="task.id"
                                class="d-flex flex-column justify-center align-center"
                                @drop="drop($event, 'To Do')"
                                @dragover="allowDrop($event)"
                            >
                                <v-card
                                    class="mx-5 my-2"
                                    :color="task.color"
                                    :elevation="5"
                                    :draggable="true"
                                    @dragstart="drag($event, task)"
                                    @dragover.prevent
                                >
                                    <v-card-title class="text-h6">
                                        {{ task.tasktitle }}
                                    </v-card-title>
                                    <v-card-text>
                                        {{ task.description }}
                                    </v-card-text>
                                    <v-card-actions>
                                        <v-btn class="mx-5" @click="editTask(task)">Edit</v-btn>
                                        <v-btn class="mx-5" @click="deletetask(task)">Delete</v-btn>
                                    </v-card-actions>
                                </v-card>
                            </v-col>
                        </v-row>
                    </v-col>

                    <!-- Column for tasks with status "In Progress" -->
                    <v-col cols="12" md="4" lg="3" class="d-flex flex-column align-center">
                        <h2
                        @drop="drop($event, 'In Progress')"
                        @dragover="allowDrop($event)">In Progress</h2> <!-- Heading for "In Progress" column -->
                        <v-row>
                            <v-col
                                v-for="task in alltasks.filter(t => t.status === 'In Progress')"
                                :key="task.id"
                                class="d-flex flex-column justify-center align-center"
                                @drop="drop($event, 'In Progress')"
                                @dragover="allowDrop($event)"
                            >
                                <v-card
                                    class="mx-5 my-2"
                                    :color="task.color"
                                    :elevation="5"
                                    :draggable="true"
                                    @dragstart="drag($event, task)"
                                    @dragover.prevent
                                >
                                    <v-card-title class="text-h6">
                                        {{ task.tasktitle }}
                                    </v-card-title>
                                    <v-card-text>
                                        {{ task.description }}
                                    </v-card-text>
                                    <v-card-actions>
                                        <v-btn class="mx-5" @click="editTask(task)">Edit</v-btn>
                                        <v-btn class="mx-5" @click="deletetask(task)">Delete</v-btn>
                                    </v-card-actions>
                                </v-card>
                            </v-col>
                        </v-row>
                    </v-col>

                    <!-- Column for tasks with status "Done" -->
                    <v-col cols="12" md="4" lg="3"class="d-flex flex-column align-center">
                        <h2
                        @drop="drop($event, 'Done')"
                        @dragover="allowDrop($event)">Done</h2> <!-- Heading for "Done" column -->
                        <v-row>
                            <v-col
                                v-for="task in alltasks.filter(t => t.status === 'Done')"
                                :key="task.id"
                                class="d-flex flex-column justify-center align-center"
                                @drop="drop($event, 'Done')"
                                @dragover="allowDrop($event)"
                            >
                                <v-card
                                    class="mx-5 my-2"
                                    :color="task.color"
                                    :elevation="5"
                                    :draggable="true"
                                    @dragstart="drag($event, task)"
                                    @dragover.prevent
                                >
                                    <v-card-title class="text-h6">
                                        {{ task.tasktitle }}
                                    </v-card-title>
                                    <v-card-text>
                                        {{ task.description }}
                                    </v-card-text>
                                    <v-card-actions>
                                        <v-btn class="mx-5" @click="editTask(task)">Edit</v-btn>
                                        <v-btn class="mx-5" @click="deletetask(task)">Delete</v-btn>
                                    </v-card-actions>
                                </v-card>
                            </v-col>
                        </v-row>
                    </v-col>
                </v-container>

                
 

                <v-snackbar
                    :timeout = "3000"
                    v-model = "snackbaron"
                    absolute
                    bottom 
                    color="primary"
                >
                    {{ snackbartext }}
                </v-snackbar>
        </v-container> 
      </v-main>
    </v-app>
</template>

<script>
import { v4 as uuidv4} from 'uuid'
export default{
    data(){
        return {
            snackbartext: 'Successful',
            addtaskdialog: false,
            snackbaron: false, 
            dialogopen: false, 
            board: {
                title: '', 
                color: ''
            }, 
            task: {
                id: '',
                tasktitle:'',
                description: '',
                status: '',
                color: ''
            }, 
            alltasks: [],
        }
    }, 
    mounted(){
        if (typeof window !== 'undefined'){
            this.alltasks = JSON.parse(window.localStorage.getItem('tasks')) || [];
        }
    },
    methods:{
        addtask(){
            if (this.task.id !== ''){
                const index = this.alltasks.findIndex(task => task.id === this.task.id);
                this.alltasks[index] = this.task;
                localStorage.setItem('tasks', JSON.stringify(this.alltasks)); 
                this.task = {
                    id: '',
                    tasktitle:'',
                    description: '',
                    status: '',
                    color: ''
                }
                this.addtaskdialog=false; 
                this.snackbartext = 'Task updated successfully'; 
                this.snackbaron = true; 
                return;
            }
            this.task.id = uuidv4();
            if(this.task.tasktitle == '' || this.task.description == '' || this.task.status == ''){
                this.snackbartext = 'Please fill in all fields'; 
                this.snackbaron = true; 
                return;
            }
            this.alltasks.push(this.task); 
            localStorage.setItem('tasks', JSON.stringify(this.alltasks)); 
            this.task = {
                id: '',
                tasktitle:'',
                description: '',
                status: '',
                color: ''
            }
            this.addtaskdialog=false;   
            this.snackbaron=true; 
        }, 
        deletetask(id){
            this.alltasks = this.alltasks.filter(task => task.id !== id.id);
            localStorage.setItem('tasks', JSON.stringify(this.alltasks)); 
            this.snackbartext = 'Task deleted successfully'; 
            this.snackbaron = true; 
        },
        editTask(incomingtask){
            this.task = {...incomingtask, id: incomingtask.id};
            this.addtaskdialog = true;
        }, 
        allowDrop(ev){
            ev.preventDefault();
        },
        drop(ev, status){
            ev.preventDefault();
            const task = JSON.parse(ev.dataTransfer.getData('task'));
            task.status = status;
            const index = this.alltasks.findIndex(t => t.id === task.id);
            this.alltasks[index] = task;
            localStorage.setItem('tasks', JSON.stringify(this.alltasks)); 
        },
        drag(ev, task){
            ev.dataTransfer.setData('task', JSON.stringify(task));
        }


    }
}
</script>