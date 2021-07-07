<template>
    <div  style="height: 100vh" class="d-flex justify-center align-center">
        <v-data-table
                :headers="headers"
                :items="curators"
                class="elevation-1 pa-5"
                :search="search"
                :custom-filter="filterByName"
                height="600"
                :sort-by="['firstName', 'fat']"
                :sort-desc="[false, true]"
                multi-sort
        >
            <template v-slot:top>
                <v-toolbar
                        flat
                >
                    <v-toolbar-title>Curators</v-toolbar-title>
                        <v-text-field
                                v-model="search"
                                label="Search"
                                class="ml-15 mt-4"
                        ></v-text-field>
                    <v-spacer></v-spacer>
                    <v-dialog
                            v-model="dialog"
                            max-width="500px"
                    >
                        <template v-slot:activator="{ on, attrs }">

                            <v-btn
                                    color="blue"
                                    dark
                                    class="mb-2"
                                    v-bind="attrs"
                                    v-on="on"
                            >
                                New Curator
                            </v-btn>
                        </template>
                        <v-card>
                            <v-card-title>
                                <span class="text-h5">{{ formTitle }}</span>
                            </v-card-title>

                            <v-card-text>
                                <v-container>
                                    <v-row>
                                        <v-col
                                                cols="12"
                                                sm="6"
                                                md="4"
                                        >
                                            <v-text-field
                                                    v-model="editedItem.firstName"
                                                    label="First Name"
                                            ></v-text-field>
                                        </v-col>
                                        <v-col
                                                cols="12"
                                                sm="6"
                                                md="4"
                                        >
                                            <v-text-field
                                                    v-model="editedItem.lastName"
                                                    label="Last Name"
                                            ></v-text-field>
                                        </v-col>
                                        <v-col
                                                cols="12"
                                                sm="6"
                                                md="4"
                                        >
                                            <v-text-field
                                                    v-model="editedItem.discipline"
                                                    label="Discipline"
                                            ></v-text-field>
                                        </v-col>
                                    </v-row>
                                </v-container>
                            </v-card-text>

                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn
                                        color="blue darken-1"
                                        text
                                        @click="close"
                                >
                                    Cancel
                                </v-btn>
                                <v-btn
                                        color="blue darken-1"
                                        text
                                        @click="save"
                                >
                                    Save
                                </v-btn>
                            </v-card-actions>
                        </v-card>
                    </v-dialog>
                    <v-dialog v-model="dialogDelete" max-width="500px">
                        <v-card>
                            <v-card-title class="text-h5">Are you sure you want to delete this item?</v-card-title>
                            <v-card-actions>
                                <v-spacer></v-spacer>
                                <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
                                <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
                                <v-spacer></v-spacer>
                            </v-card-actions>
                        </v-card>
                    </v-dialog>
                </v-toolbar>
            </template>
            <template v-slot:item.actions="{ item }">
                <v-icon
                        small
                        class="mr-2"
                        @click="editItem(item)"
                >
                    mdi-pencil
                </v-icon>
                <v-icon
                        small
                        @click="deleteItem(item)"
                >
                    mdi-delete
                </v-icon>
            </template>
            <template v-slot:no-data>
                <v-btn
                        color="primary"
                        @click="initialize"
                >
                    Reset
                </v-btn>
            </template>
        </v-data-table>
    </div>

</template>
<script>
    import axios from 'axios'
    export default {
        name: "Curators",

        data: () => ({
            search: '',
            dialog: false,
            dialogDelete: false,
            headers: [
                {
                    align: 'start',
                    sortable: false,
                    value: 'name',
                },
                {text: 'First Name', value: 'firstName'},
                {text: 'Last Name', value: 'lastName'},
                {text: 'Discipline', value: 'discipline'},
                { text: 'Actions', value: 'actions', sortable: false },
            ],
            curators: [],
            editedIndex: -1,
            editedItem: {
                firstName: '',
                lastName: '',
                discipline: '',
            },
            defaultItem: {
                firstName: '',
                lastName: '',
                discipline: '',
            },
        }),
        computed: {
            formTitle() {
                return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
            },
        },

        watch: {
            dialog(val) {
                val || this.close()
            },
            dialogDelete(val) {
                val || this.closeDelete()
            },
        },

        created() {
            this.initialize()
        },

        methods: {
            initialize() {
                axios.get('curators')
                    .then(resp => {
                        this.curators = resp.data;
                    })
            },

            editItem(item) {
                this.editedIndex = this.curators.indexOf(item)
                this.editedItem = Object.assign({}, item)
                this.dialog = true
            },

            deleteItem(item) {
                this.editedIndex = this.curators.indexOf(item)
                this.editedItem = Object.assign({}, item)
                this.dialogDelete = true
            },

            deleteItemConfirm() {
                axios.delete(`curators/${this.editedItem.id}`)
                    .then(() => {
                        axios.get('curators')
                            .then(resp => {
                                this.curators = resp.data;
                                this.closeDelete()
                            })
                    })
            },

            close() {
                this.dialog = false
                this.$nextTick(() => {
                    this.editedItem = Object.assign({}, this.defaultItem)
                    this.editedIndex = -1
                })
            },

            closeDelete() {
                this.dialogDelete = false
                this.$nextTick(() => {
                    this.editedItem = Object.assign({}, this.defaultItem)
                    this.editedIndex = -1
                })
            },

            save() {
                if (this.editedIndex > -1) {
                    axios.put(`curators/${this.editedItem.id}`, this.editedItem).then(() => {
                        axios.get('curators')
                            .then(resp => {
                                this.curators = resp.data;
                                this.close()
                            })
                    })
                } else {
                    axios.post('curators', this.editedItem)
                        .then(() => {
                            axios.get('curators')
                                .then(resp => {
                                    this.curators = resp.data;
                                    this.close()
                                })
                        })
                }
                this.close()
            },

            filterByName(value, search){
                return value != null && true && typeof value == 'string' &&
                        value.toString().toLocaleLowerCase().indexOf(search) !== -1
            }
        },
    }

</script>

<style scoped>

</style>
