<template>
    <div v-if="$parent.permissions.action_0" v-show="$system_variables.status_data_loaded==1">
      <a-card :class="'d-print-none'">
        <router-link to="/sys_user_group/add" :class="'ant-btn ant-btn-primary ant-btn-md mr-2 mb-2'">{{$system_functions.get_label('button_new')}}</router-link>  
        <a-button type="primary" :class="'mr-2 mb-2'" v-if="$parent.permissions.action_0" onclick="window.print();">{{$system_functions.get_label('button_print')}}</a-button>
        <a-button type="primary" :class="'mr-2 mb-2'" v-if="$parent.permissions.action_0" @click="$parent.init">{{$system_functions.get_label('button_refresh')}}</a-button>
      </a-card>
      <a-card>
        <a-table 
        :columns="$parent.columns.display_columns"  
        :data-source="$parent.items" 
        :loading="$parent.grid_data_loading" 
        bordered 
        :pagination="$parent.pagination"
        :scroll="{ y: 240 }"
        @change="handleTableChange"
        >
        <template slot="date_created" slot-scope="value"> {{$system_functions.display_date(value)}} </template>
        </a-table>        
      </a-card>
  </div>
</template>

<script>


export default {
  name: 'List',
  components: {
//    Pagination,ColumnControl   
  },
  data:function(){
    return{
      show_column_controls:true,
      show_fitler_options:false
    }
  },
  computed:{   
    modified_items:function(){       
      var filterd_items=this.$system_functions.get_filter_items(this.$parent.items,this.$parent.columns.filter_columns); 
      this.$parent.pagination.num_item_showing=filterd_items.length;         
      return filterd_items;
    },          
    get_csv_headers(){
      return this.$parent.columns.display_columns.filter((item)=>{ if(item.key=='actions'){return false;} return true});
    }
  },  
  mounted:function()
  {      
  }, 
  methods:{ 
    handleTableChange(pagination, filters, sorter) 
    {
      console.log("table change");
      if((this.$parent.pagination.current_page!=pagination.current)||(this.$parent.pagination.items_per_page!=pagination.pageSize))
      {
        this.$parent.pagination.current_page=pagination.current; 
        this.$parent.pagination.items_per_page=pagination.pageSize; 
        this.$parent.reload_items=true;    
        this.$parent.get_items();
      }      
      // console.log(filters);
      // console.log(sorter);
    },   
       
  } 
}
</script>
