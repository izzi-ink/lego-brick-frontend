<template>
  <div id="app" class="portal-layout">
    <!-- Top Navigation Bar -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-primary fixed-top">
      <div class="container-fluid">
        <a class="navbar-brand d-flex align-items-center" href="#">
          <span class="brand-icon me-2">🧱</span>
          <span class="brand-text">LEGO Brick System</span>
        </a>
        
        <div class="navbar-nav ms-auto">
          <div class="nav-item dropdown">
            <a class="nav-link dropdown-toggle d-flex align-items-center" href="#" role="button" data-bs-toggle="dropdown">
              <span class="me-2">Business Analyst Portal</span>
              <div class="avatar-circle">BA</div>
            </a>
          </div>
        </div>
      </div>
    </nav>

    <!-- Main Container -->
    <div class="main-container">
      <!-- Sidebar -->
      <aside class="sidebar">
        <div class="sidebar-content">
          <div class="nav-section">
            <h6 class="nav-section-title">MAIN</h6>
            <ul class="nav nav-pills flex-column">
              <li class="nav-item">
                <a class="nav-link" :class="{ active: activeView === 'dashboard' }" @click="setActiveView('dashboard')" href="#">
                  <i class="icon">📊</i> Dashboard
                </a>
              </li>
              <li class="nav-item">
                <a class="nav-link" :class="{ active: activeView === 'create' }" @click="setActiveView('create')" href="#">
                  <i class="icon">➕</i> Create Brick
                </a>
              </li>
              <li class="nav-item">
                <a class="nav-link" :class="{ active: activeView === 'browse' }" @click="setActiveView('browse')" href="#">
                  <i class="icon">🔍</i> Browse Bricks
                </a>
              </li>
            </ul>
          </div>
          
          <div class="nav-section">
            <h6 class="nav-section-title">ANALYTICS</h6>
            <ul class="nav nav-pills flex-column">
              <li class="nav-item">
                <a class="nav-link" @click="setActiveView('analytics')" href="#">
                  <i class="icon">📈</i> Service Map
                </a>
              </li>
              <li class="nav-item">
                <a class="nav-link" @click="setActiveView('reports')" href="#">
                  <i class="icon">📋</i> Reports
                </a>
              </li>
            </ul>
          </div>
        </div>
      </aside>

      <!-- Main Content Area -->
      <main class="content-area">
        <!-- Dashboard View -->
        <div v-if="activeView === 'dashboard'" class="view-container">
          <div class="page-header">
            <h1 class="page-title">Dashboard</h1>
            <p class="page-subtitle">Overview of your LEGO Brick System</p>
          </div>
          
          <!-- Stats Cards -->
          <div class="row stats-row">
            <div class="col-lg-3 col-md-6 mb-4">
              <div class="stat-card">
                <div class="stat-card-body">
                  <div class="stat-icon">🧱</div>
                  <div class="stat-content">
                    <h3 class="stat-number">{{ totalBricks }}</h3>
                    <p class="stat-label">Total Bricks</p>
                  </div>
                </div>
              </div>
            </div>
            <div class="col-lg-3 col-md-6 mb-4">
              <div class="stat-card">
                <div class="stat-card-body">
                  <div class="stat-icon">📦</div>
                  <div class="stat-content">
                    <h3 class="stat-number">{{ totalProjects }}</h3>
                    <p class="stat-label">Projects</p>
                  </div>
                </div>
              </div>
            </div>
            <div class="col-lg-3 col-md-6 mb-4">
              <div class="stat-card">
                <div class="stat-card-body">
                  <div class="stat-icon">👥</div>
                  <div class="stat-content">
                    <h3 class="stat-number">{{ totalCreators }}</h3>
                    <p class="stat-label">Contributors</p>
                  </div>
                </div>
              </div>
            </div>
            <div class="col-lg-3 col-md-6 mb-4">
              <div class="stat-card">
                <div class="stat-card-body">
                  <div class="stat-icon">🏷️</div>
                  <div class="stat-content">
                    <h3 class="stat-number">{{ totalTags }}</h3>
                    <p class="stat-label">Tags</p>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Quick Actions -->
          <div class="row">
            <div class="col-lg-8">
              <div class="card quick-actions-card">
                <div class="card-header">
                  <h5 class="card-title">Quick Actions</h5>
                </div>
                <div class="card-body">
                  <div class="row">
                    <div class="col-md-6 mb-3">
                      <button class="btn btn-primary btn-lg w-100" @click="setActiveView('create')">
                        <i class="icon me-2">➕</i> Create New Brick
                      </button>
                    </div>
                    <div class="col-md-6 mb-3">
                      <button class="btn btn-outline-primary btn-lg w-100" @click="setActiveView('browse')">
                        <i class="icon me-2">🔍</i> Browse All Bricks
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <div class="col-lg-4">
              <div class="card recent-activity-card">
                <div class="card-header">
                  <h5 class="card-title">Recent Activity</h5>
                </div>
                <div class="card-body">
                  <p class="text-muted">Recent bricks and updates will appear here</p>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Create Brick View -->
        <div v-if="activeView === 'create'" class="view-container">
          <div class="page-header">
            <h1 class="page-title">Create New Brick</h1>
            <p class="page-subtitle">Add a new service or feature to the system</p>
          </div>
          <BrickForm @brick-created="handleBrickCreated" />
        </div>

        <!-- Browse Bricks View -->
        <div v-if="activeView === 'browse'" class="view-container">
          <div class="page-header">
            <h1 class="page-title">Browse Bricks</h1>
            <p class="page-subtitle">Explore and manage all system components</p>
          </div>
          <BrickList ref="brickList" @brick-updated="updateBricksData" @brick-deleted="updateBricksData" />
        </div>

        <!-- Analytics/Service Map View -->
        <div v-if="activeView === 'analytics'" class="view-container">
          <div class="page-header">
            <h1 class="page-title">Service Map</h1>
            <p class="page-subtitle">Visualize service relationships and data flow</p>
          </div>
          <div class="card">
            <div class="card-body text-center py-5">
              <h5 class="text-muted">Service Map Visualization</h5>
              <p class="text-muted">Coming soon - Interactive service dependency mapping</p>
            </div>
          </div>
        </div>

        <!-- Reports View -->
        <div v-if="activeView === 'reports'" class="view-container">
          <div class="page-header">
            <h1 class="page-title">Reports</h1>
            <p class="page-subtitle">Generate insights and documentation</p>
          </div>
          <div class="card">
            <div class="card-body text-center py-5">
              <h5 class="text-muted">Reports & Analytics</h5>
              <p class="text-muted">Coming soon - Detailed reporting and export functionality</p>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<script>
