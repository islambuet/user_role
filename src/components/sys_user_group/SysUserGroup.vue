<template>
  <div v-if="$system_variables.status_task_loaded==1">
     i am loaded
  </div>

</template>
<script>


export default {
  name: 'SysUserGroup',
  components: {
      //List,AddEdit   
  },
  mounted:function()
  {
    this.$system_functions.load_task_languages([
            {language:this.$system_variables.language,file:'components/sys_user_group/language.js'},
        ]);
    this.init();        
  },
  data() {
    return {
        method:'list',        
        permissions:{},
        columns:{hidden_columns:[],control_columns:[],filter_columns:{},display_columns:[]},
        items:[],
        types:[],
        item:{},        
        default_item:{id:0,name_en:"",name_bn:"",type:"",parent:'',controller:'',ordering:99,status:'Active',status_notification:''},
        max_level:0,
        reload_items:true
      }
  },  
  methods: {
    init:function()
    { 
        this.$system_variables.status_task_loaded=-3; 
        /*this.$system_variables.status_task_loaded=0;  
        this.$system_variables.status_data_loaded=1;  
        this.reload_items=true;
        var form_data=new FormData();
        form_data.append ('token_auth', this.$system_variables.user.token_auth);      
        this.$axios.post('/sys_module_task/initialize',form_data)
        .then(response=>{
            if(response.data.error_type)        
            {            
                this.$system_variables.status_task_loaded=0;
                this.$bvToast.toast(this.$system_variables.get_label(response.data.error_type), {title: this.$system_variables.get_label('label_error'),variant:'danger',autoHideDelay: 5000,appendToast: false});

            }
            else
            {
                this.permissions=response.data.permissions; 
                this.types=response.data.types; 
                Object.assign(this.default_item, response.data.default_item);                  
                this.$system_variables.status_task_loaded=1;  
                this.routing(this.$route);
            }        
        })
        .catch(error => {     
            console.log(error);
            this.$system_variables.status_task_loaded=-1;
        });*/
    },
  },
};
</script>
