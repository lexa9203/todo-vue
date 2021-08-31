<template>

      
    <v-card>
      
        <v-text-field
            v-model="newTask"
            class="pa-3 mb-n10"
            outlined
            label="Add task"
            append-icon="mdi-plus"
            @click:append="addTask()"
            @keydown.enter="addTask()"
            clearable
        >
            <v-icon>mdi-plus</v-icon>
        </v-text-field>

        <v-list dense>
            <v-list-item-group
                color="primary"
            >
                <v-list-item :class="{'blue lighten-5' : task.done}" v-for="task in tasks" :key="task.id">
                    <v-list-item-icon>
                        <v-checkbox @click="doneTask(task)" class="my-n1"></v-checkbox>
                    </v-list-item-icon>
                    <v-list-item-content>
                        <v-list-item-title
                          :class="{'text-decoration-line-through' : task.done}"
                            v-text="task.text"
                        ></v-list-item-title>
                    </v-list-item-content>
                    <v-icon v-if="!task.done" class="mr-2 .rounded-circle" @click="importantTask(task)" :class="{'red' : task.flag}">mdi-bookmark</v-icon>
                    <v-icon @click="deleteTasks(task.id)">mdi-close</v-icon>
                </v-list-item>


                
            </v-list-item-group>
        </v-list>
        <v-divider></v-divider>


        <v-list dense v-if="doneTasks.length>0">
          <v-list-item-title v-if="doneTasks.length==1">Done task </v-list-item-title>
          <v-list-item-title v-if="doneTasks.length>1">Done tasks </v-list-item-title>
            <v-list-item-group
                color="primary"
            >
                <v-list-item :class="{'blue lighten-5' : task.done}" v-for="task in doneTasks" :key="task.id">
                    <v-list-item-icon>
                        <v-checkbox @click="returnTask(task)" input-value="true" class="my-n1"></v-checkbox>
                    </v-list-item-icon>
                    <v-list-item-content>
                        <v-list-item-title
                          :class="{'text-decoration-line-through' : task.done}"
                            v-text="task.text"
                        ></v-list-item-title>
                    </v-list-item-content>
                    <v-icon @click="deleteDoneTasks(task.id)">mdi-close</v-icon>
                </v-list-item>


                
            </v-list-item-group>
        </v-list>
    </v-card>
</template>

<script>
export default {
    name: "Home",
   
    data() {
        return {
            newTask: null,
            tasks: [],
            doneTasks: [],
            importantTasks:[],
        };
    },
    created() {
        const tasksData = localStorage.getItem("tasks");
        if (tasksData) {
            this.tasks = JSON.parse(tasksData);
        }
    },
    methods: {
        addTask() {
            const task = {
                id: Date.now(),
                text: this.newTask,
                done: false,
                flag: false
            };
            this.tasks.push(task);
            localStorage.setItem("tasks", JSON.stringify(this.tasks));
            this.newTask = "";
        },
        deleteTasks(id) {
          this.tasks = this.tasks.filter((task) => {
            return task.id !== id;
          });
        },
        deleteDoneTasks(id) {
          this.doneTasks = this.doneTasks.filter((task) => {
            return task.id !== id;
          });
        },
        importantTask(task) {
          task.flag = !task.flag;
          this.importantTasks.push(task);
          console.log(task, this.importantTasks);
        },
        doneTask(task) {
          task.flag = false;
          task.done = true;
          this.doneTasks.push(task);
          this.tasks = this.tasks.filter((el) => {
            return task.id  !== el.id;
          });
          console.log(this.tasks);
        },
        returnTask(task){
          task.done = false;
          this.tasks.push(task);
          this.doneTasks = this.doneTasks.filter((el) => {
            return task.id !== el.id;
          });
        }
        
    }
};
</script>
