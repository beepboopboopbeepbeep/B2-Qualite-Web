<template>
  <div class="min-h-screen bg-zinc-950 p-6">
    <div class="max-w-6xl mx-auto">
      <!-- Header -->
      <div class="text-center mb-8">
        <h1 class="text-4xl font-bold text-zinc-100 mb-2">
          Gestion des Alias Email
        </h1>
        <p class="text-zinc-400">
          Créez des adresses email alias pour mieux contrôler votre courrier
        </p>
      </div>

      <!-- Main Content -->
      <div class="space-y-8">
        <!-- Info Card -->
        <div
          class="bg-zinc-900 rounded-lg shadow-md p-6 border border-zinc-800"
        >
          <h2 class="text-xl font-bold text-zinc-100 mb-3">
            Comment ça marche ?
          </h2>
          <p class="text-zinc-400 mb-4">
            Les alias email vous permettent de créer des variations de votre
            adresse principale avec le format
            <code class="bg-zinc-800 px-2 py-1 rounded text-zinc-200"
              >votremail+tag@exemple.com</code
            >. Tous les emails sont reçus dans la même boîte.
          </p>
          <div class="grid md:grid-cols-2 gap-4">
            <div class="bg-zinc-800 p-3 rounded border border-zinc-700">
              <p class="text-sm text-zinc-300">
                <span class="font-semibold text-zinc-100">Exemple :</span><br />
                <code class="text-zinc-200">jean.dupont@gmail.com</code><br />
                devient<br />
                <code class="text-zinc-200"
                  >jean.dupont+shopping@gmail.com</code
                >
              </p>
            </div>
            <div class="bg-zinc-800 p-3 rounded border border-zinc-700">
              <p class="text-sm text-zinc-300">
                <span class="font-semibold text-zinc-100">Cas d'usage :</span
                ><br />
                Utilisez des tags pour classer vos inscriptions (shopping,
                travail, forum, etc.)
              </p>
            </div>
          </div>
        </div>

        <!-- Create Alias Section -->
        <div
          class="bg-zinc-900 rounded-lg shadow-md p-6 border border-zinc-800"
        >
          <h2 class="text-xl font-bold text-zinc-100 mb-6">Créer un Alias</h2>
          <form @submit.prevent="createAlias" class="space-y-4">
            <div>
              <label class="block text-sm font-medium text-zinc-200 mb-2">
                Adresse Email Principale
              </label>
              <input
                v-model="mainEmail"
                type="email"
                disabled
                class="w-full px-4 py-2 border border-zinc-700 bg-zinc-800 rounded-lg text-zinc-100 opacity-75"
              />
            </div>

            <div class="grid md:grid-cols-2 gap-4">
              <div>
                <label class="block text-sm font-medium text-zinc-200 mb-2">
                  Tag/Alias
                </label>
                <input
                  v-model="newAlias.tag"
                  type="text"
                  placeholder="shopping, travail, forum..."
                  class="w-full px-4 py-2 border border-zinc-700 bg-zinc-800 rounded-lg text-zinc-100 focus:ring-2 focus:ring-zinc-500 focus:border-transparent"
                />
              </div>
              <div>
                <label class="block text-sm font-medium text-zinc-200 mb-2">
                  Description (optionnel)
                </label>
                <input
                  v-model="newAlias.description"
                  type="text"
                  placeholder="Ex: Pour les achats en ligne"
                  class="w-full px-4 py-2 border border-zinc-700 bg-zinc-800 rounded-lg text-zinc-100 focus:ring-2 focus:ring-zinc-500 focus:border-transparent"
                />
              </div>
            </div>

            <div class="bg-zinc-800 p-4 rounded border border-zinc-700">
              <p class="text-sm text-zinc-300">
                <span class="font-semibold text-zinc-100"
                  >Adresse générée :</span
                ><br />
                <code class="text-zinc-200">{{ previewAlias }}</code>
              </p>
            </div>

            <button
              type="submit"
              class="w-full bg-zinc-700 text-zinc-100 py-3 rounded-lg font-semibold hover:bg-zinc-600 transition-colors"
            >
              Créer Cet Alias
            </button>
          </form>
        </div>

        <!-- Active Aliases -->
        <div
          class="bg-zinc-900 rounded-lg shadow-md p-6 border border-zinc-800"
        >
          <h2 class="text-2xl font-bold text-zinc-100 mb-6">
            Vos Alias Actifs ({{ aliases.length }})
          </h2>

          <div v-if="aliases.length === 0" class="text-center py-12">
            <p class="text-zinc-400">Vous n'avez pas encore créé d'alias</p>
          </div>

          <div v-else class="space-y-4">
            <div
              v-for="(alias, index) in aliases"
              :key="index"
              class="bg-zinc-800 rounded-lg p-4 border border-zinc-700 hover:border-zinc-600 transition-colors"
            >
              <div class="flex items-start justify-between mb-3">
                <div class="flex-1">
                  <h3 class="text-lg font-semibold text-zinc-100">
                    {{ alias.fullEmail }}
                  </h3>
                  <p
                    v-if="alias.description"
                    class="text-sm text-zinc-400 mt-1"
                  >
                    {{ alias.description }}
                  </p>
                </div>
                <button
                  @click="deleteAlias(index)"
                  class="ml-4 px-3 py-2 bg-red-900 text-red-100 rounded-lg hover:bg-red-800 transition-colors text-sm font-semibold"
                >
                  Supprimer
                </button>
              </div>

              <div class="grid grid-cols-2 md:grid-cols-4 gap-3">
                <div>
                  <p class="text-xs text-zinc-500 mb-1">Créé le</p>
                  <p class="text-sm text-zinc-300">{{ alias.createdDate }}</p>
                </div>
                <div>
                  <p class="text-xs text-zinc-500 mb-1">Emails reçus</p>
                  <p class="text-sm text-zinc-300">
                    {{ alias.emailsReceived }}
                  </p>
                </div>
                <div>
                  <p class="text-xs text-zinc-500 mb-1">Statut</p>
                  <span
                    class="inline-block px-2 py-1 bg-green-900 text-green-300 rounded text-xs font-semibold"
                  >
                    ✓ Actif
                  </span>
                </div>
                <div>
                  <p class="text-xs text-zinc-500 mb-1">Dernier email</p>
                  <p class="text-sm text-zinc-300">{{ alias.lastEmail }}</p>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Security Tips -->
        <div
          class="bg-zinc-900 rounded-lg shadow-md p-6 border border-zinc-800"
        >
          <h2 class="text-xl font-bold text-zinc-100 mb-4">
            Avantages des Alias Email
          </h2>
          <div class="space-y-3">
            <div class="flex gap-3">
              <div
                class="w-6 h-6 bg-zinc-800 rounded flex items-center justify-center text-zinc-300 font-bold text-sm flex-shrink-0"
              >
                ✓
              </div>
              <div>
                <p class="text-zinc-100 font-semibold">Filtrage Facile</p>
                <p class="text-sm text-zinc-400">
                  Triez les emails par tag pour une meilleure organisation
                </p>
              </div>
            </div>
            <div class="flex gap-3">
              <div
                class="w-6 h-6 bg-zinc-800 rounded flex items-center justify-center text-zinc-300 font-bold text-sm flex-shrink-0"
              >
                ✓
              </div>
              <div>
                <p class="text-zinc-100 font-semibold">Traçabilité</p>
                <p class="text-sm text-zinc-400">
                  Identifiez qui vend/partage votre adresse email
                </p>
              </div>
            </div>
            <div class="flex gap-3">
              <div
                class="w-6 h-6 bg-zinc-800 rounded flex items-center justify-center text-zinc-300 font-bold text-sm flex-shrink-0"
              >
                ✓
              </div>
              <div>
                <p class="text-zinc-100 font-semibold">Sécurité Renforcée</p>
                <p class="text-sm text-zinc-400">
                  Limitez l'exposition de votre adresse principale
                </p>
              </div>
            </div>
            <div class="flex gap-3">
              <div
                class="w-6 h-6 bg-zinc-800 rounded flex items-center justify-center text-zinc-300 font-bold text-sm flex-shrink-0"
              >
                ✓
              </div>
              <div>
                <p class="text-zinc-100 font-semibold">Contrôle Total</p>
                <p class="text-sm text-zinc-400">
                  Désactivez les alias qui reçoivent trop de spam
                </p>
              </div>
            </div>
          </div>
        </div>

        <!-- FAQ -->
        <div
          class="bg-zinc-900 rounded-lg shadow-md p-6 border border-zinc-800"
        >
          <h2 class="text-xl font-bold text-zinc-100 mb-4">
            Questions Fréquentes
          </h2>
          <div class="space-y-4">
            <div>
              <h3 class="font-semibold text-zinc-100 mb-2">
                Les emails envoyés à mes alias apparaissent-ils avec mon adresse
                alias ?
              </h3>
              <p class="text-sm text-zinc-400">
                Oui, quand vous utilisez un alias pour vous inscrire, le site
                reconnaît cet alias comme votre adresse email valide. Les
                confirmations et notifications seront envoyées à cet alias.
              </p>
            </div>
            <div>
              <h3 class="font-semibold text-zinc-100 mb-2">
                Puis-je répondre aux emails avec mon alias ?
              </h3>
              <p class="text-sm text-zinc-400">
                Non, les réponses seront envoyées avec votre adresse principale.
                Les alias sont principalement pour recevoir des emails, pas pour
                en envoyer.
              </p>
            </div>
            <div>
              <h3 class="font-semibold text-zinc-100 mb-2">
                Combien d'alias puis-je créer ?
              </h3>
              <p class="text-sm text-zinc-400">
                Vous pouvez créer jusqu'à 50 alias actifs. Les alias inactifs
                peuvent être supprimés ou réactivés.
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'EmailAliasManagement',
  data() {
    return {
      mainEmail: 'jean.dupont@gmail.com',
      newAlias: {
        tag: '',
        description: '',
      },
      aliases: [
        {
          tag: 'shopping',
          fullEmail: 'jean.dupont+shopping@gmail.com',
          description: 'Pour les achats en ligne',
          createdDate: '15 Jan 2026',
          emailsReceived: 12,
          lastEmail: 'Il y a 2 jours',
        },
        {
          tag: 'travail',
          fullEmail: 'jean.dupont+travail@gmail.com',
          description: 'Pour les inscriptions professionnelles',
          createdDate: '10 Jan 2026',
          emailsReceived: 8,
          lastEmail: 'Il y a 5 jours',
        },
        {
          tag: 'forum',
          fullEmail: 'jean.dupont+forum@gmail.com',
          description: 'Pour les forums et communautés',
          createdDate: '5 Jan 2026',
          emailsReceived: 25,
          lastEmail: 'Il y a 1 jour',
        },
      ],
    }
  },
  computed: {
    previewAlias() {
      if (!this.newAlias.tag) return 'jean.dupont+tag@gmail.com'
      const basePart = this.mainEmail.split('@')[0]
      const domain = this.mainEmail.split('@')[1]
      return `${basePart}+${this.newAlias.tag}@${domain}`
    },
  },
  methods: {
    createAlias() {
      if (!this.newAlias.tag.trim()) {
        alert('Veuillez entrer un tag pour votre alias')
        return
      }

      const basePart = this.mainEmail.split('@')[0]
      const domain = this.mainEmail.split('@')[1]

      const newAliasObj = {
        tag: this.newAlias.tag,
        fullEmail: `${basePart}+${this.newAlias.tag}@${domain}`,
        description: this.newAlias.description,
        createdDate: new Date().toLocaleDateString('fr-FR', {
          day: '2-digit',
          month: 'short',
          year: 'numeric',
        }),
        emailsReceived: 0,
        lastEmail: 'Jamais',
      }

      this.aliases.push(newAliasObj)
      this.newAlias = { tag: '', description: '' }
    },
    deleteAlias(index) {
      if (confirm(`Êtes-vous sûr de vouloir supprimer cet alias ?`)) {
        this.aliases.splice(index, 1)
      }
    },
  },
}
</script>

<style scoped>
code {
  font-family: 'Courier New', monospace;
}
</style>