import BrickForm from './components/BrickForm.vue'
import BrickList from './components/BrickList.vue'

export default {
  name: 'App',
  components: {
    BrickForm,
    BrickList
  },
  data() {
    return {
      activeView: 'dashboard',
      bricks: []
    }
  },
  computed: {
    totalBricks() {
      return this.bricks.length
    },
    totalProjects() {
      const projects = new Set(this.bricks.map(brick => brick.projectName).filter(p => p))
      return projects.size
    },
    totalCreators() {
      const creators = new Set(this.bricks.map(brick => brick.creatorName).filter(c => c))
      return creators.size
    },
    totalTags() {
      const allTags = []
      this.bricks.forEach(brick => {
        if (brick.tags) {
          allTags.push(...brick.tags.split(',').map(tag => tag.trim()).filter(tag => tag))
        }
      })
      const uniqueTags = new Set(allTags)
      return uniqueTags.size
    }
  },
  methods: {
    setActiveView(view) {
      this.activeView = view
    },
    handleBrickCreated() {
      this.refreshBrickList()
      this.setActiveView('dashboard')
    },
    refreshBrickList() {
      if (this.$refs.brickList) {
        this.$refs.brickList.fetchBricks()
      }
      this.updateBricksData()
    },
    async updateBricksData() {
      try {
        const response = await fetch('http://localhost:5213/api/bricks')
        if (response.ok) {
          this.bricks = await response.json()
        }
      } catch (error) {
        console.error('Error fetching bricks for stats:', error)
      }
    }
  },
  mounted() {
    this.updateBricksData()
  }
}
</script>

<style>
/* Global Portal Styles */
* {
  box-sizing: border-box;
}

body {
  background-color: #f8f9fa;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Roboto', sans-serif;
  margin: 0;
  padding: 0;
}

.portal-layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

/* Top Navigation */
.navbar {
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
  z-index: 1030;
}

.brand-icon {
  font-size: 1.5rem;
}

.brand-text {
  font-weight: 600;
  font-size: 1.25rem;
}

.avatar-circle {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background-color: rgba(255,255,255,0.2);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.8rem;
  font-weight: 600;
  color: white;
}

/* Main Layout */
.main-container {
  display: flex;
  flex: 1;
  margin-top: 56px; /* navbar height */
}

/* Sidebar */
.sidebar {
  width: 280px;
  background: white;
  border-right: 1px solid #e9ecef;
  box-shadow: 2px 0 4px rgba(0,0,0,0.05);
  position: fixed;
  top: 56px;
  left: 0;
  height: calc(100vh - 56px);
  overflow-y: auto;
  z-index: 1020;
}

.sidebar-content {
  padding: 1.5rem 0;
}

.nav-section {
  margin-bottom: 2rem;
}

