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
        </v-text-field>
        <v-divider v-if="tasks.length > 0" class="mt-3"></v-divider>
        <v-list dense>
            <v-list-item-group color="primary">
                <v-slide-y-transition class="py-0" group>
                    <v-list-item
                        :class="{ 'blue lighten-5': task.done }"
                        v-for="task in tasks"
                        :key="task.id"
                    >
                        <v-list-item-icon>
                            <v-checkbox
                                @click="doneTask(task)"
                                class="my-n1"
                            ></v-checkbox>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title
                                :class="{
                                    'text-decoration-line-through': task.done,
                                }"
                                v-text="task.text"
                            ></v-list-item-title>
                        </v-list-item-content>
                        <v-icon
                            v-if="!task.done && !task.flag"
                            class="mr-2"
                            @click="importantTask(task)"
                            >mdi-bookmark</v-icon
                        >
                        <v-icon
                            v-if="task.flag"
                            color="red"
                            class="mr-2"
                            @click="importantTask(task)"
                            >mdi-bookmark</v-icon
                        >

                        <v-icon @click="deleteTasks(task.id)">mdi-close</v-icon>
                    </v-list-item>
                </v-slide-y-transition>
            </v-list-item-group>
        </v-list>
        <v-divider></v-divider>

        <v-list dense v-if="doneTasks.length > 0">
            <v-list-item-title class="pl-5" v-if="doneTasks.length == 1"
                >Done task
            </v-list-item-title>
            <v-list-item-title class="pl-5" v-if="doneTasks.length > 1"
                >Done tasks
            </v-list-item-title>
            <v-list-item-group color="primary">
                    <v-list-item
                        :class="{ 'blue lighten-5': task.done }"
                        v-for="task in doneTasks"
                        :key="task.id"
                    >
                        <v-list-item-icon>
                            <v-checkbox
                                @click="returnTask(task)"
                                input-value="true"
                                class="my-n1"
                            ></v-checkbox>
                        </v-list-item-icon>
                        <v-list-item-content>
                            <v-list-item-title
                                :class="{
                                    'text-decoration-line-through': task.done,
                                }"
                                v-text="task.text"
                            ></v-list-item-title>
                        </v-list-item-content>
                        <v-icon @click="deleteDoneTasks(task.id)"
                            >mdi-close</v-icon
                        >
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
        };
    },
    created() {
        const tasksData = localStorage.getItem("tasks");
        if (tasksData) {
            this.tasks = JSON.parse(tasksData);
        }
        const doneTasksData = localStorage.getItem("doneTasks");
        if (doneTasksData) {
            this.doneTasks = JSON.parse(doneTasksData);
        }
    },
    methods: {
        addTask() {
            const task = {
                id: Date.now(),
                text: this.newTask,
                done: false,
                flag: false,
            };
            if(this.newTask){
                this.tasks.unshift(task);
                localStorage.setItem("tasks", JSON.stringify(this.tasks));
                this.newTask = "";
            }
        },
        deleteTasks(id) {
            localStorage.removeItem("tasks");
            this.tasks = this.tasks.filter((task) => {
                return task.id !== id;
            });
            localStorage.setItem("tasks", JSON.stringify(this.tasks));
        },
        deleteDoneTasks(id) {
            localStorage.removeItem("doneTasks");
            this.doneTasks = this.doneTasks.filter((task) => {
                return task.id !== id;
            });
            localStorage.setItem("doneTasks", JSON.stringify(this.doneTasks));
        },
        importantTask(task) {
            const index = this.tasks.indexOf(task);
            if (!task.flag) {
                this.array_move(this.tasks, index, 0);
            }
            localStorage.removeItem("tasks");
            task.flag = !task.flag;
            localStorage.setItem("tasks", JSON.stringify(this.tasks));
        },
        array_move(arr, old_index, new_index) {
            if (new_index >= arr.length) {
                var k = new_index - arr.length + 1;
                while (k--) {
                    arr.push(undefined);
                }
            }
            arr.splice(new_index, 0, arr.splice(old_index, 1)[0]);
        },
        doneTask(task) {
            task.flag = false;
            task.done = true;
            this.doneTasks.push(task);
            this.tasks = this.tasks.filter((el) => {
                return task.id !== el.id;
            });
            localStorage.setItem("doneTasks", JSON.stringify(this.doneTasks));
            localStorage.removeItem("tasks");
            localStorage.setItem("tasks", JSON.stringify(this.tasks));
        },
        returnTask(task) {
            task.done = false;
            this.tasks.push(task);
            localStorage.setItem("tasks", JSON.stringify(this.tasks));
            localStorage.removeItem("doneTasks");
            this.doneTasks = this.doneTasks.filter((el) => {
                return task.id !== el.id;
            });
            localStorage.setItem("doneTasks", JSON.stringify(this.doneTasks));
        },
    },
};
</script>
