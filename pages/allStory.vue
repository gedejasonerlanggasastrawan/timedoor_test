<template>
  <Header class="mb-5"></Header>
  <h1 class="container my-4 fontPlayfair fw-bold">All Stories</h1>
  <nav class="breadcrumb container-fluid fontDmSans">
    <a href="/" class="breadcrumb-item"><span>Home</span></a>
    <span class="breadcrumb-separator">/</span>
    <a href="allStory" class="breadcrumb-item"><span>All Stories</span></a>
  </nav>
  <div class="container fontDmSans">
    <div class="d-flex align-items-center justify-content-between">
      <div class="filters my-4">
        <div class="sort me-5">
          <label for="sort">Sort By :</label>
          <select id="sort" v-model="sortOption">
            <option value="newest">Newest</option>
            <option value="popular">Popular</option>
            <option value="az">A - Z</option>
            <option value="za">Z - A</option>
          </select>
        </div>
        <div class="category">
          <label for="category">Category :</label>
          <select id="category" v-model="selectedCategory">
            <option value="all">All</option>
            <option value="1">Comedy</option>
            <option value="7">Romance</option>
            <option value="8">Horror</option>
            <option value="4">Drama</option>
            <option value="2">Fiction</option>
            <option value="3">Fantasy</option>
            <option value="5">Heartfelt</option>
            <option value="6">Mystery</option>
          </select>
        </div>
      </div>
      <div>
        <div class="search-container">
          <input
            type="text"
            v-model="searchQuery"
            placeholder="Search story"
            class="search-input"
          />
          <i class="fa-solid fa-magnifying-glass search-icon"></i>
        </div>
      </div>
    </div>
    <div class="story-list pt-5">
      <Card
        v-for="story in paginatedStories"
        :key="story.title"
        :imageSrc="`${ngrokUrl}/storage/${story.images[0].image_path}`"
        :profilePic="`${ngrokUrl}/storage/${story.users.profile_image}`"
        :title="story.title"
        :description="story.content"
        :userName="story.users.username"
        :createdAt="story.created_at"
        :category="story.category_id"
      />
    </div>
    <div class="pagination py-5 my-5">
      <span v-for="page in totalPages" :key="page">
        <button
          v-if="
            page === 1 ||
            page === totalPages ||
            (page >= currentPage - 1 && page <= currentPage + 1)
          "
          @click="currentPage = page"
          :class="{ active: currentPage === page }"
        >
          {{ page }}
        </button>
        <span v-if="page === currentPage + 2">...</span>
      </span>
      <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
    </div>
  </div>
  <Footer class="mt-5"></Footer>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { useAuthStore } from "@/store/auth";
import Card from "@/components/Card.vue"; // Import Card.vue
import imageSrc from "../asset/home/test.jpg"; // Gambar placeholder
import profilePic from "../asset/home/test.jpg"; // Gambar profil placeholder
import { ngrokUrl } from "@/store/ngrokConfig";
import { useRoute } from "vue-router";
import axios from "axios";

const authStore = useAuthStore();
const route = useRoute();
const sortOption = ref("newest");
const selectedCategory = ref("all");
// const searchQuery = ref("");
const currentPage = ref(1);
const itemsPerPage = 12; // Jumlah item per halaman diubah menjadi 12

const stories = ref([]);

const searchQuery = computed(() => route.query.searchquery || "");

const filteredStories = computed(() => {
  return stories.value
    .filter((story) => {
      return (
        selectedCategory.value === "all" || story.category_id === selectedCategory.value
      );
    })
    .filter((story) =>
      story.title.toLowerCase().includes(searchQuery.value.toLowerCase())
    );
});

const totalPages = computed(() => {
  return Math.ceil(filteredStories.value.length / itemsPerPage);
});

const paginatedStories = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  return filteredStories.value.slice(start, start + itemsPerPage);
});

const nextPage = () => {
  if (currentPage.value < totalPages.value) {
    currentPage.value++;
  }
};

