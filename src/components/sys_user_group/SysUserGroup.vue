<template>
  <div v-if="$system_variables.status_task_loaded==1">
     <List v-show="method=='list'"/>
  </div>

</template>
<script>
import List from './List.vue'

export default {
  name: 'SysUserGroup',
  components: {
      List//,AddEdit   
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
        item:{},        
        default_item:{},
        max_modules_tasks_level:1,
        modules_tasks:[],
        max_module_task_action:9,
        reload_items:true,
        grid_data_loading:false
      }
  }, 
  watch: {
      $route(to, from) {
      this.routing(to);      
      }
  }, 
  methods: {
    routing:function(route)
    {      
      if(route.path=='/sys_user_group')
      {
        this.method='list';
        this.get_items();
      }
      /*else if(route.path=="/sys_user_group/add")
      {
        this.method='add';
        this.add_edit(0);
      }
      else if(route.path.indexOf("/sys_user_group/edit/")!=-1)
      {
        this.method='edit';        
        this.add_edit(route.params['item_id']);        
      }
      else if(route.path.indexOf("/sys_user_group/role/")!=-1)
      {
        this.method='role';        
        this.role(route.params['item_id']);        
      }*/
    },
    init:function()
    { 
        this.$system_variables.status_task_loaded=0; 
        this.$system_variables.status_data_loaded=0;  
        this.reload_items=true;
        var form_data=this.$system_functions.get_form_data_with_auth(new FormData());
        this.$axios.post('/sys_user_group/initialize',form_data)
        .then(response=>{
            
            if(response.data.error_type)        
            {   
                this.$system_functions.response_error_task(response);
            }
            else
            {
                this.permissions=response.data.permissions; 
                this.default_item=response.data.default_item;
                this.item={ ...this.default_item };

                if(response.data.hidden_columns)  
                {
                  this.columns.hidden_columns=response.data.hidden_columns;
                }               
                if(response.data.max_module_task_action)  
                {
                  this.max_module_task_action=response.data.max_module_task_action;
                }               
                this.max_modules_tasks_level=response.data.max_modules_tasks_level;
                this.modules_tasks=response.data.modules_tasks;  
                this.$system_variables.status_task_loaded=1;

                this.set_display_columns();


                this.routing(this.$route);

            }        
            this.$system_variables.status_data_loaded=1;
        })
        .catch(error => {     
            console.log(error);
            this.$system_variables.status_task_loaded=-1;
            this.$system_variables.status_data_loaded=1;
        });
    },
    
    //for list view
    set_display_columns:function()
    {
      //var columns={};
      /*columns['actions']={label:this.$system_variables.get_label('label_action'), hidden:this.columns.false,sticky_column:false};//cannot set it sticky becuase dropdown will goes bellow
      columns['id']={label:this.$system_variables.get_label('label_id'), hidden:this.columns.hidden_columns.indexOf('id')>=0?true:false,sticky_column:true,sortable:false};
      columns['purpose']={label:this.$system_variables.get_label_task('label_purpose'), hidden:this.columns.hidden_columns.indexOf('purpose')>=0?true:false,sticky_column:false,sortable:false};
      columns['description']={label:this.$system_variables.get_label_task('label_description'), hidden:this.columns.hidden_columns.indexOf('description')>=0?true:false,sticky_column:false,sortable:false};
      columns['config_value']={label:this.$system_variables.get_label_task('label_config_value'), hidden:this.columns.hidden_columns.indexOf('config_value')>=0?true:false,sticky_column:false,sortable:false};
      columns['status']={label:this.$system_variables.get_label('label_status'), hidden:this.columns.hidden_columns.indexOf('status')>=0?true:false,sticky_column:false,sortable:false};
      this.columns.display_columns=this.$system_functions.get_display_columns(columns); */
      this.columns.display_columns = [
          {
            title:'Id',            
            dataIndex: 'id',
          },
          {
            title: 'Name',
            dataIndex: 'name',
          },
          {
            title: 'Status',
            dataIndex: 'status',            
          },
          {
            title: 'Action',
            key: 'action',
            scopedSlots: { customRender: 'action' },
          },
        ];
    },
    get_items:function()
    {
      console.log("render list view");
      if(this.reload_items)
      {
        this.grid_data_loading=true;          
          var form_data=this.$system_functions.get_form_data_with_auth(new FormData());
          this.$axios.post('/sys_user_group/get_items',form_data)
          .then(response=>{                      
            if(response.data.error_type)        
            {   
                this.$system_functions.response_error_task(response);
            }
            else
            {   
                
                this.items=response.data.items;
                //this.pagination.num_items=response.data.num_items;//here returning all data                                        
                //this.pagination.num_items=this.items.length;
                this.reload_items=false;
            }  
            this.grid_data_loading=false;     
          })
          .catch(error => {   
            this.grid_data_loading=false;
            this.$notification.error({message: this.$system_functions.get_label('msg_response_error_title'),duration:10,description:this.$system_functions.get_label('msg_loading_failed_message')});
          });
      }
    },
  },
};
</script>
