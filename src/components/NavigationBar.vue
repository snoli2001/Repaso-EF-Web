<template>
    <div>
        <v-navigation-drawer
                v-model="drawer"
                app
        >
            <v-list dense>
                <v-list-item v-for="item in items" :key="item.title" link :to="item.route">
                    <v-list-item-action>
                        <v-icon>{{item.icon}}</v-icon>
                    </v-list-item-action>
                    <v-list-item-content>
                        <v-list-item-title>{{item.title}}</v-list-item-title>
                    </v-list-item-content>
                </v-list-item>
            </v-list>
        </v-navigation-drawer>

        <v-app-bar
                app
                color="blue"
                dark
        >
            <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
            <v-toolbar-title>Mali.pe</v-toolbar-title>
            <v-spacer></v-spacer>
        </v-app-bar>
    </div>
</template>
<script>
    export default {
        name: "NavigationBar",


        data: () => ({
            drawer: false,
            items: [
                {icon: 'mdi-home', title: 'Home', route: '/home'},
                {icon: 'mdi-account-group', title: 'Curators', route: '/curators'}
            ],
        }),
    }
</script>

<style scoped>

</style>
