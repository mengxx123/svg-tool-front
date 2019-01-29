<template>
    <my-page title="SVG" :page="page">
        <app-list :data="groups" />
    </my-page>
</template>

<script>
    export default {
        data () {
            return {
                groups: [
                    {
                        // name: '分类',
                        apps: [
                            {
                                name: 'SVG 转 Canvas',
                                desc: 'SVG 转 Canvas 转换器',
                                icon: '/static/img/svg.svg',
                                to: '/canvas'
                            },
                            {
                                name: 'SVG 压缩',
                                desc: '',
                                icon: '/static/img/svg.svg',
                                to: '/compress'
                            }
                        ]
                    }
                ],
                page: {
                    menu: [
                        {
                            type: 'icon',
                            icon: 'apps',
                            href: 'https://app.yunser.com/',
                            target: '_blank',
                            title: '应用'
                        }
                    ]
                }
            }
        }
    }
</script>

<style scoped>
</style>
