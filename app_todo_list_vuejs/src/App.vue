<template>
  <div class="container d-flex justify-content-center align-items-center">
    <div class="card mt-3 col-7">
      <div class="card-body">
        <h1 class="text-center">Список завдань</h1>
        <div
          class="d-flex flex-column flex-sm-row justify-content-between mt-3 mt-sm-5"
        >
          <div class="col-sm-9">
            <input
              type="text"
              class="form-control"
              placeholder="Введіть нове завдання"
              v-model="newTask"
              @keyup.enter="addTask"
              :disabled="tasks.length > 9"
            />
          </div>
          <div class="mt-3 mt-sm-0">
            <button
              type="button"
              class="btn btn-primary"
              @click="addTask"
              :disabled="tasks.length > 9"
            >
              &nbsp;&nbsp;Додати &nbsp;&nbsp;
            </button>
          </div>
        </div>
        <div class="mt-2 mt-sm-3">
          <div
            class="card item-card mt-1"
            v-for="(task, index) in tasks"
            :key="index"
          >
            <div class="card-body">
              <div class="d-flex justify-content-between">
                <input
                  class="form-check-input"
                  type="checkbox"
                  v-model="task.isDone"
                />
                <div>
                  <p class="fw-semibold">{{ task.description }}</p>
                </div>
                <div class="mt-0">
                  <button
                    type="button"
                    class="btn btn-danger"
                    @click="deleteTask(index)"
                  >
                    Видалити
                  </button>
                </div>
              </div>
            </div>
          </div>
          <hr />
          <div class="d-flex flex-column flex-sm-row justify-content-between">
            <div>
              <p
                class="fw-bold"
                v-show="pendingTasks == 0 || pendingTasks >= 5"
              >
                У вас є {{ pendingTasks }} невирішених завдань
              </p>
              <p class="fw-bold" v-show="pendingTasks == 1">
                У вас є {{ pendingTasks }} невирішене завдання
              </p>
              <p class="fw-bold" v-show="pendingTasks > 1 && pendingTasks < 5">
                У вас є {{ pendingTasks }} невирішені завдання
              </p>
            </div>
            <div>
              <button
                type="button"
                class="btn btn-warning"
                @click="deleteAllTasks"
                v-show="tasks.length > 0"
              >
                Видалити всі
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<!-- Javascript -->
<script>
import { ref, onMounted, computed, watch } from "vue";
 
export default {
  name: "App",
  setup() {
    const newTask = ref("");
    const tasks = ref([
      { description: "Опанувати React.js", isDone: false },
      { description: "Опанувати TypeScript", isDone: false },
      { description: "Опанувати Node.js", isDone: false },
      { description: "Опанувати JavaScript", isDone: true },
      { description: "Опанувати Git та GitHub", isDone: true },
      { description: "Опанувати HTML та CSS", isDone: true },
    ]);
 
    const addTask = () => {
      if (!newTask.value) return;
      tasks.value.unshift({
        description: newTask.value,
        isDone: false,
      });
      localStorage.setItem("tasks", JSON.stringify(tasks.value));
      newTask.value = "";
    };
 
    const deleteTask = (index) => {
      tasks.value.splice(index, 1);
      localStorage.setItem("tasks", JSON.stringify(tasks.value));
    };
 
    const deleteAllTasks = () => {
      tasks.value = [];
      localStorage.removeItem("tasks");
    };
 
    const pendingTasks = computed(() => {
      return tasks.value.filter((x) => x.isDone === false).length;
    });
 
    watch(
      tasks,
      () => {
        if (tasks.value.length > 10) {
          alert("Можна додати не більше 10 завдань!");
        }
      },
      { deep: true }
    );
 
    onMounted(() => {
      if (localStorage.tasks) {
        tasks.value = JSON.parse(localStorage.getItem("tasks")) || [];
      }
    });
 
    return {
      newTask,
      tasks,
      addTask,
      deleteTask,
      deleteAllTasks,
      pendingTasks,
    };
  },
};
</script>
<!-- Css -->
<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto+Mono&display=swap");
 
body {
  background-color: silver;
  color: white;
  font-family: "Roboto Mono", monospace;
}
 
.card-body h1 {
  color: #a813ff;
}
 
.btn-primary {
  background-color: #a813ff;
  border: transparent;
}
 
.btn-danger {
  background-color: #5f8dba;
  border: transparent;
}
 
.btn-warning {
  background-color: #440c7e;
  color: #fff;
  border: transparent;
}
 
.message {
  color: green;
  font-weight: bold;
}
 
.item-card {
  height: 64px;
  background-color: #440c7e;
  color: #fff;
}
</style>
