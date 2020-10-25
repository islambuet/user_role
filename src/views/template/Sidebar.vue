<template>
<a-layout-sider
      v-model="sidebar_inactive"     
      collapsedWidth=0
      :style="{height: '100vh'}"
      :width="250"
      :collapsible="true"
      @collapse="on_sidebar_collapse"
    >
    <div :style="{overflow: 'auto',height: '100%'}">
      <div class="logo" />
      <a-menu theme="dark" mode="inline">
        <a-menu-item>          
          <span class="nav-text">nav 1</span>
        </a-menu-item>
        <a-sub-menu>
          <span slot="title"><a-icon type="user" />nav 2</span>
          <a-menu-item>
                option1
              </a-menu-item>
              <a-menu-item>
                option2
              </a-menu-item>
              <a-sub-menu>
                <span slot="title"><a-icon type="user" />Option3</span>
                <a-menu-item>
                      option31
                    </a-menu-item>
                    <a-menu-item>
                      option32
                    </a-menu-item>
                    <a-menu-item>
                      option33
                    </a-menu-item>
              </a-sub-menu>
              <a-menu-item>
                option4
              </a-menu-item>

        </a-sub-menu>
        <a-menu-item>          
          <span class="nav-text">nav 3</span>
        </a-menu-item>
        <a-menu-item>          
          <span class="nav-text">nav 4</span>
        </a-menu-item>
        <a-sub-menu>
          <span slot="title"><a-icon type="user" />nav 5</span>
          <a-menu-item>
                option1
              </a-menu-item>
              <a-menu-item>
                option2
              </a-menu-item>
              <a-menu-item>
                option3
              </a-menu-item>
        </a-sub-menu>
        <a-sub-menu>
          <span slot="title"><a-icon type="user" />nav 5</span>
          <a-menu-item>
                option1
              </a-menu-item>
              <a-menu-item>
                option2
              </a-menu-item>
              <a-menu-item>
                option3
              </a-menu-item>
        </a-sub-menu>
        <a-sub-menu>
          <span slot="title"><a-icon type="user" />last</span>
          <a-menu-item>
                option1
              </a-menu-item>
              <a-menu-item>
                option2
              </a-menu-item>
              <a-menu-item>
                option last
              </a-menu-item>
        </a-sub-menu>
      </a-menu>
    </div>
    </a-layout-sider>
</template>
<script>
//import MenuTree from "./MenuTree.vue";
export default {
  name: 'Sidebar',  
  //components:{MenuTree}  
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
    }
  },
};

</script>

