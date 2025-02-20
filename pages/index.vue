<template>
  <Header></Header>
  <div class="container">
    <h1 class="h1Judul">
      <span v-if="isLoggedIn">Hi, {{ user?.username }}.</span>
    </h1>
    <h1 class="h1Judul">Welcome to Storytime</h1>
    <p class="pJudul">
      The world's most-loved social storytelling platform. Storytime connects a global
      community of 90 million readers and writers through the power of story.
    </p>
    <div class="search-container">
      <input
        type="text"
        placeholder="Search story"
        class="search-input"
        @keyup.enter="goToSearchPage"
        v-model="searchquery"
      />
      <i class="fa-solid fa-magnifying-glass search-icon"></i>
    </div>
    <div class="imgAwal">
      <img src="../asset/home/imgJudul.png" alt="imgJudul" class="imgJudul" />
    </div>

    <!-- Latest Stories Section -->
    <div class="latest-stories">
      <div class="latest-judul justify-content-between align-items-center">
        <h2 class="h2Judul">Latest Story</h2>
        <nuxt-link class="d-flex explore text-decoration-none" to="/allStory">
          <p class="pExplore me-2">Explore More</p>
          <i class="fa-solid fa-arrow-right fs-3"></i>
        </nuxt-link>
      </div>
      <div class="custom-hr">
        <hr />
      </div>
      <div class="stories-grid" v-if="Array.isArray(stories) && stories.length > 0">
        <CardLatest
          v-for="(story, index) in stories"
          :id="story.id"
          :key="index"
          :imageSrc="`${ngrokUrl}/storage/${story.images[0].image_path}`"
          :profilePic="`${ngrokUrl}/storage/${story.users.profile_image}`"
          :title="story.title"
          :description="story.content"
          :userName="story.users.username"
          :createdAt="formatDate(story.created_at)"
          :category="story.category_id"
        />
      </div>
      <div v-else>LOADING...</div>
    </div>

    <!-- Comedy Section -->
    <div class="container-fluid comedy-section">
      <div class="comedy-judul justify-content-between align-items-center">
        <h2 class="h2Judul">Comedy</h2>
        <div class="d-flex explore">
          <p class="pExplore me-2">Explore More</p>
          <i class="fa-solid fa-arrow-right fs-3"></i>
        </div>
      </div>
      <div class="custom-hr">
        <hr />
      </div>
      <div class="row" v-if="Array.isArray(comedies) && comedies.length > 0">
        <div class="col-lg-8 col-md-6">
          <CardBig
            v-for="(comedy, index) in comedies.slice(0, 1)"
            :id="comedy.id"
            :key="index"
            :imageSrc="`${ngrokUrl}/storage/${comedy.images[0].image_path}`"
            :profilePic="`${ngrokUrl}/storage/${comedy.users.profile_image}`"
            :title="comedy.title"
            :description="comedy.content"
            :userName="comedy.users.username"
            :createdAt="formatDate(comedy.created_at)"
            :category="comedy.category_id"
          />
        </div>
        <div class="col-lg-4 col-md-6">
          <div class="d-flex flex-column">
            <CardSmall
              v-for="(comedy, index) in comedies.slice(1, 3)"
              :id="comedy.id"
              :key="index"
              :imageSrc="`${ngrokUrl}/storage/${comedy.images[0].image_path}`"
              :profilePic="`${ngrokUrl}/storage/${comedy.users.profile_image}`"
              :title="comedy.title"
              :description="comedy.content"
              :userName="comedy.users.username"
              :createdAt="formatDate(comedy.created_at)"
              :category="comedy.category_id"
            />
          </div>
        </div>
      </div>
      <div v-else>LOADING...</div>
    </div>

    <!-- Romance Section -->
    <div class="romance-section">
      <div class="romance-judul justify-content-between align-items-center">
        <h2 class="h2Judul">Romance</h2>
        <div class="d-flex explore">
          <p class="pExplore me-2">Explore More</p>
          <i class="fa-solid fa-arrow-right fs-3"></i>
        </div>
      </div>
      <div class="custom-hr">
        <hr />
      </div>
      <div class="row">
        <div
          class="col-lg-4 col-md-6"
          v-for="(romance, index) in romances.slice(0, 3)"
          :key="index"
          v-if="Array.isArray(romances) && romances.length > 0"
        >
          <Card
            :id="romance.id"
            :imageSrc="`${ngrokUrl}/storage/${romance.images[0].image_path}`"
            :profilePic="`${ngrokUrl}/storage/${romance.users.profile_image}`"
            :title="romance.title"
            :description="romance.content"
            :userName="romance.users.username"
            :createdAt="formatDate(romance.created_at)"
          />
        </div>
        <div v-else>LOADING...</div>
      </div>
    </div>

    <!-- Horror Section -->
    <div class="container-fluid horror-section">
      <div class="horror-judul justify-content-between align-items-center">
        <h2 class="h2Judul">Horror</h2>
        <div class="d-flex explore">
          <p class="pExplore me-2">Explore More</p>
          <i class="fa-solid fa-arrow-right fs-3"></i>
        </div>
      </div>
      <div class="custom-hr">
        <hr />
      </div>
      <div class="row" v-if="Array.isArray(horrors) && horrors.length > 0">
        <div class="col-lg-8 col-md-6">
          <CardBig
            v-for="(horror, index) in horrors.slice(0, 1)"
            :id="horror.id"
            :key="index"
            :imageSrc="`${ngrokUrl}/storage/${horror.images[0].image_path}`"
            :profilePic="`${ngrokUrl}/storage/${horror.users.profile_image}`"
            :title="horror.title"
            :description="horror.content"
            :userName="horror.users.username"
            :createdAt="formatDate(horror.created_at)"
            :category="horror.category_id"
          />
        </div>
        <div class="col-lg-4 col-md-6">
          <div class="d-flex flex-column">
            <CardSmall
              v-for="(horror, index) in horrors.slice(1, 3)"
              :id="horror.id"
              :key="index"
              :imageSrc="`${ngrokUrl}/storage/${horror.images[0].image_path}`"
              :profilePic="`${ngrokUrl}/storage/${horror.users.profile_image}`"
              :title="horror.title"
              :description="horror.content"
              :userName="horror.users.username"
              :createdAt="formatDate(horror.created_at)"
              :category="horror.category_id"
            />
          </div>
        </div>
      </div>
      <div v-else>LOADING...</div>
    </div>

    <!-- More Categories Section -->
    <div class="more-categories">
      <h2 class="h2Judul">More Categories</h2>
      <div class="custom-hr">
        <hr />
      </div>
      <div class="categories-container">
        <button class="category-button fontDmSans">Adventure</button>
        <button class="category-button fontDmSans">Fiction</button>
        <button class="category-button fontDmSans">Fantasy</button>
        <button class="category-button fontDmSans">Drama</button>
        <button class="category-button fontDmSans">Heartfelt</button>
        <button class="category-button fontDmSans">Mystery</button>
      </div>
    </div>

  </div>
  <Footer></Footer>
