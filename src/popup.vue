<template>
  <ul style="min-width: 700px">
    <li>Current URL: {{ currentURL }}</li>
    <li>Current Time: {{ new Date().toLocaleTimeString() }}</li>
  </ul>
  <button
      @click="() => count++"
      style="margin-right: 5px"
  >
    count up
  </button>
  <button @click="changeBackground">change background</button>
</template>

<script setup>
import { ref, watch, watchEffect } from 'vue';

const count = ref(0);
const currentURL = ref('');

watch(count, (newValue) => {
  chrome.action.setBadgeText({ text: newValue.toString() });
});

watchEffect(() => {
  chrome.tabs.query({ active: true, currentWindow: true }, function (tabs) {
    currentURL.value = tabs[0].url;
  });
});

const changeBackground = () => {
  chrome.tabs.query({ active: true, currentWindow: true }, function (tabs) {
    const tab = tabs[0];
    if (tab.id) {
      chrome.tabs.sendMessage(
          tab.id,
          {
            color: "#555555",
          },
          (msg) => {
            console.log("result message:", msg);
          }
      );
    }
  });
};
</script>

<style scoped>
</style>
