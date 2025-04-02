<template>
  <div
    class="min-h-without-header flex flex-col items-center justify-center text-purple-400 p-4"
  >
    <h1 class="text-3xl font-bold mb-4">Image Uploader 3</h1>
    <p class="mb-6 text-purple-300">
      A full-featured uploader with modal preview, zoom, navigation,
      drag-and-drop, and image management.
    </p>

    <!-- Upload Box -->
    <div
      class="w-full max-w-2xl p-6 rounded-xl border border-purple-400 bg-white/10 backdrop-blur-md text-white shadow-md transition"
      @dragover.prevent
      @dragenter.prevent="isDragging = true"
      @dragleave.prevent="isDragging = false"
      @drop.prevent="handleDrop"
      :class="{ 'bg-purple-400/10': isDragging }"
    >
      <p class="text-center text-sm text-purple-400 mb-4">
        Drag & Drop images into the box or use the button below:
      </p>

      <!-- Drag-and-drop zone -->
      <div
        class="border-2 border-dashed border-purple-400 rounded-md p-6 flex flex-col items-center justify-center space-y-4 hover:bg-purple-200/10 transition cursor-pointer"
        @click="fileInput.click()"
      >
        <img
          src="../assets/icons/icon-upload.svg"
          alt="Upload Icon"
          class="h-10 w-10 opacity-80"
        />
        <span class="text-sm text-purple-400">
          Click to upload or drop files here
        </span>
      </div>

      <input
        ref="fileInput"
        type="file"
        accept="image/*"
        multiple
        class="hidden"
        @change="onFilesSelected"
      />

      <input
        ref="replaceInput"
        type="file"
        accept="image/*"
        class="hidden"
        @change="onReplaceSelected"
      />

      <div
        v-if="images.length"
        class="grid grid-cols-2 sm:grid-cols-3 gap-4 mt-6"
      >
        <div
          v-for="(img, i) in images"
          :key="i"
          class="relative group border rounded-md shadow-sm hover:shadow-lg overflow-hidden"
        >
          <img
            :src="img.url"
            :alt="img.name"
            class="object-cover w-full h-40 cursor-pointer"
            @click="openModal(i)"
          />

          <!-- Controls Overlay -->
          <div
            class="absolute inset-0 bg-black/50 opacity-0 group-hover:opacity-100 transition flex items-center justify-center"
          >
            <div class="flex gap-4">
              <!-- Preview -->
              <button
                @click.stop="openModal(i)"
                title="Preview"
                class="rounded-full p-1 bg-white hover:bg-white/80 transition"
              >
                <img
                  src="../assets/icons/eye.svg"
                  alt="Preview"
                  class="w-5 h-5"
                />
              </button>

              <!-- Replace -->
              <button
                @click.stop="triggerReplace(i)"
                title="Replace"
                class="rounded-full p-1 bg-white hover:bg-white/80 transition"
              >
                <img
                  src="../assets/icons/replace.svg"
                  alt="Replace"
                  class="w-5 h-5"
                />
              </button>

              <!-- Delete -->
              <button
                @click.stop="removeImage(i)"
                title="Delete"
                class="rounded-full p-1 bg-white hover:bg-white/80 transition"
              >
                <img
                  src="../assets/icons/delete.svg"
                  alt="Delete"
                  class="w-5 h-5"
                />
              </button>
            </div>
          </div>

          <div class="text-[12px] text-center p-2 text-gray-400">
            <p class="truncate">{{ img.name }}</p>
            <p>{{ formatSize(img.size) }}</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal Preview -->
    <div
      v-if="showModal"
      class="fixed inset-0 bg-black/80 flex items-center justify-center z-50"
    >
      <div
        class="relative bg-white rounded-md p-4 max-w-full max-h-[90vh] mx-4 flex flex-col items-center"
      >
        <!-- Image Preview Area -->
        <div
          class="overflow-hidden flex items-center justify-center cursor-grab active:cursor-grabbing"
          @mousedown="startDrag"
          @mousemove="onDrag"
          @mouseup="endDrag"
          @mouseleave="endDrag"
          @wheel.prevent="onWheelZoom"
        >
          <img
            :src="currentImage.url"
            :alt="currentImage.name"
            class="object-contain max-w-full max-h-[80vh] transition-transform"
            :style="zoomedStyle"
            draggable="false"
          />

          <!-- Navigation Buttons Floating Center Left/Right -->
          <button
            @click.stop="prevImage"
            class="absolute left-2 top-1/2 -translate-y-1/2 bg-purple-400/50 hover:bg-purple-600/50 text-white px-3 py-2 rounded-full shadow-md"
          >
            ←
          </button>
          <button
            @click.stop="nextImage"
            class="absolute right-2 top-1/2 -translate-y-1/2 bg-purple-400/50 hover:bg-purple-600/50 text-white px-3 py-2 rounded-full shadow-md"
          >
            →
          </button>
        </div>

        <!-- Footer Buttons -->
        <div
          class="mt-4 flex justify-between items-center text-sm text-gray-600 px-4 w-full"
        >
          <div>
            <p>
              <strong>{{ currentImage.name }}</strong>
            </p>
            <p>{{ formatSize(currentImage.size) }}</p>
          </div>
          <div class="flex gap-2">
            <button
              @click="zoomIn"
              class="bg-purple-500 hover:bg-purple-600 text-white px-3 py-1 rounded-sm"
            >
              +
            </button>
            <button
              @click="zoomOut"
              class="bg-purple-500 hover:bg-purple-600 text-white px-3 py-1 rounded-sm"
            >
              −
            </button>
            <button
              @click="resetZoom"
              class="bg-purple-500 hover:bg-purple-600 text-white px-3 py-1 rounded-sm"
            >
              100%
            </button>
            <a
              :href="currentImage.url"
              target="_blank"
              class="bg-blue-500 hover:bg-blue-600 text-white px-3 py-1 rounded-sm"
            >
              Open</a
            >
            <button
              @click="closeModal"
              class="bg-gray-400 hover:bg-gray-500 text-white px-3 py-1 rounded-sm"
            >
              Close
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from "vue";

