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

    <!-- Image gallery with each image in a separate box -->
    <div v-if="images.length">
      <div class="gallery">
        <!-- Display each image with controls in a separate container -->
        <div
          v-for="(image, index) in images"
          :key="index"
          class="image-container"
        >
          <img :src="image.url" alt="Uploaded image" @click="openPreview(index)" />
          <!-- Metadata and Delete buttons for each image -->
          <button @click="showMetadata(index)">Show Metadata</button>
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

    <!-- Metadata Modal -->
    <div v-if="metadataVisible" class="metadata-modal">
      <div class="metadata-content">
        <button class="close-button" @click="closeMetadata">×</button>
        <h2>Image Metadata</h2>
        <p><strong>Name:</strong> {{ currentMetadata.name }}</p>
        <p><strong>Size:</strong> {{ currentMetadata.size }} bytes</p>
        <p><strong>Type:</strong> {{ currentMetadata.type }}</p>
        <p><strong>Dimensions:</strong> {{ currentMetadata.width }} x {{ currentMetadata.height }}</p>
      </div>
    </div>
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
      metadataVisible: false,
      currentMetadata: {},
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
      files.forEach((file) => {
        const reader = new FileReader();
        reader.onload = (e) => {
          const img = new Image();
          img.onload = () => {
            this.images.push({
              url: e.target.result,
              name: file.name,
              size: file.size,
              type: file.type,
              width: img.width,
              height: img.height,
            });
          };
          img.src = e.target.result;
        };
        reader.readAsDataURL(file);
      });
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
    showMetadata(index) {
      const image = this.images[index];
      this.currentMetadata = {
        name: image.name,
        size: image.size,
        type: image.type,
        width: image.width,
        height: image.height,
      };
      this.metadataVisible = true;
    },
    closeMetadata() {
      this.metadataVisible = false;
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
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  padding: 20px;
}

.image-container {
  position: relative;
  background-color: #ffffff;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  padding: 10px;
  width: 200px; /* Adjust the width as needed */
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.image-container img {
  max-width: 100%;
  max-height: 150px;
  border-radius: 4px;
  margin-bottom: 10px;
  transition: transform 0.2s;
}

.image-container img:hover {
  transform: scale(1.05);
}

.image-container button {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 3px 7px;
  cursor: pointer;
  opacity: 0.8;
  border-radius: 3px;
  margin-top: 4px;
  width: 70%; /* Buttons take full width within the container */
}

.image-container button:hover {
  opacity: 1;
}

.metadata-modal {
  position: fixed;
  top: 0;
  left: 0;  
  width: 100%;
  height: 100%;
  background: rgba(254, 254, 254, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.metadata-content {
  background: rgb(31, 170, 205);
  padding: 20px;
  border-radius: 10px;
  position: relative;
  width: 300px;
  max-width: 90%;
}

.close-button {
  position: absolute;
  top: 10px;
  right: 10px;
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
}
</style>
