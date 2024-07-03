<template>
  <div>
    <h1>Спортивные увлечения</h1>
    <button @click="openDialog">Добавить активность</button>

    <table>
      <thead>
        <tr>
          <th>№</th>
          <th>Название активности</th>
          <th>Действия</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(activity, index) in activities" :key="activity.id">
          <td>{{ index + 1 }}</td>
          <td>{{ activity.name }}</td>
          <td><button @click="removeActivity(index)">Удалить</button></td>
        </tr>
      </tbody>
    </table>

    <dialog ref="dialog">
      <form @submit.prevent="addActivity">
        <div class="formRow">
          <label>Активность:</label>
          <select v-model="newActivity.name" @change="handleActivityChange">
            <option value="Горные лыжи">Горные лыжи</option>
            <option value="Виндсерфинг">Виндсерфинг</option>
            <option value="Кайтсерфинг">Кайтсерфинг</option>
            <option value="Сноуборды">Сноуборды</option>
            <option value="Плавание">Плавание</option>
            <option value="Яхтинг">Яхтинг</option>
            <option value="Боулинг">Боулинг</option>
            <option value="Велосипеды">Велосипеды</option>
          </select>
        </div>
        <div v-if="newActivity.name === 'Велосипеды'" class="formRow">
          <label>Тип велосипеда:</label>
          <select v-model="newActivity.bikeType">
            <option value="Шоссейные">Шоссейные</option>
            <option value="Горные">Горные</option>
            <option value="BMX">BMX</option>
          </select>
        </div>
        <div class="formRow">
          <label>Сколько лет увлекаетесь:</label>
          <input type="number" min="0" v-model="newActivity.years" />
        </div>
        <div class="formRow">
          <label>Комментарий:</label>
          <textarea v-model="newActivity.comment"></textarea>
        </div>
        <button type="submit">Добавить</button>
        <button type="button" @click="closeDialog">Отменить</button>
      </form>
    </dialog>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";

interface Activity {
  id: number;
  name: string;
  bikeType?: string;
  years: number;
  comment: string;
}

interface HTMLDialogElementExtended extends HTMLDialogElement {
  showModal: () => void;
  close: () => void;
}

export default defineComponent({
  name: "SportSurvey",
  setup() {
    const activities = ref<Activity[]>([]);
    const newActivity = ref<Partial<Activity>>({
      name: "",
      years: 0,
      comment: "",
    });
    const dialog = ref<HTMLDialogElementExtended | null>(null);

    const openDialog = () => {
      dialog.value?.showModal();
    };

    const closeDialog = () => {
      dialog.value?.close();
    };

    const addActivity = () => {
      if (newActivity.value.name && newActivity.value.years !== undefined) {
        const id = activities.value.length
          ? activities.value[activities.value.length - 1].id + 1
          : 1;
        activities.value.push({ ...newActivity.value, id } as Activity);
        newActivity.value = { name: "", years: 0, comment: "" };
        closeDialog();
      }
    };

    const removeActivity = (index: number) => {
      activities.value.splice(index, 1);
    };

    const handleActivityChange = () => {
      if (newActivity.value.name !== "Велосипеды") {
        delete newActivity.value.bikeType;
      }
    };

    return {
      activities,
      newActivity,
      dialog,
      openDialog,
      closeDialog,
      addActivity,
      removeActivity,
      handleActivityChange,
    };
  },
});
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}

button {
  cursor: pointer;
}

th,
td {
  border: 1px solid black;
  padding: 8px;
  box-sizing: border-box;
  text-align: left;
}

dialog {
  padding: 20px;
  box-sizing: border-box;
  border: solid 1px black;
}

form {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.formRow {
  display: flex;
  justify-content: space-between;
}
</style>
