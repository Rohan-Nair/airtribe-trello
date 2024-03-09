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
                                <v-dialog v-model="addtaskdialog">
                                    <v-container class="d-flex flex-column justify-center align-center w-50" :style="board.color == '' ? `background-color:white` : `background-color:${board.color}`" >
                                        <p class="text-h4 rounded px-2">
                                            Add a task
                                        </p>
                                        <v-text-field
                                            label="Task title"
                                            name="title"
                                            type="text"
                                            :rules="[(title) => !!title || 'Task title is required']"
                                            required  
                                            v-model="task.title"
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
                                            :value="'To Do'"
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
                <v-snackbar
                    :timeout = "3000"
                    v-model = "snackbaron"
                    absolute
                    bottom 
                    color="primary"
                >
                    Successful
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
            addtaskdialog: false,
            snackbaron: false, 
            dialogopen: false, 
            board: {
                title: '', 
                color: ''
            }, 
            task: {
                id: '',
                title:'',
                description: '',
                status: '',
                color: ''
            }
        }
    }, 
    methods:{
        addtask(){
            this.task.id = uuidv4(),
            this.snackbaron=true,
            this.addtaskdialog=false   
        }

    }
}
</script>