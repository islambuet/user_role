<template>
  <a-row >
      <a-col :lg="{ span: 11, offset: 7 }" :sm="{ span: 13, offset: 5 }">              
        <a-form :form="form" id="form_login" @submit.prevent="login($event)">            
            <a-alert v-if="alert_message != ''"
                :message="$system_functions.get_label_task('alret_login_error_title')" 
                :type="alert_type"
                closable
                >
                <p slot="description" v-html="alert_message">
                </p>
                </a-alert>
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
                            message: 'Password is required!',
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
        this.$system_functions.load_task_languages([
            {language:this.$system_variables.language,file:'components/login/language.js'},
        ]);
        this.init();
    },
    data() {
        return {            
            form: this.$form.createForm(this, { name: 'dynamic_rule' }),
            alert_message: '',
            alert_type:'error',

            // otp_required: false,
            // token_sms: '',
            // form_title: 'Fill out the form below to login.',
            // otp_error_message: ''
        }
    },
    methods:{    
        init: function()
        {   
            this.$system_variables.status_task_loaded=1;
            this.$system_variables.status_data_loaded=1;
        },               
        login: function(event)
        {
            this.form.validateFields((err, values) => 
            {
                if (!err) 
                {
                    this.alert_message = '';
                    this.$system_variables.status_data_loaded = 0;
                    var form_data=new FormData(document.getElementById('form_login'));
                    form_data.append ('token_device', this.$system_variables.user.token_device);
                    this.$axios.post('/login',form_data)
                    .then(response=>{
                        console.log(response.data);
                        if(response.data.error_type == '')
                        {
                            localStorage.setItem('token_auth', response.data.user.token_auth);
                            localStorage.setItem('token_csrf', response.data.user.token_csrf);
                            localStorage.setItem('token_device', response.data.user.token_device);
                            this.$system_functions.set_user(response.data.user);
                            this.$router.push("/");
                        }
                        else if(response.data.error_type == 'USER_NOT_FOUND')
                        {
                            this.alert_message = this.$system_functions.get_label('USER_NOT_FOUND');
                            this.alert_type = 'error';
                        }
                        else if(response.data.error_type == 'PASSWORD_INCORRECT')
                        {
                            this.alert_message = this.$system_functions.get_label('PASSWORD_INCORRECT') + 
                                                    '<br/>Your account will be suspended, if you enter wrong password '+ (response.data.remaining+1) +' more time(s).';
                            this.alert_type = 'error';
                        }
                        else if(response.data.error_type == 'USER_SUSPEND_PASSWORD_TRY_LIMIT_EXCEEDED')
                        {
                            this.alert_message = this.$system_functions.get_label('USER_SUSPEND_PASSWORD_TRY_LIMIT_EXCEEDED');
                            this.alert_type = 'error';
                        }
                        /*else if(response.data.error_type == 'OTP_VERIFICATION_REQUIRED')
                        {
                            this.token_sms = response.data.token_sms;
                            this.alert_message = this.$system_variables.get_label('OTP_VERIFICATION_REQUIRED');
                            this.alert_variant = 'warning';
                            this.otp_required = true;
                        }
                        else if(response.data.error_type == 'OTP_WAIT')
                        {
                            this.alert_message = this.$system_variables.get_label('OTP_WAIT');
                            this.alert_variant = 'warning';
                            this.otp_required = true;
                        }*/
                        else
                        {
                            this.alert_message = response.data.error_type;
                            this.alert_type = 'error';
                        }
                        this.$system_variables.status_data_loaded = 1;
                    })
                    .catch(error => {
                        console.log(error);
                        //this.alert_message = this.$system_variables.get_msg_response_error();
                        //this.alert_variant = 'danger';
                        this.$system_variables.status_data_loaded = 1;
                    });
                   
                }
                
            });
            
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