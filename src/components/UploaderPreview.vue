<template>
  <div class="w-1/3 flex flex-col items-center">
    <h3 class="text-base font-semibold mb-4">Preview</h3>

    <!-- Phone Frame -->
    <div
      class="w-[320px] h-[640px] border-[12px] border-black rounded-[2rem] bg-white shadow-inner flex flex-col overflow-hidden relative"
    >
      <!-- Status Bar -->
      <div
        class="h-6 bg-black text-white text-xs flex justify-between items-center px-3"
      >
        <span>9:41</span>
        <div class="flex gap-1">
          <div class="w-4 h-2 bg-white rounded-xs"></div>
          <div class="w-3 h-2 bg-white rounded-xs"></div>
          <div class="w-2 h-2 bg-white rounded-xs"></div>
        </div>
      </div>

      <!-- Content Area -->
      <div class="flex-1 p-4 flex flex-col gap-2 bg-gray-50">
        <!-- Cover image or placeholder -->
        <img
          :src="data?.cover || placeholder"
          alt="Cover"
          class="w-full h-40 object-cover rounded-sm"
        />

        <!-- Title -->
        <h4 class="text-sm font-semibold mt-1">
          {{ data?.title || "No title provided" }}
        </h4>

        <!-- Like & Share Row in darker container -->
        <div class="bg-gray-200 rounded-md py-2 px-4">
          <div class="flex justify-between max-w-full">
            <div class="w-1/3 flex justify-center items-center gap-1 text-sm">
              <img src="../assets/icons/like.svg" alt="Like" class="w-5 h-5" />
              <span>{{ data?.likeCount || 0 }}</span>
            </div>
            <div class="w-1/3"></div>
            <div class="w-1/3 flex justify-center items-center gap-1 text-sm">
              <img
                src="../assets/icons/share.svg"
                alt="Share"
                class="w-5 h-5"
              />
              <span>{{ data?.shareCount || 0 }}</span>
            </div>
          </div>
        </div>

        <!-- Description -->
        <p class="text-xs text-gray-500">
          {{ data?.description || "No description provided" }}
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import placeholder from "../assets/images/go-placeholder.png";

defineProps({
  data: Object,
});
</script>
