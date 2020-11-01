<template>
  <a-row >
      <a-col :lg="{ span: 11, offset: 7 }" :sm="{ span: 13, offset: 5 }"> 
        <a-alert v-if="alert_message != ''"            
            :type="alert_type"
            closable
            >
            <p slot="description" v-html="alert_message">
            </p>
        </a-alert>             
        <a-form :form="form" id="form_login" @submit.prevent="login($event)" v-if="!otp_required">            
            
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
                    {{this.$system_functions.get_label('button_signin')}}
                </a-button>
            </a-form-item>
        </a-form>
        <a-form :form="form" id="form_otp" @submit.prevent="login_sms($event)" v-if="otp_required">            
            <input type="hidden" name="token_sms" :value="token_sms"/>
            <a-form-item has-feedback>
                <a-input
                    name="otp"
                    :placeholder="this.$system_functions.get_label_task('label_otp_form_placeholder_otp')"
                    v-decorator="[
                    'otp',
                    {
                        rules: [
                        {
                            required: true,
                            message: 'Otp is required!',
                        },
                        ],
                    },
                    ]"
                />
            </a-form-item>
            <a-form-item>
                <a-button type="primary" html-type="submit">
                    {{this.$system_functions.get_label('button_send')}}
                </a-button>
                <a-button type="primary" :style="{float:'right'}" @click="otp_required = false; alert_message = ''">
                    {{this.$system_functions.get_label('button_cancel')}}
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
            otp_required: false,
            token_sms: '',

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
                    form_data.append ('language', this.$system_variables.language);
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
                        else if(response.data.error_type == 'OTP_VERIFICATION_REQUIRED')
                        {
                            this.token_sms = response.data.token_sms;
                            this.alert_message = this.$system_functions.get_label('OTP_VERIFICATION_REQUIRED');
                            this.alert_type = 'success';
                            this.otp_required = true;
                        }
                        else if(response.data.error_type == 'OTP_WAIT')
                        {
                            this.alert_message = this.$system_functions.get_label('OTP_WAIT');
                            this.alert_type = 'success';
                            this.otp_required = true;
                        }
                        else
                        {
                            this.alert_message = response.data.error_type;
                            this.alert_type = 'error';
                        }
                        this.$system_variables.status_data_loaded = 1;
                    })
                    .catch(error => {
                        console.log(error);
                        this.alert_message = this.$system_functions.get_msg_response_error();
                        this.alert_type = 'error';
                        this.$system_variables.status_data_loaded = 1;
                    });
                   
                }
                
            });
            
        },
        login_sms: function(event)
        {
            this.form.validateFields((err, values) => 
            {
                if (!err) 
                {
                    this.alert_message = '';
                    this.$system_variables.status_data_loaded = 0;

                    var form_data=new FormData(document.getElementById('form_otp'));
                    form_data.append ('token_device', this.$system_variables.user.token_device);
                    form_data.append ('language', this.$system_variables.language);

                    this.$axios.post('/Login/login_sms',form_data)
                    .then(response=>{                
                        if(response.data.error_type == '')
                        {
                            localStorage.setItem('token_auth', response.data.user.token_auth);
                            localStorage.setItem('token_csrf', response.data.user.token_csrf);
                            localStorage.setItem('token_device', response.data.user.token_device);
                            this.$system_functions.set_user(response.data.user);
                            this.$router.push("/");
                        }
                        else if(response.data.error_type == 'OTP_INCORRECT')
                        {
                            this.alert_message = this.$system_functions.get_label('OTP_INCORRECT');
                            this.alert_type = 'error';
                        }
                        else if(response.data.error_type == 'OTP_ALREADY_USED')
                        {
                            this.alert_message = this.$system_functions.get_label('OTP_ALREADY_USED');
                            this.alert_type = 'error';
                        }
                        else if(response.data.error_type == 'OTP_EXPIRED')
                        {
                            this.alert_message = this.$system_functions.get_label('OTP_EXPIRED');
                            this.alert_type = 'error';
                        }
                        else if(response.data.error_type == 'OTP_NOT_LAST')
                        {
                            this.alert_message = this.$system_functions.get_label('OTP_NOT_LAST');
                            this.alert_type = 'error';
                        }
                        else
                        {
                            this.alert_message = response.data.error_type;
                            this.alert_type = 'error';
                        }
                        this.$system_variables.status_data_loaded = 1;
                    })
                    .catch(error => {
                        console.log(error);
                        this.alert_message = this.$system_functions.get_msg_response_error();
                        this.alert_type = 'error';
                        this.$system_variables.status_data_loaded = 1;
                    });
                }
            });
        },
    }
}
</script>