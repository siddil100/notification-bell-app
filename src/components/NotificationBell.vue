<template>
    <div class="notification-bell" @click="toggleNotifications">
      <!-- Bell icon with conditional classes for glowing -->
      <i class="fas fa-bell" :class="{ 'notify': hasNewNotifications }"></i>
      <!-- Display unread count -->
      <span v-if="unreadCount > 0" class="badge">{{ unreadCount }}</span>
  
      <!-- Notification dropdown -->
      <div v-if="showNotifications" class="notifications-dropdown">
        <p v-if="notifications.length === 0">No new notifications</p>
        <ul v-else>
          <li v-for="(notification, index) in notifications" :key="index">
            {{ notification }}
          </li>
        </ul>
      </div>
    </div>
  </template>
  
  <script>
  import * as signalR from '@microsoft/signalr';
  
  export default {
    data() {
      return {
        unreadCount: 0,
        hasNewNotifications: false,
        showNotifications: false,
        notifications: [],
        connection: null,
      };
    },
    created() {
      // SignalR connection to listen for notifications
      this.connection = new signalR.HubConnectionBuilder()
        .withUrl('https://notificationstudentapi20241006105521.azurewebsites.net/notificationHub')
        .build();
  
      this.connection.start().then(() => {
        console.log('Connected to SignalR');
        this.connection.on('ReceiveNotification', (studentName) => {
          console.log('New notification received:', studentName);
          this.unreadCount++;
          this.hasNewNotifications = true;
          this.notifications.push(studentName);  // Add student name to the notifications
          console.log('Updated notifications array:', this.notifications);  // Debugging log
        });
      }).catch((err) => {
        console.error('SignalR connection error:', err);
      });
    },
    methods: {
      toggleNotifications() {
        this.showNotifications = !this.showNotifications;
        if (this.showNotifications) {
          this.hasNewNotifications = false;
          this.unreadCount = 0;  // Reset unread count when viewing notifications
        }
      },
    },
  };
  </script>
  
  <style scoped>
  .notification-bell {
    position: relative;
    cursor: pointer;
  }
  
  .fas.fa-bell {
    color: yellow; /* Initial color of the bell icon */
    font-size: 40px; /* Adjust this value to increase or decrease the size */
  }
  
  .notify {
    animation: blink-red 1s infinite alternate; /* Blink red when there's a notification */
  }
  
  .badge {
    position: absolute;
    top: -5px;
    right: -10px;
    background-color: red;
    color: white;
    padding: 2px 5px;
    border-radius: 50%;
  }
  
  .notifications-dropdown {
    position: absolute;
    top: 30px;
    right: 0;
    background-color: rgb(138, 121, 121);
    border: 1px solid #ccc;
    padding: 10px;
    width: 200px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  }
  
  .notifications-dropdown ul {
    list-style: none;
    padding: 0;
  }
  
  .notifications-dropdown li {
    padding: 5px 0;
    border-bottom: 1px solid #ccc;
  }
  
  .notifications-dropdown li:last-child {
    border-bottom: none;
  }
  
  @keyframes blink-red {
    from {
      color: yellow; /* Initial state is yellow */
    }
    to {
      color: red; /* Blink to red when a notification is received */
    }
  }
  </style>
  