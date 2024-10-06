<template>
  <div id="app">
    <nav class="navbar">
      <h1>Student Notification</h1>
      <!-- Bell Icon Component in the Navbar -->
      <notification-bell />
    </nav>
  </div>
</template>

<script>
import NotificationBell from './components/NotificationBell.vue';

export default {
  components: {
    NotificationBell,  // Import the bell icon component
  },
};
</script>

<style scoped>
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  background-color: #4CAF50;
  color: white;
}
</style>
