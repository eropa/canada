<template>
    <div>
        <span data-toggle="modal" data-target="#formLoginModal">
             Login
        </span>

        <!-- Modal -->
        <div class="modal fade"
             id="formLoginModal"
             tabindex="-1"
             role="dialog"
             aria-labelledby="formLoginModalLabel"
             aria-hidden="true">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="formLoginModalLabel">Login</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <div class="row">
                            <div class="col-md-12">
                                <div class="alert alert-danger"
                                     v-if="showErrors"
                                     role="alert">
                                   <span>{{textErrors}}</span>
                                </div>
                                <div class="alert alert-success"
                                     v-if="showLogin"
                                     role="alert">
                                     Welcome. The page will reload 2 sec.
                                </div>
                                <label for="idLogin">Email</label>
                            </div>
                            <div class="col-md-12">
                                <input type="email"
                                       class="form-control"
                                       id="idLogin"
                                       v-model="loginUser"
                                       placeholder="Enter email">
                            </div>
                            <div class="col-md-12">
                                <label for="idPassword">Password</label>
                            </div>
                            <div class="col-md-12">
                                <input type="password"
                                       class="form-control"
                                       id="idPassword"
                                       v-model="passUser"
                                       placeholder="Enter email">
                            </div>
                            <div class="col-md-12">
                                <button type="button"
                                        v-if="showSpinner==0"
                                        v-on:click="btnLoginClick()"
                                        class="btn btn-success enterlogin">Success</button>

                                <div class="spinner-border text-success z"
                                     v-if="showSpinner"
                                     role="status">
                                    <span class="sr-only">Loading...</span>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "LoginComponent",
        props: ['message'],
        data() {
            return {
                showSpinner:0,
                loginUser:"",
                passUser:"",
                showErrors:0,
                showLogin:0,
                textErrors:"",
            }
        },
        methods: {
            btnLoginClick:function(){
                this.showErrors=0;
                this.showLogin=0;
                this.textErrors="";
                this.showSpinner=1;
                let isEmail=validateEmail(this.loginUser);
                if(!isEmail){
                    this.showErrors=1;
                    this.textErrors="Errors validate email";
                    this.showSpinner=0;
                    return 1;
                }
                if(this.passUser==""){
                    this.showErrors=1;
                    this.textErrors="Not enter password";
                    this.showSpinner=0;
                    return  1;
                }
                axios.post('/login', {
                    'email':this.loginUser,
                    'password':this.passUser,
                }).then ((response)=>{
                    if(response.data=="login"){
                        this.showLogin=1;
                        this.showErrors=0;
                        location.reload();
                        return 1;
                    }
                    if(response.data=="error"){
                        this.showLogin=0;
                        this.showErrors=1;
                        this.textErrors="Not login";
                        this.showSpinner=0;
                        return 1;
                    }
                    this.showSpinner=0;
                }).catch(error => {
                    this.showSpinner=0;
                    this.showErrors=1;
                    this.textErrors=error;
                    return 1;
                });
            },


        }
    }
    //

    function validateEmail(email) {
        const re = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
        return re.test(String(email).toLowerCase());
    }


</script>

<style scoped>

</style>