const fileInput = ref(null);
const replaceInput = ref(null);
const replaceIndex = ref(null);

const images = ref([]);
const isDragging = ref(false);

const showModal = ref(false);
const currentIndex = ref(null);

const zoom = ref(1);
const isDraggingImage = ref(false);
const dragStart = ref({ x: 0, y: 0 });
const dragOffset = ref({ x: 0, y: 0 });

const currentImage = computed(() => images.value[currentIndex.value] || {});
const zoomedStyle = computed(() => ({
  transform: `scale(${zoom.value}) translate(${dragOffset.value.x}px, ${dragOffset.value.y}px)`,
  transition: isDraggingImage.value ? "none" : "transform 0.3s",
}));

function onFilesSelected(event) {
  const files = event.target.files;
  handleFiles(files);
  fileInput.value.value = "";
}

function handleDrop(event) {
  const files = event.dataTransfer.files;
  handleFiles(files);
}

function handleFiles(fileList) {
  for (const file of fileList) {
    if (file.type.startsWith("image/")) {
      const url = URL.createObjectURL(file);
      images.value.push({
        name: file.name,
        size: file.size,
        url,
        objectUrl: url,
      });
    }
  }
}

function removeImage(index) {
  const removed = images.value.splice(index, 1)[0];
  URL.revokeObjectURL(removed?.objectUrl);
  if (index === currentIndex.value) closeModal();
}

function triggerReplace(index) {
  replaceIndex.value = index;
  replaceInput.value?.click();
}

function onReplaceSelected(event) {
  const file = event.target.files[0];
  if (file && file.type.startsWith("image/") && replaceIndex.value !== null) {
    const url = URL.createObjectURL(file);
    const old = images.value[replaceIndex.value];
    URL.revokeObjectURL(old?.objectUrl);

    images.value[replaceIndex.value] = {
      name: file.name,
      size: file.size,
      url,
      objectUrl: url,
    };
  }
  replaceInput.value.value = "";
  replaceIndex.value = null;
}

function formatSize(bytes) {
  if (bytes > 1048576) return (bytes / 1048576).toFixed(2) + " MB";
  if (bytes > 1024) return (bytes / 1024).toFixed(1) + " KB";
  return `${bytes} B`;
}

function openModal(index) {
  currentIndex.value = index;
  resetZoom();
  showModal.value = true;
}

function closeModal() {
  showModal.value = false;
  resetZoom();
}

function zoomIn() {
  zoom.value = Math.min(zoom.value + 0.25, 3);
}

function zoomOut() {
  zoom.value = Math.max(zoom.value - 0.25, 0.5);
}

function resetZoom() {
  zoom.value = 1;
  dragOffset.value = { x: 0, y: 0 };
}

function startDrag(e) {
  if (zoom.value <= 1) return;
  isDraggingImage.value = true;
  dragStart.value = { x: e.clientX, y: e.clientY };
}

function onDrag(e) {
  if (!isDraggingImage.value) return;
  const dx = e.clientX - dragStart.value.x;
  const dy = e.clientY - dragStart.value.y;
  dragOffset.value.x += dx;
  dragOffset.value.y += dy;
  dragStart.value = { x: e.clientX, y: e.clientY };
}

function endDrag() {
  isDraggingImage.value = false;
}

function prevImage() {
  if (images.value.length < 2) return;
  currentIndex.value =
    (currentIndex.value - 1 + images.value.length) % images.value.length;
  resetZoom();
}

function nextImage() {
  if (images.value.length < 2) return;
  currentIndex.value = (currentIndex.value + 1) % images.value.length;
  resetZoom();
}

function handleKeyDown(e) {
  if (!showModal.value) return;
  if (e.key === "Escape") {
    closeModal();
  } else if (e.key === "ArrowLeft") {
    prevImage();
  } else if (e.key === "ArrowRight") {
    nextImage();
  }
}

onMounted(() => {
  window.addEventListener("keydown", handleKeyDown);
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", handleKeyDown);
});
</script>
