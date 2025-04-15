<template>
  <div id="app">
    <h1>Список задач</h1>
    <ul>
      <li v-for="task in tasks" :key="task.id">
        <input
          type="checkbox"
          :checked="task.done"
          @change="toggleTask(task.id)"
        />
        <span :class="{ done: task.done }">{{ task.title }}</span>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';


export default {
  setup() {
    const tasks = ref([]);

    // Загрузка данных из localStorage
    const loadTasksFromStorage = () => {
      const storedTasks = localStorage.getItem('tasks');
      if (storedTasks) {
        try {
          tasks.value = JSON.parse(storedTasks);
        } catch (error) {
          console.error('Ошибка при чтении данных из localStorage:', error);
        }
      } else {
        fetch('/tasks.json')
          .then((response) => {
            if (!response.ok) {
              throw new Error('Не удалось загрузить tasks.json');
            }
            return response.json();
          })
          .then((data) => {
            tasks.value = data;
            saveTasksToStorage();
          })
          .catch((error) => {
            console.error('Ошибка при загрузке данных:', error);
          });
      }
    };

    // Сохранение данных в localStorage
    const saveTasksToStorage = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value));
    };

    // Переключение статуса задачи
    const toggleTask = (taskId) => {
      const task = tasks.value.find((t) => t.id === taskId);
      if (task) {
        task.done = !task.done;
        saveTasksToStorage();
      }
    };

    // Загрузка задач при монтировании компонента
    onMounted(() => {
      loadTasksFromStorage();
      console.log('Загруженные задачи:', tasks.value);
    });

    return {
      tasks,
      toggleTask,
    };
  },
};
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  padding: 20px;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin-bottom: 10px;
}

.done {
  text-decoration: line-through;
  color: gray;
}
</style>