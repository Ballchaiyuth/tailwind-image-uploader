<template>
  <div class="flex flex-col min-h-screen w-full">
    <!-- Navigation Header -->
    <header class="nav-header">
      <nav class="nav-container">
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/upload1">Uploader 1</RouterLink>
        <RouterLink to="/upload2">Uploader 2</RouterLink>
        <RouterLink to="/upload3">Uploader 3</RouterLink>
        <RouterLink to="/upload4">Uploader 4</RouterLink>
      </nav>
    </header>

    <!-- Routed Content (fills remaining space) -->
    <main class="flex-1 overflow-y-auto">
      <router-view />
    </main>
  </div>
</template>

<style scoped>
.nav-header {
  @apply bg-gray-800 text-white shadow;
}

.nav-container {
  @apply max-w-5xl mx-auto px-4 py-4 flex justify-center gap-4 text-base;
}

.nav-container a {
  @apply px-3 py-1 rounded font-medium transition hover:bg-gray-700;
}
</style>