// Fetch comedy stories
const fetchAllStories = async () => {
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

// lastest
const fetchLastest = async () => {
  try {
    const response = await axios.get(`${ngrokUrl}/api/newest-stories`, {
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

// newest
const fetchNewst = async () => {
  try {
    const response = await axios.get(`${ngrokUrl}/api/newest-stories`, {
      headers: {
        "ngrok-skip-browser-warning": "69420",
      },
    });
    stories.value = response.data.data.data; // Access the nested data array
    // console.log("--data---");
    // console.log(stories.value);
  } catch (error) {
    console.error("Error fetching latest stories:", error);
  }
};

// popular-stories
const fetchPopularStories = async () => {
  try {
    const response = await axios.get(`${ngrokUrl}/api/PopularStories`, {
      headers: {
        "ngrok-skip-browser-warning": "69420",
      },
    });
    stories.value = response.data.stories.data; // Access the nested data array
    // console.log("--data---");
    console.log(stories.value);
  } catch (error) {
    console.error("Error fetching latest stories:", error);
  }
};

// stories-a-z
const fetchStoriesAZ = async () => {
  try {
    const response = await axios.get(`${ngrokUrl}/api/stories-a-z`, {
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

// stories-z-a
const fetchStoriesZA = async () => {
  try {
    const response = await axios.get(`${ngrokUrl}/api/stories-z-a`, {
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

onMounted(async () => {
  await authStore.fetchUserData(); // Fetch user data on component mount
  await fetchAllStories();
});

watch(async() => {
  if (sortOption.value == "newest") {
    await fetchNewst();
  }
  if (sortOption.value == "popular") {
    await fetchPopularStories();
  }
  if (sortOption.value == "az") {
    await fetchStoriesAZ();
  }
  if (sortOption.value == "za") {
    await fetchStoriesZA();
  }
  console.log(selectedCategory.value);


  // 


});
</script>

<style scoped>
.fontPlayfair {
  font-family: playfair-display, serif;
}

.fontDmSans {
  font-family: dm-sans, sans-serif;
}

.container-fluid {
  padding: 25px 100px;
  max-height: 70px;
  background-color: #f8f9fa; /* Warna latar belakang untuk container */
  border-radius: 5px; /* Membuat sudut container menjadi melengkung */
}

.breadcrumb {
  display: flex; /* Mengatur elemen dalam satu baris */
  align-items: center; /* Menyelaraskan elemen secara vertikal */
}

.breadcrumb-item {
  margin: 0 10px; /* Menambahkan jarak horizontal antar elemen */
  text-decoration: none; /* Menghilangkan garis bawah pada tautan */
}

.breadcrumb-item:hover {
  border-bottom: 2px solid #222222; /* Mengatur border bawah saat hover */
}

.breadcrumb-separator {
  margin: 0 10px; /* Menambahkan jarak horizontal pada pemisah */
}

.breadcrumb {
  margin-bottom: 20px; /* Jarak antara breadcrumb dan judul */
  font-size: 14px; /* Ukuran font untuk breadcrumb */
  background-color: #f0f5ed;
  display: flex; /* Mengatur elemen dalam satu baris */
  align-items: center; /* Menyelaraskan elemen secara vertikal */
}

.breadcrumb a {
  text-decoration: none; /* Menghilangkan garis bawah pada link */
  color: #222222; /* Warna link */
}

.breadcrumb span {
  color: #222222; /* Mengubah warna teks untuk bagian yang tidak bisa diklik */
}

.filters {
  display: flex;
  align-items: center; /* Pastikan semua elemen sejajar secara vertikal */
  font-size: 18px;
}

.sort,
.category {
  display: flex;
  align-items: center; /* Menjaga label dan dropdown sejajar */
  margin-right: 20px; /* Memberikan jarak antara sort dan category */
}

label {
  margin-right: 10px; /* Memberikan jarak antara label dan dropdown */
}

.sort select,
.category select {
  border: none; /* Menghilangkan border pada dropdown */
  outline: none; /* Menghilangkan outline saat fokus */
  padding: 5px; /* Menambahkan padding untuk dropdown */
}

.search-container {
  position: relative; /* Pastikan kontainer memiliki posisi relatif */
  width: 400px; /* Atur lebar kontainer */
}

.search-input {
  padding: 15px;
  padding-right: 40px; /* Beri ruang untuk ikon di dalam input */
  width: 100%; /* Pastikan input mengisi lebar kontainer */
  border-radius: 10px;
  border: 2px solid #cccccc;
  font-size: 20px;
}

.search-icon {
  position: absolute; /* Posisi absolut untuk menempatkan ikon */
  right: 25px; /* Jarak dari kanan */
  top: 50%; /* Posisikan di tengah vertikal */
  transform: translateY(-50%); /* Sesuaikan posisi vertikal */
  pointer-events: none; /* Agar klik tidak mengganggu input */
  font-size: 20px;
}

.story-list {
  display: grid;
  grid-template-columns: repeat(3, 1fr); /* Maksimal 3 kolom */
  gap: 20px; /* Jarak antar kartu */
}

.pagination {
  display: flex;
  justify-content: center; /* Pusatkan pagination */
  margin-top: 20px; /* Jarak atas untuk pagination */
}

.pagination button:hover {
  background-color: #31472f; /* Warna untuk tombol aktif */
  color: white; /* Warna teks untuk tombol aktif */
  transition: all 0.3s ease-in-out;
}

.pagination button {
  padding: 15px 25px; /* Padding untuk tombol */
  margin: 0 5px; /* Jarak antar tombol */
  border: none; /* Menghilangkan border */
  background-color: #f0f5ed; /* Warna latar belakang tombol */
  color: black; /* Warna teks tombol */
  border-radius: 5px; /* Sudut melengkung */
  cursor: pointer; /* Kursor pointer saat hover */
  font-size: 20px;
  font-weight: bold;
}

.pagination button:disabled {
  background-color: #cccccc; /* Warna latar belakang tombol yang dinonaktifkan */
  cursor: not-allowed; /* Kursor tidak diizinkan saat dinonaktifkan */
  pointer-events: none; /* Nonaktifkan interaksi dengan tombol */
  opacity: 0.6; /* Mengurangi opasitas untuk menunjukkan bahwa tombol dinonaktifkan */
}

.pagination button.active {
  background-color: #31472f; /* Warna untuk tombol aktif */
  color: white; /* Warna teks untuk tombol aktif */
}
</style>
