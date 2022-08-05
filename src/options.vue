<template>
  <div>
    Favorite color: <select
      v-model="color"
  >
    <option value="red">red</option>
    <option value="green">green</option>
    <option value="blue">blue</option>
    <option value="yellow">yellow</option>
  </select>
  </div>
  <div>
    <label>
      <input
          type="checkbox"
          v-model="like"
      />
      I like colors.
    </label>
  </div>
  <div>{{ status }}</div>
  <button @click="saveOptions">Save</button>
</template>

<script setup>
import { ref, watchEffect } from 'vue';

let color = ref('');
let status = ref('');
let like = ref(false);

watchEffect(() => {
  // Restores select box and checkbox state using the preferences
  // stored in chrome.storage.
  chrome.storage.sync.get(
      {
        favoriteColor: 'red',
        likesColor: true,
      },
      (items) => {
        color.value = items.favoriteColor;
        like.value = items.likesColor;
      },
  );
});

const saveOptions = () => {
  // Saves options to chrome.storage.sync.
  chrome.storage.sync.set(
      {
        favoriteColor: color.value,
        likesColor: like.value,
      },
      () => {
        // Update status to let user know options were saved.
        status.value = 'Options saved.';
        const id = setTimeout(() => {
          status.value = ''
        }, 1000);
        return () => clearTimeout(id);
      },
  );
};
</script>
