<template>
  <div class="container" style="font-family: 'YourFontFamily', sans-serif;">
    <h2 class="text-center mt-5">Task Application</h2>
    <!--Input-->
    <div class="d-flex mt-3">
      <input v-model="taskTitle" type="text" placeholder="Enter title" class="form-control" @input="hideAlert" />
      <input v-model="taskDescription" type="text" placeholder="Enter task" class="form-control mx-2" @input="hideAlert" />
      <button @click="submitTask" class="btn btn-secondary rounded-2 mx-1" :disabled="isInputEmpty">SUBMIT</button>
    </div>
    <!-- Validation msg -->
    <div v-if="isInputEmpty" class="alert alert-danger mt-2">{{ alertMessage }}</div>

    <div class="d-flex mt-3">
      <input v-model="customStatus" type="text" placeholder="Enter Custom Status" class="form-control" style="width: 40ch; margin-right: 10px;" />
      <button @click="addCustomStatus" class="btn btn-primary rounded-2">Add Custom Status</button>
      <div class="btn-group btn-group-toggle" style="margin-left: 10px;">
        <select v-model="selectedFilter" class="form-select" @change="setFilter(selectedFilter)">
          <option value="all">All</option>
          <option v-for="status in availableStatuses" :value="status" :key="status">{{ firstCharUpper(status) }}</option>
        </select>
      </div>
      <div class="form-group col-12 col-md-2" style="margin-left: 10px;">
        <input type="text" v-model="search" placeholder="Filter tasks" class="form-control rounded-2">
      </div>
    </div>

    <!-- buttons to show the tasks based on the status -->
    <div class="container d-flex mt-4 justify-content-between flex-wrap">
      <div class="col-12 col-md-5 d-flex">
      </div>
    </div>

    <!-- Task Table -->
    <table class="table table-bordered mt-3">
      <thead>
        <tr>
          <th scope="col">Task Title</th>
          <th scope="col">Tasks Description</th>
          <th scope="col" class="text-center">Status</th>
          <th scope="col" class="text-center">Delete</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in filteredTasks" :key="index">
          <td :class="getStatusClass(task.status)">{{ task.title }}</td>
          <td :class="getStatusClass(task.status)">{{ task.description }}</td>
          <td style="width: 150px" class="text-center">
            <select v-model="tasks[index].status" @change="changeStatus(index)" class="form-select pointer border border-none px-0 py-1 rounded" @blur="isEditing[index] = false">
              <option v-for="status in availableStatuses" :value="status" :key="status">{{ firstCharUpper(status) }}</option>
            </select>
          </td>
          <td style="width: 150px">
            <div class="text-center" @click="deleteTask(index)" :class="getStatusClass(task.status)">
              <span class="fa fa-trash"></span>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  data() {
    return {
      task: "",
      availableStatuses: ["in-completed", "in-progress", "completed"],
      search: "",
      selectedFilter: "all",
      taskTitle: "",
      taskDescription: "",
      tasks: [
        {
          title: "Grocery Shopping",
          description: "Buy milk, eggs, bread, and vegetables.",
          status: "completed",
        },
        {
          title: "Study for Exam",
          description: "Review chapters 3 to 5 for the math exam.",
          status: "in-completed",
        },
      ],
      isInputEmpty: false,
      alertMessage: "",
      slideOut: false,
      customStatus: "",
    };
  },
  mounted() {
    const tasks = localStorage.getItem("tasks");
    if (tasks) {
      this.tasks = JSON.parse(tasks);
    }
  },
  beforeDestroy() {
    localStorage.setItem("tasks", JSON.stringify(this.tasks));
  },
  methods: {
    submitTask() {
      if (this.taskTitle.length === 0 || this.taskDescription.length === 0) {
        this.isInputEmpty = true;
        this.alertMessage = "Please enter a task before submitting.";
        return;
      }
      this.tasks.push({
        title: this.taskTitle,
        description: this.taskDescription,
        status: this.selectedFilter,
      });
      this.taskTitle = "";
      this.taskDescription = "";
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    hideAlert() {
      this.isInputEmpty = false;
      this.alertMessage = "";
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    changeStatus(index) {
      // Optional: Implement logic for custom status changes here
    },
    firstCharUpper(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
    setFilter(status) {
      this.selectedFilter = status.toLowerCase();
    },
    addCustomStatus() {
      if (this.customStatus.trim() !== "" && !this.availableStatuses.includes(this.customStatus)) {
        this.availableStatuses.push(this.customStatus);
        this.customStatus = "";
      }
    },
    getStatusClass(status) {
      return {
        "text-warning": status === "in-completed",
        "text-primary": status === "in-progress",
        "text-success": status === "completed",
      };
    },
  },
  computed: {
    filteredTasks() {
      if (this.selectedFilter === "all") {
        return this.tasks.filter(task => task.status.toLowerCase().includes(this.search.toLowerCase()));
      } else {
        return this.tasks.filter(task => task.status.toLowerCase() === this.selectedFilter.toLowerCase() && task.status.toLowerCase().includes(this.search.toLowerCase()));
      }
    },
  },
};
</script>



<style scoped>
@media (max-width: 768px) {
  /* Styles for mobile devices */
  .d-flex {
    flex-direction: column;
  }
  .form-control {
    width: 100%;
    margin-bottom: 10px;
  }
  .btn {
    width: 100%;
    margin-bottom: 10px;
  }
  .form-group {
    width: 100%;
  }
  .btn-group-toggle {
    margin-left: 0;
    width: 100%;
    margin-bottom: 10px;
  }
}
</style>