</template>

<script setup>
import { useAuthStore } from "@/store/auth";
import { ref, onMounted, computed } from "vue";
import Cookies from "js-cookie";
import axios from "axios";
import { ngrokUrl } from "@/store/ngrokConfig";
import Card from "@/components/Card.vue";
import CardBig from "@/components/CardBig.vue";
import CardSmall from "@/components/CardSmall.vue";
import CardLatest from "@/components/CardLatest.vue";
import { useRouter } from "vue-router";
import imageSrc from "../asset/home/test.jpg";
import profilePic from "../asset/home/test.jpg";

const searchquery = ref("");
const router = useRouter();
const authStore = useAuthStore();
const user = ref(null);
const isLoggedIn = computed(() => {
  return Cookies.get("isLoggedIn") === "true";
});

const getUserData = () => {
  const userData = Cookies.get("user");
  return userData ? JSON.parse(userData) : null;
};

// Format date function
const formatDate = (dateString) => {
  const date = new Date(dateString);
  return date.toLocaleDateString("en-US", {
    day: "numeric",
    month: "long",
    year: "numeric",
  });
};

const showLoginModal = ref(false);
const showRegisterModal = ref(false);
const stories = ref([]);
const romances = ref([]); // Pastikan ini ada di bagian setup
const comedies = ref([]); // Inisialisasi array komedi
const horrors = ref([]); // Inisialisasi array horror

