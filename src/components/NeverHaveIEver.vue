<template>
  <div
    class="never-have-i-ever"
    :class="{ 'theme-true': selectedTheme === 'true', 'theme-false': selectedTheme === 'false' }"
  >
    
    
    <div class="statement-container">
      <p class="datetime">
        {{ formatDateTime }}
        <br>
        <span class="countdown">Volgende stelling over: {{ timeUntilNext }}</span>
      </p>
      <h1>Voor ik ga meuren, ga dees nog gebeuren..</h1>
      <h2>{{ currentStatement.text }}</h2>
      <div class="info-icon" @click="toggleExtraInfo" v-if="currentStatement.extraInfo">
        <i class="fa-solid fa-circle-info"></i>
      </div>
      
      <!-- Nieuwe popup component -->
      <div class="popup-overlay" v-if="showExtraInfo" @click="toggleExtraInfo">
        <div class="popup" @click.stop>
          <div class="popup-content">
            {{ currentStatement.extraInfo }}
          </div>
          <button class="popup-close" @click="toggleExtraInfo">
            <i class="fa-solid fa-times"></i>
          </button>
        </div>
      </div>
      
      <div class="button-container" v-if="hasCurrentStatement">
        <button
          class="btn true"
          @click="handleAnswer(true)"
        >
          Zekers
        </button>
        <button
          class="btn false"
          @click="handleAnswer(false)"
        >
          Denk van niet
        </button>
      </div>
    </div>

  </div>
</template>

<script>
import statements from '@/assets/statements.json'

export default {
  name: 'NeverHaveIEver',
  data() {
    return {
      currentStatement: { text: 'Laden...' },
      currentDate: new Date(),
      selectedTheme: null,
      showExtraInfo: false,
      timeLeft: {
        minutes: 0,
        seconds: 0,
        display: ''
      }
    }
  },
  computed: {
    formatDateTime() {
      return this.currentDate.toLocaleString('nl-NL', {
        weekday: 'long',
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        hour: '2-digit',
        minute: '2-digit'
      })
    },
    timeUntilNext() {
      return this.timeLeft.display
    },
    hasCurrentStatement() {
      return this.currentStatement && this.currentStatement.date !== undefined
    }
  },
  created() {
    this.updateStatement()
    this.startCountdown()
  },
  methods: {
    getCurrentHour() {
      return this.currentDate.getHours()
    },
    getCurrentDateString() {
      return this.currentDate.toISOString().split('T')[0]
    },
    updateStatement() {
      const currentHour = this.getCurrentHour()
      const currentDate = this.getCurrentDateString()
      
      const statement = statements.statements.find(s => 
        s.date === currentDate && s.hour === currentHour
      )
      
      this.currentStatement = statement || { 
        text: 'Geen stelling beschikbaar voor deze datum en tijd' 
      }
      this.currentDate = new Date() // Update huidige tijd
    },
    handleAnswer(answer) {
      this.selectedTheme = answer ? 'true' : 'false'
      // Add theme class to body
      document.body.className = answer ? 'theme-true' : 'theme-false'
      console.log(`Antwoord: ${answer ? 'Wel waar' : 'Niet waar'}`)
    },
    toggleExtraInfo() {
      this.showExtraInfo = !this.showExtraInfo;
    },
    startCountdown() {
      const updateCountdown = () => {
        const now = new Date()
        
        // Zoek de volgende statement
        const currentDate = now.toISOString().split('T')[0]
        const currentHour = now.getHours()
        
        // Zoek alle toekomstige statements
        const futureStatements = statements.statements.filter(s => {
          const statementDate = s.date
          if (statementDate > currentDate) return true
          if (statementDate === currentDate && s.hour > currentHour) return true
          return false
        })

        // Sorteer op datum en tijd
        futureStatements.sort((a, b) => {
          if (a.date !== b.date) return a.date.localeCompare(b.date)
          return a.hour - b.hour
        })

        if (futureStatements.length > 0) {
          // Neem de eerste toekomstige statement
          const nextStatement = futureStatements[0]
          const nextDate = new Date(nextStatement.date)
          nextDate.setHours(nextStatement.hour, 0, 0, 0)
          
          // Bereken verschil in milliseconden
          const diff = nextDate - now

          // Converteer naar minuten en seconden
          const totalMinutes = Math.floor(diff / (1000 * 60))
          this.timeLeft.minutes = totalMinutes % 60
          this.timeLeft.seconds = Math.floor((diff % (1000 * 60)) / 1000)

          if (totalMinutes >= 60) {
            // Als er meer dan een uur over is, toon dan uren
            const hours = Math.floor(totalMinutes / 60)
            this.timeLeft.display = `${hours} uur en ${this.timeLeft.minutes} minuten`
          } else {
            // Anders toon minuten en seconden
            this.timeLeft.display = `${this.timeLeft.minutes}:${this.timeLeft.seconds.toString().padStart(2, '0')}`
          }
        } else {
          this.timeLeft.display = 'Geen volgende stelling gepland'
        }
      }

      // Update direct en dan elke seconde
      updateCountdown()
      setInterval(updateCountdown, 1000)
    },
  }
}
</script>

