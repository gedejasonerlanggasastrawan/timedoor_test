<template>
  <div>
    <Loading v-if="loadingStatus" />
    <Notif v-if="notifBookmark" message="Saved Bookmark!" />
    <Header></Header>

    <!-- Breadcrumb Navigation -->
    <nav class="breadcrumb container-fluid fontDmSans">
      <a href="/" class="breadcrumb-item"><span>Home</span></a>
      <span class="breadcrumb-separator">/</span>
      <a href="allStory" class="breadcrumb-item"
        ><span>{{ data_.title }}</span>
      </a>
    </nav>

    <main class="detail-page container" v-if="data_.title">
      <article class="detail-content">
        <div class="detail-header">
          <p class="date fontDmSans">{{ formatDate(data_.created_at) }}</p>
          <h1 class="title my-5 fontPlayfair">{{ data_.title }}</h1>
          <div class="author-info mb-5">
            <img
              :src="`${ngrokUrl}/storage/${data_.users.profile_image}`"
              alt="Profile Picture"
              class="profile-pic"
            />
            <p class="author fontDmSans">{{ data_.users.name }}</p>
          </div>
        </div>
        <div class="row content-wrapper mb-5">
          <div class="col-md-4 pe-4">
            <img
              :src="`${ngrokUrl}/storage/${data_.images[0].image_path}`"
              alt="Guardians of the Galaxy"
              class="cover-image"
            />
            <div class="image-grid">
              <div class="grid-container">
                <img
                  v-for="(img, index) in data_.images"
                  :key="index"
                  :src="`${ngrokUrl}/storage/${img.image_path}`"
                  alt="Gallery Image"
                  class="grid-image"
                />
              </div>
            </div>
          </div>
          <div class="col-md-8 text-content ps-4 mb-5 fontDmSans">
            {{ data_.content }}
          </div>
        </div>
        <div class="bookmark-icon" @click="bookmarkStory"></div>
      </article>

      <!-- Similar Stories Section -->
      <section class="similar-stories mt-5">
        <h2 class="fontPlayfair">Similar Stories</h2>
        <hr class="my-5" />
        <div
          class="d-flex flex-wrap"
          v-if="Array.isArray(dataall_) && dataall_.length > 0"
        >
          <CardLatest
            v-for="(item, index) in dataall_"
            :key="index"
            :imageSrc="`${ngrokUrl}/storage/${item.images[0].image_path}`"
            :profilePic="`${ngrokUrl}/storage/${item.users.profile_image}`"
            :title="item.title"
            :description="item.content"
            :userName="item.users.username"
            :createdAt="item.created_at"
            :category="item.category_id"
            class="mb-5"
          />
        </div>
        <div v-else>Loading...</div>
      </section>
    </main>

    <div v-else>Loading...</div>
    <Footer class="mt-5"></Footer>
  </div>
</template>

<script setup>
import imageSrc from "../asset/home/test.jpg"; // Adjust the path as necessary
import profilePic from "../asset/home/test.jpg"; // Use the specified profile picture
import CardLatest from "~/components/CardLatest.vue";
import { ngrokUrl } from "@/store/ngrokConfig";
import axios from "axios";

const notifBookmark = ref(false);
const data_ = ref([]);
const dataall_ = ref([]);
const route = useRoute();
const loadingStatus = ref(false);

// format date
function formatDate(isoString) {
  const date = new Date(isoString);
  const options = { year: "numeric", month: "long", day: "2-digit" };
  return date.toLocaleDateString("en-US", options);
}

// Fetch detail
const fetchData = async () => {
  loadingStatus.value = true;

  try {
    const response = await axios.get(`${ngrokUrl}/api/stories/${route.params.id}`, {
      headers: {
        "ngrok-skip-browser-warning": "69420",
      },
    });
    data_.value = response.data.story; // Access the nested data array
    console.log("--data---");
    console.log(data_.value);
  } catch (error) {
    console.error("Error fetching latest stories:", error);
  } finally {
    loadingStatus.value = false;
  }
};

// Fetch All
const fetchAll = async () => {
  loadingStatus.value = true;
  try {
    const response = await axios.get(`${ngrokUrl}/api/stories`, {
      headers: {
        "ngrok-skip-browser-warning": "69420",
      },
    });
    dataall_.value = response.data.stories.data; // Access the nested data array
    console.log("--data---");
    console.log(dataall_.value);
  } catch (error) {
    console.error("Error fetching latest stories:", error);
  } finally {
    loadingStatus.value = false;
  }
};

