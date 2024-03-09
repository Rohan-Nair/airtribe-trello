<template>
  <NuxtLayout>
    <v-app>
      <NuxtPage />
    </v-app>
  </NuxtLayout>
</template>

<script setup lang="ts">
useHead({
  title: "Trello | Manage your Tasks", 
  meta: [
    { name: 'Trello Clone with Nuxt', content: 'This is a Trello Clone for the Airtribe take home project' }
  ],
})
</script>