<template>
    <a-layout-header :style="{ background: '#fff'}" >
        <div :style="{float:'right'}">
            <img :src="$system_variables.user.info.profile_picture" v-if="$system_variables.user.info.profile_picture" alt="profileImage" class="rounded-circle" :style="{height:'32px',width:'32px'}">
            <img :src="'/images/logo.png'" v-else alt="Image" class="rounded-circle" :style="{height:'32px',width:'32px'}">
            <a-dropdown :trigger="['click']" :style="{float:'right'}">
                <a class="ant-dropdown-link" @click.prevent>
                 <a-icon type="down" />
                </a>
                <a-menu slot="overlay">
                <a-menu-item ><router-link to="/change_password">{{$system_functions.get_label('label_change_password')}}</router-link></a-menu-item >
                <a-menu-item ><router-link to="/profile_picture">{{$system_functions.get_label('label_change_profile_picture')}}</router-link></a-menu-item >
                <a-menu-item >{{$system_functions.get_label('label_logout')}}</a-menu-item>
            </a-menu>                
            </a-dropdown>
        </div>
        <a-dropdown :trigger="['click']" :style="{float:'right',marginRight:'10px'}">
            <a class="ant-dropdown-link" @click.prevent>
            {{$system_functions.get_label('label_change_language')}} <a-icon type="down" />
            </a>
            <a-menu slot="overlay">
                <a-menu-item @click="$system_htm_elements_action.click_change_language($event,'en')">English</a-menu-item>
                <a-menu-item @click="$system_htm_elements_action.click_change_language($event,'bn')">বাংলা</a-menu-item>
            </a-menu>
        </a-dropdown>
        
    </a-layout-header>
</template>
<style scoped>
.rounded-circle{
border-radius: 50%;
}
</style>