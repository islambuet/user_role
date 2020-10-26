<template>
    <div>
        <h1>{{$system_functions.get_label('msg_loading_failed_title')}}</h1>
        <h3>{{$system_functions.get_label('msg_loading_failed_header')}}</h3>
        <p>{{$system_functions.get_label('msg_loading_failed_message')}}</p>   
        <a-button onclick="window.location.reload();" type="primary">{{$system_functions.get_label('button_refresh_page')}}</a-button>        
    </div>
</template>