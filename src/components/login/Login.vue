<template>
  <a-row >
      <a-col :lg="{ span: 11, offset: 7 }" :sm="{ span: 13, offset: 5 }">
        <a-form :form="form" @submit="handleSubmit"  >
            <a-form-item has-feedback>
                <a-input
                    name="username"
                    placeholder="UserName"
                    v-decorator="[
                    'username',
                    {
                        rules: [
                        {
                            required: true,
                            message: 'Username is required!',
                        },
                        ],
                    },
                    ]"
                />
            </a-form-item>
            <a-form-item has-feedback>
                <a-input-password 
                    name="password"
                    allow-clear 
                    placeholder="Password" 
                    v-decorator="[
                    'password',
                    {
                        rules: [
                        {
                            required: true,
                            message: 'Please input your password!',
                        }
                        ],
                    },
                    ]"
                />
            </a-form-item>
                <a-form-item>
                <a-button type="primary" html-type="submit">
                    Login
                </a-button>
            </a-form-item>
        </a-form>
      </a-col>
    </a-row>
</template>

<script>

export default {
    name: 'Login',
    components: { },
    mounted:function(){
        this.init();
    },
    data() {
        return {
            form: this.$form.createForm(this, { name: 'dynamic_rule' }),
            error_message_loading_fail: '',
            otp_required: false,
            token_sms: '',
            form_title: 'Fill out the form below to login.',
            otp_error_message: ''
        }
    },
    methods:{    
        init: function()
        {   
            this.$system_variables.status_task_loaded=1;
            this.$system_variables.status_data_loaded=1;
        },    
        handleSubmit(e) {
            e.preventDefault();
            this.form.validateFields((err, values) => 
            {
                if (!err) {
                    console.log('Received values of form: ', values);            
                }
                else{
                    console.log('Eroor',err,values);
                }
            });
            

        },     
        login: function(event)
        {
            this.$system_variables.status_data_loaded = 0;
            this.error_message_loading_fail = this.$system_variables.error_message_api_server;
            // Fetch from Local Storage
            var device_token = (localStorage.getItem("token_device") !== null)? localStorage.getItem("token_device") : '';

            var formData=new FormData(document.getElementById('form_login'));
            formData.append ('token_device', device_token);
            formData.append ('device[token_device]', device_token);
            formData.append ('device[device_info]', {});

            this.$axios.post('/login',formData)
            .then(response=>{
                if(response.data.status_code == 0)
                {
                    this.error_message_loading_fail = response.data.message;
                    this.$system_variables.labels.msgLoadingErrorHeader = response.data.message;
                    this.$system_variables.labels.msgLoadingErrorSubHeader = response.data.message_warning;
                    this.$system_variables.labels.msgLoadingErrorInfo = '';
                }
                else if(response.data.status_code == 100)
                {
                    this.error_message_loading_fail = response.data.message;
                    this.$system_variables.labels.msgLoadingErrorHeader = response.data.message;
                    this.$system_variables.labels.msgLoadingErrorSubHeader = response.data.message_warning;
                    this.$system_variables.labels.msgLoadingErrorInfo = '';
                }
                else if(response.data.status_code == 1101){
                    this.token_sms = response.data.token_sms;
                    this.formTitle = response.data.message_warning;
                    //this.$parent.set_user_data(response.data.user);
                    this.otp_required = true;
                }
                else
                {                    
                    this.$parent.set_user_data(response.data.user.user);                    
                    // Device Token
                    localStorage.setItem('token_device', response.data.user.device.token_device);
                    this.$router.push({name:'Home'}).catch(()=>{}); 
                }
                this.$system_variables.status_data_loaded = 1;
            })
            .catch(error => {
                console.log("AJAX Error: " + error);
                this.$system_variables.status_data_loaded = 1;
            })
        },
        login_sms: function(event)
        {
            this.$system_variables.status_data_loaded = 0;
            this.error_message_loading_fail = this.$system_variables.error_message_api_server;
            
            var device_token = ''; // Device Token Not Available while sending OTP code

            var formData=new FormData(document.getElementById('form_otp'));
            formData.append ('token_device', device_token);
            formData.append ('device[token_device]', device_token);

            this.$axios.post('/login_sms',formData)
            .then(response=>{
                if(response.data.status_code == 110)
                {
                    this.error_message_loading_fail = response.data.message;
                    this.otp_error_message = '<b style="color:#ff0000">OTP Expired or, already used!</b>';
                }
                else if(response.data.status_code == 300) // OTP code not Found
                {
                    this.error_message_loading_fail = response.data.message;
                    this.otp_error_message = '<b style="color:#ff0000">OTP Code Not Found!</b>';
                }
                else if(response.data.status_code == 301)
                {
                    this.error_message_loading_fail = response.data.message;
                    this.otp_error_message = '<b style="color:#ff0000">Incorrect OTP Code!</b>';
                }
                else if(response.data.status_code == 302)
                {
                    this.error_message_loading_fail = response.data.message;
                    this.otp_error_message = '<b style="color:#ff0000">Maximum OTP Retry Exceeded!</b>';
                }
                else
                {
                    this.$parent.set_user_data(response.data.user.user);
                    localStorage.setItem('token_device', response.data.user.device.token_device)
                    this.$router.push({name:'Home'}).catch(()=>{}); 
                }
                this.$system_variables.status_data_loaded = 1;
            })
            .catch(error => {
                console.log(error);
                this.$system_variables.status_data_loaded = 1;
            })
        },
    }
}
</script>