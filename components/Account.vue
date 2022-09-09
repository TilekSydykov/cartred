<template>
  <v-alert-title>
    Account Settings
  </v-alert-title>

  <v-card>
    <v-form class="form-widget" @submit.prevent="updateProfile">
      <div>
        <v-label for="email">Email</v-label>
        <v-text-field v-model="user.email" disabled/>
      </div>
      <div>
        <v-label for="username">Username</v-label>
        <v-text-field v-model="username" />
      </div>
      <div>
        <v-label for="website">Website</v-label>
        <v-text-field id="website" type="website" v-model="website" />
      </div>

      <div>
        <v-btn
            type="submit"
            class="button primary block"
            :disabled="loading"
        >
          {{ loading ? 'Loading ...' : 'Update' }}
        </v-btn>
      </div>
    </v-form>
  </v-card>
  <v-alert-title>
    Danger zone
  </v-alert-title>
  <v-card class="danger-bg">
    <v-btn @click="signOut" :disabled="loading">
      Sign Out
    </v-btn>
  </v-card>
</template>

<script setup>
const supabase = useSupabaseClient()

const loading = ref(true)
const username = ref('')
const website = ref('')
const avatar_path = ref('')


loading.value = true
const user = useSupabaseUser();
let { data } = await supabase
    .from('profiles')
    .select(`username, website, avatar_url`)
    .eq('id', user.value.id)
    .single()
if (data) {
  username.value = data.username
  website.value = data.website
  avatar_path.value = data.avatar_url
}
loading.value = false

async function updateProfile() {
  try {
    loading.value = true
    const user = useSupabaseUser();
    const updates = {
      id: user.value.id,
      username: username.value,
      website: website.value,
      avatar_url: avatar_path.value,
      updated_at: new Date(),
    }
    let { error } = await supabase.from('profiles').upsert(updates, {
      returning: 'minimal', // Don't return the value after inserting
    })
    if (error) throw error
  } catch (error) {
    alert(error.message)
  } finally {
    loading.value = false
  }
}

async function signOut() {
  try {
    loading.value = true
    let { error } = await supabase.auth.signOut()
    if (error) throw error
  } catch (error) {
    alert(error.message)
  } finally {
    loading.value = false
  }
}
</script>

<style scoped>
.v-card {
  margin: 10px;
  padding: 10px;
}
.danger-bg{
  background-color: rgba(255, 0,0, 0.09);
}
</style>

