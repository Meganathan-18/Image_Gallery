<template>
  <div>
    <!-- Custom styled label for the file input -->
    <label for="file-upload" class="custom-file-upload">
      Choose Files
    </label>
    <input
      id="file-upload"
      type="file"
      @change="onFileChange"
      multiple
      style="display: none;"
    />

    <!-- Display message when no files are chosen -->
    <p v-if="selectedFiles.length === 0 && images.length === 0">No file chosen</p>

    <!-- Image gallery -->
    <div v-if="images.length">
      <div class="gallery">
        <!-- Display each image with a click handler to open in lightbox -->
        <div
          v-for="(image, index) in images"
          :key="index"
          class="image-container"
        >
          <img :src="image.url" alt="Uploaded image" @click="openPreview(index)" />
          <!-- Delete button for each image -->
          <button @click="deleteImage(index)">Delete</button>
        </div>
      </div>
    </div>

    <!-- Lightbox for image preview -->
    <vue-easy-lightbox
      :visible="visible"
      :imgs="lightboxImages"
      :index="currentIndex"
      @hide="visible = false"
    />
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
  },
  methods: {
    onFileChange(event) {
      const newFiles = Array.from(event.target.files);
      this.selectedFiles = [...this.selectedFiles, ...newFiles];
      this.previewImages(newFiles);
      this.uploadImages(newFiles); // Automatically upload images when selected
    },
    previewImages(files) {
      const newImages = files.map((file) => ({
        url: URL.createObjectURL(file),
      }));
      this.images = [...this.images, ...newImages];
    },
    async uploadImages(files) {
      const formData = new FormData();
      files.forEach((file) => {
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
    deleteImage(index) {
      // Remove the image from the images array
      this.images.splice(index, 1);
      // Also remove the corresponding file from selectedFiles array
      this.selectedFiles.splice(index, 1);
    },
    openPreview(index) {
      this.currentIndex = index;
      this.visible = true;
    },
  },
};
</script>

<style scoped>
.custom-file-upload {
  display: inline-block;
  padding: 12px 24px;
  cursor: pointer;
  font-size: 18px;
  color: white;
  background-color: #007bff;
  border: none;
  border-radius: 4px;
  text-align: center;
  transition: background-color 0.2s;
}

.custom-file-upload:hover {
  background-color: #0056b3;
}

.gallery {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 20px;
  padding: 20px;
  background-color: #f0f0f0;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  margin-top: 20px;
  position: relative;
  overflow: hidden;
}

.image-container {
  position: relative;
  background-color: #ffffff;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  padding: 10px;
  z-index: 1;
  text-align: center;
}

.image-container img {
  max-width: 100%;
  max-height: 150px;
  border-radius: 4px;
  transition: transform 0.2s;
}

.image-container img:hover {
  transform: scale(1.05);
}

.image-container button {
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  background-color: red;
  color: white;
  border: none;
  padding: 5px 10px;
  cursor: pointer;
  opacity: 0.8;
  border-radius: 4px;
  z-index: 2;
}

.image-container button:hover {
  opacity: 1;
}
</style>
