<template>
<a-layout-sider
      v-model="sidebar_inactive"     
      collapsedWidth=0
      :style="{height: '100vh','z-index':'100'}"
      :width="250"
      :collapsible="true"
      @collapse="on_sidebar_collapse"
    >
    <div :style="{overflow: 'auto',height: '100%'}">
      <div class="logo" />
      <a-menu theme="dark" mode="inline">
        <a-menu-item @click="on_task_click">          
          <router-link to="/">{{$system_functions.get_label('label_dashboard')}}</router-link>
        </a-menu-item>
        <sub-menu v-for="item in $system_variables.user.tasks" :key="item.id" :menu-info="item" :on_task_click="on_task_click"/> 
      </a-menu>             
    </div>
    </a-layout-sider>
</template>
<script>
import SubMenu from "./SubMenu.vue";
export default {
  name: 'Sidebar',  
  components:{SubMenu},  
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
    on_task_click()
    {
      console.log('i am clucked');
      if(window.innerWidth<992)
      {
        this.sidebar_inactive=true;
        this.on_sidebar_collapse(true);
      }
    }
      
  },
};

</script>

