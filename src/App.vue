<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-white">
    <!-- Header -->
    <header class="bg-bleu-nuit text-white shadow-lg">
      <div class="w-full px-4 lg:px-8 py-4">
        <div class="flex items-center justify-between">
          <h1 class="text-2xl font-bold">Gestion des Tontines</h1>
          <div class="flex space-x-4">
            <button
              @click="currentView = 'dashboard'"
              :class="['px-4 py-2 rounded-lg transition-colors', currentView === 'dashboard' ? 'bg-bleu-ciel text-white' : 'bg-gray-700 hover:bg-bleu-ciel hover:text-white']"
            >
              Tableau de bord
            </button>
            <button
              @click="currentView = 'tontines'"
              :class="['px-4 py-2 rounded-lg transition-colors', currentView === 'tontines' ? 'bg-bleu-ciel text-white' : 'bg-gray-700 hover:bg-bleu-ciel hover:text-white']"
            >
              Tontines
            </button>
            <button
              @click="currentView = 'reports'"
              :class="['px-4 py-2 rounded-lg transition-colors', currentView === 'reports' ? 'bg-bleu-ciel text-white' : 'bg-gray-700 hover:bg-bleu-ciel hover:text-white']"
            >
              Rapports
            </button>
          </div>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="w-full px-4 lg:px-8 py-8">
      <!-- Dashboard View -->
      <div v-if="currentView === 'dashboard'" class="space-y-6">
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
          <div class="bg-white rounded-xl shadow-lg p-6 border-l-4 border-bleu-ciel">
            <h3 class="text-lg font-semibold text-bleu-nuit mb-2">Total Tontines</h3>
            <p class="text-3xl font-bold text-bleu-ciel">{{ tontines.length }}</p>
          </div>
          <div class="bg-white rounded-xl shadow-lg p-6 border-l-4 border-bleu-nuit">
            <h3 class="text-lg font-semibold text-bleu-nuit mb-2">Participants Actifs</h3>
            <p class="text-3xl font-bold text-bleu-nuit">{{ totalParticipants }}</p>
          </div>
          <div class="bg-white rounded-xl shadow-lg p-6 border-l-4 border-gray-400">
            <h3 class="text-lg font-semibold text-bleu-nuit mb-2">Montant Total</h3>
            <p class="text-3xl font-bold text-gray-600">{{ totalAmount.toLocaleString() }} FC</p>
          </div>
        </div>

        <!-- Recent Activities -->
        <div class="bg-white rounded-xl shadow-lg p-6">
          <h3 class="text-xl font-semibold text-bleu-nuit mb-4">Tontines Actives</h3>
          <div class="space-y-4">
            <div v-for="tontine in tontines" :key="tontine.id" class="flex items-center justify-between p-4 bg-slate-50 rounded-lg">
              <div>
                <h4 class="font-semibold text-slate-800">{{ tontine.nom }}</h4>
                <p class="text-sm text-slate-600">{{ tontine.participants.length }} participants - {{ tontine.montant.toLocaleString() }} FC/mois</p>
              </div>
              <button
                @click="selectTontine(tontine)"
                class="px-4 py-2 bg-bleu-ciel hover:bg-bleu-nuit text-white rounded-lg transition-colors"
              >
                Gérer
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- Tontines Management View -->
      <div v-if="currentView === 'tontines'" class="space-y-6">
        <!-- Create New Tontine -->
        <div class="bg-white rounded-xl shadow-lg p-6">
          <h3 class="text-xl font-semibold text-bleu-nuit mb-4">
            {{ editingTontine ? 'Modifier la Tontine' : 'Créer une Nouvelle Tontine' }}
          </h3>
          <form @submit.prevent="saveTontine" class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <label class="block text-sm font-medium text-slate-700 mb-2">Nom de la Tontine</label>
              <input
                v-model="tontineForm.nom"
                type="text"
                required
                class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent"
                placeholder="Ex: Tontine Janvier 2024"
              >
            </div>
            <div>
              <label class="block text-sm font-medium text-slate-700 mb-2">Montant Mensuel (FC)</label>
              <input
                v-model.number="tontineForm.montant"
                type="number"
                required
                class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent"
                placeholder="25000"
              >
            </div>
            <div class="md:col-span-2">
              <label class="block text-sm font-medium text-slate-700 mb-2">Description</label>
              <textarea
                v-model="tontineForm.description"
                class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent"
                rows="3"
                placeholder="Description de la tontine..."
              ></textarea>
            </div>
            <div class="md:col-span-2 flex space-x-4">
              <button
                type="submit"
                class="px-6 py-2 bg-bleu-ciel hover:bg-bleu-nuit text-white rounded-lg transition-colors"
              >
                {{ editingTontine ? 'Mettre à jour' : 'Créer' }}
              </button>
              <button
                v-if="editingTontine"
                type="button"
                @click="cancelEdit"
                class="px-6 py-2 bg-slate-500 text-white rounded-lg hover:bg-slate-600 transition-colors"
              >
                Annuler
              </button>
            </div>
          </form>
        </div>

        <!-- Tontines List -->
        <div class="bg-white rounded-xl shadow-lg p-6">
          <h3 class="text-xl font-semibold text-bleu-nuit mb-4">Liste des Tontines</h3>
          <div class="overflow-x-auto">
            <table class="w-full">
              <thead>
                <tr class="border-b border-slate-200">
                  <th class="text-left py-3 px-4 font-semibold text-slate-700">Nom</th>
                  <th class="text-left py-3 px-4 font-semibold text-slate-700">Montant</th>
                  <th class="text-left py-3 px-4 font-semibold text-slate-700">Participants</th>
                  <th class="text-left py-3 px-4 font-semibold text-slate-700">Actions</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="tontine in tontines" :key="tontine.id" class="border-b border-slate-100 hover:bg-slate-50">
                  <td class="py-3 px-4">
                    <div>
                      <div class="font-medium text-slate-800">{{ tontine.nom }}</div>
                      <div class="text-sm text-slate-600">{{ tontine.description }}</div>
                    </div>
                  </td>
                  <td class="py-3 px-4 text-slate-700">{{ tontine.montant.toLocaleString() }} FC</td>
                  <td class="py-3 px-4 text-slate-700">{{ tontine.participants.length }}</td>
                  <td class="py-3 px-4">
                    <div class="flex space-x-2">
                      <button
                        @click="selectTontine(tontine)"
                        class="px-3 py-1 bg-bleu-ciel hover:bg-bleu-nuit text-white text-sm rounded transition-colors"
                      >
                        Gérer
                      </button>
                      <button
                        @click="editTontine(tontine)"
                        class="px-3 py-1 bg-bleu-nuit hover:bg-bleu-ciel text-white text-sm rounded transition-colors"
                      >
                        Modifier
                      </button>
                      <button
                        @click="deleteTontine(tontine.id)"
                        class="px-3 py-1 bg-red-500 text-white text-sm rounded hover:bg-red-600 transition-colors"
                      >
                        Supprimer
                      </button>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>

      <!-- Reports View -->
      <div v-if="currentView === 'reports'" class="space-y-6">
        <div class="bg-white rounded-xl shadow-lg p-6">
          <h3 class="text-xl font-semibold text-bleu-nuit mb-4">Rapports Mensuels</h3>
          <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
            <div>
              <label class="block text-sm font-medium text-slate-700 mb-2">Sélectionner une Tontine</label>
              <select
                v-model="selectedReportTontine"
                class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent"
              >
                <option value="">Choisir une tontine</option>
                <option v-for="tontine in tontines" :key="tontine.id" :value="tontine.id">
                  {{ tontine.nom }}
                </option>
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-slate-700 mb-2">Mois</label>
              <input
                v-model="selectedMonth"
                type="month"
                class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent"
              >
            </div>
            <div class="flex items-end">
              <button
                @click="generateReport"
                class="px-6 py-2 bg-bleu-ciel hover:bg-bleu-nuit text-white rounded-lg transition-colors"
              >
                Générer Rapport
              </button>
            </div>
          </div>

          <!-- Report Display -->
          <div v-if="currentReport" class="space-y-4">
            <div class="flex justify-between items-center">
              <h4 class="text-lg font-semibold text-slate-800">
                Rapport - {{ currentReport.tontineName }} ({{ formatMonth(selectedMonth) }})
              </h4>
              <button
                @click="printReport"
                class="px-4 py-2 bg-bleu-nuit hover:bg-bleu-ciel text-white rounded-lg transition-colors"
              >
                Imprimer
              </button>
            </div>
            
            <div id="printable-report" class="bg-slate-50 p-6 rounded-lg">
              <div class="text-center mb-6">
                <h2 class="text-2xl font-bold text-slate-800">{{ currentReport.tontineName }}</h2>
                <p class="text-slate-600">Rapport du mois de {{ formatMonth(selectedMonth) }}</p>
                <p class="text-slate-600">Montant mensuel: {{ currentReport.montant.toLocaleString() }} FC</p>
              </div>

              <div class="overflow-x-auto">
                <table class="w-full border-collapse border border-slate-300">
                  <thead>
                    <tr class="bg-slate-200">
                      <th class="border border-slate-300 px-4 py-2 text-left">Participant</th>
                      <th class="border border-slate-300 px-4 py-2 text-left">Parts</th>
                      <th class="border border-slate-300 px-4 py-2 text-left">Montant Dû</th>
                      <th class="border border-slate-300 px-4 py-2 text-left">Statut Paiement</th>
                      <th class="border border-slate-300 px-4 py-2 text-left">A Reçu sa Part</th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr v-for="participant in currentReport.participants" :key="participant.id">
                      <td class="border border-slate-300 px-4 py-2">
                        {{ participant.prenom }} {{ participant.nom }}
                      </td>
                      <td class="border border-slate-300 px-4 py-2">{{ participant.parts }}</td>
                      <td class="border border-slate-300 px-4 py-2">
                        {{ (currentReport.montant * participant.parts).toLocaleString() }} FC
                      </td>
                      <td class="border border-slate-300 px-4 py-2">
                        <span :class="participant.aPaye ? 'text-green-600 font-semibold' : 'text-red-600 font-semibold'">
                          {{ participant.aPaye ? 'Payé' : 'Non payé' }}
                        </span>
                      </td>
                      <td class="border border-slate-300 px-4 py-2">
                        <span :class="participant.aRecuSaPart ? 'text-green-600 font-semibold' : 'text-orange-600 font-semibold'">
                          {{ participant.aRecuSaPart ? 'Oui' : 'Non' }}
                        </span>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>

              <div class="mt-6 grid grid-cols-1 md:grid-cols-3 gap-4">
                <div class="bg-white p-4 rounded-lg">
                  <h5 class="font-semibold text-slate-800">Total Collecté</h5>
                  <p class="text-xl font-bold text-green-600">
                    {{ currentReport.totalCollecte.toLocaleString() }} FC
                  </p>
                </div>
                <div class="bg-white p-4 rounded-lg">
                  <h5 class="font-semibold text-slate-800">Participants Payés</h5>
                  <p class="text-xl font-bold text-blue-600">
                    {{ currentReport.participantsPayes }}/{{ currentReport.participants.length }}
                  </p>
                </div>
                <div class="bg-white p-4 rounded-lg">
                  <h5 class="font-semibold text-slate-800">Parts Distribuées</h5>
                  <p class="text-xl font-bold text-orange-600">
                    {{ currentReport.partsDistribuees }}/{{ currentReport.participants.length }}
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- Tontine Management Modal -->
    <div v-if="selectedTontine" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4 z-50">
      <div class="bg-white rounded-xl shadow-2xl max-w-7xl w-full max-h-[90vh] overflow-y-auto">
        <div class="p-6 border-b border-slate-200">
          <div class="flex justify-between items-center">
            <h3 class="text-2xl font-semibold text-slate-800">{{ selectedTontine.nom }}</h3>
            <button
              @click="selectedTontine = null"
              class="text-slate-500 hover:text-slate-700 text-2xl"
            >
              ×
            </button>
          </div>
          <p class="text-slate-600 mt-2">{{ selectedTontine.description }}</p>
          <p class="text-slate-600">Montant mensuel: {{ selectedTontine.montant.toLocaleString() }} FC</p>
        </div>

        <div class="p-6 space-y-6">
          <!-- Add Participant Form -->
          <div class="bg-slate-50 p-4 rounded-lg">
            <h4 class="text-lg font-semibold text-bleu-nuit mb-4">Ajouter un Participant</h4>
            <form @submit.prevent="addParticipant" class="grid grid-cols-1 md:grid-cols-4 gap-4">
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Prénom</label>
                <input
                  v-model="participantForm.prenom"
                  type="text"
                  required
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent"
                >
              </div>
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Nom</label>
                <input
                  v-model="participantForm.nom"
                  type="text"
                  required
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent"
                >
              </div>
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Nombre de Parts</label>
                <input
                  v-model.number="participantForm.parts"
                  type="number"
                  min="1"
                  required
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent"
                >
              </div>
              <div class="flex items-end">
                <button
                  type="submit"
                  class="w-full px-4 py-2 bg-bleu-ciel hover:bg-bleu-nuit text-white rounded-lg transition-colors"
                >
                  Ajouter
                </button>
              </div>
            </form>
          </div>

          <!-- Participants List -->
          <div>
            <h4 class="text-lg font-semibold text-bleu-nuit mb-4">Liste des Participants</h4>
            <div class="overflow-x-auto">
              <table class="w-full">
                <thead>
                  <tr class="border-b border-slate-200">
                    <th class="text-left py-3 px-4 font-semibold text-slate-700">Participant</th>
                    <th class="text-left py-3 px-4 font-semibold text-slate-700">Parts</th>
                    <th class="text-left py-3 px-4 font-semibold text-slate-700">Montant</th>
                    <th class="text-left py-3 px-4 font-semibold text-slate-700">Paiement</th>
                    <th class="text-left py-3 px-4 font-semibold text-slate-700">Part Reçue</th>
                    <th class="text-left py-3 px-4 font-semibold text-slate-700">Actions</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="participant in selectedTontine.participants" :key="participant.id" class="border-b border-slate-100 hover:bg-slate-50">
                    <td class="py-3 px-4">
                      <div class="font-medium text-slate-800">{{ participant.prenom }} {{ participant.nom }}</div>
                    </td>
                    <td class="py-3 px-4 text-slate-700">{{ participant.parts }}</td>
                    <td class="py-3 px-4 text-slate-700">{{ (selectedTontine.montant * participant.parts).toLocaleString() }} FC</td>
                    <td class="py-3 px-4">
                      <button
                        @click="togglePaiement(participant)"
                        :class="[
                          'px-3 py-1 text-sm rounded-full font-medium transition-colors',
                          participant.aPaye 
                            ? 'bg-green-100 text-green-800 hover:bg-green-200' 
                            : 'bg-red-100 text-red-800 hover:bg-red-200'
                        ]"
                      >
                        {{ participant.aPaye ? 'Payé' : 'Non payé' }}
                      </button>
                    </td>
                    <td class="py-3 px-4">
                      <button
                        @click="togglePartRecue(participant)"
                        :class="[
                          'px-3 py-1 text-sm rounded-full font-medium transition-colors',
                          participant.aRecuSaPart 
                            ? 'bg-green-100 text-green-800 hover:bg-green-200' 
                            : 'bg-orange-100 text-orange-800 hover:bg-orange-200'
                        ]"
                      >
                        {{ participant.aRecuSaPart ? 'Reçue' : 'Non reçue' }}
                      </button>
                    </td>
                    <td class="py-3 px-4">
                      <button
                        @click="removeParticipant(participant.id)"
                        class="px-3 py-1 bg-red-500 text-white text-sm rounded hover:bg-red-600 transition-colors"
                      >
                        Supprimer
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue'

