<template>
    <my-page title="SVG 转换器 - SVG to HTML5 Canvas Converter">
        
        <div class="row">
            <div class="col-sm-12">
                <h1 class="convert-title">SVG 转 Canvas 转换器</h1>
                <!--<a href="https://github.com/gabelerner/canvg">canvg.js</a>-->
            </div>
        </div>
        <div class="row">
            <div class="col-sm-6">
                <div class="canvas-box">
                    <canvas class="canvas" id="canvas"></canvas>
                    <div id="dropMessage">拖拽 SVG 文件到这里</div>
                </div>
            </div>
            <div class="col-sm-6">
                <textarea class="code" id="code" spellcheck='false' placeholder="转换代码"></textarea>
                <div id="page"></div>
            </div>
        </div>
    </my-page>
</template>

<script>
    /* eslint-disable */

    export default {
        data () {
            return {
            }
        },
        mounted() {
            this.init()
        },
        methods: {
            init() {
                var imageMaxWidth = 80;
                var page = document.getElementById('page');
                var code_out = document.getElementById('code');
                var canvas = document.getElementById('canvas');
                var left = document.getElementById('left');
                var dropMessage = document.getElementById('dropMessage');
                var g = canvas.g = canvas.getContext('2d');

                var backingStoreRatio = g.webkitBackingStorePixelRatio ||
                    g.mozBackingStorePixelRatio ||
                    g.msBackingStorePixelRatio ||
                    g.oBackingStorePixelRatio ||
                    g.backingStorePixelRatio || 1;
                var ratio = (window.devicePixelRatio || 1) / backingStoreRatio;

                function setSize(width, height, canvas) {
                    canvas = canvas || window.canvas;
                    canvas.width = width * ratio;
                    canvas.height = height * ratio;
                    canvas.style.width = width + 'px';
                    canvas.style.height = height + 'px';
                }

                setSize(canvas.parentNode.clientWidth, canvas.parentNode.clientHeight - 6);

                function drawSVGImageByCanvg(img, x, y) {
                    var tempCanvas = document.createElement('canvas');
                    tempCanvas.name = img.name;
                    tempCanvas.x = x;
                    tempCanvas.y = y;
//        setSize(img.width, img.height, tempCanvas);
                    tempCanvas.width = img.width;
                    tempCanvas.height = img.height;
                    tempCanvas.style.zIndex = -1;
                    tempCanvas.style.visibility = 'hidden'
                    page.appendChild(tempCanvas);

                    canvg(tempCanvas, atob(img.src.substring('data:image/svg+xml;base64,'.length)), {
                        ignoreMouse: true,
                        ignoreAnimation: true,
                        ignoreDimensions: true,
                        ignoreClear: true,
//            offsetX: x,
//            offsetY: y,
                        scaleWidth: img.width,
                        renderCallback: function (dom) {
//                drawCanvas.call(tempCanvas);
//                tempCanvas.parentNode.removeChild(tempCanvas);
                        }
                    });
                }

                canvas.ondragenter = function () {
                    this.style.background = '#FF0'
                    return false;
                };

                canvas.ondragleave = function () {
                    this.style.background = ''
                }

                canvas.ondragover = function () {
                    return false;
                };

                var images = [];
                var completedImageCount = 0;

                function drawCanvas() {
                    g.save();
                    g.scale(ratio, ratio);
                    g.drawImage(this, this.x, this.y);
                    g.restore();

                    this.parentNode.removeChild(this);
                }

                window.onSVGDraw = function (code, canvas) {
                    setTimeout(drawCanvas.bind(canvas), 500)

                    completedImageCount++;
                    code_out.value += '"' + canvas.name + '": ' + code;

                }

                function finish() {
                    dropMessage.innerHTML = '';
                    canvas.style.position = 'relative';

                    code_out.value = 'var SVGIcons = {\n';
                    completedImageCount = 0;

                    var x = 10, y = 0, gap = 5;
                    var borderWidth = 3;
                    var width = canvas.parentNode.clientWidth - borderWidth * 2;
                    var height = 0;

                    images.forEach(function (image) {
                        var w = image.width;
                        var h = image.height;
                        if (w > imageMaxWidth) {
                            h = imageMaxWidth * h / w;
                            w = imageMaxWidth;
                            image.width = w;
                            image.height = h;
                        }
                        height += h + gap;
                    });
                    height = Math.max(canvas.parentNode.clientHeight - borderWidth * 2, height + 30);

                    setSize(width, height);

                    g.save();
                    g.scale(ratio, ratio);

                    g.fillText('draw SVG by image', width * 0.1, 20);
                    g.fillText('draw SVG by canvg.js', width * 0.6, 20);

                    y += 30;

                    var i = 0;
                    var length = images.length;
                    while (i < length) {
                        var image = images[i];
                        i++;

                        var w = image.width;
                        var h = image.height;

                        g.drawImage(image, x, y, w, h);
                        drawSVGImageByCanvg(image, x + width / 2, y);
                        y += gap + image.height;
                    }
                    g.restore();
                }
                canvas.ondrop = function (e) {
                    this.style.background = '';

                    code_out.value = '';
                    images = [];
                    e.stopPropagation();
                    e.preventDefault();
                    var files = e.dataTransfer.files;
                    for (var i = 0; i < files.length; i++) {
                        var f = files[i];
                        var numbers = files.length;
                        if (f.type.indexOf('image') == 0) {
                            (function (f) {
                                var name = f.name,
                                    type = f.type;
                                if (typeof FileReader == 'undefined') {
                                    alert('Sorry, FileReader() not supported, switch to Chrome and try again.')
                                }
                                var reader = new FileReader();
                                reader.onload = function (e) {
                                    var dataURL = e.target.result;
                                    var imgEL = new Image();
                                    imgEL.name = name;
                                    imgEL.onload = function (e) {
                                        var image = e.target;

//                            name = name.substring(0, name.lastIndexOf('.'));
                                        image.name = name;
                                        images.push(image);
                                        --numbers;
                                        if (numbers === 0) {
                                            finish();
                                        }
                                    }
                                    imgEL.src = dataURL;
                                };
                                reader.readAsDataURL(f);

                            })(f);
                        }
                    }
                };
            }
        }
    }
</script>

<style scoped>
    .canvas-box {
        position: relative;
    }
    .canvas {
        height: 500px;
        background-color: #EEE;
        overflow-x: hidden;
        overflow-y: auto;
        border: dashed 5px #888;
    }
    .convert-title {
        margin-bottom: 16px;
        text-align: center;
        font-size: 20px;
    }

    .code {
        border: solid 1px #E0E0E0;
        background-color: #FFE;
        padding: 16px;
        width: 100%;
        height: 500px;
        outline: none;
    }

    #dropMessage {
        position: absolute;
        left: 32px;
        top: 48px;
        color: #2898E0;
        font-size: 1.5em;
        font-weight: bold;
    }

    #q-canvas {
        width: 100%;
        height: 100%;
        border: solid 1px #2898E0;
    }

    .title-pane > div {
        padding: 10px;
        font-size: 1.5em;
        background-color: #EEE;
        display: inline-block;
    }
</style>