<style>
body {
  margin: 0;
  min-height: 100vh;
  transition: all 0.3s ease;
}

/* Theme True (Green) */
body.theme-true {
  background-color: #1b5e20;
}

/* Theme False (Red) */
body.theme-false {
  background-color: #b71c1c;
}

h1 {
  color: #666;
  font-size: 40px;
}

.never-have-i-ever {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
  transition: all 0.3s ease;
}

/* Theme True (Green) */
.theme-true {
  background-color: #1b5e20;
}

.theme-true .statement-container {
  background-color: #2e7d32;
}

.theme-true .datetime {
  color: #ffffff;
}

.theme-true h1, .theme-true h2 {
  color: #ffffff;
}

/* Theme False (Red) */
.theme-false {
  background-color: #b71c1c;
}

.theme-false .statement-container {
  background-color: #d32f2f;
}

.theme-false .datetime {
  color: #ffffff;
}

.theme-false h1, .theme-false h2 {
  color: #ffffff;
}

.statement-container {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  margin-top: 40px;
  padding: 20px;
  border-radius: 8px;
}

.statement-container h2 {
  margin: 0;
}

.datetime {
  color: #666;
  font-size: 0.9em;
  margin-bottom: 15px;
}

.countdown {
  font-size: 0.9em;
  opacity: 0.8;
}

.button-container {
  margin-top: 20px;
  display: flex;
  justify-content: center;
  gap: 20px;
}

.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
  color: white;
}

.btn.true {
  background-color: #2e7d32;
}

.btn.false {
  background-color: #d32f2f;
}

.btn:hover {
  opacity: 0.9;
}

.info-icon {
  cursor: pointer;
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #666;
  transition: color 0.3s ease;
  margin: 15px auto;
}

.info-icon:hover {
  color: #333;
}

/* Theme-specific colors for the info icon */
.theme-true .info-icon {
  color: rgba(255, 255, 255, 0.8);
}

.theme-true .info-icon:hover {
  color: #ffffff;
}

.theme-false .info-icon {
  color: rgba(255, 255, 255, 0.8);
}

.theme-false .info-icon:hover {
  color: #ffffff;
}

.info-icon i {
  font-size: 40px;
}

/* Verwijder de oude extra-info stijlen en voeg nieuwe popup stijlen toe */
.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.popup {
  background: white;
  padding: 20px;
  border-radius: 8px;
  max-width: 80%;
  width: 400px;
  position: relative;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.popup-content {
  margin-right: 20px;
}

.popup-close {
  position: absolute;
  top: 10px;
  right: 10px;
  background: none;
  border: none;
  cursor: pointer;
  font-size: 20px;
  color: #666;
}

.popup-close:hover {
  color: #333;
}
</style> 