export default {
  name: 'TontineApp',
  setup() {
    // Reactive data
    const currentView = ref('dashboard')
    const tontines = ref([])
    const selectedTontine = ref(null)
    const editingTontine = ref(null)
    const selectedReportTontine = ref('')
    const selectedMonth = ref('')
    const currentReport = ref(null)

    // Forms
    const tontineForm = ref({
      nom: '',
      montant: 0,
      description: ''
    })

    const participantForm = ref({
      prenom: '',
      nom: '',
      parts: 1
    })

    // Computed properties
    const totalParticipants = computed(() => {
      return tontines.value.reduce((total, tontine) => total + tontine.participants.length, 0)
    })

    const totalAmount = computed(() => {
      return tontines.value.reduce((total, tontine) => total + tontine.montant, 0)
    })

    // Methods
    const generateId = () => {
      return Date.now().toString(36) + Math.random().toString(36).substr(2)
    }

    const saveTontine = () => {
      if (editingTontine.value) {
        // Update existing tontine
        const index = tontines.value.findIndex(t => t.id === editingTontine.value.id)
        if (index !== -1) {
          tontines.value[index] = {
            ...editingTontine.value,
            ...tontineForm.value
          }
        }
        editingTontine.value = null
      } else {
        // Create new tontine
        const newTontine = {
          id: generateId(),
          ...tontineForm.value,
          participants: [],
          dateCreation: new Date().toISOString()
        }
        tontines.value.push(newTontine)
      }
      
      // Reset form
      tontineForm.value = {
        nom: '',
        montant: 0,
        description: ''
      }
      
      saveToLocalStorage()
    }

    const editTontine = (tontine) => {
      editingTontine.value = tontine
      tontineForm.value = {
        nom: tontine.nom,
        montant: tontine.montant,
        description: tontine.description
      }
    }

    const cancelEdit = () => {
      editingTontine.value = null
      tontineForm.value = {
        nom: '',
        montant: 0,
        description: ''
      }
    }

    const deleteTontine = (id) => {
      if (confirm('Êtes-vous sûr de vouloir supprimer cette tontine ?')) {
        tontines.value = tontines.value.filter(t => t.id !== id)
        saveToLocalStorage()
      }
    }

    const selectTontine = (tontine) => {
      selectedTontine.value = tontine
    }

    const addParticipant = () => {
      if (!selectedTontine.value) return

      const newParticipant = {
        id: generateId(),
        ...participantForm.value,
        aPaye: false,
        aRecuSaPart: false,
        dateAjout: new Date().toISOString()
      }

      selectedTontine.value.participants.push(newParticipant)
      
      // Reset form
      participantForm.value = {
        prenom: '',
        nom: '',
        parts: 1
      }
      
      saveToLocalStorage()
    }

    const removeParticipant = (participantId) => {
      if (!selectedTontine.value) return
      
      if (confirm('Êtes-vous sûr de vouloir supprimer ce participant ?')) {
        selectedTontine.value.participants = selectedTontine.value.participants.filter(p => p.id !== participantId)
        saveToLocalStorage()
      }
    }

    const togglePaiement = (participant) => {
      participant.aPaye = !participant.aPaye
      saveToLocalStorage()
    }

    const togglePartRecue = (participant) => {
      participant.aRecuSaPart = !participant.aRecuSaPart
      saveToLocalStorage()
    }

    const generateReport = () => {
      if (!selectedReportTontine.value || !selectedMonth.value) {
        alert('Veuillez sélectionner une tontine et un mois')
        return
      }

      const tontine = tontines.value.find(t => t.id === selectedReportTontine.value)
      if (!tontine) return

      const totalCollecte = tontine.participants
        .filter(p => p.aPaye)
        .reduce((total, p) => total + (tontine.montant * p.parts), 0)

      const participantsPayes = tontine.participants.filter(p => p.aPaye).length
      const partsDistribuees = tontine.participants.filter(p => p.aRecuSaPart).length

      currentReport.value = {
        tontineName: tontine.nom,
        montant: tontine.montant,
        participants: tontine.participants,
        totalCollecte,
        participantsPayes,
        partsDistribuees
      }
    }

    const formatMonth = (monthString) => {
      if (!monthString) return ''
      const [year, month] = monthString.split('-')
      const months = [
        'Janvier', 'Février', 'Mars', 'Avril', 'Mai', 'Juin',
        'Juillet', 'Août', 'Septembre', 'Octobre', 'Novembre', 'Décembre'
      ]
      return `${months[parseInt(month) - 1]} ${year}`
    }

    const printReport = () => {
      const printContent = document.getElementById('printable-report')
      const originalContent = document.body.innerHTML
      
      document.body.innerHTML = printContent.outerHTML
      window.print()
      document.body.innerHTML = originalContent
      
      // Reload the page to restore Vue functionality
      window.location.reload()
    }

    const saveToLocalStorage = () => {
      localStorage.setItem('tontines-data', JSON.stringify(tontines.value))
    }

    const loadFromLocalStorage = () => {
      const saved = localStorage.getItem('tontines-data')
      if (saved) {
        tontines.value = JSON.parse(saved)
      } else {
        // Initialize with sample data
        tontines.value = [
          {
            id: 'sample-1',
            nom: 'Tontine Exemple',
            montant: 25000,
            description: 'Tontine d\'exemple pour démonstration',
            participants: [
              {
                id: 'p1',
                prenom: 'Jean',
                nom: 'Dupont',
                parts: 1,
                aPaye: true,
                aRecuSaPart: false,
                dateAjout: new Date().toISOString()
              },
              {
                id: 'p2',
                prenom: 'Marie',
                nom: 'Martin',
                parts: 2,
                aPaye: false,
                aRecuSaPart: false,
                dateAjout: new Date().toISOString()
              }
            ],
            dateCreation: new Date().toISOString()
          }
        ]
        saveToLocalStorage()
      }
    }

    // Initialize current month
    const initializeCurrentMonth = () => {
      const now = new Date()
      selectedMonth.value = `${now.getFullYear()}-${String(now.getMonth() + 1).padStart(2, '0')}`
    }

    // Lifecycle
    onMounted(() => {
      loadFromLocalStorage()
      initializeCurrentMonth()
    })

    return {
      // Reactive data
      currentView,
      tontines,
      selectedTontine,
      editingTontine,
      selectedReportTontine,
      selectedMonth,
      currentReport,
      tontineForm,
      participantForm,
      
      // Computed
      totalParticipants,
      totalAmount,
      
      // Methods
      saveTontine,
      editTontine,
      cancelEdit,
      deleteTontine,
      selectTontine,
      addParticipant,
      removeParticipant,
      togglePaiement,
      togglePartRecue,
      generateReport,
      formatMonth,
      printReport
    }
  }
}
</script>

<style>
:root {
  --bleu-nuit: #1e3a8a;
  --bleu-ciel: #0ea5e9;
  --blanc: #ffffff;
}

.bg-bleu-nuit { background-color: var(--bleu-nuit); }
.bg-bleu-ciel { background-color: var(--bleu-ciel); }
.text-bleu-nuit { color: var(--bleu-nuit); }
.text-bleu-ciel { color: var(--bleu-ciel); }
.border-bleu-ciel { border-color: var(--bleu-ciel); }
.hover\:bg-bleu-ciel:hover { background-color: var(--bleu-ciel); }
.hover\:bg-bleu-nuit:hover { background-color: var(--bleu-nuit); }

@media print {
  body * {
    visibility: hidden;
  }
  #printable-report, #printable-report * {
    visibility: visible;
  }
  #printable-report {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
  }
}
</style>