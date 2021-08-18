<template>
  <div v-show="showAddTask">
    <AddTask @add-task="addTask" />
  </div>

  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import Tasks from "../components/Tasks.vue";
import AddTask from "../components/AddTask.vue";

export default {
  name: "Home",
  data() {
    return { tasks: [] };
  },
  props: {
    showAddTask: Boolean,
  },
  components: {
    Tasks,
    AddTask,
  },
  methods: {
    deleteTask(id) {
      if (confirm("Are you sure?")) {
        fetch(`api/tasks/${id}`, {
          method: "DELETE",
        }).then((res) => {
          res.status === 200
            ? (this.tasks = this.tasks.filter((task) => task.id !== id))
            : alert("Error deleting task.");
        });
      }
    },
    async toggleReminder(id) {
      this.fetchTask(id)
        .then((res) => {
          return { ...res, reminder: !res.reminder };
        })
        .then((res) =>
          fetch(`api/tasks/${id}`, {
            method: "PUT",
            headers: {
              "Content-type": "application/json",
            },
            body: JSON.stringify(res),
          })
        )
        .then((res) => res.json())
        .then((res) => {
          this.tasks = this.tasks.map((task) =>
            task.id === id ? { ...task, reminder: res.reminder } : task
          );
        });

      // this.tasks[id - 1].reminder = !this.tasks[id - 1].reminder;
    },
    addTask(task) {
      fetch("api/tasks", {
        method: "POST",
        headers: {
          "Content-type": "application/json",
        },
        body: JSON.stringify(task),
      })
        .then((res) => res.json())
        .then((res) => {
          this.tasks = [...this.tasks, res];
        });
    },
    async fetchTasks() {
      let res = await fetch("api/tasks");
      let data = await res.json();

      return data;
    },
    async fetchTask(id) {
      let res = await fetch(`api/tasks/${id}`);
      let data = await res.json();

      return data;
    },
  },
  created() {
    this.fetchTasks().then((res) => {
      this.tasks = res;
    });
  },
};
</script>
