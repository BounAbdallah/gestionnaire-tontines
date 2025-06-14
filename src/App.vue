<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-white">
    <!-- Login Screen -->
    <div v-if="!isAuthenticated" class="min-h-screen flex items-center justify-center p-4">
      <div class="bg-white rounded-xl shadow-2xl p-6 sm:p-8 w-full max-w-md">
        <div class="text-center mb-6 sm:mb-8">
          <h1 class="text-2xl sm:text-3xl font-bold text-bleu-nuit mb-2">Gestion des Tontines</h1>
          <p class="text-slate-600 text-sm sm:text-base">Connexion Administrateur</p>
        </div>
        
        <form @submit.prevent="login" class="space-y-4 sm:space-y-6">
          <div>
            <label class="block text-sm font-medium text-slate-700 mb-2">Nom d'utilisateur</label>
            <input
              v-model="loginForm.username"
              type="text"
              required
              class="w-full px-3 sm:px-4 py-2 sm:py-3 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm sm:text-base"
              placeholder="admin"
            >
          </div>
          
          <div>
            <label class="block text-sm font-medium text-slate-700 mb-2">Mot de passe</label>
            <input
              v-model="loginForm.password"
              type="password"
              required
              class="w-full px-3 sm:px-4 py-2 sm:py-3 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm sm:text-base"
              placeholder="••••••••"
            >
          </div>
          
          <div v-if="loginError" class="text-red-600 text-sm text-center bg-red-50 p-2 rounded">
            {{ loginError }}
          </div>
          
          <button
            type="submit"
            class="w-full px-4 py-2 sm:py-3 bg-bleu-ciel hover:bg-bleu-nuit text-white rounded-lg transition-colors font-medium text-sm sm:text-base"
          >
            Se connecter
          </button>
        </form>
        
        <div class="mt-4 sm:mt-6 text-center text-xs sm:text-sm text-slate-500 bg-slate-50 p-3 rounded">
          <p class="font-medium mb-1">Identifiants par défaut:</p>
          <p>Utilisateur: <strong>admin</strong></p>
          <p>Mot de passe: <strong>admin123</strong></p>
        </div>
      </div>
    </div>

    <!-- Main Application (when authenticated) -->
    <div v-else>
      <!-- Header -->
      <header class="bg-bleu-nuit text-white shadow-lg">
        <div class="w-full px-4 lg:px-8 py-3 sm:py-4">
          <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4">
            <h1 class="text-xl sm:text-2xl font-bold text-center sm:text-left">Gestion des Tontines</h1>
            <div class="flex flex-col sm:flex-row items-center gap-2 sm:gap-4">
              <div class="flex flex-wrap justify-center sm:justify-start gap-2">
                <button
                  @click="currentView = 'dashboard'"
                  :class="['px-3 py-2 rounded-lg transition-colors text-sm', currentView === 'dashboard' ? 'bg-bleu-ciel text-white' : 'bg-gray-700 hover:bg-bleu-ciel hover:text-white']"
                >
                  Tableau de bord
                </button>
                <button
                  @click="currentView = 'tontines'"
                  :class="['px-3 py-2 rounded-lg transition-colors text-sm', currentView === 'tontines' ? 'bg-bleu-ciel text-white' : 'bg-gray-700 hover:bg-bleu-ciel hover:text-white']"
                >
                  Tontines
                </button>
                <button
                  @click="currentView = 'reports'"
                  :class="['px-3 py-2 rounded-lg transition-colors text-sm', currentView === 'reports' ? 'bg-bleu-ciel text-white' : 'bg-gray-700 hover:bg-bleu-ciel hover:text-white']"
                >
                  Rapports
                </button>
              </div>
              <button
                @click="logout"
                class="px-3 py-2 bg-red-600 hover:bg-red-700 text-white rounded-lg transition-colors text-sm"
              >
                Déconnexion
              </button>
            </div>
          </div>
        </div>
      </header>

      <!-- Main Content -->
      <main class="w-full px-4 lg:px-8 py-4 sm:py-8">
        <!-- Dashboard View -->
        <div v-if="currentView === 'dashboard'" class="space-y-4 sm:space-y-6">
          <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 sm:gap-6">
            <div class="bg-white rounded-xl shadow-lg p-4 sm:p-6 border-l-4 border-bleu-ciel">
              <h3 class="text-base sm:text-lg font-semibold text-bleu-nuit mb-2">Total Tontines</h3>
              <p class="text-2xl sm:text-3xl font-bold text-bleu-ciel">{{ tontines.length }}</p>
            </div>
            <div class="bg-white rounded-xl shadow-lg p-4 sm:p-6 border-l-4 border-bleu-nuit">
              <h3 class="text-base sm:text-lg font-semibold text-bleu-nuit mb-2">Participants Actifs</h3>
              <p class="text-2xl sm:text-3xl font-bold text-bleu-nuit">{{ totalParticipants }}</p>
            </div>
            <div class="bg-white rounded-xl shadow-lg p-4 sm:p-6 border-l-4 border-green-500">
              <h3 class="text-base sm:text-lg font-semibold text-bleu-nuit mb-2">Tontines Actives</h3>
              <p class="text-2xl sm:text-3xl font-bold text-green-600">{{ activeTontines }}</p>
            </div>
            <div class="bg-white rounded-xl shadow-lg p-4 sm:p-6 border-l-4 border-gray-400">
              <h3 class="text-base sm:text-lg font-semibold text-bleu-nuit mb-2">Montant Total</h3>
              <p class="text-xl sm:text-2xl lg:text-3xl font-bold text-gray-600">{{ totalAmount.toLocaleString() }} FC</p>
            </div>
          </div>

          <!-- Recent Activities -->
          <div class="bg-white rounded-xl shadow-lg p-4 sm:p-6">
            <h3 class="text-lg sm:text-xl font-semibold text-bleu-nuit mb-4">Tontines Actives</h3>
            <div class="space-y-3 sm:space-y-4">
              <div v-for="tontine in tontines" :key="tontine.id" class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-3 p-3 sm:p-4 bg-slate-50 rounded-lg">
                <div class="flex-1">
                  <h4 class="font-semibold text-slate-800 text-sm sm:text-base">{{ tontine.nom }}</h4>
                  <p class="text-xs sm:text-sm text-slate-600 mt-1">
                    {{ tontine.nombreParticipants }} participants - {{ tontine.montant.toLocaleString() }} FC/mois
                  </p>
                  <p class="text-xs sm:text-sm text-slate-600">
                    Durée: {{ tontine.duree }} mois - 
                    Du {{ formatDateShort(tontine.dateDebut) }} au {{ formatDateShort(tontine.dateFin) }}
                  </p>
                  <div class="flex items-center mt-2">
                    <div class="w-24 sm:w-32 bg-gray-200 rounded-full h-2 mr-2">
                      <div 
                        class="bg-bleu-ciel h-2 rounded-full transition-all duration-300" 
                        :style="{ width: getProgressPercentage(tontine) + '%' }"
                      ></div>
                    </div>
                    <span class="text-xs text-slate-600">{{ getProgressPercentage(tontine) }}%</span>
                  </div>
                </div>
                <button
                  @click="selectTontine(tontine)"
                  class="px-3 sm:px-4 py-2 bg-bleu-ciel hover:bg-bleu-nuit text-white rounded-lg transition-colors text-sm w-full sm:w-auto"
                >
                  Gérer
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Tontines Management View -->
        <div v-if="currentView === 'tontines'" class="space-y-4 sm:space-y-6">
          <!-- Create New Tontine -->
          <div class="bg-white rounded-xl shadow-lg p-4 sm:p-6">
            <h3 class="text-lg sm:text-xl font-semibold text-bleu-nuit mb-4">
              {{ editingTontine ? 'Modifier la Tontine' : 'Créer une Nouvelle Tontine' }}
            </h3>
            <form @submit.prevent="saveTontine" class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Nom de la Tontine</label>
                <input
                  v-model="tontineForm.nom"
                  type="text"
                  required
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm"
                  placeholder="Ex: Tontine Janvier 2024"
                >
              </div>
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Montant Mensuel (FC)</label>
                <input
                  v-model.number="tontineForm.montant"
                  type="number"
                  required
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm"
                  placeholder="25000"
                >
              </div>
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Nombre de Participants</label>
                <input
                  v-model.number="tontineForm.nombreParticipants"
                  type="number"
                  min="2"
                  required
                  @input="updateDateFin"
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm"
                  placeholder="5"
                >
              </div>
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Durée (mois)</label>
                <input
                  v-model.number="tontineForm.duree"
                  type="number"
                  min="1"
                  required
                  @input="updateDateFin"
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm"
                  placeholder="12"
                >
              </div>
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Date de début</label>
                <input
                  v-model="tontineForm.dateDebut"
                  type="date"
                  required
                  @change="updateDateFin"
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm"
                >
              </div>
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Date de fin (calculée automatiquement)</label>
                <input
                  v-model="tontineForm.dateFin"
                  type="date"
                  readonly
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg bg-gray-100 text-sm"
                >
              </div>
              <div class="md:col-span-2">
                <label class="block text-sm font-medium text-slate-700 mb-2">Description</label>
                <textarea
                  v-model="tontineForm.description"
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm"
                  rows="3"
                  placeholder="Description de la tontine..."
                ></textarea>
              </div>
              <div class="md:col-span-2 flex flex-col sm:flex-row gap-2 sm:gap-4">
                <button
                  type="submit"
                  class="px-4 sm:px-6 py-2 bg-bleu-ciel hover:bg-bleu-nuit text-white rounded-lg transition-colors text-sm"
                >
                  {{ editingTontine ? 'Mettre à jour' : 'Créer' }}
                </button>
                <button
                  v-if="editingTontine"
                  type="button"
                  @click="cancelEdit"
                  class="px-4 sm:px-6 py-2 bg-slate-500 text-white rounded-lg hover:bg-slate-600 transition-colors text-sm"
                >
                  Annuler
                </button>
              </div>
            </form>
          </div>

          <!-- Tontines List -->
          <div class="bg-white rounded-xl shadow-lg p-4 sm:p-6">
            <h3 class="text-lg sm:text-xl font-semibold text-bleu-nuit mb-4">Liste des Tontines</h3>
            <div class="overflow-x-auto">
              <table class="w-full min-w-[700px]">
                <thead>
                  <tr class="border-b border-slate-200">
                    <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Nom</th>
                    <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Montant</th>
                    <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Participants</th>
                    <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Durée</th>
                    <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Période</th>
                    <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Statut</th>
                    <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Actions</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="tontine in tontines" :key="tontine.id" class="border-b border-slate-100 hover:bg-slate-50">
                    <td class="py-3 px-2 sm:px-4">
                      <div>
                        <div class="font-medium text-slate-800 text-sm">{{ tontine.nom }}</div>
                        <div class="text-xs text-slate-600 hidden sm:block">{{ tontine.description }}</div>
                      </div>
                    </td>
                    <td class="py-3 px-2 sm:px-4 text-slate-700 text-sm">{{ tontine.montant.toLocaleString() }} FC</td>
                    <td class="py-3 px-2 sm:px-4 text-slate-700 text-sm">{{ tontine.participants.length }}/{{ tontine.nombreParticipants }}</td>
                    <td class="py-3 px-2 sm:px-4 text-slate-700 text-sm">{{ tontine.duree }} mois</td>
                    <td class="py-3 px-2 sm:px-4 text-slate-700 text-xs">
                      {{ formatDateShort(tontine.dateDebut) }} - {{ formatDateShort(tontine.dateFin) }}
                    </td>
                    <td class="py-3 px-2 sm:px-4">
                      <span :class="getTontineStatusClass(tontine)">
                        {{ getTontineStatus(tontine) }}
                      </span>
                    </td>
                    <td class="py-3 px-2 sm:px-4">
                      <div class="flex flex-col sm:flex-row gap-1 sm:gap-2">
                        <button
                          @click="selectTontine(tontine)"
                          class="px-2 py-1 bg-bleu-ciel hover:bg-bleu-nuit text-white text-xs rounded transition-colors"
                        >
                          Gérer
                        </button>
                        <button
                          @click="editTontine(tontine)"
                          class="px-2 py-1 bg-bleu-nuit hover:bg-bleu-ciel text-white text-xs rounded transition-colors"
                        >
                          Modifier
                        </button>
                        <button
                          @click="deleteTontine(tontine.id)"
                          class="px-2 py-1 bg-red-500 text-white text-xs rounded hover:bg-red-600 transition-colors"
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
        <div v-if="currentView === 'reports'" class="space-y-4 sm:space-y-6">
          <div class="bg-white rounded-xl shadow-lg p-4 sm:p-6">
            <h3 class="text-lg sm:text-xl font-semibold text-bleu-nuit mb-4">Rapports Mensuels</h3>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Sélectionner une Tontine</label>
                <select
                  v-model="selectedReportTontine"
                  @change="onTontineSelect"
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm"
                >
                  <option value="">Choisir une tontine</option>
                  <option v-for="tontine in tontines" :key="tontine.id" :value="tontine.id">
                    {{ tontine.nom }}
                  </option>
                </select>
              </div>
              <div>
                <label class="block text-sm font-medium text-slate-700 mb-2">Mois de la Tontine</label>
                <select
                  v-model="selectedTontineMonth"
                  :disabled="!selectedReportTontine"
                  class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent disabled:bg-gray-100 text-sm"
                >
                  <option value="">Choisir un mois</option>
                  <option v-for="month in availableMonths" :key="month.value" :value="month.value">
                    {{ month.label }}
                  </option>
                </select>
              </div>
              <div class="flex items-end">
                <button
                  @click="generateReport"
                  :disabled="!selectedReportTontine || !selectedTontineMonth"
                  class="w-full px-4 sm:px-6 py-2 bg-bleu-ciel hover:bg-bleu-nuit text-white rounded-lg transition-colors disabled:bg-gray-400 disabled:cursor-not-allowed text-sm"
                >
                  Générer Rapport
                </button>
              </div>
            </div>

            <!-- Report Display -->
            <div v-if="currentReport" class="space-y-4">
              <div class="flex flex-col sm:flex-row sm:justify-between sm:items-center gap-2">
                <h4 class="text-base sm:text-lg font-semibold text-slate-800">
                  Rapport - {{ currentReport.tontineName }} ({{ currentReport.monthLabel }})
                </h4>
                <button
                  @click="printReport"
                  class="px-3 sm:px-4 py-2 bg-bleu-nuit hover:bg-bleu-ciel text-white rounded-lg transition-colors text-sm"
                >
                  Imprimer
                </button>
              </div>
              
              <div id="printable-report" class="bg-slate-50 p-4 sm:p-6 rounded-lg">
                <div class="text-center mb-4 sm:mb-6">
                  <h2 class="text-xl sm:text-2xl font-bold text-slate-800">{{ currentReport.tontineName }}</h2>
                  <p class="text-slate-600 text-sm sm:text-base">{{ currentReport.monthLabel }}</p>
                  <p class="text-slate-600 text-sm sm:text-base">Montant mensuel: {{ currentReport.montant.toLocaleString() }} FC</p>
                  <p class="text-slate-600 text-sm sm:text-base">Bénéficiaire du mois: <strong>{{ currentReport.beneficiaire || 'Non défini' }}</strong></p>
                </div>

                <div class="overflow-x-auto">
                  <table class="w-full border-collapse border border-slate-300 min-w-[500px]">
                    <thead>
                      <tr class="bg-slate-200">
                        <th class="border border-slate-300 px-2 sm:px-4 py-2 text-left text-xs sm:text-sm">Participant</th>
                        <th class="border border-slate-300 px-2 sm:px-4 py-2 text-left text-xs sm:text-sm">Parts</th>
                        <th class="border border-slate-300 px-2 sm:px-4 py-2 text-left text-xs sm:text-sm">Montant Dû</th>
                        <th class="border border-slate-300 px-2 sm:px-4 py-2 text-left text-xs sm:text-sm">Statut Paiement</th>
                        <th class="border border-slate-300 px-2 sm:px-4 py-2 text-left text-xs sm:text-sm">Bénéficiaire</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="participant in currentReport.participants" :key="participant.id">
                        <td class="border border-slate-300 px-2 sm:px-4 py-2 text-xs sm:text-sm">
                          {{ participant.prenom }} {{ participant.nom }}
                        </td>
                        <td class="border border-slate-300 px-2 sm:px-4 py-2 text-xs sm:text-sm">{{ participant.parts }}</td>
                        <td class="border border-slate-300 px-2 sm:px-4 py-2 text-xs sm:text-sm">
                          {{ (currentReport.montant * participant.parts).toLocaleString() }} FC
                        </td>
                        <td class="border border-slate-300 px-2 sm:px-4 py-2">
                          <span :class="participant.isPaid ? 'text-green-600 font-semibold' : 'text-red-600 font-semibold'" class="text-xs sm:text-sm">
                            {{ participant.isPaid ? 'Payé' : 'Non payé' }}
                          </span>
                        </td>
                        <td class="border border-slate-300 px-2 sm:px-4 py-2">
                          <span :class="currentReport.beneficiaire === `${participant.prenom} ${participant.nom}` ? 'text-green-600 font-semibold' : 'text-slate-600'" class="text-xs sm:text-sm">
                            {{ currentReport.beneficiaire === `${participant.prenom} ${participant.nom}` ? 'Oui' : 'Non' }}
                          </span>
                        </td>
                      </tr>
                    </tbody>
                  </table>
                </div>

                <div class="mt-4 sm:mt-6 grid grid-cols-1 sm:grid-cols-3 gap-3 sm:gap-4">
                  <div class="bg-white p-3 sm:p-4 rounded-lg">
                    <h5 class="font-semibold text-slate-800 text-sm">Total Collecté</h5>
                    <p class="text-lg sm:text-xl font-bold text-green-600">
                      {{ currentReport.totalCollecte.toLocaleString() }} FC
                    </p>
                  </div>
                  <div class="bg-white p-3 sm:p-4 rounded-lg">
                    <h5 class="font-semibold text-slate-800 text-sm">Participants Payés</h5>
                    <p class="text-lg sm:text-xl font-bold text-blue-600">
                      {{ currentReport.participantsPayes }}/{{ currentReport.participants.length }}
                    </p>
                  </div>
                  <div class="bg-white p-3 sm:p-4 rounded-lg">
                    <h5 class="font-semibold text-slate-800 text-sm">Montant à Distribuer</h5>
                    <p class="text-lg sm:text-xl font-bold text-orange-600">
                      {{ currentReport.montantADistribuer.toLocaleString() }} FC
                    </p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </main>

      <!-- Tontine Management Modal -->
      <div v-if="selectedTontine" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-2 sm:p-4 z-50">
        <div class="bg-white rounded-xl shadow-2xl w-full max-w-7xl max-h-[95vh] overflow-y-auto">
          <div class="p-4 sm:p-6 border-b border-slate-200">
            <div class="flex justify-between items-start">
              <div class="flex-1 pr-4">
                <h3 class="text-xl sm:text-2xl font-semibold text-slate-800">{{ selectedTontine.nom }}</h3>
                <p class="text-slate-600 mt-1 sm:mt-2 text-sm sm:text-base">{{ selectedTontine.description }}</p>
                <div class="flex flex-col sm:flex-row sm:gap-4 mt-2 text-sm text-slate-600">
                  <p>Montant mensuel: {{ selectedTontine.montant.toLocaleString() }} FC</p>
                  <p>Durée: {{ selectedTontine.duree }} mois</p>
                  <p>Participants: {{ selectedTontine.participants.length }}/{{ selectedTontine.nombreParticipants }}</p>
                </div>
              </div>
              <button
                @click="selectedTontine = null"
                class="text-slate-500 hover:text-slate-700 text-2xl flex-shrink-0"
              >
                ×
              </button>
            </div>
          </div>

          <div class="p-4 sm:p-6 space-y-4 sm:space-y-6">
            <!-- Monthly Management Tabs -->
            <div class="border-b border-slate-200">
              <nav class="flex space-x-4 sm:space-x-8 overflow-x-auto">
                <button
                  @click="activeTab = 'participants'"
                  :class="['py-2 px-1 border-b-2 font-medium text-sm transition-colors whitespace-nowrap', activeTab === 'participants' ? 'border-bleu-ciel text-bleu-ciel' : 'border-transparent text-slate-500 hover:text-slate-700']"
                >
                  Participants
                </button>
                <button
                  @click="activeTab = 'monthly'"
                  :class="['py-2 px-1 border-b-2 font-medium text-sm transition-colors whitespace-nowrap', activeTab === 'monthly' ? 'border-bleu-ciel text-bleu-ciel' : 'border-transparent text-slate-500 hover:text-slate-700']"
                >
                  Calendrier des Mois
                </button>
              </nav>
            </div>

            <!-- Participants Tab -->
            <div v-if="activeTab === 'participants'" class="space-y-4 sm:space-y-6">
              <!-- Add Participant Form -->
              <div class="bg-slate-50 p-3 sm:p-4 rounded-lg" v-if="selectedTontine.participants.length < selectedTontine.nombreParticipants">
                <h4 class="text-base sm:text-lg font-semibold text-bleu-nuit mb-3 sm:mb-4">Ajouter un Participant</h4>
                <form @submit.prevent="addParticipant" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-3 sm:gap-4">
                  <div>
                    <label class="block text-sm font-medium text-slate-700 mb-2">Prénom</label>
                    <input
                      v-model="participantForm.prenom"
                      type="text"
                      required
                      class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm"
                    >
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-slate-700 mb-2">Nom</label>
                    <input
                      v-model="participantForm.nom"
                      type="text"
                      required
                      class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm"
                    >
                  </div>
                  <div>
                    <label class="block text-sm font-medium text-slate-700 mb-2">Nombre de Parts</label>
                    <input
                      v-model.number="participantForm.parts"
                      type="number"
                      min="1"
                      required
                      class="w-full px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm"
                    >
                  </div>
                  <div class="flex items-end">
                    <button
                      type="submit"
                      class="w-full px-3 sm:px-4 py-2 bg-bleu-ciel hover:bg-bleu-nuit text-white rounded-lg transition-colors text-sm"
                    >
                      Ajouter
                    </button>
                  </div>
                </form>
              </div>

              <div v-else class="bg-yellow-50 border border-yellow-200 p-4 rounded-lg">
                <p class="text-yellow-800 text-sm">
                  <strong>Limite atteinte:</strong> Vous avez atteint le nombre maximum de participants ({{ selectedTontine.nombreParticipants }}) pour cette tontine.
                </p>
              </div>

              <!-- Participants List -->
              <div>
                <h4 class="text-base sm:text-lg font-semibold text-bleu-nuit mb-3 sm:mb-4">
                  Liste des Participants ({{ selectedTontine.participants.length }}/{{ selectedTontine.nombreParticipants }})
                </h4>
                <div class="overflow-x-auto">
                  <table class="w-full min-w-[500px]">
                    <thead>
                      <tr class="border-b border-slate-200">
                        <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Participant</th>
                        <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Parts</th>
                        <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Montant</th>
                        <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Mois de Réception</th>
                        <th class="text-left py-3 px-2 sm:px-4 font-semibold text-slate-700 text-sm">Actions</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(participant, index) in selectedTontine.participants" :key="participant.id" class="border-b border-slate-100 hover:bg-slate-50">
                        <td class="py-3 px-2 sm:px-4">
                          <div class="font-medium text-slate-800 text-sm">{{ participant.prenom }} {{ participant.nom }}</div>
                        </td>
                        <td class="py-3 px-2 sm:px-4 text-slate-700 text-sm">{{ participant.parts }}</td>
                        <td class="py-3 px-2 sm:px-4 text-slate-700 text-sm">{{ (selectedTontine.montant * participant.parts).toLocaleString() }} FC</td>
                        <td class="py-3 px-2 sm:px-4 text-slate-700 text-sm">
                          <span class="bg-bleu-ciel text-white px-2 py-1 rounded-full text-xs">
                            Mois {{ getParticipantMonth(selectedTontine, participant.id) }}
                          </span>
                        </td>
                        <td class="py-3 px-2 sm:px-4">
                          <button
                            @click="removeParticipant(participant.id)"
                            class="px-2 sm:px-3 py-1 bg-red-500 text-white text-xs rounded hover:bg-red-600 transition-colors"
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

            <!-- Monthly Calendar Tab -->
            <div v-if="activeTab === 'monthly'" class="space-y-4 sm:space-y-6">
              <div class="mb-4">
                <h4 class="text-base sm:text-lg font-semibold text-bleu-nuit mb-2">Calendrier des Mois</h4>
                <p class="text-sm text-slate-600">Cliquez sur une carte de mois pour voir les détails et gérer les paiements.</p>
              </div>

              <!-- Monthly Cards Grid -->
              <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4">
                <div 
                  v-for="(month, index) in getMonthsList(selectedTontine)" 
                  :key="index"
                  @click="selectMonthDetail(index + 1)"
                  class="bg-white border-2 rounded-lg p-4 cursor-pointer transition-all duration-200 hover:shadow-lg"
                  :class="[
                    selectedMonthDetail === index + 1 ? 'border-bleu-ciel bg-blue-50' : 'border-slate-200 hover:border-bleu-ciel',
                    getMonthStatus(selectedTontine, index + 1) === 'completed' ? 'bg-green-50 border-green-200' : '',
                    getMonthStatus(selectedTontine, index + 1) === 'current' ? 'bg-yellow-50 border-yellow-200' : '',
                    getMonthStatus(selectedTontine, index + 1) === 'future' ? 'bg-slate-50 border-slate-200' : ''
                  ]"
                >
                  <div class="flex justify-between items-start mb-3">
                    <div>
                      <h5 class="font-semibold text-slate-800 text-sm">Mois {{ index + 1 }}</h5>
                      <p class="text-xs text-slate-600">{{ month.label }}</p>
                    </div>
                    <div class="flex items-center">
                      <span :class="getMonthStatusBadgeClass(selectedTontine, index + 1)" class="text-xs px-2 py-1 rounded-full">
                        {{ getMonthStatusText(selectedTontine, index + 1) }}
                      </span>
                    </div>
                  </div>
                  
                  <div class="space-y-2">
                    <div class="flex justify-between items-center">
                      <span class="text-xs text-slate-600">Bénéficiaire:</span>
                      <span class="text-xs font-medium text-slate-800">
                        {{ getMonthBeneficiary(selectedTontine, index + 1) }}
                      </span>
                    </div>
                    
                    <div class="flex justify-between items-center">
                      <span class="text-xs text-slate-600">Paiements:</span>
                      <span class="text-xs font-medium" :class="getPaymentRatio(selectedTontine, index + 1).color">
                        {{ getPaymentRatio(selectedTontine, index + 1).text }}
                      </span>
                    </div>
                    
                    <div class="w-full bg-gray-200 rounded-full h-2">
                      <div 
                        class="bg-bleu-ciel h-2 rounded-full transition-all duration-300" 
                        :style="{ width: getPaymentRatio(selectedTontine, index + 1).percentage + '%' }"
                      ></div>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Month Detail Panel -->
              <div v-if="selectedMonthDetail" class="bg-slate-50 rounded-lg p-4 sm:p-6 mt-6">
                <div class="flex justify-between items-center mb-4">
                  <h4 class="text-lg font-semibold text-bleu-nuit">
                    Détails du Mois {{ selectedMonthDetail }} - {{ getMonthLabel(selectedTontine, selectedMonthDetail) }}
                  </h4>
                  <button
                    @click="selectedMonthDetail = null"
                    class="text-slate-500 hover:text-slate-700 text-xl"
                  >
                    ×
                  </button>
                </div>
                
                <!-- Beneficiary Selection -->
                <div class="bg-white p-4 rounded-lg mb-4">
                  <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between gap-4">
                    <div class="flex-1">
                      <h5 class="font-semibold text-slate-800 mb-2">🏆 Bénéficiaire du mois</h5>
                      <p class="text-sm text-slate-600">
                        Sélectionnez qui recevra la totalité des cotisations collectées ce mois-ci.
                      </p>
                    </div>
                    <div class="flex-shrink-0">
                      <select
                        :value="getMonthBeneficiaryId(selectedTontine, selectedMonthDetail)"
                        @change="setMonthBeneficiary(selectedMonthDetail, $event.target.value)"
                        class="px-3 py-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-bleu-ciel focus:border-transparent text-sm min-w-[200px]"
                      >
                        <option value="">Sélectionner un bénéficiaire</option>
                        <option 
                          v-for="participant in selectedTontine.participants" 
                          :key="participant.id" 
                          :value="participant.id"
                        >
                          {{ participant.prenom }} {{ participant.nom }}
                        </option>
                      </select>
                    </div>
                  </div>
                  
                  <div v-if="getMonthBeneficiary(selectedTontine, selectedMonthDetail) !== 'Non défini'" class="mt-3 p-3 bg-green-50 border border-green-200 rounded-lg">
                    <p class="text-green-800 text-sm">
                      <strong>{{ getMonthBeneficiary(selectedTontine, selectedMonthDetail) }}</strong> est le bénéficiaire de ce mois.
                      Il/Elle recevra <strong>{{ getTotalToCollect(selectedTontine).toLocaleString() }} FC</strong> si tous les participants paient.
                    </p>
                  </div>
                </div>

                <!-- Participants Management -->
                <div class="bg-white p-4 rounded-lg mb-4">
                  <div class="flex justify-between items-center mb-4">
                    <h5 class="font-semibold text-slate-800">Gestion des Participations</h5>
                    <div class="flex gap-2">
                      <button
                        @click="markAllPaid(selectedMonthDetail)"
                        class="px-3 py-1 bg-green-500 hover:bg-green-600 text-white text-xs rounded transition-colors"
                      >
                        Tout marquer payé
                      </button>
                      <button
                        @click="markAllUnpaid(selectedMonthDetail)"
                        class="px-3 py-1 bg-red-500 hover:bg-red-600 text-white text-xs rounded transition-colors"
                      >
                        Tout marquer non payé
                      </button>
                    </div>
                  </div>

                  <div class="overflow-x-auto">
                    <table class="w-full min-w-[400px]">
                      <thead>
                        <tr class="border-b border-slate-200">
                          <th class="text-left py-3 px-4 font-semibold text-slate-700 text-sm">Participant</th>
                          <th class="text-left py-3 px-4 font-semibold text-slate-700 text-sm">Montant à Payer</th>
                          <th class="text-left py-3 px-4 font-semibold text-slate-700 text-sm">Statut</th>
                          <th class="text-left py-3 px-4 font-semibold text-slate-700 text-sm">Action</th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr v-for="participant in selectedTontine.participants" :key="participant.id" class="border-b border-slate-100 hover:bg-slate-50">
                          <td class="py-3 px-4">
                            <div class="flex items-center">
                              <div class="font-medium text-slate-800 text-sm">{{ participant.prenom }} {{ participant.nom }}</div>
                              <span v-if="getMonthBeneficiaryId(selectedTontine, selectedMonthDetail) === participant.id" class="ml-2 text-yellow-500">
                                👑
                              </span>
                            </div>
                          </td>
                          <td class="py-3 px-4 text-slate-700 text-sm">{{ (selectedTontine.montant * participant.parts).toLocaleString() }} FC</td>
                          <td class="py-3 px-4">
                            <span :class="getPaymentStatus(participant, selectedMonthDetail) ? 'bg-green-100 text-green-800' : 'bg-red-100 text-red-800'" class="px-2 py-1 text-xs rounded-full font-medium">
                              {{ getPaymentStatus(participant, selectedMonthDetail) ? '✅ Payé' : '❌ Non payé' }}
                            </span>
                          </td>
                          <td class="py-3 px-4">
                            <button
                              @click="toggleMonthlyPayment(participant, selectedMonthDetail)"
                              :class="[
                                'px-3 py-1 text-xs rounded font-medium transition-colors',
                                getPaymentStatus(participant, selectedMonthDetail) 
                                  ? 'bg-red-500 hover:bg-red-600 text-white' 
                                  : 'bg-green-500 hover:bg-green-600 text-white'
                              ]"
                            >
                              {{ getPaymentStatus(participant, selectedMonthDetail) ? 'Marquer non payé' : 'Marquer payé' }}
                            </button>
                          </td>
                        </tr>
                      </tbody>
                    </table>
                  </div>
                </div>

                <!-- Month Summary -->
                <div class="grid grid-cols-1 sm:grid-cols-4 gap-4">
                  <div class="bg-white p-4 rounded-lg">
                    <h6 class="font-semibold text-slate-800 text-sm">Total à Collecter</h6>
                    <p class="text-lg font-bold text-bleu-ciel">
                      {{ getTotalToCollect(selectedTontine).toLocaleString() }} FC
                    </p>
                  </div>
                  <div class="bg-white p-4 rounded-lg">
                    <h6 class="font-semibold text-slate-800 text-sm">Montant Collecté</h6>
                    <p class="text-lg font-bold text-green-600">
                      {{ getCollectedAmount(selectedTontine, selectedMonthDetail).toLocaleString() }} FC
                    </p>
                  </div>
                  <div class="bg-white p-4 rounded-lg">
                    <h6 class="font-semibold text-slate-800 text-sm">Participants Payés</h6>
                    <p class="text-lg font-bold text-orange-600">
                      {{ getPaidParticipants(selectedTontine, selectedMonthDetail) }}/{{ selectedTontine.participants.length }}
                    </p>
                  </div>
                  <div class="bg-white p-4 rounded-lg">
                    <h6 class="font-semibold text-slate-800 text-sm">Reste à Collecter</h6>
                    <p class="text-lg font-bold text-red-600">
                      {{ (getTotalToCollect(selectedTontine) - getCollectedAmount(selectedTontine, selectedMonthDetail)).toLocaleString() }} FC
                    </p>
                  </div>
                </div>

                <!-- Action Buttons -->
                <div class="mt-6 flex flex-col sm:flex-row gap-3 justify-end">
                  <button
                    v-if="getMonthBeneficiaryId(selectedTontine, selectedMonthDetail) && getPaidParticipants(selectedTontine, selectedMonthDetail) === selectedTontine.participants.length"
                    @click="finalizeMonth(selectedMonthDetail)"
                    class="px-4 py-2 bg-green-600 hover:bg-green-700 text-white rounded-lg transition-colors text-sm font-medium"
                  >
                    🎉 Finaliser le mois (Distribuer {{ getCollectedAmount(selectedTontine, selectedMonthDetail).toLocaleString() }} FC)
                  </button>
                  <button
                    @click="resetMonth(selectedMonthDetail)"
                    class="px-4 py-2 bg-gray-500 hover:bg-gray-600 text-white rounded-lg transition-colors text-sm"
                  >
                    Réinitialiser le mois
                  </button>
                </div>
              </div>
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
    // Authentication
    const isAuthenticated = ref(false)
    const loginForm = ref({
      username: '',
      password: ''
    })
    const loginError = ref('')

    // Main app state
    const currentView = ref('dashboard')
    const tontines = ref([])
    const selectedTontine = ref(null)
    const editingTontine = ref(null)
    const selectedReportTontine = ref('')
    const selectedTontineMonth = ref('')
    const currentReport = ref(null)
    const activeTab = ref('participants')
    const selectedMonth = ref(1)
    const selectedMonthDetail = ref(null)
    const availableMonths = ref([])

    // Forms
    const tontineForm = ref({
      nom: '',
      montant: 0,
      nombreParticipants: 0,
      duree: 0,
      description: '',
      dateDebut: '',
      dateFin: ''
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

    const activeTontines = computed(() => {
      return tontines.value.filter(tontine => getTontineStatus(tontine) === 'Active').length
    })

    // Authentication methods
    const login = () => {
      if (loginForm.value.username === 'admin' && loginForm.value.password === 'admin123') {
        isAuthenticated.value = true
        loginError.value = ''
        localStorage.setItem('tontine-auth', 'true')
      } else {
        loginError.value = 'Nom d\'utilisateur ou mot de passe incorrect'
      }
    }

    const logout = () => {
      isAuthenticated.value = false
      localStorage.removeItem('tontine-auth')
      loginForm.value = { username: '', password: '' }
    }

    const checkAuth = () => {
      const auth = localStorage.getItem('tontine-auth')
      if (auth === 'true') {
        isAuthenticated.value = true
      }
    }

    // Utility methods
    const generateId = () => {
      return Date.now().toString(36) + Math.random().toString(36).substr(2)
    }

    const addMonths = (date, months) => {
      const result = new Date(date)
      result.setMonth(result.getMonth() + months)
      return result
    }

    const formatDate = (date) => {
      return new Date(date).toLocaleDateString('fr-FR', { 
        year: 'numeric', 
        month: 'long' 
      })
    }

    const formatDateShort = (date) => {
      return new Date(date).toLocaleDateString('fr-FR', { 
        day: '2-digit',
        month: '2-digit',
        year: 'numeric'
      })
    }

    const updateDateFin = () => {
      if (tontineForm.value.dateDebut && tontineForm.value.duree) {
        const startDate = new Date(tontineForm.value.dateDebut)
        const endDate = addMonths(startDate, tontineForm.value.duree)
        tontineForm.value.dateFin = endDate.toISOString().split('T')[0]
      }
    }

    // Tontine management methods
    const saveTontine = () => {
      if (editingTontine.value) {
        const index = tontines.value.findIndex(t => t.id === editingTontine.value.id)
        if (index !== -1) {
          tontines.value[index] = {
            ...editingTontine.value,
            ...tontineForm.value
          }
        }
        editingTontine.value = null
      } else {
        const newTontine = {
          id: generateId(),
          ...tontineForm.value,
          participants: [],
          dateCreation: new Date().toISOString(),
          monthlyPayments: {},
          participantOrder: []
        }
        tontines.value.push(newTontine)
      }
      
      tontineForm.value = {
        nom: '',
        montant: 0,
        nombreParticipants: 0,
        duree: 0,
        description: '',
        dateDebut: '',
        dateFin: ''
      }
      
      saveToLocalStorage()
    }

    const editTontine = (tontine) => {
      editingTontine.value = tontine
      tontineForm.value = {
        nom: tontine.nom,
        montant: tontine.montant,
        nombreParticipants: tontine.nombreParticipants,
        duree: tontine.duree,
        description: tontine.description,
        dateDebut: tontine.dateDebut,
        dateFin: tontine.dateFin
      }
    }

    const cancelEdit = () => {
      editingTontine.value = null
      tontineForm.value = {
        nom: '',
        montant: 0,
        nombreParticipants: 0,
        duree: 0,
        description: '',
        dateDebut: '',
        dateFin: ''
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
      activeTab.value = 'participants'
      selectedMonth.value = 1
      selectedMonthDetail.value = null
    }

    const selectMonthDetail = (monthNumber) => {
      selectedMonthDetail.value = selectedMonthDetail.value === monthNumber ? null : monthNumber
    }

    // Participant management
    const addParticipant = () => {
      if (!selectedTontine.value) return
      if (selectedTontine.value.participants.length >= selectedTontine.value.nombreParticipants) {
        alert('Nombre maximum de participants atteint')
        return
      }

      const newParticipant = {
        id: generateId(),
        ...participantForm.value,
        dateAjout: new Date().toISOString()
      }

      selectedTontine.value.participants.push(newParticipant)
      
      // Add to participant order for month assignment
      if (!selectedTontine.value.participantOrder) {
        selectedTontine.value.participantOrder = []
      }
      selectedTontine.value.participantOrder.push(newParticipant.id)
      
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
        
        // Remove from participant order
        if (selectedTontine.value.participantOrder) {
          selectedTontine.value.participantOrder = selectedTontine.value.participantOrder.filter(id => id !== participantId)
        }
        
        saveToLocalStorage()
      }
    }

    const getParticipantMonth = (tontine, participantId) => {
      if (!tontine.participantOrder) return 'Non défini'
      const index = tontine.participantOrder.indexOf(participantId)
      return index !== -1 ? index + 1 : 'Non défini'
    }

    // Monthly payment management
    const toggleMonthlyPayment = (participant, month) => {
      if (!selectedTontine.value.monthlyPayments) {
        selectedTontine.value.monthlyPayments = {}
      }
      
      const key = `${participant.id}-${month}`
      selectedTontine.value.monthlyPayments[key] = !selectedTontine.value.monthlyPayments[key]
      
      saveToLocalStorage()
    }

    const getPaymentStatus = (participant, month) => {
      if (!selectedTontine.value?.monthlyPayments) return false
      const key = `${participant.id}-${month}`
      return selectedTontine.value.monthlyPayments[key] || false
    }

    // Month management functions
    const getMonthBeneficiary = (tontine, monthNumber) => {
      // Check if there's a manually set beneficiary
      if (tontine.monthlyBeneficiaries && tontine.monthlyBeneficiaries[monthNumber]) {
        const participantId = tontine.monthlyBeneficiaries[monthNumber]
        const participant = tontine.participants.find(p => p.id === participantId)
        return participant ? `${participant.prenom} ${participant.nom}` : 'Non défini'
      }
      
      // Fallback to automatic order (if exists)
      if (tontine.participantOrder && tontine.participants) {
        const participantId = tontine.participantOrder[monthNumber - 1]
        if (participantId) {
          const participant = tontine.participants.find(p => p.id === participantId)
          return participant ? `${participant.prenom} ${participant.nom}` : 'Non défini'
        }
      }
      
      return 'Non défini'
    }

    const getMonthStatus = (tontine, monthNumber) => {
      if (!tontine.dateDebut) return 'future'
      
      const startDate = new Date(tontine.dateDebut)
      const monthDate = addMonths(startDate, monthNumber - 1)
      const currentDate = new Date()
      
      if (monthDate > currentDate) return 'future'
      if (monthDate.getMonth() === currentDate.getMonth() && monthDate.getFullYear() === currentDate.getFullYear()) return 'current'
      return 'completed'
    }

    const getMonthStatusText = (tontine, monthNumber) => {
      const status = getMonthStatus(tontine, monthNumber)
      const statusTexts = {
        'future': 'À venir',
        'current': 'En cours',
        'completed': 'Terminé'
      }
      return statusTexts[status]
    }

    const getMonthStatusBadgeClass = (tontine, monthNumber) => {
      const status = getMonthStatus(tontine, monthNumber)
      const classes = {
        'future': 'bg-gray-100 text-gray-800',
        'current': 'bg-yellow-100 text-yellow-800',
        'completed': 'bg-green-100 text-green-800'
      }
      return classes[status]
    }

    const getPaymentRatio = (tontine, monthNumber) => {
      if (!tontine.participants.length) return { text: '0/0', percentage: 0, color: 'text-slate-600' }
      
      const paidCount = tontine.participants.filter(p => getPaymentStatus(p, monthNumber)).length
      const totalCount = tontine.participants.length
      const percentage = Math.round((paidCount / totalCount) * 100)
      
      let color = 'text-red-600'
      if (percentage === 100) color = 'text-green-600'
      else if (percentage >= 50) color = 'text-yellow-600'
      
      return {
        text: `${paidCount}/${totalCount}`,
        percentage,
        color
      }
    }

    const getTotalToCollect = (tontine) => {
      return tontine.participants.reduce((total, p) => total + (tontine.montant * p.parts), 0)
    }

    const getCollectedAmount = (tontine, monthNumber) => {
      return tontine.participants
        .filter(p => getPaymentStatus(p, monthNumber))
        .reduce((total, p) => total + (tontine.montant * p.parts), 0)
    }

    const getPaidParticipants = (tontine, monthNumber) => {
      return tontine.participants.filter(p => getPaymentStatus(p, monthNumber)).length
    }

    // Tontine status and progress
    const getTontineStatus = (tontine) => {
      if (!tontine.dateDebut || tontine.participants.length === 0) return 'En attente'
      
      const startDate = new Date(tontine.dateDebut)
      const currentDate = new Date()
      const endDate = new Date(tontine.dateFin)
      
      if (currentDate < startDate) return 'À venir'
      if (currentDate > endDate) return 'Terminée'
      return 'Active'
    }

    const getTontineStatusClass = (tontine) => {
      const status = getTontineStatus(tontine)
      const classes = {
        'En attente': 'px-2 py-1 text-xs font-medium bg-yellow-100 text-yellow-800 rounded-full',
        'À venir': 'px-2 py-1 text-xs font-medium bg-blue-100 text-blue-800 rounded-full',
        'Active': 'px-2 py-1 text-xs font-medium bg-green-100 text-green-800 rounded-full',
        'Terminée': 'px-2 py-1 text-xs font-medium bg-gray-100 text-gray-800 rounded-full'
      }
      return classes[status]
    }

    const getCurrentMonthNumber = (tontine) => {
      if (!tontine.dateDebut) return 0
      
      const startDate = new Date(tontine.dateDebut)
      const currentDate = new Date()
      
      const monthsDiff = (currentDate.getFullYear() - startDate.getFullYear()) * 12 + 
                        (currentDate.getMonth() - startDate.getMonth()) + 1
      
      return Math.max(1, Math.min(monthsDiff, tontine.duree))
    }

    const getProgressPercentage = (tontine) => {
      if (tontine.duree === 0) return 0
      const currentMonth = getCurrentMonthNumber(tontine)
      return Math.round((currentMonth / tontine.duree) * 100)
    }

    // Months management
    const getMonthsList = (tontine) => {
      if (!tontine.dateDebut || tontine.duree === 0) return []
      
      const startDate = new Date(tontine.dateDebut)
      const months = []
      
      for (let i = 0; i < tontine.duree; i++) {
        const monthDate = addMonths(startDate, i)
        months.push({
          number: i + 1,
          label: formatDate(monthDate),
          date: monthDate
        })
      }
      
      return months
    }

    const getMonthLabel = (tontine, monthNumber) => {
      const months = getMonthsList(tontine)
      return months[monthNumber - 1]?.label || `Mois ${monthNumber}`
    }

    // Reports
    const onTontineSelect = () => {
      if (!selectedReportTontine.value) {
        availableMonths.value = []
        return
      }
      
      const tontine = tontines.value.find(t => t.id === selectedReportTontine.value)
      if (tontine) {
        availableMonths.value = getMonthsList(tontine).map(month => ({
          value: month.number,
          label: `Mois ${month.number} - ${month.label}`
        }))
      }
    }

    const generateReport = () => {
      if (!selectedReportTontine.value || !selectedTontineMonth.value) {
        alert('Veuillez sélectionner une tontine et un mois')
        return
      }

      const tontine = tontines.value.find(t => t.id === selectedReportTontine.value)
      if (!tontine) return

      const monthNumber = parseInt(selectedTontineMonth.value)
      const monthLabel = getMonthLabel(tontine, monthNumber)
      const beneficiaire = getMonthBeneficiary(tontine, monthNumber)

      let totalCollecte = 0
      let participantsPayes = 0

      // Créer une copie des participants avec leur statut de paiement
      const participantsWithStatus = tontine.participants.map(participant => {
        const isPaid = getPaymentStatusForReport(tontine, participant, monthNumber)
        if (isPaid) {
          totalCollecte += tontine.montant * participant.parts
          participantsPayes++
        }
        
        return {
          ...participant,
          isPaid: isPaid
        }
      })

      const montantADistribuer = getTotalToCollect(tontine)

      currentReport.value = {
        tontineName: tontine.nom,
        monthLabel,
        monthNumber,
        montant: tontine.montant,
        participants: participantsWithStatus,
        beneficiaire,
        totalCollecte,
        participantsPayes,
        montantADistribuer
      }
    }

    // Ajouter cette nouvelle fonction pour les rapports
    const getPaymentStatusForReport = (tontine, participant, month) => {
      if (!tontine?.monthlyPayments) return false
      const key = `${participant.id}-${month}`
      return tontine.monthlyPayments[key] || false
    }

    const printReport = () => {
      const printContent = document.getElementById('printable-report')
      const originalContent = document.body.innerHTML
      
      document.body.innerHTML = printContent.outerHTML
      window.print()
      document.body.innerHTML = originalContent
      
      window.location.reload()
    }

    // Storage
    const saveToLocalStorage = () => {
      localStorage.setItem('tontines-data', JSON.stringify(tontines.value))
    }

    const loadFromLocalStorage = () => {
      const saved = localStorage.getItem('tontines-data')
      if (saved) {
        tontines.value = JSON.parse(saved)
      } else {
        // Initialize with sample data
        const today = new Date()
        const dateDebut = today.toISOString().split('T')[0]
        const dateFin = addMonths(today, 6).toISOString().split('T')[0]
        
        tontines.value = [
          {
            id: 'sample-1',
            nom: 'Tontine Exemple',
            montant: 25000,
            nombreParticipants: 4,
            duree: 6,
            description: 'Tontine d\'exemple pour démonstration',
            dateDebut,
            dateFin,
            participants: [
              {
                id: 'p1',
                prenom: 'Jean',
                nom: 'Dupont',
                parts: 1,
                dateAjout: new Date().toISOString()
              },
              {
                id: 'p2',
                prenom: 'Marie',
                nom: 'Martin',
                parts: 2,
                dateAjout: new Date().toISOString()
              }
            ],
            participantOrder: ['p1', 'p2'],
            monthlyPayments: {
              'p1-1': true,
              'p2-1': false
            },
            dateCreation: new Date().toISOString()
          }
        ]
        saveToLocalStorage()
      }
    }

    // Beneficiary management
    const getMonthBeneficiaryId = (tontine, monthNumber) => {
      if (!tontine.monthlyBeneficiaries) return ''
      return tontine.monthlyBeneficiaries[monthNumber] || ''
    }

    const setMonthBeneficiary = (monthNumber, participantId) => {
      if (!selectedTontine.value.monthlyBeneficiaries) {
        selectedTontine.value.monthlyBeneficiaries = {}
      }
      
      if (participantId) {
        selectedTontine.value.monthlyBeneficiaries[monthNumber] = participantId
      } else {
        delete selectedTontine.value.monthlyBeneficiaries[monthNumber]
      }
      
      saveToLocalStorage()
    }

    // Bulk payment actions
    const markAllPaid = (monthNumber) => {
      if (!selectedTontine.value.monthlyPayments) {
        selectedTontine.value.monthlyPayments = {}
      }
      
      selectedTontine.value.participants.forEach(participant => {
        const key = `${participant.id}-${monthNumber}`
        selectedTontine.value.monthlyPayments[key] = true
      })
      
      saveToLocalStorage()
    }

    const markAllUnpaid = (monthNumber) => {
      if (!selectedTontine.value.monthlyPayments) {
        selectedTontine.value.monthlyPayments = {}
      }
      
      selectedTontine.value.participants.forEach(participant => {
        const key = `${participant.id}-${monthNumber}`
        selectedTontine.value.monthlyPayments[key] = false
      })
      
      saveToLocalStorage()
    }

    const finalizeMonth = (monthNumber) => {
      const beneficiaryId = getMonthBeneficiaryId(selectedTontine.value, monthNumber)
      const beneficiary = selectedTontine.value.participants.find(p => p.id === beneficiaryId)
      const amount = getCollectedAmount(selectedTontine.value, monthNumber)
      
      if (confirm(`Confirmer la distribution de ${amount.toLocaleString()} FC à ${beneficiary.prenom} ${beneficiary.nom} pour le mois ${monthNumber} ?`)) {
        // Mark month as finalized
        if (!selectedTontine.value.finalizedMonths) {
          selectedTontine.value.finalizedMonths = {}
        }
        selectedTontine.value.finalizedMonths[monthNumber] = {
          beneficiaryId,
          amount,
          date: new Date().toISOString()
        }
        
        saveToLocalStorage()
        alert(`Mois ${monthNumber} finalisé ! ${beneficiary.prenom} ${beneficiary.nom} a reçu ${amount.toLocaleString()} FC.`)
      }
    }

    const resetMonth = (monthNumber) => {
      if (confirm(`Êtes-vous sûr de vouloir réinitialiser tous les paiements du mois ${monthNumber} ?`)) {
        // Reset all payments for this month
        if (selectedTontine.value.monthlyPayments) {
          selectedTontine.value.participants.forEach(participant => {
            const key = `${participant.id}-${monthNumber}`
            delete selectedTontine.value.monthlyPayments[key]
          })
        }
        
        // Reset beneficiary
        if (selectedTontine.value.monthlyBeneficiaries) {
          delete selectedTontine.value.monthlyBeneficiaries[monthNumber]
        }
        
        // Reset finalization
        if (selectedTontine.value.finalizedMonths) {
          delete selectedTontine.value.finalizedMonths[monthNumber]
        }
        
        saveToLocalStorage()
      }
    }

    // Lifecycle
    onMounted(() => {
      checkAuth()
      loadFromLocalStorage()
    })

    return {
      // Authentication
      isAuthenticated,
      loginForm,
      loginError,
      login,
      logout,
      
      // Main state
      currentView,
      tontines,
      selectedTontine,
      editingTontine,
      selectedReportTontine,
      selectedTontineMonth,
      currentReport,
      activeTab,
      selectedMonth,
      selectedMonthDetail,
      availableMonths,
      tontineForm,
      participantForm,
      
      // Computed
      totalParticipants,
      totalAmount,
      activeTontines,
      
      // Methods
      updateDateFin,
      saveTontine,
      editTontine,
      cancelEdit,
      deleteTontine,
      selectTontine,
      selectMonthDetail,
      addParticipant,
      removeParticipant,
      getParticipantMonth,
      toggleMonthlyPayment,
      getPaymentStatus,
      getMonthBeneficiary,
      getMonthStatus,
      getMonthStatusText,
      getMonthStatusBadgeClass,
      getPaymentRatio,
      getTotalToCollect,
      getCollectedAmount,
      getPaidParticipants,
      getTontineStatus,
      getTontineStatusClass,
      getCurrentMonthNumber,
      getProgressPercentage,
      getMonthsList,
      getMonthLabel,
      formatDateShort,
      onTontineSelect,
      generateReport,
      printReport,
      // Nouvelles méthodes à ajouter
      getMonthBeneficiaryId,
      setMonthBeneficiary,
      markAllPaid,
      markAllUnpaid,
      finalizeMonth,
      resetMonth
    }
  }
}
</script>

<style>
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
