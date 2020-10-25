<template>
  <a-layout>
    <Loading v-show="$system_variables.status_data_loaded == 0"/>    
    <Sidebar />
    <a-layout :id="'container_content'">
      <Header/>
      <a-layout-content :style="{ margin: '24px 16px 0',padding: '24px',background: '#fff', overflow: 'auto', height: 'calc(100vh - 88px)' }">
         ...
          <br />
          Really
          <br />...<br />...<br />...<br />
          long
          <br />...<br />...<br />...<br />...<br />...<br />...
          <br />...<br />...<br />...<br />...<br />...<br />...
          <br />...<br />...<br />...<br />...<br />...<br />...
          <br />...<br />...<br />...<br />...<br />...<br />...
          <br />...<br />...<br />...<br />...<br />...<br />...
          <br />...<br />...<br />...<br />...<br />...<br />...
          <br />...<br />...<br />...<br />...<br />...<br />
          content
      </a-layout-content>
      
    </a-layout>
  </a-layout>
</template>
<script>
import Loading from '@/views/busy_states/Loading.vue'
import Sidebar from '@/views/template/Sidebar.vue'
import Header from '@/views/template/Header.vue'
export default {
  name: 'App',
  components: {
    Loading,Sidebar,Header
  },
  data() {
    return {
      sidebar_inactive:false
    };
  },
  mounted: function()//before create
  {
    //console.log(window.innerWidth); 
    //console.log(screen.width); 
    if(window.innerWidth<992)
    {
      this.sidebar_inactive=true
    }
    this.$system_functions.load_languages();
    this.$system_functions.set_page_title(this.$system_functions.get_label('label_site_title'));
  },
  methods: {
    on_sidebar_collapse(collapsed, type) {
      var element = document.getElementById("container_content");
      if(!collapsed)
      { 
        element.classList.add("sidebar_active");
      }
      else
      {
         element.classList.remove("sidebar_active");
      }
    }
  },
};
</script>

<style>
.logo {
  height: 32px;
  background: rgba(255, 255, 255, 0.2);
  margin: 16px;
}

@media (max-width:991px)
{
    #container_content.sidebar_active{
    margin-left: -250px;
  }

}
</style>
