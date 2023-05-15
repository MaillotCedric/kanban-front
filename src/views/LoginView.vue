<template>
    <div class="login">
        <v-form @submit.prevent="login_">
            <v-text-field variant="underlined" clearable label="username" v-model="username"></v-text-field>
            <v-text-field variant="underlined" clearable label="password" type="password" v-model="password"></v-text-field>
            <v-btn variant="outlined" type="submit" :loading="loading">Login</v-btn>
        </v-form>
        <Transition>
            <v-alert
                v-if="show_alert"
                :type="login_status"
                class="mt-6">
                {{ message }}
            </v-alert>
        </Transition>
        <!-- <div id="jk-container"></div> -->
    </div>
</template>

<script lang="ts">
    import * as tb from "../scripts/toolbox"
    import axios from "axios";
    
    export default {
        data() {
            return {
                boards: [
                    {
                        "id"    : "board-id-1",               // id of the board
                        "title" : "To Do",              // title of the board
                        "class" : "class1,class2,...",        // css classes to add at the title
                        // "dragTo": ['another-board-id',...],   // array of ids of boards where items can be dropped (default: [])
                        "item"  : [                           // item of this board
                            {
                                "id"    : "item-id-1",        // id of the item
                                "title" : "Subscribe to O2Switch"            // title of the item
                                // "class" : ["myClass",...]     // array of additional classes
                            },
                            {
                                "id"    : "item-id-2",
                                "title" : "Admin task"
                            }
                        ]
                    },
                    {
                        "id"    : "board-id-2",
                        "title" : "W.I.P."
                    },
                    // {
                    //     "id"    : "board-id-3",
                    //     "title" : "Done"
                    // }
                ],
                loading: false,
                login_status: undefined,
                message: "",
                password: "",
                show_alert: false,
                username: ""
            }
        },
        methods: {
            init_kanban() {
            // @ts-ignore
                new jKanban({
                    element          : '#jk-container',                                           // selector of the kanban container
                    gutter           : '15px',                                       // gutter of the board
                    widthBoard       : '250px',                                      // width of the board
                    responsivePercentage: false,                                    // if it is true I use percentage in the width of the boards and it is not necessary gutter and widthBoard
                    dragItems        : true,                                         // if false, all items are not draggable
                    boards           : this.boards,                                           // json of boards
                    dragBoards       : true,                                         // the boards are draggable, if false only item can be dragged
                    itemAddOptions: {
                        enabled: false,                                              // add a button to board for easy item creation
                        content: '+',                                                // text or html content of the board button   
                        class: 'kanban-title-button btn btn-default btn-xs',         // default class of the button
                        footer: false                                                // position the button on footer
                    },    
                    itemHandleOptions: {
                        enabled             : false,                                 // if board item handle is enabled or not
                        handleClass         : "item_handle",                         // css class for your custom item handle
                        customCssHandler    : "drag_handler",                        // when customHandler is undefined, jKanban will use this property to set main handler class
                        customCssIconHandler: "drag_handler_icon",                   // when customHandler is undefined, jKanban will use this property to set main icon handler class. If you want, you can use font icon libraries here
                        customHandler       : "<span class='item_handle'>+</span> %title% "  // your entirely customized handler. Use %title% to position item title 
                                                                                            // any key's value included in item collection can be replaced with %key%
                    },
                    click            : function (el) {},                             // callback when any board's item are clicked
                    context          : function (el, event) {},                      // callback when any board's item are right clicked
                    dragEl           : function (el, source) {},                     // callback when any board's item are dragged
                    dragendEl        : function (el) {},                             // callback when any board's item stop drag
                    dropEl           : function (el, target, source, sibling) {},    // callback when any board's item drop in a board
                    dragBoard        : function (el, source) {},                     // callback when any board stop drag
                    dragendBoard     : function (el) {},                             // callback when any board stop drag
                    buttonClick      : function(el, boardId) {},                     // callback when the board's button is clicked
                    propagationHandlers: [],                                         // the specified callback does not cancel the browser event. possible values: "click", "context"
                })
            },
            login_() {
                tb.print_(this.username);
                // tb.print_(this.password);

                const FORM_DATA = {
                    username: this.username,
                    password: this.password
                }

                this.loading = true

                axios
                    .post("/api/token/", FORM_DATA)
                    .then((response) => {
                        this.loading = false
                        this.display_alert()
                        this.login_status = "success"
                        this.message = "Logged in"
                        tb.print_(response.data)
                    })
                    .catch((error) => {
                        this.loading = false
                        this.display_alert()
                        this.login_status = "error"
                        this.message = error.response.data.detail
                        tb.print_(error)
                    })
            },
            display_alert() {
                this.show_alert === false ? this.show_alert = true
                                                    : "" // do nothing
                setTimeout(() => (this.show_alert = false), 2000) // hide alert after 2 seconds
            }
        },
        mounted() {
            // this.init_kanban()
        }
    }
</script>
  
<style>
    @media (min-width: 1024px) {
        .about {
            min-height: 100vh;
            display: flex;
            align-items: center;
        }
    }
    .v-enter-active,
    .v-leave-active {
    transition: opacity 0.5s ease;
    }

    .v-enter-from,
    .v-leave-to {
    opacity: 0;
    }
</style>
