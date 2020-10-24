<template>
  <a-layout id="components-layout-demo-responsive">
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
    <a-layout :id="'container_content'">
      <a-layout-header :style="{ background: '#fff', padding: 0 }" />
      <a-layout-content :style="{ margin: '24px 16px 0' }">
        <div :style="{ padding: '24px', background: '#fff', minHeight: '360px' }">
          content 1content 2content 3content 4content 5content 6
        </div>
      </a-layout-content>
      <a-layout-footer style="textAlign: center">
        Ant Design ©2018 Created by Ant UED
      </a-layout-footer>
    </a-layout>
  </a-layout>
</template>
<script>
export default {
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

<style>
#components-layout-demo-responsive .logo {
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
