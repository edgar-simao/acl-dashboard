<template>
    <div v-if="selected !== null" class="details_list">
        <h1 v-if="details && details.length > 0" class="title">Shared with</h1>
        <h1 v-else class="title">Not being shared</h1>
        <div v-if="details !== null" class="listing">
            <v-list
             :style="(mode === 'dark') ? 'background: #37474F;' : 'background: #ffffff;'"
             rounded
             three-line
             avatar>
                <v-list-item
                 v-for="(detail, index) in details"
                 :key="index"
                 :dark="(mode === 'dark')">
                    <v-list-item-avatar class="avatar">
                        <v-icon x-large>{{ user_photo }}</v-icon>
                    </v-list-item-avatar>

                    <v-list-item-content class="name">
                        <v-list-item-title>{{ detail.user }}</v-list-item-title>
                    </v-list-item-content>

                    <v-list-item-content>
                        <v-list-item-subtitle>Scopes:</v-list-item-subtitle>
                        <p
                         v-for="(scope, i) in detail.scopes"
                         :key="i"
                        >
                            {{ scope }}
                        </p>
                    </v-list-item-content>

                    <v-list-item-content class="del" v-on:click="revoke(index)">
                        <v-icon large class="delico">delete</v-icon>
                    </v-list-item-content>
                </v-list-item>
            </v-list>
        </div>
    </div>
</template>

<script>
const axios = require('axios')

export default {
    name: "ResourceDetails",
    props: {
        resources: Array,
        selected: Number,
        details: Array
    },
    data () {
        return {
            user_photo: 'person',
            color: "#fafafa"
        }
    },
    methods: {
        revoke(index) {
            var owner_id = this.resources[this.selected].owner.id

            var requester = this.details[index].user

            var resource_id = this.resources[this.selected]._id

            let url = process.env.VUE_APP_BACKEND_URL +
                        'revoke'

            let data = 'owner_id=' + owner_id +
                        '&requester=' + requester +
                        '&resource_id=' + resource_id

            axios.post(url, data)
            .then(() => {
                this.$emit('refresh')
            })
            .catch(() => {
                alert('Error revoking access')
            })
        }
    },
    computed: {
        mode: function() {
            return this.$store.state.mode
        }
    }
}
</script>

<style scoped>
    .details_list {
        padding: 5%;
    }

    .title {
        text-align: center;
        font-size: 300%;
    }

    .del {
        margin: 0 !important;
        position: absolute;
        right: 5%;
        top: 50%;
        -ms-transform: translateY(-50%);
        transform: translateY(-50%);
    }

    .avatar {
        margin: 0 !important;
        position: absolute;
        left: 5%;
        top: 50%;
        -ms-transform: translateY(-50%);
        transform: translateY(-50%);
    }

    .name {
        margin-left: 15%;
    }

    .delico {
        color: #800000;
    }
</style>