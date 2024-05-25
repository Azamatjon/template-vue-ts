<script setup lang="ts">
import { ref, onMounted } from 'vue';
import axios from 'axios';

import HelloWorld from './components/HelloWorld.vue'

// Telegram user information
const userId = ref<number | null>(null);
const userName = ref<string | null>(null);

// Initialize Telegram WebApp API
onMounted(async () => {
	if (window.Telegram?.WebApp) {
		const tg = window.Telegram.WebApp;
		
		// Expand the WebApp to fill the entire viewport
		tg.expand();
		
		// Get user information
		const user = tg.initDataUnsafe.user;
		
		if (user) {
			userId.value = user.id;
			userName.value = `${user.first_name} ${user.last_name}`;
			
			// Send the initialization data to your backend for verification
			try {
				const response = await axios.post('http://localhost:3000/verify', {
					initData: tg.initData,
					initDataUnsafe: tg.initDataUnsafe
				});
				
				if (response.data.success) {
					console.log('User verified successfully');
				} else {
					console.error('User verification failed');
				}
			} catch (error) {
				console.error('Error verifying user:', error);
			}
		} else {
			console.error('User information is not available.');
		}
		
	} else {
		console.error('Telegram WebApp API is not available.');
	}
});
</script>

<template>
  <div>
    <a href="https://vitejs.dev" target="_blank">
      <img src="/vite.svg" class="logo" alt="Vite logo" />
    </a>
    <a href="https://vuejs.org/" target="_blank">
      <img src="./assets/vue.svg" class="logo vue" alt="Vue logo" />
    </a>
	  
	  <p v-if="userId">User ID: {{ userId }}, Name: {{ userName }}</p>
	  <p v-else>Loading user information...</p>
	  
	  
	  ver: latest
  </div>
  <HelloWorld msg="Vite + Vue" />
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
