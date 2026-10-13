<!-- ============================================================
 *  Login.vue - Main Application Component (Options API)
 *  
 *  This component handles:
 *  - User authentication using local JSON data
 *  - Role-based dashboard rendering
 *  - All Vue.js directives (v-model, v-on, v-if, v-else, v-show, v-for)
 *  - Dynamic CSS theming based on user roles
 * ============================================================ -->

<template>
  <div class="login-wrapper">

    <!-- ============================================================
     *  LOGIN PAGE (shown when user is NOT logged in)
     *  Uses: v-if to conditionally show login form
     * ============================================================ -->
    <div v-if="!isLoggedIn" class="login-card">
      <div class="login-header">
        <h1>Welcome Back</h1>
        <p>Sign in to access your dashboard</p>
      </div>

      <!-- Error message for invalid credentials -->
      <div v-if="errorMessage" class="error-message">
        {{ errorMessage }}
      </div>

      <!-- ==========================================================
       *  LOGIN FORM
       *  v-model: Two-way binding for username and password inputs
       *  v-on:click: Trigger login on button click
       * ========================================================== -->
      <div class="form-group">
        <label for="username">Username</label>
        <input
          type="text"
          id="username"
          v-model="username"
          placeholder="Enter your username"
          class="form-input"
        />
      </div>

      <div class="form-group">
        <label for="password">Password</label>
        <input
          type="password"
          id="password"
          v-model="password"
          placeholder="Enter your password"
          class="form-input"
        />
      </div>

      <button class="btn btn-login" @click="login">
        Login
      </button>

      <!-- Quick user hints for testing -->
      <div class="login-hints">
        <p>Demo accounts:</p>
        <span>admin / admin123</span>
        <span>teacher / teacher123</span>
        <span>student / student123</span>
      </div>
    </div>
    <!-- END Login Card -->

    <!-- ============================================================
     *  DASHBOARD (shown when user IS logged in)
     *  Uses: v-else to show dashboard when not showing login
     *  Dynamic class binding for role-based theming
     * ============================================================ -->
    <div v-else class="dashboard" :class="themeClass">
      
      <!-- Dashboard Header with Welcome Message and Logout -->
      <div class="dashboard-header">
        <div>
          <h1>{{ welcomeMessage }}</h1>
          <p class="role-badge">{{ userRole }}</p>
        </div>
        <button class="btn btn-logout" @click="logout">
          Logout
        </button>
      </div>

      <!-- ==========================================================
       *  MENU ITEMS SECTION
       *  Uses: v-for to render role-specific menu items from arrays
       * ========================================================== -->
      <div class="menu-section">
        <h2>Menu</h2>
        <div class="menu-grid">
          <div
            v-for="(item, index) in menuItems"
            :key="index"
            class="menu-card"
          >
            <span class="menu-icon">{{ item.icon }}</span>
            <span class="menu-label">{{ item.label }}</span>
          </div>
        </div>
      </div>

      <!-- ==========================================================
       *  SETTINGS TOGGLE SECTION
       *  Uses: v-on:click to toggle settings visibility
       *  Uses: v-show to show/hide the settings panel
       * ========================================================== -->
      <div class="settings-section">
        <button class="btn btn-toggle" @click="toggleSettings">
          {{ showSettings ? 'Hide Settings' : 'Toggle Settings' }}
        </button>

        <div v-show="showSettings" class="settings-panel">
          <h3>Settings Panel</h3>
          <p>This panel is shown/hidden using the <code>v-show</code> directive.</p>
          <div class="setting-item">
            <label>Notifications</label>
            <span class="toggle-indicator">Enabled</span>
          </div>
          <div class="setting-item">
            <label>Theme Preference</label>
            <span class="toggle-indicator">Auto</span>
          </div>
          <div class="setting-item">
            <label>Language</label>
            <span class="toggle-indicator">English</span>
          </div>
        </div>
      </div>
    </div>
    <!-- END Dashboard -->

  </div>
