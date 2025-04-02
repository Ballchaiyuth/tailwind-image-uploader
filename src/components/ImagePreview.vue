<template>
  <div
    class="relative group border rounded-md shadow-sm hover:shadow-lg overflow-hidden w-full"
  >
    <img
      :src="url"
      class="object-cover w-full h-32 cursor-pointer"
      @click="$emit('view')"
    />

    <!-- Action Buttons -->
    <div
      class="absolute inset-0 bg-black/50 opacity-0 group-hover:opacity-100 transition flex items-center justify-center"
    >
      <div class="flex gap-2">
        <button
          @click.stop="$emit('view')"
          title="Preview"
          class="p-1 bg-white rounded-full hover:bg-white/80"
        >
          <img src="../assets/icons/eye.svg" alt="View" class="w-5 h-5" />
        </button>
        <button
          @click.stop="$emit('replace')"
          title="Replace"
          class="p-1 bg-white rounded-full hover:bg-white/80"
        >
          <img
            src="../assets/icons/replace.svg"
            alt="Replace"
            class="w-5 h-5"
          />
        </button>
        <button
          @click.stop="$emit('remove')"
          title="Delete"
          class="p-1 bg-white rounded-full hover:bg-white/80"
        >
          <img src="../assets/icons/delete.svg" alt="Delete" class="w-5 h-5" />
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted } from "vue";

defineProps({
  url: String,
});

defineEmits(["view", "replace", "remove"]);

onMounted(() => {
  // You could put additional dynamic logic here if needed later
});
</script>
