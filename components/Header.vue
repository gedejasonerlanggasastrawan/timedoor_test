<template>
  <Notif v-if="logoutStatus" message="You have successfully logout." />
  <Loading v-if="loadingStatus"/>

  <nav class="navbar navbar-expand-lg bg-white p-3 sticky-top">
    <div class="container">
      <!-- Logo -->
      <nuxt-link class="navbar-brand" to="/">
        <img src="../asset/logo/storytimeLogo.png" alt="Logo" class="logo" />
      </nuxt-link>

      <!-- Buttons -->
      <div class="d-flex align-items-center">
        <nuxt-link v-if="!isLoggedIn" to="/register">
          <button class="btn register me-2">Register</button>
        </nuxt-link>
        <nuxt-link v-if="!isLoggedIn" to="/login">
          <button class="btn login">Login</button>
        </nuxt-link>
        <div v-if="isLoggedIn" class="dropdown">
          <button
            class="btn profile dropdown-toggle d-flex align-items-center fw-bold"
            id="profileDropdown"
            data-bs-toggle="dropdown"
            aria-expanded="false"
          >
            <img
              v-if="user?.image"
              :src="user.image"
              alt="Profile Image"
              class="profile-image me-2"
            />
            <img
              v-else
              :src="defaultImage"
              alt="Default Profile Image"
              class="profile-image me-2"
            />
            {{ user.name || "Loading..." }}
          </button>
          <ul class="dropdown-menu" aria-labelledby="profileDropdown">
            <li>
              <nuxt-link class="dropdown-item" to="/profile">My Profile</nuxt-link>
            </li>
            <li>
              <button class="dropdown-item" @click="openLogoutModal">Logout</button>
            </li>
          </ul>
        </div>
      </div>
    </div>

    <!-- Logout Modal -->
    <div v-if="isLogoutModalOpen" class="modal-overlay">
      <div class="modal-content">
        <div class="align-items-center">
          <h2 class="fs-2 mb-3">Logout</h2>
        </div>
        <p>Are you sure you want to logout?</p>
        <div class="button mt-3 align-items-center">
          <button @click="closeLogoutModal" class="btn btn-cancel me-5">Cancel</button>
          <button @click="logout" class="btn btn-logout">Logout</button>
        </div>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { useAuthStore } from "@/store/auth";
import defaultImage from "@/asset/icon/User.png"; // Import default image
import { ref, computed, watch } from "vue"; // Import ref and computed
import { useRouter } from "vue-router"; // Import useRouter
import Cookies from "js-cookie"; // Import js-cookie for cookie management
import axios from "axios";
import { ngrokUrl } from "@/store/ngrokConfig";
import { useRoute } from "vue-router";

const loadingStatus = ref(false)
const route = useRoute();
const logoutStatus = ref(false);
const authStore = useAuthStore();
const isLogoutModalOpen = ref(false); // State for logout modal
const router = useRouter(); // Initialize router

// Reactive property for login status
const isLoggedIn = computed(() => {
  // return Cookies.get('isLoggedIn') === 'true';
  // return true;
  const token = localStorage.getItem("TOKEN"); // Save token to localStorage
  if (token) {
    return true;
  }
});

// Function to get user data from cookies

const user = ref({});
// const getUserData = async () => {
//   await authStore.fetchUserData();
//   console.log("yui", authStore.user);
//   user.value = authStore.user;
// };
// Get user data from cookies
// getUserData();

const openLogoutModal = () => {
  isLogoutModalOpen.value = true; // Show logout modal
};

const closeLogoutModal = () => {
  isLogoutModalOpen.value = false; // Close logout modal
};

const userFetchData = async () => {
  try {
    // await authStore.fetchUserData();
    const tokenid = localStorage.getItem("IDUSER");
    const token = localStorage.getItem("TOKEN");

    const response = await axios.get(`${ngrokUrl}/api/user/${tokenid}`, {
      headers: {
        "ngrok-skip-browser-warning": "69420",
        Authorization: `Bearer ${token}`, // Add Sanctum Token
        Accept: "application/json",
      },
    });

    // console.log(response.data.data);

    user.value = response.data.data || {};
    // formData.value.name = user.value.name || "";
    // formData.value.email = user.value.email || "";
    // formData.value.about = user.value.about || "";
    // formData.value.image = user.value.image || "";
    Cookies.set("user", JSON.stringify(user.value));
  } catch (error) {
    console.error("Error fetching user data:", error);
  }
};

const logout = async () => {

  loadingStatus.value = true;
  setTimeout(() => {
    localStorage.removeItem("TOKEN");
    localStorage.removeItem("IDUSER");
    
    router.push("/?logoutStatus=true");

    loadingStatus.value = false;
  }, 1000)
};

onMounted(async () => {
  await userFetchData();

  // Ambil nilai dari query dan pastikan konversi ke boolean
  logoutStatus.value = route.query.logoutStatus === "true";

  if (logoutStatus.value) {
    setTimeout(() => {
      logoutStatus.value = false;
    }, 3000);
  }
});
</script>

<style scoped>
.fontPlayfair {
  font-family: playfair-display, serif;
}

.fontDmSans {
  font-family: dm-sans, sans-serif;
}

.logo {
  height: 50px;
  width: auto;
}

.navbar {
  border-bottom: 1px solid #dee2e6;
  z-index: 1;
}

.register {
  border: 2px solid #466543;
  color: #466543;
}

.register:hover {
  border: 2px solid #222222;
  color: #222222;
}

.login {
  background-color: #466543;
  color: white;
}

.login:hover {
  background-color: #364934;
  color: white;
}

.profile {
  background-color: transparent;
  border: none;
  font-size: 18px;
}

.profile-image {
  width: 50px;
  height: 50px;
  border-radius: 50%;
}

.dropdown-menu {
  min-width: 150px;
}

.dropdown-item {
  color: black; /* Default text color */
  background-color: white; /* Ensure background is white */
}

.dropdown-item:hover {
  color: #466543; /* Change text color on hover */
  background-color: white; /* Keep background white on hover */
}

.dropdown-item:focus {
  color: #466543; /* Change text color when focused */
  background-color: white; /* Keep background white when focused */
}

/* Styles for Logout Modal */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  text-align: center;
  background: white;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
  width: 370px;
}

.btn-cancel {
  padding: 10px 20px;
  font-size: 20px;
  border: 2px solid #466543;
  color: #466543;
}

.btn-cancel:hover {
  border: 2px solid #222222;
  color: #222222;
}

.btn-logout {
  padding: 10px 20px;
  font-size: 20px;
  background-color: #466543;
  color: white;
}

.btn-logout:hover {
  background-color: #364934;
  color: white;
}
</style>