// Fetch latest stories
const fetchLatestStories = async () => {
  try {
    const response = await axios.get(`${ngrokUrl}/api/stories`, {
      headers: {
        "ngrok-skip-browser-warning": "69420",
      },
    });
    stories.value = response.data.stories.data; // Access the nested data array
    // console.log("--data---");
    // console.log(stories.value);
  } catch (error) {
    console.error("Error fetching latest stories:", error);
  }
};

// Fetch comedy stories
const fetchComedyStories = async () => {
  try {
    const response = await axios.get(`${ngrokUrl}/api/stories-by-category/1`, {
      headers: {
        "ngrok-skip-browser-warning": "69420",
      },
    });
    comedies.value = response.data.data.data; // Access the nested data array
    // console.log("--data---");
    // console.log(comedies.value);
  } catch (error) {
    console.error("Error fetching latest stories:", error);
  }
};

const fetchRomanceStories = async () => {
  try {
    const response = await axios.get(`${ngrokUrl}/api/stories-by-category/8`, {
      headers: {
        "ngrok-skip-browser-warning": "69420",
      },
    });
    romances.value = response.data.data.data; // Access the nested data array
    console.log("--data---");
    console.log(romances.value);
  } catch (error) {
    console.error("Error fetching latest stories:", error);
  }
};

const fetchHorrorStories = async () => {
  try {
    const response = await axios.get(`${ngrokUrl}/api/stories-by-category/9`, {
      headers: {
        "ngrok-skip-browser-warning": "69420",
      },
    });
    horrors.value = response.data.data.data; // Access the nested data array
    console.log("--data---");
    console.log(horrors.value);
  } catch (error) {
    console.error("Error fetching latest stories:", error);
  }
};

const showSuccessModal = () => {
  if (authStore.isLoggedIn) {
    showLoginModal.value = true;
  } else if (authStore.isRegistered) {
    showRegisterModal.value = true;
  }

  setTimeout(() => {
    closeLoginModal();
    closeRegisterModal();
  }, 10000);
};

const goToSearchPage = () => {
  if (searchquery.value.trim()) {
    router.push(`/allstory?searchquery=${encodeURIComponent(searchquery.value)}`);
  }
};

onMounted(async () => {
  user.value = getUserData();
  await authStore.fetchUserData();
  await fetchLatestStories();
  await fetchRomanceStories(); // Ambil cerita romansa
  await fetchComedyStories(); // Ambil cerita komedi
  await fetchHorrorStories(); // Ambil cerita horor
  showSuccessModal();
});

watch(() => {
  console.log("user search query : " + searchquery.value);
});

const closeLoginModal = () => {
  showLoginModal.value = false;
};

const closeRegisterModal = () => {
  showRegisterModal.value = false;
};
</script>

<style scoped>
.fontPlayfair {
  font-family: playfair-display, serif;
}

.fontDmSans {
  font-family: dm-sans, sans-serif;
}

.container {
  text-align: center;
  padding: 50px;
}

.h1Judul {
  margin-top: 20px;
  font-family: playfair-display, serif;
  font-size: 50px;
  font-weight: 800;
  text-shadow: 2px 2px 5px rgba(0, 0, 0, 0.5);
}

