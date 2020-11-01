<template>
    <div v-if="$parent.permissions.action_0" v-show="$system_variables.status_data_loaded==1">
      <a-card :class="'d-print-none'">
        <router-link to="/sys_user_group/add" :class="'ant-btn ant-btn-primary ant-btn-md mr-2 mb-2'">{{$system_functions.get_label('button_new')}}</router-link>  
        <a-button type="primary" :class="'mr-2 mb-2'" v-if="$parent.permissions.action_0" onclick="window.print();">{{$system_functions.get_label('button_print')}}</a-button>
        <a-button type="primary" :class="'mr-2 mb-2'" v-if="$parent.permissions.action_0" @click="$parent.init">{{$system_functions.get_label('button_refresh')}}</a-button>
      </a-card>
      <a-card>
        <a-input-search v-model="search_id" placeholder="Search Id" style="width: 200px" />
    <br /><br />
    <a-input-search v-model="search_name" placeholder="Serach Name" enter-button/>
    <br /><br />
        <a-date-picker :format="'DD-MMM-YYYY'" v-model="search_date"/>
        <a-table 
        :columns="$parent.columns.display_columns"  
        :data-source="modified_items" 
        rowKey='id'
        :loading="$parent.grid_data_loading" 
        bordered 
        :pagination="$parent.pagination"
        :scroll="{ y: 240}"  
        @change="onTableChange"      
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
      search_id:'',
      search_name:'',
      search_status:'',
      search_date:null,
      show_column_controls:true,
      show_fitler_options:false
    }
  },
  computed:{   
    modified_items:function(){       
      //var filterd_items=this.$system_functions.get_filter_items(this.$parent.items,this.$parent.columns.filter_columns); 
      //this.$parent.pagination.num_item_showing=filterd_items.length;         
      return this.$parent.items.filter((item)=>{        
        if((this.search_id.length>0) && (item.id != this.search_id))        
        {
          return false;
        }
        if((this.search_name.length>0) && (item.name.toLowerCase().indexOf(this.search_name.toLowerCase())==-1) )                 
        {
          return false;
        }
        if(this.search_date !=null)                 
        {
          if(item.date_created>(this.search_date.toDate().getTime()/1000))
          {
            return false;
          }
        }
        
        return true;
      });
    },          
    get_csv_headers(){
      return this.$parent.columns.display_columns.filter((item)=>{ if(item.key=='actions'){return false;} return true});
    }
  },  
  mounted:function()
  {      
  }, 
  methods:{ 
     onChangeDate(date, dateString) 
     {
       if(date==null)
       {
         this.search_date='';         
       }
       else
       {
         this.search_date=date.toDate().getTime() / 1000;         
       }
    },
    onTableChange(pagination, filters, sorter) 
    {
      console.log('params', pagination, filters, sorter);    
      
    },
       
  } 
}
</script>