onMounted(async () => {
  await fetchData();
  await fetchAll();
});

const bookmarkStory = async () => {
  const token = localStorage.getItem("TOKEN");
  const formData = new FormData();

  formData.append("story_id", route.params.id);

  // check login
  if (token) {

    loadingStatus.value = true;

    try {
      const response = await axios.post(`${ngrokUrl}/api/bookmarks`, formData, {
        headers: {
          "ngrok-skip-browser-warning": "69420",
          Authorization: `Bearer ${token}`, // Add Sanctum Token
          Accept: "application/json",
        },
        method: "POST",
      });
      // comedies.value = response.data.data.data; // Access the nested data array
      // console.log("--data---");
      // console.log(comedies.value);
      
      setTimeout(() => {
        notifBookmark.value = true;
      }, 1000);

    } catch (error) {
      console.error("Error fetching latest stories:", error);
    } finally {
      loadingStatus.value = false;
    }

  } else {
    alert('please login before..')
  }

  // Show success message
  // alert("Bookmark Successfully!");
};
</script>

<style scoped>
.fontPlayfair {
  font-family: playfair-display, serif;
}

.fontDmSans {
  font-family: dm-sans, sans-serif;
}

/* Breadcrumb Styles */
.fontPlayfair {
  font-family: playfair-display, serif;
}

.fontDmSans {
  font-family: dm-sans, sans-serif;
}

.detail-page {
  padding: 20px;
}

.detail-header {
  text-align: center;
  margin-bottom: 20px;
}

.detail-content {
  margin: auto;
}

.title {
  font-size: 50px;
  font-weight: bold;
  margin-bottom: 10px;
}

.date,
.author {
  color: black;
  margin: auto 10px;
}

.author-info {
  display: flex;
  align-items: center;
  justify-content: center;
}

.profile-pic {
  width: 3%; /* Adjust size */
  height: 3%; /* Adjust size */
  border-radius: 50%; /* Make it circular */
}

.content-wrapper {
  margin: 20px 0;
}

.cover-image {
  width: 100%;
  height: auto;
  border-radius: 10px;
}

.image-grid {
  margin-top: 10px;
  overflow-x: auto;
}

.grid-container {
  display: flex;
  gap: 10px;
}

.grid-image {
  width: 150px;
  height: auto;
  border-radius: 10px;
}

.similar-stories {
  margin-top: 40px;
}

.similar-stories h2 {
  font-weight: bold;
}

/* Bookmark Styles */
.bookmark-icon {
  position: absolute; /* Change to absolute for precise positioning */
  top: 250px; /* Adjust to position it higher or lower */
  right: 150px; /* Adjust to position it left or right */
  width: 50px; /* Adjust size */
  height: 50px; /* Adjust size */
  background-color: #466543; /* Background color */
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
  border-radius: 50%;
  transition: transform 0.3s ease; /* Transition for hover effect */
}

.bookmark-icon::after {
  content: "";
  width: 55%; /* Adjust size */
  height: 55%; /* Adjust size */
  background-image: url("../../asset/icon/iconBookmark.png"); /* Path to your icon */
  background-size: cover; /* Cover the entire area */
}

.breadcrumb {
  display: flex; /* Arrange elements in a single row */
  align-items: center; /* Vertically align elements */
  margin-bottom: 20px; /* Space between breadcrumb and title */
  font-size: 16px; /* Font size for breadcrumb */
  background-color: #f0f5ed; /* Background color for breadcrumb */
  padding: 25px 100px;
}

.breadcrumb-item {
  margin: 0 10px; /* Horizontal spacing between elements */
  text-decoration: none; /* Remove underline from links */
}

.breadcrumb-item:hover {
  border-bottom: 2px solid #222222; /* Underline on hover */
}

.breadcrumb-separator {
  margin: 0 10px; /* Horizontal spacing for separator */
}

.breadcrumb a {
  text-decoration: none; /* Remove underline from links */
  color: #222222; /* Link color */
}

.breadcrumb span {
  color: #222222; /* Color for non-clickable text */
}
</style>
