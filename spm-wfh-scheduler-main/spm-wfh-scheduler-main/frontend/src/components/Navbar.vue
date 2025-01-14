<template>
  <div :style="{ height: `${navbarHeight}px` }"></div>
  <nav class="navbar navbar-expand-lg custom-navbar" ref="navbar">
    <div class="container-fluid">
      <a class="navbar-brand d-flex align-items-center" href="#">
    <img :src="companyLogo" alt="Company Logo" class="company-logo">
    <span class="ms-2">WFH Scheduler</span>
  </a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavDropdown" aria-controls="navbarNavDropdown" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNavDropdown">
        <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        </ul>
        <ul class="navbar-nav">
          <li class="nav-item">
            <router-link class="nav-link" to="/request-list">My Requests</router-link>
          </li>
          <li class="nav-item">
            <router-link class="nav-link" to="/schedule">My Schedule</router-link>
          </li>
          <li class="nav-item" v-if="user.role==3">
            <router-link class="nav-link" to="/teamschedule-manager">Team Schedule</router-link>
          </li>
          <li class="nav-item" v-if="user.role==1 && user.position!='MD'">
            <router-link class="nav-link" to="/teamschedule-director">Team Schedule</router-link>
          </li>
          <li class="nav-item" v-if="user.role==2">
            <router-link class="nav-link" to="/teamschedule-staff">Team Schedule</router-link>
          </li>
          <li class="nav-item" v-if="(user.position=='HR Team'|| user.dept=='CEO')">
            <router-link class="nav-link" to="/overallschedule">Overall Schedule</router-link>
          </li>

          <li class="nav-item">
            <router-link class="nav-link" to="/application">Apply</router-link>
          </li>
          <li class="nav-item" v-if="user.position=='Director' || user.role==3 || user.position=='MD'">
            <router-link class="nav-link" to="/requests">Requests</router-link>
          </li>
          <li class="nav-item dropdown">
            <a class="nav-link dropdown-toggle" href="#" id="navbarDropdownMenuLink" role="button" data-bs-toggle="dropdown" aria-expanded="false">
              {{ user.fname }}
            </a>
            <ul class="dropdown-menu dropdown-menu-end" aria-labelledby="navbarDropdownMenuLink">
              <li class="user-info">
                <div class="user-avatar">{{ user.staff_fname?.[0]?.toUpperCase() }}</div>
                <div class="user-details">
                  <span class="user-name">{{ user.staff_fname }}</span>
                  <span class="user-role">{{ user.position }}</span>
                </div>
              </li>
              <li><hr class="dropdown-divider"></li>
              <li>
                <a class="dropdown-item" href="#" @click="logout">
                  <font-awesome-icon icon="sign-out-alt" />
                  Logout
                </a>
              </li>
            </ul>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue';
import { useRouter } from 'vue-router';
import companyLogo from '../assets/images/company-logo.jpg';

const router = useRouter();
const user = ref({});
const navbar = ref(null);
const navbarHeight = ref(0);


const updateNavbarHeight = () => {
  if (navbar.value) {
    navbarHeight.value = navbar.value.offsetHeight;
  }
};

// Create a ResizeObserver to watch navbar dimensions
const resizeObserver = new ResizeObserver(() => {
  updateNavbarHeight();
});

// Handle window resize
const handleResize = () => {
  updateNavbarHeight();
};
const updateUserFromStorage = () => {
  const storedUser = localStorage.getItem('user');
  if (storedUser) {
    user.value = JSON.parse(storedUser);
  } else {
    user.value = {};
  }
};

onMounted(() => {
  updateNavbarHeight();
  updateUserFromStorage();
  window.addEventListener('resize', handleResize);
    // Observe navbar element
    if (navbar.value) {
    resizeObserver.observe(navbar.value);
  }

  // Listen for storage events (in case user logs in in another tab)
  window.addEventListener('storage', (event) => {
    if (event.key === 'user') {
      updateUserFromStorage();
    }
  });
});

// Watch for route changes
watch(() => router.currentRoute.value, () => {
  updateUserFromStorage();
}, { immediate: true });

const logout = () => {
  localStorage.removeItem('user');
  user.value = {};
  router.push('/');
};



</script>

<style scoped>
.navbar .container-fluid {
  padding-right: 0;
}

.custom-navbar {
  background-color: #141b4d;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
}

.navbar-brand,
.nav-link {
  color: white !important;
}

.nav-item {
  padding: 1rem;
}

.nav-link:hover{
  color: #C69200!important;
}
.nav-link.router-link-active {
  color: #C69200 !important;
}

.navbar-toggler-icon {
  background-image: url("data:image/svg+xml,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 30 30'%3e%3cpath stroke='rgba(255, 255, 255, 0.55)' stroke-linecap='round' stroke-miterlimit='10' stroke-width='2' d='M4 7h22M4 15h22M4 23h22'/%3e%3c/svg%3e");
}

.navbar-toggler {
  border-color: rgba(255, 255, 255, 0.1);
}

.dropdown-menu {
  background-color: #141b4d;
  border-right: 0;
  padding: 0.5rem 0;
  min-width: 240px;
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.2);
}

.user-info {
  display: flex;
  align-items: center;
  padding: 1rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.user-avatar {
  width: 40px;
  height: 40px;
  background-color: #C69200;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  color: white;
  margin-right: 1rem;
}

.user-details {
  display: flex;
  flex-direction: column;
}

.user-name {
  font-weight: bold;
  color: white;
  margin-bottom: 0.25rem;
}

.user-role {
  font-size: 0.875rem;
  color: rgba(255, 255, 255, 0.7);
}

.dropdown-divider {
  border-color: rgba(255, 255, 255, 0.1);
  margin: 0;
}

.dropdown-item {
  color: white;
  padding: 0.75rem 1rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.dropdown-item:hover {
  background-color: #1e2a6d;
  color: #C69200;
}

.dropdown-item i {
  width: 16px;
}

.dropdown-item {
  color: white;
}

.dropdown-item:hover {
  background-color: #1e2a6d;
  color: #C69200;
}

.navbar-brand {
  margin-right: 1rem;
  white-space: nowrap;
}

.company-logo {
  height: 40px;
  width: auto;
  max-width: 150px;
  object-fit: contain;
}

/* Ensure the navbar-toggler stays on the right */
.navbar > .container-fluid {
  justify-content: space-between;
}

/* Responsive adjustments */
@media (max-width: 991.98px) {
  .company-logo {
    height: 30px;
  }
  
  .navbar-brand {
    font-size: 0.9rem;
  }
}
</style>
