<template>
    <my-page title="SVG 压缩" :page="page">
        <textarea class="form-control" v-model="code" rows="6" placeholder="SVG 代码"></textarea>
        <div class="btns">
            <ui-raised-button class="btn" primary label="压缩" @click="compress" />
        </div>
        <div v-if="result">
            <textarea class="form-control" v-model="result.code" rows="6" placeholder="结果" disabled></textarea>
            <div class="tip">压缩器代码长度: {{ result.before }}，压缩后代码长度：{{ result.current }}，压缩率为：{{ result.rate }}%。</div>
        </div>
    </my-page>
</template>

<script>
    const svgSlimming = require('svg-slimming')

    export default {
        data () {
            return {
                code: `<g></g>
<g fill="red"><rect width="100" height="100"/></g>`,
                result: null,
                page: {
                    menu: [
                        // {
                        //     type: 'icon',
                        //     icon: 'apps',
                        //     href: 'https://app.yunser.com/',
                        //     target: '_blank',
                        //     title: '应用'
                        // }
                    ]
                }
            }
        },
        methods: {
            compress() {
                if (!this.code) {
                    alert('请输入 SVG 代码')
                    return
                }
                svgSlimming(this.code).then(result => {
                    this.result = {
                        code: result,
                        before: this.code.length,
                        current: result.length,
                        rate: (result.length / this.code.length * 100).toFixed(1)
                    }
                })
            }
        }
    }
</script>

<style lang="scss" scoped>
textarea.form-control {
    height: auto;
}
.form-control {
    display: block;
    width: 100%;
    max-width: 400px;
    height: 33px;
    padding: 6px 12px;
    font-size: 14px;
    line-height: 1.42;
    color: #55595c;
    background-color: #fff;
    background-image: none;
    border: 1px solid #ccc;
    border-radius: 2px;
    outline: none;
}
.charset {
    margin-top: 16px;
}
.btns {
    margin-top: 16px;
    margin-bottom: 16px;
    .btn {
        margin-right: 8px;
    }
}
.tip {
    margin-top: 16px;
    color: #999;
}
</style>
