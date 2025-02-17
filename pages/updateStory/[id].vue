<template>
    <Header></Header>
    <div class="add-story container">
      <div class="d-flex align-items-center">
        <nuxt-link to="/profile">
          <i class="text-dark fa-solid fa-arrow-left fs-1 me-5"></i>
        </nuxt-link>
        <h1>Write Story</h1>
      </div>
      <form @submit.prevent="submitStory">
        <div class="form-title my-5">
          <label for="title">Title</label>
          <input type="text" v-model="title" id="title" placeholder="Enter story title" required />
        </div>
  
        <div class="form-category my-5">
          <label for="category">Category</label>
          <select v-model="selectedCategory" id="category" required>
            <option value="" disabled selected>Select Category</option>
            <option v-for="category in categories" :key="category.id" :value="category.id">
              {{ category.name }}
            </option>
          </select>
        </div>
  
        <div class="form-content my-5">
          <label for="content">Content</label>
          <div class="content-editor">
            <div class="toolbar">
              <select @change="format('formatBlock', $event.target.value)">
                <option value="p">Normal</option>
                <option value="h1">Heading 1</option>
                <option value="h2">Heading 2</option>
                <option value="h3">Heading 3</option>
              </select>
              <button type="button" @click="format('bold')" title="Bold">
                <i class="fa-solid fa-bold ms-3 me-3"></i>
              </button>
              <button type="button" @click="format('italic')" title="Italic">
                <i class="fa-solid fa-italic me-3"></i>
              </button>
              <button type="button" @click="format('underline')" title="Underline">
                <i class="fa-solid fa-underline me-3"></i>
              </button>
              <button type="button" @click="promptForLink" title="Link">
                <i class="fa-solid fa-link me-3"></i>
              </button>
              <button type="button" @click="format('insertOrderedList')" title="Ordered List">
                <i class="fa-solid fa-list-ol me-3"></i>
              </button>
              <button type="button" @click="format('insertUnorderedList')" title="Unordered List">
                <i class="fa-solid fa-list-ul"></i>
              </button>
              <button type="button" @click="undo" title="Undo">
                <i class="fa-solid fa-undo ms-3 me-3"></i>
              </button>
              <button type="button" @click="redo" title="Redo">
                <i class="fa-solid fa-redo me-3"></i>
              </button>
            </div>
            <div class="textarea-wrapper" contenteditable="true" id="content" @input="updateContent($event)"
              placeholder="Enter content here"></div>
          </div>
        </div>
  
        <div class="form-cover-image my-5">
          <label for="coverImage">Cover Images</label>
          <div class="upload-container">
            <div class="upload-box" @click="triggerFileInput">
              <input type="file" accept="image/*" class="upload-input" @change="onFilesChange" ref="fileInput" multiple />
              <div v-if="!content_images.length" class="upload-content">
                <div class="upload-icon">
                  <i class="fa-solid fa-image"></i>
                </div>
                <span class="upload-text">Choose images</span>
              </div>
              <div v-else class="preview-grid">
                <div v-for="(file, index) in content_images" :key="index" class="preview-item">
                  <img :src="imagePreviews[index]" :alt="file.name" class="preview-image" />
                  <div class="preview-overlay">
                    <span class="file-name">{{ file.name }}</span>
                    <button class="remove-btn" @click.stop="removeImage(index)">
                      <i class="fa-solid fa-times"></i>
                    </button>
                  </div>
                </div>
                <div class="add-more" @click.stop="triggerFileInput">
                  <i class="fa-solid fa-plus"></i>
                  <span>Add More</span>
                </div>
              </div>
            </div>
          </div>
        </div>
  
        <div class="form-button my-5">
          <button type="button" @click="cancel" class="btn btnCancel me-5">Cancel</button>
          <button type="submit" class="btn btnPost">Post Story</button>
        </div>
      </form>
    </div>
    <Footer></Footer>
  </template>
  
  <script>
  import { useAuthStore } from '../store/auth';
  import Cookies from 'js-cookie';
  
  export default {
    data() {
      return {
        title: '',
        content: '',
        content_images: [],
        imagePreviews: [],
        categories: [],
        selectedCategory: '',
        history: [],
        historyIndex: -1,
      };
    },
    methods: {
      async submitStory() {
        try {
          // Validate form data
          if (!this.title || !this.selectedCategory || !this.content) {
            alert('Please fill in all required fields');
            return;
          }
  
          const authStore = useAuthStore();
          const formData = new FormData();
          const token = Cookies.get('authToken');
  
          formData.append('title', this.title);
          formData.append('category_id', this.selectedCategory);
          formData.append('content', this.content);
  
          // Append each image file
          this.content_images.forEach((file, index) => {
            formData.append(`content_images[${index}]`, file);
          });
  
          console.log('Submitting data:', {
            title: this.title,
            category_id: this.selectedCategory,
            content: this.content,
            content_images: this.content_images
          });
  
          const response = await fetch('https://c9d5-103-100-175-121.ngrok-free.app/api/stories', {
            method: 'POST',
            headers: {
              'Authorization': `Bearer ${token}`,
              'Accept': 'application/json',
              'ngrok-skip-browser-warning': '69420',
            },
            body: formData
          });
  
          // Log response details for debugging
          console.log('Response status:', response.status);
          console.log('response tOKEN', authStore.token);
          const responseText = await response.text();
          console.log('Response text:', responseText);
  
          if (!response.ok) {
            try {
              const errorData = JSON.parse(responseText);
              throw new Error(errorData.message || `HTTP error! status: ${response.status}`);
            } catch (e) {
              throw new Error(`Server error (${response.status}): ${responseText.substring(0, 100)}...`);
            }
          }
  
          let result;
          try {
            result = JSON.parse(responseText);
          } catch (e) {
            throw new Error('Invalid JSON response from server');
          }
  
          console.log('Story created successfully:', result);
  
          // Reset form after success
          this.cancel();
  
          // Show success message
          alert('Story has been created successfully!');
  
          // Redirect to profile page
          this.$router.push('/profile');
        } catch (error) {
          console.error('Error creating story:', error);
          alert(`Failed to create story: ${error.message}`);
        }
      },
  
      triggerFileInput() {
        this.$refs.fileInput.click();
      },
  
      onFilesChange(event) {
        const files = Array.from(event.target.files);
        files.forEach(file => {
          const isDuplicate = this.content_images.some(f => f.name === file.name);
          if (!isDuplicate && this.content_images.length < 5) {
            this.content_images.push(file);
            this.imagePreviews.push(URL.createObjectURL(file));
          }
        });
        event.target.value = '';
      },
  
      removeImage(index) {
        URL.revokeObjectURL(this.imagePreviews[index]);
        this.content_images.splice(index, 1);
        this.imagePreviews.splice(index, 1);
      },
  
      cancel() {
        this.title = '';
        this.selectedCategory = ''; // Changed from category to selectedCategory
        this.content = '';
        this.content_images = [];
        this.imagePreviews.forEach(url => URL.revokeObjectURL(url));
        this.imagePreviews = [];
        this.history = [];
        this.historyIndex = -1;
      },
  
      format(command, value) {
        document.execCommand(command, false, value);
        this.updateContent({ target: document.getElementById('content') });
      },
  
      updateContent(event) {
        const newContent = event.target.innerHTML;
        if (this.historyIndex === this.history.length - 1) {
          this.history.push(newContent);
        } else {
          this.history = this.history.slice(0, this.historyIndex + 1);
          this.history.push(newContent);
        }
        this.historyIndex++;
        this.content = newContent;
      },
  
      promptForLink() {
        const url = prompt('Enter URL:');
        if (url) {
          this.format('createLink', url);
        }
      },
  
      undo() {
        if (this.historyIndex > 0) {
          this.historyIndex--;
          this.content = this.history[this.historyIndex];
          document.getElementById('content').innerHTML = this.content;
        }
      },
  
      redo() {
        if (this.historyIndex < this.history.length - 1) {
          this.historyIndex++;
          this.content = this.history[this.historyIndex];
          document.getElementById('content').innerHTML = this.content;
        }
      },
  
      async fetchCategories() {
        try {
          const authStore = useAuthStore();
          const response = await fetch('https://c9d5-103-100-175-121.ngrok-free.app/api/categories', {
            method: 'GET',
            headers: {
              'Accept': 'application/json',
              'Content-Type': 'application/json',
              'ngrok-skip-browser-warning': '69420',
            },
          });
  
          if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
          }
  
          const data = await response.json();
          this.categories = data.data;
          console.log('Categories loaded:', this.categories);
        } catch (error) {
          console.error('Error fetching categories:', error);
          alert('Failed to load categories. Please refresh the page.');
        }
      }
    },
  
    mounted() {
      this.fetchCategories();
    },
  
    beforeUnmount() {
      this.imagePreviews.forEach(url => URL.revokeObjectURL(url));
    },
  };
  </script>