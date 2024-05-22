<template>
  <div class="container">
    <h1 class="title">Daftar Kegiatan</h1>
    <div class="input-container">
      <input v-model="newActivity" @keyup.enter="addActivity" placeholder="Nama Kegiatan" />
      <button @click="addActivity">Tambahkan Kegiatan</button>
    </div>
    <ul class="activities-list">
      <li v-for="(activity, index) in filteredActivities" :key="index" class="activity-item">
        <span v-if="activity.completed">✅</span>
        <span :class="{ completed: activity.completed }">{{ activity.name }}</span>
        <div class="buttons">
          <button @click="toggleActivity(index)" class="action-button">
            {{ activity.completed ? 'Uncheck' : 'Checklist' }}
          </button>
          <button @click="removeActivity(index)" class="action-button">Hapus</button>
        </div>
      </li>
    </ul>
    <button @click="filterCompleted" class="filter-button">
      {{ showOnlyIncomplete ? 'Tampilkan Semua Kegiatan' : 'Tampilkan Kegiatan Yang Belum Selesai' }}
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newActivity: '',
      activities: [],
      showOnlyIncomplete: false,
    };
  },
  computed: {
    filteredActivities() {
      if (this.showOnlyIncomplete) {
        return this.activities.filter((activity) => !activity.completed);
      }
      return this.activities;
    },
  },
  methods: {
    addActivity() {
      if (this.newActivity.trim() !== '') {
        this.activities.push({ name: this.newActivity, completed: false });
        this.newActivity = '';
      }
    },
    toggleActivity(index) {
      this.activities[index].completed = !this.activities[index].completed;
    },
    removeActivity(index) {
      this.activities.splice(index, 1);
    },
    filterCompleted() {
      this.showOnlyIncomplete = !this.showOnlyIncomplete;
    },
  },
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  font-family: Arial, sans-serif;
  /* Set your background image here */
  background-image: url('https://img.freepik.com/free-vector/abstract-geometric-pattern_1319-110.jpg?t=st=1714299332~exp=1714302932~hmac=6e9993d89a8bcd867f6788f1c11869150dd34d4723d9dc934d0c47e62349a3e0&w=740');
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
}

.title {
  font-size: 24px;
  color: #803D3B;
  margin-bottom: 20px;
}

.input-container {
  display: flex;
  margin-bottom: 20px;
}

.input-container input {
  flex: 1;
  padding: 10px;
  border: 1px solid #803D3B;
  border-radius: 5px;
}

.input-container button {
  padding: 10px 15px;
  border: none;
  border-radius: 5px;
  background-color: #803D3B;
  color: rgb(255, 255, 255);
  cursor: pointer;
}

.activities-list {
  list-style: none;
  padding: 0;
}

.activity-item {
  display: flex;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #ffffff;
}

.activity-item span {
  flex: 1;
  font-size: 16px;
}

.buttons {
  display: flex;
  justify-content: space-between;
}

.action-button {
  padding: 5px 10px;
  margin-left: 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  background-color: #803D3B;
  color: #ffffff;
}

.completed {
  text-decoration: line-through;
  color: #803D3B;
}

.filter-button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #803D3B;
  color: white;
  cursor: pointer;
  margin-top: 20px;
}
</style>