.nav-section-title {
  font-size: 0.75rem;
  font-weight: 700;
  color: #6c757d;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  padding: 0 1.5rem;
  margin-bottom: 0.75rem;
}

.nav-pills .nav-link {
  color: #495057;
  border-radius: 0;
  padding: 0.75rem 1.5rem;
  margin: 0;
  border: none;
  display: flex;
  align-items: center;
  font-weight: 500;
  transition: all 0.2s ease;
}

.nav-pills .nav-link:hover {
  background-color: #f8f9fa;
  color: #0d6efd;
}

.nav-pills .nav-link.active {
  background-color: #e7f3ff;
  color: #0d6efd;
  border-right: 3px solid #0d6efd;
}

.nav-pills .nav-link .icon {
  margin-right: 0.75rem;
  font-size: 1rem;
}

/* Content Area */
.content-area {
  flex: 1;
  margin-left: 280px;
  padding: 0;
  background-color: #f8f9fa;
  min-height: calc(100vh - 56px);
}

.view-container {
  padding: 2rem;
  max-width: 1400px;
  margin: 0 auto;
}

/* Page Header */
.page-header {
  margin-bottom: 2rem;
}

.page-title {
  font-size: 2rem;
  font-weight: 700;
  color: #212529;
  margin-bottom: 0.5rem;
}

.page-subtitle {
  font-size: 1.1rem;
  color: #6c757d;
  margin-bottom: 0;
}

/* Stats Cards */
.stats-row {
  margin-bottom: 2rem;
}

.stat-card {
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  border: 1px solid #e9ecef;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  height: 100%;
}

.stat-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0,0,0,0.12);
}

.stat-card-body {
  padding: 1.5rem;
  display: flex;
  align-items: center;
}

.stat-icon {
  font-size: 2.5rem;
  margin-right: 1rem;
  opacity: 0.8;
}

.stat-content {
  flex: 1;
}

.stat-number {
  font-size: 2rem;
  font-weight: 700;
  color: #212529;
  margin: 0;
  line-height: 1;
}

.stat-label {
  font-size: 0.9rem;
  color: #6c757d;
  margin: 0.25rem 0 0 0;
  font-weight: 500;
}

/* Cards */
.card {
  border: 1px solid #e9ecef;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
}

.card-header {
  background-color: #f8f9fa;
  border-bottom: 1px solid #e9ecef;
  padding: 1rem 1.5rem;
  border-radius: 12px 12px 0 0 !important;
}

.card-title {
  margin: 0;
  font-weight: 600;
  color: #212529;
}

.card-body {
  padding: 1.5rem;
}

/* Quick Actions */
.quick-actions-card .btn {
  border-radius: 8px;
  font-weight: 500;
  padding: 0.75rem 1rem;
  transition: all 0.2s ease;
}

.quick-actions-card .btn:hover {
  transform: translateY(-1px);
}

/* Responsive Design */
@media (max-width: 1199.98px) {
  .sidebar {
    width: 250px;
  }
  
  .content-area {
    margin-left: 250px;
  }
}

@media (max-width: 991.98px) {
  .sidebar {
    transform: translateX(-100%);
    transition: transform 0.3s ease;
  }
  
  .content-area {
    margin-left: 0;
  }
  
  .view-container {
    padding: 1rem;
  }
  
  .page-title {
    font-size: 1.75rem;
  }
  
  .stat-card-body {
    padding: 1rem;
  }
  
  .stat-icon {
    font-size: 2rem;
  }
  
  .stat-number {
    font-size: 1.5rem;
  }
}

@media (max-width: 767.98px) {
  .view-container {
    padding: 0.75rem;
  }
  
  .page-header {
    margin-bottom: 1.5rem;
  }
  
  .page-title {
    font-size: 1.5rem;
  }
  
  .page-subtitle {
    font-size: 1rem;
  }
  
  .stats-row {
    margin-bottom: 1.5rem;
  }
  
  .stat-card-body {
    flex-direction: column;
    text-align: center;
    padding: 1rem;
  }
  
  .stat-icon {
    margin-right: 0;
    margin-bottom: 0.5rem;
  }
}

/* Custom Form Styling for Portal */
.form-control, .form-select {
  border-radius: 8px;
  border: 1px solid #ced4da;
  transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}

.form-control:focus, .form-select:focus {
  border-color: #86b7fe;
  outline: 0;
  box-shadow: 0 0 0 0.2rem rgba(13, 110, 253, 0.25);
}

.btn {
  border-radius: 8px;
  font-weight: 500;
  transition: all 0.2s ease;
}

.btn:hover {
  transform: translateY(-1px);
}

/* Animation for smooth transitions */
.view-container {
  animation: fadeIn 0.3s ease-in;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>