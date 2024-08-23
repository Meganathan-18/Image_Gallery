<template>
  <div>
    <input type="file" @change="onFileChange" multiple />
    <button @click="uploadImages">Upload Images</button>

    <div v-if="images.length">
      <h3>Image Gallery</h3>
      <div class="gallery">
        <div
          v-for="(image, index) in images"
          :key="index"
          class="image-container"
          @click="openPreview(index)"
        >
          <img :src="image.url" alt="Uploaded image" />
          <p>{{ image.name }}</p>
        </div>
      </div>
    </div>

    <!-- Lightbox for image preview with metadata -->
    <vue-easy-lightbox
      :visible="visible"
      :imgs="lightboxImages"
      :index="currentIndex"
      @hide="visible = false"
    >
      <!-- Custom content inside lightbox -->
      <template v-slot:content>
        <div class="lightbox-content">
          <img :src="lightboxImages[currentIndex]" alt="Preview" />
          <div v-if="currentImageMetadata">
            <p>Name: {{ currentImageMetadata.name }}</p>
            <p>Size: {{ currentImageMetadata.size }} bytes</p>
            <p>Dimensions: {{ currentImageMetadata.width }}x{{ currentImageMetadata.height }} px</p>
          </div>
        </div>
      </template>
    </vue-easy-lightbox>
  </div>
</template>

<script>
import VueEasyLightbox from "vue-easy-lightbox";

export default {
  components: {
    VueEasyLightbox,
  },
  data() {
    return {
      selectedFiles: [],
      images: [],
      visible: false,
      currentIndex: 0,
    };
  },
  computed: {
    lightboxImages() {
      return this.images.map((image) => image.url);
    },
    currentImageMetadata() {
      return this.images[this.currentIndex]?.metadata || null;
    },
  },
  methods: {
    onFileChange(event) {
      const newFiles = Array.from(event.target.files);
      this.selectedFiles = [...this.selectedFiles, ...newFiles];
      this.previewImages(newFiles);
    },
    previewImages(files) {
      const newImages = files.map((file) => {
        const img = new Image();
        const url = URL.createObjectURL(file);
        img.src = url;

        return new Promise((resolve) => {
          img.onload = () => {
            resolve({
              url: url,
              name: file.name,
              size: file.size,
              width: img.width,
              height: img.height,
            });
          };
        });
      });

      Promise.all(newImages).then((images) => {
        this.images = [...this.images, ...images];
      });
    },
    async uploadImages() {
      const formData = new FormData();
      this.selectedFiles.forEach((file) => {
        formData.append("images[]", file);
      });

      try {
        // Example API endpoint for uploading images
        // const response = await axios.post('/api/upload', formData);

        // If using a real endpoint, you'd handle the response here

        // Simulate upload success
        alert("Images uploaded successfully!");

        // Clear selected files after upload
        this.selectedFiles = [];
      } catch (error) {
        console.error("Image upload failed:", error);
      }
    },
    openPreview(index) {
      this.currentIndex = index;
      this.visible = true;
    },
  },
};
</script>

<style scoped>
.gallery {
  display: flex;
  flex-wrap: wrap;
}
.image-container {
  margin: 10px;
  cursor: pointer;
}
.image-container img {
  max-width: 200px;
  max-height: 150px;
  transition: transform 0.2s;
}
.image-container img:hover {
  transform: scale(1.05);
}
.lightbox-content {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.lightbox-content img {
  max-width: 100%;
  max-height: 80vh;
}
</style>
