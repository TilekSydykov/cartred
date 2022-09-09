<template>
  <v-card>
    <h1 class="header">Cart red</h1>
    <p class="description">Login via github auth</p>
    <div>
      <v-btn
          :disabled="loading"
          v-on:click="handleLogin"
      >
        {{ loading ? 'Loading' : 'Login via github' }}
      </v-btn>
    </div>
  </v-card>
</template>

<script setup>
const supabase = useSupabaseClient()

const loading = ref(false)
const handleLogin = async () => {
  try {
    loading.value = true
    const { error } = await supabase.auth.signIn({provider: 'github'})
    if (error) throw error
  } catch (error) {
    alert(error.error_description || error.message)
  } finally {
    loading.value = false
  }
}
</script>

<style scoped >
.v-card {
  padding: 10px;
  margin: 10px;
}
</style>
