<template>
  <div class="create-invoice">
    <div class="alert alert-primary" show v-if="response !== ''">
      {{ response }}
    </div>
    <TaskEditorHeader @createTask="createTask" />
    <TaskList
      :tasks="tasks"
      @deleteTask="deleteTask"
      @updateTask="updateTask"
    />
    <div class="container-fluid">
      <div class="row d-flex justify-content-center"></div>
      <br />
    </div>
  </div>
</template>

<script>
import TaskEditorHeader from "@/components/TaskEditorHeader.vue";
import axios from "axios";
import TaskList from "@/components/TaskList.vue";

export default {
  name: "TaskEditor",
  components: {
    TaskEditorHeader,
    TaskList,
  },
  data() {
    return {
      tasks: [],
      selectedProject: "",
      response: "",
    };
  },
  methods: {
    getTasks() {
      var results = axios({
        method: "get",
        headers: { Authorization: `Bearer ${this.$store.state.serviceToken}` },
        url: "http://localhost:5000/tasks/mavenlink",
      })
        .then((response) => {
          this.tasks = response.data.stories;
        })
        .catch((error) => console.log(error));
    },

    deleteTask(task_id) {
      var results = axios({
        method: "delete",
        headers: { Authorization: `Bearer ${this.$store.state.serviceToken}` },
        url: "http://localhost:5000/tasks/mavenlink/delete?id=" + task_id,
      })
        .then(() => {
          this.response = "Task was successfully deleted";
          this.getTasks();
        })
        .catch((error) => {
          this.response = "Task unable to be deleted";
        });
    },

    updateTask(taskID, editTaskName, editTaskDesc, editTaskRate) {
      var results = axios({
        method: "put",
        headers: { Authorization: `Bearer ${this.$store.state.serviceToken}` },
        url: "http://localhost:5000/tasks/mavenlink/update?id=" + taskID,
        data: {
          story_type: "task",
          title: editTaskName,
          description: editTaskDesc,
          rate: editTaskRate,
        },
      })
        .then((response) => {
          this.getTasks();
        })
        .catch((error) => console.log(error));
    },

    createTask(workspace_id, newTaskName, newTaskDescription, newTaskRate) {
      var results = axios({
        method: "post",
        headers: { Authorization: `Bearer ${this.$store.state.serviceToken}` },
        url: "http://localhost:5000/tasks/mavenlink/create",
        data: {
          workspace_id: workspace_id,
          story_type: "task",
          title: newTaskName,
          description: newTaskDescription,
          rate: 10, //Static rate variable (temporary)
        },
      })
        .then((response) => {
          this.getTasks();
        })
        .catch((error) => console.log(error));
    },
  },
  mounted() {
    this.getTasks();
  },
};
</script>
