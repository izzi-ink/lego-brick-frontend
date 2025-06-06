<template>
  <div class="container mt-4">
    <h2>LEGO Brick System - All Bricks</h2>
    
    <!-- Search and Filter Section -->
    <div class="row mb-4">
      <div class="col-md-4">
        <label for="searchText" class="form-label">Search</label>
        <input 
          type="text" 
          id="searchText"
          class="form-control" 
          v-model="searchText"
          placeholder="Search services, projects, or descriptions..."
        >
      </div>
      <div class="col-md-3">
        <label for="projectFilter" class="form-label">Filter by Project</label>
        <select id="projectFilter" class="form-select" v-model="selectedProject">
          <option value="">All Projects</option>
          <option v-for="project in uniqueProjects" :key="project" :value="project">
            {{ project }}
          </option>
        </select>
      </div>
      <div class="col-md-3">
        <label for="tagFilter" class="form-label">Filter by Tag</label>
        <select id="tagFilter" class="form-select" v-model="selectedTag">
          <option value="">All Tags</option>
          <option v-for="tag in uniqueTags" :key="tag" :value="tag">
            {{ tag }}
          </option>
        </select>
      </div>
      <div class="col-md-2">
        <label class="form-label">&nbsp;</label>
        <button class="btn btn-outline-secondary d-block w-100" @click="clearFilters">
          Clear Filters
        </button>
      </div>
    </div>
    
    <!-- Results Summary -->
    <div class="mb-3" v-if="!loading">
      <span class="badge bg-info">
        {{ filteredBricks.length }} of {{ bricks.length }} bricks shown
      </span>
    </div>
    
    <!-- Loading message -->
    <div v-if="loading" class="alert alert-info">
      Loading bricks...
    </div>
    
    <!-- Error message -->
    <div v-if="error" class="alert alert-danger">
      Error: {{ error }}
    </div>
    
    <!-- No bricks message -->
    <div v-if="!loading && bricks.length === 0" class="alert alert-warning">
      No bricks found. Create your first brick!
    </div>
    
    <!-- No filtered results -->
    <div v-if="!loading && bricks.length > 0 && filteredBricks.length === 0" class="alert alert-info">
      No bricks match your search criteria. Try adjusting your filters.
    </div>
    
    <!-- Bricks display -->
    <div class="row" v-if="!loading">
      <div v-for="brick in filteredBricks" :key="`brick-${brick.id}`" class="col-md-6 col-lg-4 mb-3">
        <div class="card h-100">
          <div class="card-header">
            <h5 class="card-title">{{ brick.serviceName }}</h5>
            <small class="text-muted">{{ brick.projectName }}</small>
          </div>
          <div class="card-body">
            <p class="card-text">{{ brick.description }}</p>
            
            <div class="mb-2">
              <strong>Data Consumed:</strong> {{ brick.dataConsumed || 'None specified' }}
            </div>
            
            <div class="mb-2">
              <strong>Data Produced:</strong> {{ brick.dataProduced || 'None specified' }}
            </div>
            
            <div class="mb-2">
              <span 
                v-for="tag in getTags(brick.tags)" 
                :key="`${brick.id}-${tag}`" 
                class="badge bg-primary me-1"
              >
                {{ tag }}
              </span>
            </div>
          </div>
          <div class="card-footer text-muted">
            Created by {{ brick.creatorName }} on {{ formatDate(brick.dateCreated) }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'BrickList',
  data() {
    return {
      bricks: [],
      loading: true,
      error: null,
      searchText: '',
      selectedProject: '',
      selectedTag: ''
    }
  },
  computed: {
    filteredBricks() {
      if (!this.bricks || this.bricks.length === 0) return []
      
      let filtered = [...this.bricks]
      
      // Filter by search text
      if (this.searchText && this.searchText.trim()) {
        const searchLower = this.searchText.toLowerCase().trim()
        filtered = filtered.filter(brick => 
          (brick.serviceName && brick.serviceName.toLowerCase().includes(searchLower)) ||
          (brick.projectName && brick.projectName.toLowerCase().includes(searchLower)) ||
          (brick.description && brick.description.toLowerCase().includes(searchLower)) ||
          (brick.dataConsumed && brick.dataConsumed.toLowerCase().includes(searchLower)) ||
          (brick.dataProduced && brick.dataProduced.toLowerCase().includes(searchLower)) ||
          (brick.creatorName && brick.creatorName.toLowerCase().includes(searchLower))
        )
      }
      
      // Filter by project
      if (this.selectedProject) {
        filtered = filtered.filter(brick => brick.projectName === this.selectedProject)
      }
      
      // Filter by tag
      if (this.selectedTag) {
        filtered = filtered.filter(brick => {
          const tags = this.getTags(brick.tags)
          return tags.includes(this.selectedTag)
        })
      }
      
      return filtered
    },
    
    uniqueProjects() {
      if (!this.bricks || this.bricks.length === 0) return []
      const projects = [...new Set(this.bricks.map(brick => brick.projectName).filter(p => p))]
      return projects.sort()
    },
    
    uniqueTags() {
      if (!this.bricks || this.bricks.length === 0) return []
      const allTags = []
      this.bricks.forEach(brick => {
        allTags.push(...this.getTags(brick.tags))
      })
      const uniqueTags = [...new Set(allTags.filter(tag => tag))]
      return uniqueTags.sort()
    }
  },
  methods: {
    async fetchBricks() {
      try {
        this.loading = true
        this.error = null
        
        // Update this port to match your API
        const response = await axios.get('http://localhost:5213/api/bricks')
        this.bricks = response.data || []
        
      } catch (error) {
        console.error('Error fetching bricks:', error)
        this.error = 'Failed to load bricks. Make sure your API is running!'
      } finally {
        this.loading = false
      }
    },
    
    getTags(tagsString) {
      if (!tagsString) return []
      return tagsString.split(',').map(tag => tag.trim()).filter(tag => tag.length > 0)
    },
    
    formatDate(dateString) {
      if (!dateString) return 'Unknown'
      return new Date(dateString).toLocaleDateString()
    },
    
    clearFilters() {
      this.searchText = ''
      this.selectedProject = ''
      this.selectedTag = ''
    }
  },
  
  created() {
    this.fetchBricks()
  }
}
</script>