<template>
  <div class="rules-container">
    <button class="rules-icon" @click="showRules = true" title="Spelregels">
      📋
    </button>

    <!-- Modal/Popup -->
    <div v-if="showRules" class="modal-overlay">
      <div class="modal-content">
        <div class="modal-header">
          <h2>Spelregels</h2>
          <button class="close-button" @click="showRules = false">&times;</button>
        </div>
        <div class="modal-body">
          <div v-for="(rules, section) in gameRules" :key="section" class="rules-section">
            <h3>{{ formatSectionTitle(section) }}</h3>
            <ul>
              <li v-for="(rule, index) in rules" :key="index">
                {{ rule }}
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import gameRules from '@/assets/rules.json'

export default {
  name: 'GameRules',
  data() {
    return {
      gameRules,
      showRules: false
    }
  },
  methods: {
    formatSectionTitle(section) {
      return section.charAt(0).toUpperCase() + section.slice(1).replace('_', ' ')
    }
  }
}
</script>

<style scoped>
.rules-container {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 1000;
}

.rules-icon {
  font-size: 24px;
  background: none;
  border: none;
  cursor: pointer;
  padding: 8px;
  border-radius: 50%;
  background-color: #42b983;
  color: white;
  width: 45px;
  height: 45px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.2s;
}

.rules-icon:hover {
  transform: scale(1.1);
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1001;
}

.modal-content {
  background: white;
  border-radius: 8px;
  width: 90%;
  max-width: 600px;
  max-height: 90vh;
  display: flex;
  flex-direction: column;
}

.modal-header {
  padding: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #eee;
}

.modal-header h2 {
  margin: 0;
  color: #2c3e50;
}

.close-button {
  background: none;
  border: none;
  font-size: 24px;
  cursor: pointer;
  color: #666;
}

.close-button:hover {
  color: #000;
}

.modal-body {
  padding: 20px;
  overflow-y: auto;
}

.rules-section {
  margin-bottom: 30px;
}

h3 {
  color: #42b983;
  margin-bottom: 15px;
}

ul {
  list-style-type: disc;
  padding-left: 20px;
}

li {
  margin-bottom: 10px;
  line-height: 1.4;
}
</style> 