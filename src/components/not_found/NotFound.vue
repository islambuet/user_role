<template>
    <div>
        <h1>{{$system_functions.get_label('msg_404_title')}}</h1>
        <h3>{{$system_functions.get_label('msg_404_header')}}</h3>
        <p>{{$system_functions.get_label('msg_404_message')}}</p>   
        <a-button onclick="window.location.reload();" type="primary">{{$system_functions.get_label('button_refresh_page')}}</a-button>        
    </div>
</template>
<script>
export default { 
  name: "NotFound", 
  mounted:function()
  {
    
    console.log('404 monted');    
  },  
  
};
</script>