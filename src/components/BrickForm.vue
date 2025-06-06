<template>
  <div class="container mt-4">
    <div class="row justify-content-center">
      <div class="col-md-8">
        <div class="card">
          <div class="card-header">
            <h3>Add New LEGO Brick</h3>
          </div>
          <div class="card-body">
            
            <!-- Success message -->
            <div v-if="successMessage" class="alert alert-success">
              {{ successMessage }}
            </div>
            
            <!-- Error message -->
            <div v-if="errorMessage" class="alert alert-danger">
              {{ errorMessage }}
            </div>
            
            <form @submit.prevent="submitForm">
              <div class="row">
                <!-- Service Name -->
                <div class="col-md-6 mb-3">
                  <label for="serviceName" class="form-label">Service/Feature Name *</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="serviceName"
                    v-model="form.serviceName"
                    required
                    placeholder="e.g., User Authentication"
                  >
                </div>
                
                <!-- Project Name -->
                <div class="col-md-6 mb-3">
                  <label for="projectName" class="form-label">Project Name *</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="projectName"
                    v-model="form.projectName"
                    required
                    placeholder="e.g., Customer Portal"
                  >
                </div>
              </div>
              
              <!-- Description -->
              <div class="mb-3">
                <label for="description" class="form-label">Description *</label>
                <textarea 
                  class="form-control" 
                  id="description"
                  v-model="form.description"
                  rows="3"
                  required
                  placeholder="Brief description of what this service does (2-3 sentences)"
                ></textarea>
              </div>
              
              <div class="row">
                <!-- Data Consumed -->
                <div class="col-md-6 mb-3">
                  <label for="dataConsumed" class="form-label">Data Consumed</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="dataConsumed"
                    v-model="form.dataConsumed"
                    placeholder="e.g., username, password, session tokens"
                  >
                </div>
                
                <!-- Data Produced -->
                <div class="col-md-6 mb-3">
                  <label for="dataProduced" class="form-label">Data Produced</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="dataProduced"
                    v-model="form.dataProduced"
                    placeholder="e.g., authentication status, user profile"
                  >
                </div>
              </div>
              
              <div class="row">
                <!-- Tags -->
                <div class="col-md-6 mb-3">
                  <label for="tags" class="form-label">Tags</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="tags"
                    v-model="form.tags"
                    placeholder="e.g., auth, security, user-management"
                  >
                  <small class="form-text text-muted">Separate multiple tags with commas</small>
                </div>
                
                <!-- Creator Name -->
                <div class="col-md-6 mb-3">
                  <label for="creatorName" class="form-label">Your Name *</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="creatorName"
                    v-model="form.creatorName"
                    required
                    placeholder="Your name"
                  >
                </div>
              </div>
              
              <!-- Submit Button -->
              <div class="d-grid gap-2">
                <button 
                  type="submit" 
                  class="btn btn-primary btn-lg"
                  :disabled="submitting"
                >
                  {{ submitting ? 'Creating Brick...' : 'Create Brick' }}
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'BrickForm',
  data() {
    return {
      form: {
        serviceName: '',
        projectName: '',
        description: '',
        dataConsumed: '',
        dataProduced: '',
        tags: '',
        creatorName: ''
      },
      submitting: false,
      successMessage: '',
      errorMessage: ''
    }
  },
  methods: {
    async submitForm() {
      try {
        this.submitting = true
        this.errorMessage = ''
        this.successMessage = ''
        
        // Make API call to create brick
        await axios.post('http://localhost:5213/api/bricks', this.form)
        
        // Show success message
        this.successMessage = `Brick "${this.form.serviceName}" created successfully!`
        
        // Clear form
        this.resetForm()
        
        // Emit event to parent to refresh brick list
        this.$emit('brick-created')
        
      } catch (error) {
        console.error('Error creating brick:', error)
        this.errorMessage = 'Failed to create brick. Please try again.'
      } finally {
        this.submitting = false
      }
    },
    
    resetForm() {
      this.form = {
        serviceName: '',
        projectName: '',
        description: '',
        dataConsumed: '',
        dataProduced: '',
        tags: '',
        creatorName: ''
      }
    }
  }
}
</script>