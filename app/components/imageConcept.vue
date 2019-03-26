
<style scoped>

label{
    font-size:30px;
    text-align: center;
    padding: 20px;
    color:white;
    font-family: "Roboto", "Roboto";
 }
 .costy{
        font-family: "Roboto", "Roboto"; 
 }
.buttonWrapper{
    padding:20px;
        font-family: "Roboto", "Roboto";
}
.mainIm{
    padding:20px;
    background:grey;
}

</style>

<template>
    <Page class="page">
        <ActionBar class="action-bar">
            <Label class="action-bar-title" text="Board"></Label>
              <NavigationButton text="Go back" android.systemIcon="ic_menu_back" @tap="$navigateBack" />

        </ActionBar>

                <StackLayout backgroundColor="#3c495e">
                        <ActivityIndicator color="white" v-if="isLoading" :busy="isLoading" @busyChange="onBusyChanged" />
                        <button @tap="addPost" text="Add Post"/>
                    <StackLayout >
                        <Label :text="message" textWrap="true" />
                    </StackLayout>

 
                  


                    <ListView for="item in allImages" @itemTap="onItemTap">
                        <v-template>
                            <MDCardView rippleColor="green"  > 
                                <FlexboxLayout flexDirection="column" class="mainIm" justifyContent="center" alignItems="center" backgroundColor="#3c495e">
                                    <Image width="100%;" :src="item.imageData"  />
                                    <label textWrap="true" color="white" :text="item.genesisData[0].data[3].value"/>   
                                </FlexboxLayout>
                            </MDCardView>
                        </v-template>
                    </ListView>

                    
                  
                </StackLayout>
    </Page>
</template>

<script>
  import axios from "axios";
  var imagepicker = require("nativescript-imagepicker");

const imageSourceModule = require("tns-core-modules/image-source");
const fileSystemModule = require("tns-core-modules/file-system");


 var image_source_1 = require("tns-core-modules/image-source");

function chunkString(str, length) {
  return str.match(new RegExp('.{1,' + length + '}', 'g'));
}

import Vue from 'nativescript-vue';
import CardViewPlugin from 'nativescript-material-cardview/vue';
 
Vue.use(CardViewPlugin);

    import addPost from './comps/addPost.vue';
import addPostVue from './comps/addPost.vue';

    export default {
        data:function(){
            return {
                message:'OMG!, These posts are actually on the blockchain.',
                addresses:[],//all addresses where images are kept
                allImages:[],//all the images that will be parsed
                selectedImage:'',
                totalCost:'',
                totalTx:'',
                isSelected:false,//determines if an image is selected
                isLoading:true
            }
        },
        created(){
            this.getAddresses();
            console.log("poop")
        },
        methods: {
            addPost(){
                //add post
                      this.$navigateTo(addPostVue);
            },
            imageSelect(){
                var home=this;
                  let context = imagepicker.create({
                    mode: "single" // use "multiple" for multiple selection
                });
                //select image
                context
                .authorize()
                .then(function() {
                    return context.present();
                })
                .then(function(selection) {
                    image_source_1.fromAsset(selection[0])
                    .then(function (res) {
                    var base64img = res.toBase64String("jpeg");
                    console.log("beeb"+base64img);
                    home.selectedImage=base64img
                    });

                }).catch(function (e) {
                    // process error
                });
            },
            goBack(){},
            onButtonTap(){
                console.log("hi")
            },
            loadImageDataFromAddresss(){
                //loads a single image data
                this.addresses.forEach(item=>{
                    var addressWhereImage=item.data[0].value;
                    //this is where the image is stored
                
                    var getImageData=`https://testnodes.wavesnodes.com/transactions/address/${addressWhereImage}/limit/1000`;

                    var imageDataraw=[];
                    //all the image data will be stored here 

                    var imageData=[];

                    var imageDataString=[];
                    //stores just the value string

                    var imageString="";

                    var genesisData=null;
                    //the root genesis data will be stored here

                    axios.get(getImageData).then(d=>{
                        var dataA= d.data[0].filter(item=>{
                            if(item.type=="12"){
                                return item;
                            }
                        })
                      
                        genesisData=dataA.filter(item=>{
                            if(item.data.length>=5){
                                return item;
                            }
                        })

                        imageDataraw=dataA.filter(item=>{
                            if(item.data.length<=4){
                                return item;
                            }
                        })
                        imageDataraw.forEach(item=>{
                            imageData.push(item.data)
                        })
                        var sortedImage=imageData.sort((a,b)=>{
                            return a[3].value-b[3].value
                        })
                        sortedImage.forEach(item=>{
                            imageDataString.push(item[2].value)
                        })
                        imageString=imageDataString.join("");
                        this.allImages.push({
                            imageData:imageString,
                            genesisData:genesisData,
                            addressWhereImage:addressWhereImage
                        })
                        this.isLoading=false;
 
                    })
                    
                })
            },
            getAddresses(){
                console.log("loading addresses")
                //extracts all addresses(image addresses) from the oracle address
                var link="https://testnodes.wavesnodes.com/transactions/address/3Ms5MSwBwHHBaErKx1mgrrpwt92avds7gNA/limit/1000";
                axios.get(link).then((d)=>{
                    var res=d.data[0].filter(item=>{
                        if(item.type=="12"){
                            return item;
                        }
                    })
                    this.addresses=res; 
                    this.loadImageDataFromAddresss();
                    //parse the addresses and grab the image data
                })
            }
        },
        computed: {
            message() {
                return "Blank {N}-Vue app";
            }
        },
        components:{
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