</template>

<script>
// ============================================================
//  Import dummy user data from local JSON file
// ============================================================
import users from "../users.json";

export default {
  name: "Login",

  // ============================================================
  //  DATA
  //  Contains all reactive properties for the component
  // ============================================================
  data() {
    return {
      // v-model bound properties
      username: "",        // Bound to username input
      password: "",        // Bound to password input

      // Authentication state
      isLoggedIn: false,   // Controls which view is shown (v-if/v-else)
      userRole: "",        // Stores the current user's role
      errorMessage: "",    // Error message for invalid login

      // v-show bound property
      showSettings: false, // Controls settings panel visibility

      // ==========================================================
      //  MENU ITEMS ARRAYS
      //  Each role has a specific set of menu items
      //  Rendered using v-for directive
      // ==========================================================
      menuConfig: {
        Admin: [
          { label: "Manage Users", icon: "👥" },
          { label: "Reports", icon: "📊" },
          { label: "Settings", icon: "⚙️" }
        ],
        Teacher: [
          { label: "Upload Notes", icon: "📝" },
          { label: "Attendance", icon: "✅" },
          { label: "Marks", icon: "📋" }
        ],
        Student: [
          { label: "View Notes", icon: "📖" },
          { label: "Assignments", icon: "📚" },
          { label: "Attendance", icon: "✅" }
        ]
      }
    };
  },

  // ============================================================
  //  COMPUTED PROPERTIES
  //  Derive values based on reactive data
  // ============================================================
  computed: {
    // Dynamic welcome message based on user role
    welcomeMessage() {
      return `Welcome ${this.userRole}`;
    },

    // Get menu items for the current user's role
    menuItems() {
      return this.menuConfig[this.userRole] || [];
    },

    // Dynamic CSS class binding for role-based theming
    themeClass() {
      return {
        'theme-admin': this.userRole === 'Admin',
        'theme-teacher': this.userRole === 'Teacher',
        'theme-student': this.userRole === 'Student'
      };
    }
  },

  // ============================================================
  //  METHODS
  //  All event handlers and utility functions
  // ============================================================
  methods: {
    /**
     * login() - Handles user authentication
     * - Matches entered credentials against users.json data
     * - Uses v-on:click on the Login button
     */
    login() {
      // Clear any previous error
      this.errorMessage = "";

      // Find a user with matching username and password
      const user = users.find(
        u => u.username === this.username && u.password === this.password
      );

      if (user) {
        // Successful login: set logged-in state and role
        this.isLoggedIn = true;
        this.userRole = user.role;
        this.showSettings = false; // Reset settings toggle
      } else {
        // Failed login: show error message
        this.errorMessage = "Invalid Credentials";
      }
    },

    /**
     * logout() - Handles user logout
     * - Resets all state to initial values
     * - Uses v-on:click on the Logout button
     */
    logout() {
      this.isLoggedIn = false;
      this.userRole = "";
      this.username = "";
      this.password = "";
      this.errorMessage = "";
      this.showSettings = false;
    },

    /**
     * toggleSettings() - Toggles the settings panel visibility
     * - Uses v-show to show/hide the settings section
     * - Uses v-on:click on the Toggle button
     */
    toggleSettings() {
      this.showSettings = !this.showSettings;
    }
  }
};
</script>

<!-- ============================================================
 *  STYLES (Scoped to this component)
 *  - Card layout with rounded corners and shadows
 *  - Role-based color themes using dynamic classes
 *  - Responsive design with media queries
 *  - Smooth hover effects and transitions
 * ============================================================ -->
<style scoped>
/* ============================================================
 *  LOGIN WRAPPER & CARD STYLES
 * ============================================================ */
.login-wrapper {
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
}

.login-card {
  background: #ffffff;
  border-radius: 16px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
  padding: 2.5rem;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.login-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 15px 50px rgba(0, 0, 0, 0.15);
}

