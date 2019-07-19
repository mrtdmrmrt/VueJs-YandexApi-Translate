<template>
<!--Formun yenilenmesini önklemek için .prevent kullandık-->
	<form @submit.prevent="translateIt" class="well">
        <textarea v-model="translateText" cols="30" rows="5" class="form-control" placeholder="Çevirmek istediğiniz kelime/cümle yazınız."></textarea>
        <select v-model="translateTo" class="form-control">
            <option v-for="(value,key) in languages" :value="key">{{value}}</option>
        </select>
        <br>
        <div class="text-left" v-if="detectedLang">
            <strong>Tespit Edilen Dil : </strong> {{detectedLang}}
        </div>
        <br>
        <button type="submit" class="btn btn-primary btn-block">Çevir Gelsin!</button>
    </form>
   
</template>
<script>
    import axios from "axios"
    export default{
       data(){
        return {
            translateText : '',
            translateTo : '',
            languages : {},
            detectedLang : ''
        }
       },
       methods : {
        translateIt(){
            let url = "https://translate.yandex.net/api/v1.5/tr.json/translate?key=trnsl.1.1.20190719T081549Z.e9980bd8b3a2cc8a.10e1a10173ca02f78cc047e8bc21154378d11990&text=" + this.translateText + 
            "&lang="+ this.translateTo 
            axios.get(url)
            .then(response =>{
                this.detectedLang = this.languages[this.translateTo]
                console.log(response)
                this.$emit("translatedEvent",response.data.text[0])
                let history ={
                    from :this.languages [response.data.lang.split("-")[0]],
                    to : this.detectedLang,
                    translateText : this.translateText,
                    translatedText : response.data.text[0]
                }
                this.$emit("historyEvent",history)
                this.translateTo='';
                this.translateText='';
                this.detectedLang='';
            })
            //hata olursa diye catch
            .catch(e => console.log(e))
        }
       },
       created(){
        //dillerin türkçşe gelmnesi için url=tr dedik
        axios.get("https://translate.yandex.net/api/v1.5/tr.json/getLangs?key=trnsl.1.1.20190719T081549Z.e9980bd8b3a2cc8a.10e1a10173ca02f78cc047e8bc21154378d11990&ui=tr")
        .then(response =>{
            this.languages = response.data.langs
        })
        .catch(e=> console.log(e))
       }
    }
</script>
<style></style>