---
title: vue.js干活的基本格式
date: 2018-09-26 17:43:08
tags:
---

这一年从年初实习到现在算是把 vue.js 这个东西用的挺多的，这里放上自己写 vue.js 的一些套路。

提示：由于 vue.js 全家桶的配置每个项目每个公司都会有所不同，所以这里不对 vuex 这种东西做讲解（就个人经验来说，只接触过路由的配置，没啥深入体验）。

## 结构讲解

以下的代码使我在这个学期 ANU 项目课实现的一个课拖拽的表格的代码。由于我们在vue.js中采用组件化开发的思想，所以我们写的每一个.vue文件实际上都是页面显示一部分，例如我在下面写的就是一个会弹出桌面供用户进行拖拽操作的按钮。这个按钮会镶嵌到另一个页面，或另一个组件中。不得不说，这一思路确实很好地做到了软件工程所宣扬的高内聚，低耦合。只要功能分配合理，对整个页面就是组件之间进行模块化的拼接与安装。

1. 页面样式
    在具体的实现层面上，整个文件可以分成两大部分，一个是组件的外在样子，也就是用html以及vue.js提供的一些语法糖进行页面的最前端的开发；另一个就是页面的前端运算单元，具体的功能包括通过ajax与后端进行数据的交互，应付一些前端用户操作，控制一些反馈（比如控制加载动画，在用户输入异常信息提示错误等待）。

    首先先说template模块。在template这一标签下大部分的页面结构实现与一般的html无异（至少我所经历的是差不多的）。其中可以称作是vue.js所特有的部分，是诸如v-for，v-if，v-bind，v-onclick这种用来动态显示数据以及数据绑定的特殊标签。

    ```html
        <template>
            <div>
                <a class="button btn btn-primary text-white" data-toggle="modal" data-target="#assignModal">
                    <i class="fas fa-th"></i>
                    Easy sort
                </a>
                <div class="modal fade bd-example-modal-lg" id="assignModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
                    <div class="modal-dialog modal-lg" role="document">
                        <div class="modal-content">
                            <div class="modal-header">
                                <h5 class="modal-title" id="exampleModalLabel">Add New Team</h5>
                                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                    <span aria-hidden="true">&times;</span>
                                </button>
                            </div>
                            <div class="modal-body">
                                <div v-for="(element, index) in arrTeamPlayers" :key="index" class="list-group col-md-2 float-left min-height">
                                    <h5 class="text-center">{{element['team_name']}}</h5>
                                    <draggable v-model="element['players']" :options="{group:'people'}" class="min-height">
                                        <div v-for="(element, index) in element['players']" :key="index" class="list-group-item text-center" v-bind:class="{'list-group-item-danger': !element['is_approved']}">
                                            {{element['user_name']}}
                                        </div>
                                    </draggable>
                                </div>
                            </div>
                            <div class="modal-footer">
                                <button type="submit" data-dismiss="modal" aria-label="Close" class="btn btn-primary form-control" @click="submit">Submit the assignment</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </template>
    ```

    值得一提的是，在组件内部自己定义CSS样式的时候，这些样式只会作用于该组件，不会外溢。所以如果外部整体的CSS觉得功能不齐全，尽可以放心大胆在自己要用的组件定义需要的CSS。

    ```CSS
    <style>
    .min-height {
        min-height: 100px;
    }
    </style>
    ```

2. 数据运算
    麻雀虽小，五脏俱全。下面的代码，虽然干的事情并不多，但结构是完整的。下面来一个个介绍相应的组件及其功能。
    - components
        这里存放调用的其他组件，任何其他组件在调用的时候，都需要先引入，再在此进行注册，然后方能使用。

    - props
        这里负责存放与外部进行交互的数据，该组件的父组件可通过:name_props = “value”这一形式将参数传入该组件，实现数据交互。

    - data()
        data是函数，data是函数，data是函数，重要事情说三遍。它负责存放用于组件内部的数据。无论是调用props还是data，由于它们都绑定到了this对象，所以使用的时候，必须写成this.data的形式。

    - mounted()
        这里就涉及到vue.js的生命周期了，在这里我们可以指定组件在页面的生成阶段要做的事情，比如调用ajax从后端拿数据等待。

    - methods
        这里存放所有定义好的函数，调用的时候和data以及props一样，需要写成this.function的形式。

        ```js
        <script>
        import draggable from "vuedraggable";
        export default {
            components: {
                draggable
            },
            props: {
                competition_id: Number
            },
            data: () => {
                return {
                    arrTeamPlayers: [],
                    nNumberOfColonm: 0,
                };
            },
            mounted() {
                this.getData();
            },
            methods: {
                getData() {
                    axios
                        .get("/api/team/member", {
                            params: { competition_id: this.competition_id }
                        })
                        .then(response => {
                            console.log(response["data"]["data"])
                            let data = response["data"]["data"]
                            data["teams"].forEach(team => {
                                this.arrTeamPlayers.unshift({
                                    team_id: team["id"],
                                    team_name: team["name"],
                                    players: []
                                });
                            });
                            this.arrTeamPlayers.forEach(record => {
                                data["plays"].forEach(play => {
                                    if (play["team_id"] == record["team_id"]) {
                                        record["players"].push({
                                            user_id: play["id"],
                                            user_name: play["name"],
                                            is_approved: play["paid"] == 0 ? false : true
                                        });
                                    }
                                });
                            });
                        });
                },
                submit() {
                    axios
                        .post("/api/team/new", {
                            params: {
                                competition_id: this.competition_id,
                                arrTeamPlayers: this.arrTeamPlayers
                            }
                        })
                        .then(response => {
                            console.log(response)
                        });
                }
            }
        };
        </script>
        ```

后记：一些踩过的坑的回忆

- JS的AJAX请求是一种异步操作，所以当处理每一个返回的response时，尽量将里面的操作别做成几个进程来回相互要数据，至少我一直没实现过，我也不知道JS怎么去定义互斥锁。

- 在对数据监听的时候，如果监听对象是数组或对象，或者是数组对象中的某一属性，一定要用深监听，否则一般的监听没法侦测到区别。