.login-header {
  text-align: center;
  margin-bottom: 2rem;
}

.login-header h1 {
  font-size: 2rem;
  color: #1a1a2e;
  margin: 0 0 0.5rem 0;
  font-weight: 700;
}

.login-header p {
  color: #6c757d;
  font-size: 1rem;
  margin: 0;
}

/* Error Message */
.error-message {
  background: #fff5f5;
  color: #e53e3e;
  border: 1px solid #fed7d7;
  padding: 0.75rem 1rem;
  border-radius: 8px;
  margin-bottom: 1.5rem;
  font-weight: 500;
  animation: shake 0.5s ease-in-out;
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5px); }
  75% { transform: translateX(5px); }
}

/* ============================================================
 *  FORM STYLES
 * ============================================================ */
.form-group {
  margin-bottom: 1.25rem;
}

.form-group label {
  display: block;
  font-size: 0.875rem;
  font-weight: 600;
  color: #374151;
  margin-bottom: 0.5rem;
}

.form-input {
  width: 100%;
  padding: 0.75rem 1rem;
  font-size: 1rem;
  border: 2px solid #e5e7eb;
  border-radius: 10px;
  background: #f9fafb;
  color: #1a1a2e;
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
  box-sizing: border-box;
}

.form-input:focus {
  outline: none;
  border-color: #6366f1;
  box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.1);
  background: #ffffff;
}

.form-input::placeholder {
  color: #9ca3af;
}

/* ============================================================
 *  BUTTON STYLES
 * ============================================================ */
.btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 0.75rem 1.5rem;
  font-size: 1rem;
  font-weight: 600;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.3s ease;
  text-decoration: none;
}

.btn-login {
  width: 100%;
  background: linear-gradient(135deg, #6366f1, #8b5cf6);
  color: white;
  margin-top: 0.5rem;
  padding: 0.875rem;
}

.btn-login:hover {
  background: linear-gradient(135deg, #4f46e5, #7c3aed);
  transform: translateY(-1px);
  box-shadow: 0 4px 15px rgba(99, 102, 241, 0.4);
}

.btn-login:active {
  transform: translateY(0);
}

.btn-logout {
  background: #ef4444;
  color: white;
  padding: 0.625rem 1.25rem;
  font-size: 0.875rem;
  white-space: nowrap;
}

.btn-logout:hover {
  background: #dc2626;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(239, 68, 68, 0.4);
}

.btn-toggle {
  background: #6366f1;
  color: white;
  width: 100%;
}

.btn-toggle:hover {
  background: #4f46e5;
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(99, 102, 241, 0.3);
}

/* ============================================================
 *  LOGIN HINTS (Demo user info)
 * ============================================================ */
.login-hints {
  margin-top: 1.5rem;
  padding-top: 1.25rem;
  border-top: 1px solid #e5e7eb;
  text-align: center;
}

.login-hints p {
  font-size: 0.8rem;
  color: #6c757d;
  margin: 0 0 0.5rem 0;
  font-weight: 600;
}

.login-hints span {
  display: inline-block;
  font-size: 0.75rem;
  background: #f3f4f6;
  color: #4b5563;
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  margin: 0.25rem;
  font-family: 'Courier New', monospace;
}

/* ============================================================
 *  DASHBOARD STYLES
 * ============================================================ */
.dashboard {
  background: #ffffff;
  border-radius: 16px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
  padding: 2rem;
  transition: background-color 0.3s ease;
  width: 100%;
}

.dashboard-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 2rem;
  padding-bottom: 1.5rem;
  border-bottom: 2px solid rgba(0, 0, 0, 0.05);
}

.dashboard-header h1 {
  font-size: 1.75rem;
  margin: 0 0 0.25rem 0;
  font-weight: 700;
}

.role-badge {
  display: inline-block;
  font-size: 0.75rem;
  font-weight: 600;
  color: #6c757d;
  background: rgba(0, 0, 0, 0.05);
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

/* ============================================================
 *  MENU SECTION
 *  v-for renders role-specific menu cards
 * ============================================================ */
.menu-section {
  margin-bottom: 2rem;
}

.menu-section h2 {
  font-size: 1.25rem;
  color: #374151;
  margin: 0 0 1rem 0;
  font-weight: 600;
}

.menu-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
  gap: 1rem;
}

.menu-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 1.5rem 1rem;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.menu-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.1);
}

