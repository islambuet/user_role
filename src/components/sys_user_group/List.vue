<template>
    <div v-if="$parent.permissions.action_0" v-show="$system_variables.status_data_loaded==1">
      asdf
    
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
       
  } 
}
</script>
