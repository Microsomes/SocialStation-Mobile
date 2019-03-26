
<style scoped>

label{
    font-size:30px;
    text-align: center;
    padding: 20px;
    color:white;
    font-family: "Roboto", "Roboto";
 }
.buttonWrapper{
    padding:20px;
        font-family: "Roboto", "Roboto";
}
</style>

<template>
    <Page class="page">
        <ActionBar class="action-bar">
            <Label class="action-bar-title" text="Home"></Label>
        </ActionBar>

                <StackLayout backgroundColor="#3c495e">

                    <StackLayout height="300">
                        <Label :text="message" textWrap="true" />
                    </StackLayout>
                    <StackLayout class="buttonWrapper">
                    <Button  text="GO TO CONCEPT" @tap="onButtonTap" />
                    </StackLayout>

     
                </StackLayout>
    </Page>
</template>



<script>
    import imageConcept from './imageConcept.vue'

    export default {
        data:function(){
            return {
                message:'SocialStation aims to be the first decentralised social media platform. We have created a small proof of concept for our users. Enjoy. Visit Socialstation.io for more info on the project.'
            }
        },
        methods: {
            onButtonTap(){
                console.log("hi")
                  this.$navigateTo(imageConcept);
            }
        },
        computed: {
            message() {
                return "Blank {N}-Vue app";
            }
        }
    };
</script>

<style scoped lang="scss">
    // Start custom common variables
    @import '../app-variables';
    // End custom common variables

    // Custom styles
    .fa {
        color: $accent-dark;
    }

    .info {
        font-size: 20;
    }
</style>