.pJudul {
  margin-top: 20px;
  font-size: 20px;
  font-family: dm-sans, sans-serif;
}

.search-container {
  margin: auto;
  position: relative;
  margin-top: 30px;
  width: 800px;
}

.search-input {
  padding: 15px;
  width: 100%;
  border-radius: 10px;
  border: 2px solid #cccccc;
}

.search-icon {
  position: absolute;
  right: 15px;
  top: 50%;
  transform: translateY(-50%);
}

.imgAwal {
  margin-top: 40px;
}

.imgJudul {
  width: 650px;
  height: auto;
}

/* Latest Stories Styles */
.latest-stories {
  margin-top: 100px;
}

.h2Judul {
  font-family: playfair-display, serif;
  font-weight: bold;
}

.explore {
  font-size: 18px;
  position: relative; /* Untuk posisi border */
  cursor: pointer; /* Menunjukkan bahwa ini dapat di-hover */
  color: black;
}

.explore::after {
  content: ""; /* Membuat elemen pseudo */
  position: absolute; /* Mengatur posisi */
  left: 0; /* Mengatur posisi kiri */
  bottom: 0; /* Mengatur posisi bawah */
  width: 100%; /* Lebar border sama dengan teks */
  height: 1px; /* Tinggi border */
  background-color: transparent; /* Warna border awal */
  transition: background-color 0.3s ease; /* Transisi untuk efek halus */
}

.explore:hover::after {
  background-color: #31472f;
}

.explore:hover {
  color: #31472f;
}

.latest-judul {
  display: flex;
}

.custom-hr {
  margin-top: 20px;
  margin-bottom: 40px;
}

.stories-grid {
  display: flex;
  flex-direction: row;
  overflow-x: auto;
  overflow-y: hidden;
  scrollbar-width: none; /* Hide scrollbar for Firefox */
  -ms-overflow-style: none; /* Hide scrollbar for IE/Edge */
  scroll-snap-type: x mandatory;
  white-space: nowrap; /* Prevent wrapping */
  width: 100vw;
  left: 0;
}

.stories-grid::-webkit-scrollbar {
  display: none; /* Hide scrollbar for Chrome, Safari */
}

.story-card {
  background: #f9f9f9;
  border-radius: 10px;
  width: 350px;
  text-align: left;
  margin-right: 20px;
  scroll-snap-align: start;
  flex: 0 0 auto;
}

/* Comedy Section Styles */
.comedy-section {
  margin: 100px 0px;
}

.comedy-judul {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.comedy-grid {
  display: flex;
  justify-content: space-between; /* Aligns the columns */
  margin-top: 20px; /* Adds space between title and cards */
}

/* Romance Section Styles */
.romance-section {
  margin-top: 100px;
}

.romance-judul {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.romance-grid {
  display: grid;
  grid-template-columns: repeat(
    auto-fill,
    minmax(300px, 1fr)
  ); /* Adjust the minmax value as needed */
  gap: 20px; /* Space between cards */
  margin-top: 20px; /* Adds space between title and cards */
}

/* Horror Section Styles */
.horror-section {
  margin: 100px 0px;
}

.horror-judul {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.horror-grid {
  display: flex;
  justify-content: space-between; /* Aligns the columns */
  margin-top: 20px; /* Adds space between title and cards */
}

/* More Categories Section Styles */
.more-categories {
  margin: 100px 0px;
  text-align: left;
}

.categories-container {
  display: flex;
  justify-content: start;
  flex-wrap: wrap;
  gap: 20px; /* Space between buttons */
}

.category-button {
  background-color: #f0f5ed;
  border: none;
  border-radius: 5px;
  padding: 4% 4%;
  font-size: 24px;
  color: #31472f;
  cursor: pointer;
  transition: background-color 0.3s;
  font-weight: medium;
}

.category-button:hover {
  background-color: #d5e6d4;
  color: #222222;
}

/* Style Card */

.card-small {
  margin-left: 20px;
}
</style>
