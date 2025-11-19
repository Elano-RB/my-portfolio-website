<script setup>
    
    import { ref, onMounted, onBeforeUnmount } from 'vue';
    import { Notyf } from "notyf";
    import 'notyf/notyf.min.css';

    const notyf = new Notyf();

    const WEB3FORMS_ACCESS_KEY = "03e9cd97-d625-4cd1-9508-560c1b30f2b8"

    const subject = "New message from Portfolio Contact Form";

    const name = ref("");
    const email = ref("");
    const message = ref("");

    const isLoading = ref(false);

    const submitForm = async () => {

        if(!recaptchaToken.value) {
            notyf.error('Please verify that you are not a robot');

            return;
        }

        isLoading.value = true;

        try {

            const response = await fetch("https://api.web3forms.com/submit", {

                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                    Accept: "application/json"
                },
                body: JSON.stringify({
                    access_key: WEB3FORMS_ACCESS_KEY,
                    subject: subject,
                    name: name.value,
                    email: email.value,
                    message: message.value
                })
            });

            const result = await response.json();

            if(result.success) {
                console.log(result);

                isLoading.value = false;
                notyf.success("Message Sent!");
            }
        } catch (error) {

            console.log(error)

            isLoading.value = false;
            notyf.error("Failed to send message");
        } finally {

            resetRecaptcha();

        }
    }

    const SITE_KEY = '6LdPxREsAAAAAMCLcBfNXJ5VAvxVDljs_Mp_zb6l'

    const recaptchaContainer = ref(null);
    const recaptchaWidgetID = ref(null);
    const recaptchaToken = ref('');

    function onRecaptchaSuccess(token) {
        recaptchaToken.value = token
    }

    function onRecaptchaExpired() {
        recaptchaToken = '';
    }

    function renderRecaptcha() {
        if(!window.grecaptcha) {
            console.error('reCAPTCHA not loaded');
            return;
        }

        recaptchaWidgetID.value = window.grecaptcha.render(
            recaptchaContainer.value, {
            sitekey: SITE_KEY,
            size: 'normal',
            callback: onRecaptchaSuccess,
            'expired-callback': onRecaptchaExpired,
        })
    }

    function resetRecaptcha() {
        if(recaptchaWidgetID.value !== null) {
            window.grecaptcha.reset(recaptchaWidgetID.value);
            recaptchaToken.value = ''
        }
    }


    onMounted(() => {

        const interval = setInterval(() => {
            if(window.grecaptcha && window.grecaptcha.render) {
                renderRecaptcha();
                clearInterval(interval);
            }
        }, 100)

        onBeforeUnmount(() => {
            clearInterval(interval);
        })
    })



</script>

<template>
<!-- contact -->
<h1 class="text-center my-4 pt-5" id="contact">Contacts</h1>
<div class="contact-section">
    <div class="row align-items-center mt-4">
        <div class="col-md-6 map-container">
            <iframe id="gmap_canvas" src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d61908.95407180591!2d120.9266308777264!3d14.117879786214003!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x33bd777b1ab54c8f%3A0x6ecc514451ce2be8!2sTagaytay%20City%2C%20Cavite!5e0!3m2!1sen!2sph!4v1755864997443!5m2!1sen!2sph" frameborder="0" scrolling="no" marginheight="0" marginwidth="0"></iframe>
        </div>
        <div class="col-md-6">
            <form @submit.prevent="submitForm">
                <div class="mb-3">
                    <input type="text" v-model="name" class="form-control contact-form-control" placeholder="First Name M.I. Last Name">
                </div>
                <div class="mb-3">
                    <input type="email" v-model="email" class="form-control contact-form-control" placeholder="Email">
                </div>
                <div class="mb-3">
                    <textarea v-model="message" class="form-control contact-form-control" rows="6" placeholder="Message"></textarea>
                </div>
                <div class="form-footer">
                    <div class="social-icons">
                        <a href="https://www.linkedin.com/" target="_blank">
                                    <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRokEYt0yyh6uNDKL8uksVLlhZ35laKNQgZ9g&s" class="img-fluid rounded m-2" width="30" height="30" id="linkedin"></a>
                        <a href="https://about.gitlab.com/" target="_blank">
                                    <img src="https://www.cloudservices.store/site/wp-content/uploads/2020/10/logo-extra-whitespace.png" class="img-fluid rounded m-2" width="30" height="30" id="gitlab"></a>
                        <a href="https://github.com/" target="_blank">
                                    <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRZfLuviePOqQJ1yuhvxr80AsqV91Btr2rqZw&s" class="img-fluid rounded m-2" width="30" height="30" id="github"></a>
                    </div>
                    <button type="submit" class="submit-btn pl-5 pr-5" :disabled="isLoading">
                        {{isLoading ? "Sending..." : "Submit"}}
                    </button>
                    <!-- Recaptcha checkbox -->
                    <div class="d-flex justify-content-end mt-2">
                        <div ref="recaptchaContainer"></div>
                    </div>
                </div>
            </form>
            
        </div>
    </div>
</div>
</template>