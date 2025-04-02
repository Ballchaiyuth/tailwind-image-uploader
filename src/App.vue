<template>
  <div class="min-h-screen flex flex-col items-center justify-center space-y-6">
    <nav class="space-x-4 mt-6 text-blue-400">
      <RouterLink to="/" class="hover:underline">Home</RouterLink>
      <RouterLink to="/upload1" class="hover:underline">Uploader 1</RouterLink>
      <RouterLink to="/upload2" class="hover:underline">Uploader 2</RouterLink>
      <RouterLink to="/upload3" class="hover:underline">Uploader 3</RouterLink>
    </nav>

    <router-view />
  </div>
</template>
