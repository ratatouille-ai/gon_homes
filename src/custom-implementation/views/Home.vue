<script lang="ts">
import { defineComponent, ref, onMounted } from "vue";
import { useRouter } from "vue-router";
import CurrentAdminUserService from "../services/CurrentAdminUserService";
import RatatouilleApi from "../../api/index";

export default defineComponent({
  name: "Home",
  setup() {
    const router = useRouter();
    const adminUserCount = ref<number>(0);
    const loadingCount = ref<boolean>(true);

    const adminUser = CurrentAdminUserService.getAdminUser();

    const fetchAdminUserCount = async () => {
      try {
        loadingCount.value = true;
        const result = await RatatouilleApi.doQuery("SELECT COUNT(*) as total FROM admin_users");
        adminUserCount.value = parseInt(result[0]?.total || "0");
      } catch (error) {
        console.error("Error fetching admin user count:", error);
        adminUserCount.value = 0;
      } finally {
        loadingCount.value = false;
      }
    };

    const navigateToAdminUsers = () => {
      router.push("/admin-users");
    };

    onMounted(() => {
      fetchAdminUserCount();
    });

    return {
      adminUser,
      adminUserCount,
      loadingCount,
      navigateToAdminUsers,
    };
  },
});
</script>

<template>
  <div class="home">
    <div class="page-header">
      <div class="page-icon">
        <img :src="'https://res.cloudinary.com/dt2jraprb/image/upload/w_150/v1760222828/ChatGPT_Image_12_oct_2025_00_46_39_uustfa.webp'" alt="Ratatouille" />
      </div>
      <h1 class="page-title">Hello {{ adminUser.first_name }}</h1>
    </div>

    <div class="content-grid">
      <div class="card card-welcome">
        <h2>Welcome to Ratatouille</h2>
        <p>
          🚀 Ready to build your web platform?
          <a
            href="https://ratatouille-ai.github.io/app-generator?edit=gon_homes"
            class="link"
            target="_blank"
          >
            Start now!
          </a>
        </p>
      </div>

      <div class="card card-stats" @click="navigateToAdminUsers" role="button" tabindex="0" @keyup.enter="navigateToAdminUsers">
        <div class="stats-header">
          <div class="stats-icon">👥</div>
          <div class="stats-content">
            <h3>Admin Users</h3>
            <div class="stats-count">
              <span v-if="loadingCount" class="loading-text">Loading...</span>
              <span v-else class="count-number">{{ adminUserCount }}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.home {
  max-width: 1400px;
  margin: 0 auto;
}

.content-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 20px;
}

.card-links,
.card-getting-started {
  grid-column: span 1;
}

.card-stats {
  cursor: pointer;
  transition: all 0.3s ease;
}

.card-stats:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 25px rgba(0, 167, 111, 0.15);
}

.card-stats:focus {
  outline: 3px solid rgba(0, 167, 111, 0.3);
  outline-offset: 2px;
}

.stats-header {
  display: flex;
  align-items: center;
  gap: 16px;
}

.stats-icon {
  font-size: 32px;
  width: 56px;
  height: 56px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #e6f7f1 0%, #ccebe0 100%);
  border-radius: 12px;
  flex-shrink: 0;
}

.stats-content {
  flex: 1;
}

.stats-content h3 {
  margin: 0 0 8px 0;
  font-size: 16px;
  font-weight: 600;
  color: #333;
}

.stats-count {
  font-size: 28px;
  font-weight: 700;
  color: #00a76f;
  line-height: 1;
}

.loading-text {
  font-size: 14px;
  font-weight: 500;
  color: #666;
}

.count-number {
  color: #00a76f;
}

.links-list {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.steps-list {
  margin: 0;
  padding-left: 20px;
  color: #666;
  font-size: 14px;
  line-height: 1.8;
}

.steps-list li {
  margin-bottom: 8px;
}

.steps-list li:last-child {
  margin-bottom: 0;
}

@media (max-width: 768px) {
  .content-grid {
    grid-template-columns: 1fr;
  }
  
  .stats-header {
    gap: 12px;
  }
  
  .stats-icon {
    font-size: 24px;
    width: 48px;
    height: 48px;
  }
  
  .stats-count {
    font-size: 24px;
  }
}
</style>