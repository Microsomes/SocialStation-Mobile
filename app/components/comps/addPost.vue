
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
                          <NavigationButton text="Go back" android.systemIcon="ic_menu_back" @tap="$navigateBack" />

        </ActionBar>

                 <StackLayout style="margin-top:20px;" backgroundColor="grey" >
                        <label class="costy" text="ADD POST"/>

                            

                                <Image :src="selectedImage"/>

                        <label style="font-size:20px;" class="costy" text="This action costs waves"/>
                        <StackLayout style="padding:20px;">
                        <TextField :text="textFieldValue" hint="image caption" />
                        <TextField :text="textFieldValue" hint="your name" />
                        <TextField :text="textFieldValue" hint="url or link" />
                        <Button text="Select Image" @tap="imageSelect" />

 
                        </StackLayout>
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
            }
        },
        created(){
            this.getAddresses();
            console.log("poop")
        },
        methods: {
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

 