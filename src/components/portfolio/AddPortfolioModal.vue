<!--
        Copyright 2018 IBM Corp All Rights Reserved

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
 -->
<template>
    <div class="modal" :data-modal="hcName" role="dialog" :aria-labelledby="`modal-title-${hcId}`"
         :aria-describedby="`modal-desc-${hcId}`">
        <div class="overlay js-close-modal"></div>
        <div class="modal-card">
            <hc-button hc-style="flat" class="modal-card__close js-close-modal">
                <hc-icon href="hc(close-modal)" class="icon--close-modal"></hc-icon>
            </hc-button>
            <h4 :id="`modal-title-${hcId}`" class="h2 modal-card__heading">{{ hcTitle }}</h4>
            <p :id="`modal-desc-${hcId}`" class="modal-card__content">
                <hc-text v-model="owner" name="name" label="Name"></hc-text>
            </p>
            <hc-button hcStyle="primary" class="js-modal-focus js-close-modal" v-on:click="performAction">Ok</hc-button>
            <hc-button class="js-close-modal" hcStyle="flat">Cancel</hc-button>
        </div>
    </div>
</template>

<script>
    import hcIds from '@hybrid-cloud/cirrus-vue/src/mixins/hc-ids'
    import hcButton from '@hybrid-cloud/cirrus-vue/src/components/hc-button/hc-button'
    // MSG Bus to send new portfolio data back to the table.
    import bus from '../bus'
    // REST project
    import axios from 'axios'

    export default {
        name: 'add-portfolio-modal',
        mixins: [hcIds],
        components: {
            hcButton
        },
        props: {
            hcName: {
                type: String,
                required: true
            },
            hcTitle: {
                type: String,
                required: true
            }
        },
        methods: {
            performAction() {
                var url
                url = '/portfolio/' + this.owner
                console.log('Here is the new owner: ' + this.owner)
                var formatter = new Intl.NumberFormat('en-US', {
                    style: 'currency',
                    currency: 'USD',
                    minimumFractionDigits: 2
                    // the default value for minimumFractionDigits depends on the currency
                    // and is usually already 2
                })
                axios.post(url, '', {headers: {'Authorization': 'Bearer ' + this.$jwt.getToken()}})
                    .then(response => {
                        console.log('Here is the reponse')
                        console.log(response)
                        console.log(response.data)
                        response.data.total = formatter.format(response.data.total)
                        bus.$emit('addPerson', response.data)
                    })
                    .catch(e => {
                        console.log(e)
                    })
            }
        },
        data() {
            return {
                owner: '',
                updatedPortfolio: []
            }
        }
    }
</script>

<style lang="scss">
</style>
