<template>
  <div class="home">
    
    <Permission></Permission>
  </div>
</template>

<script>
// @ is an alias to /src
import Permission from '@/components/Permission.vue'

export default {
  name: 'Home',
  components: {
   Permission
  }
}
</script>
