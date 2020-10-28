<template>
  <a-layout>
    <Loading v-show="$system_variables.status_data_loaded == 0"/>    
    <Sidebar />
    <a-layout :id="'container_content'">
      <Header/>
      <a-layout-content :style="{ margin: '24px 16px 0',padding: '24px',background: '#fff', overflow: 'auto', height: 'calc(100vh - 88px)' }" v-if="status_site_loaded == 1">
        <LoadingFailed v-if="$system_variables.status_task_loaded == -1"/>
        <LoadingAccessDeny v-if="$system_variables.status_task_loaded == -2"/>
        <SiteOffLine v-if="$system_variables.status_task_loaded == -3"/>
         <router-view/>         
      </a-layout-content>
      <a-layout-content :style="{ margin: '24px 16px 0',padding: '24px',background: '#fff', overflow: 'auto', height: 'calc(100vh - 88px)' }" v-if="status_site_loaded == -1">
         <LoadingFailed/>         
      </a-layout-content>
      
    </a-layout>
  </a-layout>
</template>
<script>
import Loading from '@/views/busy_states/Loading.vue'
import LoadingFailed from '@/views/busy_states/LoadingFailed.vue'
import LoadingAccessDeny from '@/views/busy_states/LoadingAccessDeny.vue'
import SiteOffLine from '@/views/busy_states/SiteOffLine.vue'
import Sidebar from '@/views/template/Sidebar.vue'
import Header from '@/views/template/Header.vue'
export default {
  name: 'App',
  components: {
    Loading,LoadingFailed,LoadingAccessDeny,SiteOffLine,Sidebar,Header
  },
  data() {
    return {
      sidebar_inactive:false,
      status_site_loaded:0,//Loading=0,success=0,failed=-1  only this page 
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
    this.init();
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
    },
    init: function()
    {
      this.status_site_loaded=0; 
      this.$system_variables.status_data_loaded=0;//data-loading      
      var form_data=this.$system_functions.get_form_data_with_auth(new FormData());
      
      this.$axios.all([      
          this.$axios.post('/user/initialize',form_data),          
          //this.$axios.post('/user/get_visitor_tasks',form_data),          
        ])
        .then(this.$axios.spread((user_initialize,visitor_tasks) => 
        {
          this.$system_variables.status_data_loaded=1;

          if(!(user_initialize.data.error_type))
          { 
            if(user_initialize.data.user)
            {
              this.$system_functions.set_user(user_initialize.data.user);
              
            }  
                      
              //error
          }
          
          this.status_site_loaded=1;
          console.log(this.status_site_loaded);
        })).catch(error => {
          console.log(error);

          this.$system_variables.status_data_loaded=1;  
          this.status_site_loaded=-1;  
        });
    },
    
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
