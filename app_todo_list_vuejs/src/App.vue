<template>
  <div class="page-shell">
    <main class="todo-card card">
      <div class="card-body todo-card__body">
        <h1 class="todo-title text-center">Список завдань</h1>

        <div class="task-form d-flex gap-2">
          <input
            type="text"
            class="form-control task-input"
            placeholder="Введіть нове завдання"
            v-model="newTask"
            @keyup.enter="addTask"
            :disabled="tasks.length > 9"
          />
          <button
            type="button"
            class="btn btn-primary task-add-btn"
            @click="addTask"
            :disabled="tasks.length > 9"
          >
            Додати
          </button>
        </div>

        <div class="tasks-list">
          <div
            class="task-row card"
            v-for="(task, index) in tasks"
            :key="index"
          >
            <div class="task-row__content">
              <input
                class="form-check-input task-check"
                type="checkbox"
                v-model="task.isDone"
              />
              <p class="task-row__text">{{ task.description }}</p>
              <button
                type="button"
                class="btn btn-danger task-delete-btn"
                @click="deleteTask(index)"
              >
                Видалити
              </button>
            </div>
          </div>
        </div>

        <div class="todo-footer d-flex align-items-center justify-content-between">
          <p class="todo-summary fw-semibold">{{ pendingMessage }}</p>
          <button
            type="button"
            class="btn btn-warning todo-delete-all"
            @click="deleteAllTasks"
            v-show="tasks.length > 0"
          >
            Видалити всі
          </button>
        </div>
      </div>
    </main>
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

    const pendingMessage = computed(() => {
      if (pendingTasks.value === 1) {
        return `У вас є ${pendingTasks.value} невирішене завдання`;
      }

      if (pendingTasks.value > 1 && pendingTasks.value < 5) {
        return `У вас є ${pendingTasks.value} невирішені завдання`;
      }

      return `У вас є ${pendingTasks.value} невирішених завдань`;
    });
 
    watch(
      tasks,
      () => {
        localStorage.setItem("tasks", JSON.stringify(tasks.value));

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
      pendingMessage,
    };
  },
};
</script>
<!-- Css -->
<style>
@import url("https://fonts.googleapis.com/css2?family=Roboto+Mono&display=swap");
 
* {
  box-sizing: border-box;
}

html,
body,
#app {
  min-height: 100%;
}

body {
  margin: 0;
  background: #bdbdbd;
  color: #ffffff;
  font-family: "Roboto Mono", monospace;
}
 
.page-shell {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 24px 16px;
}

.todo-card {
  width: min(100%, 396px);
  border: 0;
  border-radius: 4px;
  background: #ffffff;
  color: #1f1f1f;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.12);
}

.todo-card__body {
  padding: 28px 14px 16px;
}

.todo-title {
  margin: 0 0 28px;
  color: #b233ff;
  font-size: 2.4rem;
  line-height: 1.05;
  letter-spacing: 0.02em;
}

.task-form {
  margin-bottom: 12px;
}

.task-input {
  min-width: 0;
  height: 35px;
  font-size: 0.9rem;
}

.task-add-btn {
  flex: 0 0 83px;
  min-height: 35px;
  background: linear-gradient(180deg, #b833ff 0%, #9a1dff 100%);
  border: 0;
  font-size: 0.88rem;
}

.task-add-btn:disabled,
.task-delete-btn:disabled,
.todo-delete-all:disabled {
  opacity: 0.65;
}

.tasks-list {
  display: grid;
  gap: 6px;
}

.task-row {
  border: 0;
  border-radius: 3px;
  background: #4b158d;
  color: #ffffff;
}

.task-row__content {
  min-height: 48px;
  display: grid;
  grid-template-columns: 18px 1fr auto;
  align-items: center;
  gap: 12px;
  padding: 8px 10px 8px 11px;
}

.task-check {
  margin: 0;
  width: 13px;
  height: 13px;
  justify-self: center;
  accent-color: #1f88ff;
}

.task-row__text {
  margin: 0;
  text-align: center;
  font-size: 0.92rem;
  font-weight: 700;
  color: #ffffff;
}

.task-delete-btn {
  min-width: 68px;
  min-height: 34px;
  border: 0;
  border-radius: 4px;
  background: #5f8dba;
  font-size: 0.86rem;
}

.todo-footer {
  margin-top: 8px;
  padding-top: 10px;
  border-top: 1px solid #e6e6e6;
  gap: 12px;
}

.todo-summary {
  margin: 0;
  color: #1a1a1a;
  font-size: 0.95rem;
}

.todo-delete-all {
  min-width: 96px;
  min-height: 34px;
  border: 0;
  background: #4b158d;
  color: #ffffff;
  font-size: 0.86rem;
}

.btn-primary:hover,
.btn-danger:hover,
.btn-warning:hover {
  filter: brightness(1.05);
}

.btn-primary:focus,
.btn-danger:focus,
.btn-warning:focus,
.form-control:focus {
  box-shadow: none;
}

.card-body h1 {
  color: #a813ff;
}
</style>