.menu-icon {
  font-size: 2rem;
  margin-bottom: 0.5rem;
}

.menu-label {
  font-size: 0.9rem;
  font-weight: 600;
  color: #374151;
  text-align: center;
}

/* ============================================================
 *  SETTINGS SECTION
 *  Uses v-show to toggle visibility
 * ============================================================ */
.settings-section {
  margin-top: 1rem;
}

.settings-panel {
  margin-top: 1rem;
  padding: 1.25rem;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.settings-panel h3 {
  font-size: 1.1rem;
  color: #374151;
  margin: 0 0 0.5rem 0;
}

.settings-panel > p {
  font-size: 0.85rem;
  color: #6c757d;
  margin: 0 0 1rem 0;
}

.settings-panel code {
  background: #f3f4f6;
  padding: 0.125rem 0.375rem;
  border-radius: 4px;
  font-size: 0.8rem;
  color: #6366f1;
}

.setting-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.75rem 0;
  border-bottom: 1px solid #f3f4f6;
}

.setting-item:last-child {
  border-bottom: none;
}

.setting-item label {
  font-size: 0.9rem;
  font-weight: 500;
  color: #374151;
}

.toggle-indicator {
  font-size: 0.85rem;
  color: #10b981;
  font-weight: 600;
}

/* ============================================================
 *  ROLE-BASED THEMES
 *  Dynamic class binding applied to the dashboard
 * ============================================================ */

/* Admin Theme - Light Red */
.theme-admin {
  background: linear-gradient(135deg, #fff5f5, #ffe8e8);
}
.theme-admin .dashboard-header h1 {
  color: #dc2626;
}
.theme-admin .menu-card:hover {
  background: #fff5f5;
  box-shadow: 0 8px 25px rgba(220, 38, 38, 0.15);
}

/* Teacher Theme - Light Green */
.theme-teacher {
  background: linear-gradient(135deg, #f0fdf4, #dcfce7);
}
.theme-teacher .dashboard-header h1 {
  color: #16a34a;
}
.theme-teacher .menu-card:hover {
  background: #f0fdf4;
  box-shadow: 0 8px 25px rgba(22, 163, 74, 0.15);
}

/* Student Theme - Light Blue */
.theme-student {
  background: linear-gradient(135deg, #eff6ff, #dbeafe);
}
.theme-student .dashboard-header h1 {
  color: #2563eb;
}
.theme-student .menu-card:hover {
  background: #eff6ff;
  box-shadow: 0 8px 25px rgba(37, 99, 235, 0.15);
}

/* ============================================================
 *  RESPONSIVE DESIGN
 * ============================================================ */
@media (max-width: 640px) {
  .login-wrapper {
    padding: 0;
  }

  .login-card {
    padding: 1.5rem;
    border-radius: 12px;
  }

  .dashboard {
    padding: 1.25rem;
  }

  .dashboard-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 1rem;
  }

  .dashboard-header h1 {
    font-size: 1.35rem;
  }

  .menu-grid {
    grid-template-columns: repeat(auto-fill, minmax(130px, 1fr));
    gap: 0.75rem;
  }

  .menu-card {
    padding: 1rem;
  }

  .menu-icon {
    font-size: 1.5rem;
  }
}

@media (max-width: 400px) {
  .menu-grid {
    grid-template-columns: 1fr;
  }
}
</style>
