<template>
  <div>
      <div v-if="$system_variables.status_task_loaded==1">
        <a-card  v-if="$system_variables.user.id == 0">
          <router-link to="/login" class="ant-btn ant-btn-primary ant-btn-md"><a-icon type="login" /><span>{{$system_functions.get_label('label_login')}}</span></router-link>
        </a-card>
      </div>
  </div>
</template>

<script>
export default {
  name: 'Home',
  props: {
    msg: String
  },
  mounted:function()
  {
      this.$system_variables.status_task_loaded=1;  
      this.$system_variables.status_data_loaded=1;        
  },
  data() {
      return {
        value: '',
        
      }
    }
}
</script>
