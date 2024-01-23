<script setup>
// import gop from './gop.vue'



</script>
<template>
    <div>
        <div id="markdown-container"/>
        <!-- <gop :cont="child_con"></gop> -->
    </div>
</template>
<!-- <script src="/path/to/external-file.js" data-style-url="https://goplus.org/_next/static/widgets/code.f81abac15122c88e65d7.css"></script> -->


<script>
import Cherry from 'cherry-markdown'
import axios from 'axios'

axios.defaults.baseURL = 'http://localhost:8080/';



// import 'https://unpkg.com/vue@2.6.12/dist/vue.min.js'


    // import {
    //   getToken
    // } from '@/utils/auth'
    import 'cherry-markdown/dist/cherry-markdown.min.css'
    // import "https://goplus.org/_next/static/widgets/code.85827e18ab6a0fa63bdc.js"
    export default {
        props: {

        },
        data() {
            return {
                content: null,
                cherrInstance: null,
                child_con: 'Hellll',
                content_id: -1,
                isAdd: false
                // 自定义 
                // CustomHookA: Cherry.createSyntaxHook('important', Cherry.constants.HOOKS_TYPE_LIST.SEN, {
                //     makeHtml(str) {
                //         return str.replace(this.RULE.reg, function(whole, m1, m2) {
                //             return `<span style="color: red;"><strong>${m2}</strong></span>`;
                //         });
                //     },
                //     rule(str) {
                //         console.log(str)
                //         return { reg: /(\*\*\*)([^\*]+)\1/g };
                //     },
                // })
            }
        },
        mounted() {
            this.initCherryMD()
            this.init_req()

            let t = "https://goplus.org/_next/static/widgets/code.85827e18ab6a0fa63bdc.js"
            let e = "https://goplus.org/_next/static/widgets/code.f81abac15122c88e65d7.css"
            var o=document.createElement("script");
            o.src=t,null!=e&&o.setAttribute("data-style-url",e),document.body.appendChild(o)
            
            // gop代码
            // !function(){
            //     var t,e={
            //         code:[
            //             "https://goplus.org/_next/static/widgets/code.f81abac15122c88e65d7.css",
            //             "https://goplus.org/_next/static/widgets/code.85827e18ab6a0fa63bdc.js"
            //         ],
            //         footer:[
            //             "https://goplus.org/_next/static/widgets/footer.5f76b11115203767e7c3.css",
            //             "https://goplus.org/_next/static/widgets/footer.64aca03204c8ae8ad97e.js"
            //         ],
            //         header:[
            //             "https://goplus.org/_next/static/widgets/header.5445867c6da0bcd5587b.css",
            //             "https://goplus.org/_next/static/widgets/header.9ea108e482eb1e740a30.js"
            //         ]
            //     };
            //     // ((null===(t=document.currentScript)||void 0===t?void 0:'code')||"").split(",")
            //     ["code"]
            //     .map((function(t){return t.trim()}))
            //     .filter(Boolean)
            //     .forEach((
            //         function(t){
            //             var s=e[t]||[];
            //             function o(){
            //                 var t=s.find((function(t){return/\.js$/.test(t)})),
            //                 e=s.find((function(t){return/\.css$/.test(t)}));
            //                 if(null!=t){
            //                     var o=document.createElement("script");
            //                     o.src=t,null!=e&&o.setAttribute("data-style-url",e),document.body.appendChild(o)
            //                     console.log(o)
            //                     // 添加了一个script标签 src设置为 core.js style设置为 core.css
            //                 }
            //             }
            //             "loading"!==document.readyState?o():window.addEventListener("DOMContentLoaded",o)
            //         }
            //     ))
            // }();
        },
        methods: {
            // 获取id
            // getQueryString(name) {
            //     var reg = new RegExp('(^|&)' + name + '=([^&]*)(&|$)', 'i');
            //     var r = window.location.search.substr(1).match(reg);
            //     if (r != null) {
            //         return unescape(r[2]);
            //     }
            //     return null;
            // },
            // 初始化请求
            init_req() {
                this.setMarkdown("heeee", false)
                let url_len = window.location.href.split("/")
                if (url_len.length > 4) {
                    // ID 不为空 编辑页面
                    let id = url_len[4]
                    axios({
                        method: 'get',
                        url: '/getArticle/'+id,
                        params: {
                            // id: id
                        }
                        }).then(response => {
                            let article = response.data.data
                            console.log(article)
                            if(article) {
                                // 是编辑 要设定文章 id 与 内容     
                                this.setMarkdown(article)
                                this.content_id = id
                                this.isAdd = false
                            }                      
                    })
                } else {
                    // 为空 新建页面
                    this.isAdd = true
                }
                
            },
            
            submit_markdown() {

                let formData = new FormData()

                let title = this.getToc()
                if (title.length > 0) {
                    title = title[0].text
                } else {
                    title = "Default Title"
                }

                formData.append("title",title)
                formData.append("content",this.getCherryContent())
                formData.append("html",this.getCherryHtml())


                let trans = ""
                
                if (this.content_id > 0) {
                    trans = "translate"
                    formData.append("id",this.content_id)
                }
                // 添加内容 标题修改 ----hxy
              

                // 结束
                formData.append("trans",trans)
                formData.append("cover","")
                formData.append("tags","")
               
                
                // commit接口
                // let data = {
                //     title: 'Default Title',
                //     content: this.getCherryContent(),
                //     html_content: this.getCherryHtml(),
                //     trans: trans,
                //     cover: "",
                //     tags: "",
                //     id: this.content_id
                // }
                axios({
                    method: 'post',
                    // url: '/api/commit', // 后端接口地址，需要根据实际情况修改
                    url: '/commit', // 后端接口地址，需要根据实际情况修改
                    data: formData,
                    headers: {
                    }
                })
                    .then(response => {
                        console.log('内容发送成功');
                        // console.log(response.data); // 输出服务器返回的数据（可选） 获取 返回的ID

                        // let id = response.data.article.id
                        // let md = response.data.article.content
                        // alert("文章提交成功")
                        window.location.href="http://localhost:8080/p/" + response.data.data; 
                    })
                    .catch(error => {
                        console.error('内容发送失败');
                        console.error(error);
                    });
 
            },
            tranlate_md() {
                let formData = new FormData()
                formData.append("content",this.getCherryContent())
                formData.append("html",this.getCherryHtml())

                if (this.content_id > 0) {
                    formData.append("id",this.content_id) 
                }


                // let data = {
                //     content: this.getCherryContent(),
                //     html: this.getCherryHtml()
                // }
                axios({
                    method: 'post',
                    url: '/translate', // 后端接口地址，需要根据实际情况修改
                    // url: '/api/translate', // 后端接口地址，需要根据实际情况修改
                    data: formData,
                    headers: {
                    }
                })
                    .then(response => {
                        console.log('内容发送成功');
                        console.log(response.data.id); // 输出服务器返回的数据（可选） 获取 返回的ID
                        let trans_content = response.data.data
                        console.log(trans_content)
                        this.setMarkdown(trans_content)
                        this.content_id = response.data.id
                    })
                    .catch(error => {
                        console.error('内容发送失败');
                        console.error(error);
                    });
            },  

            //获取cookie
            getcookie(sname){
                var acookie = document.cookie.split("; ");
                for (var i = 0; i < acookie.length; i++) {
                    var arr = acookie[i].split("=");
                    if (sname == arr[0]) {
                        if (arr.length > 1)
                            return unescape(arr[1]);
                        else
                            return "";
                    }
                }
                return "";
            },
            // 初始化编辑器
            initCherryMD(value, config) {
                var {
                    afterChange,
                    afterInit,
                    beforeImageMounted,
                    fileUpload,
                    // mdId
                } = this
                //   var defaultValue = value || this.value
                // var defaultValue = `Hello`
                // 自定义工具栏 - 提交按钮
                var customMenuC = Cherry.createMenuHook('Submit', {
                    noIcon: true,
                    name: 'Submit',
                    onClick: (selection) => {
                        console.log("点击")
                        // 调用 提交函数
                        this.submit_markdown()

                    },
                }); 
                // 自定义工具栏 - 翻译按钮
                var customMenuF = Cherry.createMenuHook('Translate', {
                    noIcon: true,
                    name: 'Translate',
                    onClick: (selection) => {
                        console.log("点击翻译")
                        // 调用 提交函数
                        this.tranlate_md()

                    },
                }); 

                // var myBlockHook = Cherry.createSyntaxHook('myBlock', Cherry.constants.HOOKS_TYPE_LIST.PAR, { 
                //     makeHtml(str) {
                //         return str.replace(this.RULE.reg, function(whole, m1) {
                //             // 如果执行一个加入element元素的代码呢
                //             return `<div style="border: 1px solid;border-radius: 15px;background: gold;" id="div1">${m1}</div>`;
                //             // return `<gop :cont="child_con"></gop>`
                //             // return str
                //         });
                //     },
                //     beforeMakeHtml(str) {
                //         return str
                //     },
                //     afterMakeHtml(str) {
                //         console.log("after", str)
                //         // let idx = str.indexOf("goplus")
                //         // // console.log(str.indexOf("goplus"))
                //         // var pre_str = str.substring(0, idx)
                //         // // console.log(str.substring(idx, idx+6))
                //         // // var new_str = '<goplus-code half-code language="gop">' + "println" + '</goplus-code>'

                //         // var new_str = '<gop :cont="child_con"></gop>'
                //         // var div = document.createElement("div");
                //         // div.innerHTML = new_str;


                //         // // 在这里把 str 渲染成 最终的div形式
                //         // return str.replace(this.RULE.reg, function(whole, m1) {
                //         //     console.log("after")

                //         //     return `<gop :cont="child_con">${m1}</gop>`
                //         // })
                //         return str
                //     },
                //     rule(str) {
                //         return { reg: /\n\+\+(\n[\s\S]+?\n)\+\+\n/g };
                //     },
                // });
                this.cherrInstance = new Cherry({
                    id: 'markdown-container',                    
                    // value: defaultValue,
                    // customSyntax: {
                    //     importHook: {
                    //         syntaxClass: CustomHookA, // 将自定义语法对象挂载到 importHook.syntaxClass上
                    //         force: true, // true： 当cherry自带的语法中也有一个“importHook”时，用自定义的语法覆盖默认语法； false：不覆盖
                    //         before: 'fontEmphasis', // 定义该自定义语法的执行顺序，当前例子表明在加粗/斜体语法前执行该自定义语法
                    //     },
                    //     myBlock: {
                    //         syntaxClass: myBlockHook,
                    //         force: true,
                    //         before: 'blockquote',
                    //     },
                    // },
                    fileUpload: this.fileUpload,
                    // 第三方包
                    externals: { // externals
                    },
                    // 解析引擎配置
                    engine: {
                        // 全局配置
                        global: {
                            // 是否启用经典换行逻辑
                            // true：一个换行会被忽略，两个以上连续换行会分割成段落，
                            // false： 一个换行会转成<br>，两个连续换行会分割成段落，三个以上连续换行会转成<br>并分割段落
                            classicBr: false,
                            /**
                             * 全局的URL处理器
                             * @param {string} url 来源url
                             * @param {'image'|'audio'|'video'|'autolink'|'link'} srcType 来源类型
                             * @returns
                             */
                            urlProcessor: this.urlProcessor,
            
                            /**
                             * 额外允许渲染的html标签
                             * 标签以英文竖线分隔，如：htmlWhiteList: 'iframe|script|style'
                             * 默认为空，默认允许渲染的html见src/utils/sanitize.js whiteList 属性
                             * 需要注意：
                             *    - 启用iframe、script等标签后，会产生xss注入，请根据实际场景判断是否需要启用
                             *    - 一般编辑权限可控的场景（如api文档系统）可以允许iframe、script等标签
                             */
                            htmlWhiteList: ''
                        },
                        // 内置语法配置
                        syntax: {
                            // 语法开关
                            // 'hookName': false,
                            // 语法配置
                            // 'hookName': {
                            //
                            // }
                            autoLink: {
                                /** 是否开启短链接 */
                                enableShortLink: true,
                
                                /** 短链接长度 */
                                shortLinkLength: 20
                            },
                            list: {
                                listNested: false,
                                // 同级列表类型转换后变为子级
                                indentSpace: 2 // 默认2个空格缩进
                            },
                            table: {
                                enableChart: false // chartRenderEngine: EChartsTableEngine,
                                // externals: ['echarts'],
            
                            },
                            inlineCode: {
                                theme: 'red'
                            },
                            codeBlock: {
                                theme: 'dark',
                                // 默认为深色主题
                                wrap: true,
                                // 超出长度是否换行，false则显示滚动条
                                lineNumber: true,
                                // 默认显示行号
                                copyCode: true,
                                // 是否显示“复制”按钮
                                customRenderer: { // 自定义语法渲染器
                                    // 创建自定义渲染函数
                                    gop: {
                                        render: (src, sign, cherryEnding)=> {
                                            console.log("custom render", src)
                                            // return `<p style="color: red">${src}</p>`;
                                            return `<goplus-code half-code language="gop" style="width: 85vw">${src}</goplus-code> `;
                                        }
                                    }
                                },
                                mermaid: {
                                    svg2img: false, // 是否将mermaid生成的画图变成img格式
                                },
                
                                /**
                                 * indentedCodeBlock是缩进代码块是否启用的开关
                                 *
                                 *    在6.X之前的版本中默认不支持该语法。
                                 *    因为cherry的开发团队认为该语法太丑了（容易误触）
                                 *    开发团队希望用```代码块语法来彻底取代该语法
                                 *    但在后续的沟通中，开发团队发现在某些场景下该语法有更好的显示效果
                                 *    因此开发团队在6.X版本中才引入了该语法
                                 *    已经引用6.x以下版本的业务如果想做到用户无感知升级，可以去掉该语法：
                                 *        indentedCodeBlock：false
                                 */
                                indentedCodeBlock: true
                            },
                            emoji: {
                                useUnicode: false, // 是否使用unicode进行渲染
                                customResourceURL: 'https://github.githubassets.com/images/icons/emoji/unicode/${code}.png?v8',
                                upperCase: true,
                            },
                            fontEmphasis: {
                                /**
                                 * 是否允许首尾空格
                                 * 首尾、前后的定义： 语法前**语法首+内容+语法尾**语法后
                                 * 例：
                                 *    true:
                                 *           __ hello __  ====>   <strong> hello </strong>
                                 *           __hello__    ====>   <strong>hello</strong>
                                 *    false:
                                 *           __ hello __  ====>   <em>_ hello _</em>
                                 *           __hello__    ====>   <strong>hello</strong>
                                 */
                                allowWhitespace: false
                            },
                            strikethrough: {
                                /**
                                 * 是否必须有前后空格
                                 * 首尾、前后的定义： 语法前**语法首+内容+语法尾**语法后
                                 * 例：
                                 *    true:
                                 *            hello wor~~l~~d     ====>   hello wor~~l~~d
                                 *            hello wor ~~l~~ d   ====>   hello wor <del>l</del> d
                                 *    false:
                                 *            hello wor~~l~~d     ====>   hello wor<del>l</del>d
                                 *            hello wor ~~l~~ d     ====>   hello wor <del>l</del> d
                                 */
                                needWhitespace: false
                            },
                            mathBlock: {
                                engine: 'MathJax',
                                // katex或MathJax
                                src: 'https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-svg.js', // 如果使用MathJax plugins，则需要使用该url通过script标签引入
                                plugins: true // 默认加载插件
                            },
                            header: {
                                /**
                                 * 标题的样式：
                                 *  - default       默认样式，标题前面有锚点
                                 *  - autonumber    标题前面有自增序号锚点
                                 *  - none          标题没有锚点
                                 */
                                anchorStyle: 'default',
                            },
                            inlineMath: {
                                engine: 'MathJax',
                                // katex或MathJax
                                src: 'https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-svg.js' // 如果使用MathJax plugins，则需要使用该url通过script标签引入
                            },
                            toc: {
                                /** 默认只渲染一个目录 */
                                allowMultiToc: false
                            },
                            header: {
                                /**
                                 * 标题的样式：
                                 *  - default       默认样式，标题前面有锚点
                                 *  - autonumber    标题前面有自增序号锚点
                                 *  - none          标题没有锚点
                                 */
                                anchorStyle: 'default'
                            }
                        },
                        customSyntax: {
                            // SyntaxHookClass
                            // CustomHook: {
                            //     syntaxClass: myBlockHook,
                            //     force: false,
                            //     after: 'br',
                            // },
                        },
                    },
                    editor: {
                        id: 'code',
                        // textarea 的id属性值
                        name: 'code',
                        // textarea 的name属性值
                        autoSave2Textarea: false,
                        // 是否自动将编辑区的内容回写到textarea里
                        theme: 'default',
                        // depend on codemirror theme name: https://codemirror.net/demo/theme.htm
                        // 编辑器的高度，默认100%，如果挂载点存在内联设置的height则以内联样式为主
                        height: '100%',
                        // defaultModel 编辑器初始化后的默认模式，一共有三种模式：1、双栏编辑预览模式；2、纯编辑模式；3、预览模式
                        // edit&preview: 双栏编辑预览模式
                        // editOnly: 纯编辑模式（没有预览，可通过toolbar切换成双栏或预览模式）
                        // previewOnly: 预览模式（没有编辑框，toolbar只显示“返回编辑”按钮，可通过toolbar切换成编辑模式）
                        defaultModel: 'edit&preview',
                        // 粘贴时是否自动将html转成markdown
                        convertWhenPaste: true,
                        codemirror: {
                            // 是否自动focus 默认为true
                            autofocus: true
                        }
                    },
                    toolbars: {
                        theme: 'dark',
                        // light or dark
                        showToolbar: true,
                        // false：不展示顶部工具栏； true：展示工具栏; toolbars.showToolbar=false 与 toolbars.toolbar=false 等效
                        toolbar: ['bold', 'italic',
                            {
                                strikethrough: ['strikethrough', 'underline', 'sub', 'sup', 'ruby', 'customMenuAName'],
                            },
                            'size','|', 'color', 'header', '|','drawIo', '|', 'list',
                            'panel', 
                            // 'justify', // 对齐方式，默认不推荐这么“复杂”的样式要求
                            'detail', '|', {
                                insert: ['image', 'audio', 'video', 'link', 'hr', 'br', 'code', 'formula', 'toc', 'table',
                                    'line-table', 'bar-table', 'pdf', 'word'
                                ]
                            }, 'graph', 'export', 'codeTheme', 'switchModel', 'togglePreview',
                            // {
                            //   customMenuBName: ['ruby', 'audio', 'video', 'customMenuAName'], //实验室
                            // },
                            'settings', 'customMenuCName', 'customMenuFName', 'theme', 
                        ],
                        toolbarRight: ['fullScreen', '|'],
                        bubble: ['bold', 'italic', 'underline', 'strikethrough', 'sub', 'sup', 'quote', 'ruby', '|', 'size',
                            'color'
                        ],
                        // array or false
                        "float": ['h1', 'h2', 'h3', '|', 'checklist', 'quote', 'quickTable', 'code'], // array or false
                        sidebar: ['mobilePreview', 'theme'], // 'copy',
                        customMenu: {
                            // customMenuAName: customMenuA,
                            // customMenuBName: customMenuB,
                            customMenuCName: customMenuC,
                            customMenuFName: customMenuF
                        },
                    },
                    // 打开draw.io编辑页的url，如果为空则drawio按钮失效
                    drawioIframeUrl: window.location.origin + '/CherryMarkdown/drawio_demo.html',
                    /**
                     * 上传文件的时候用来指定文件类型
                     */
                    fileTypeLimitMap: {
                        video: 'video/*',
                        audio: 'audio/*',
                        image: 'image/*',
                        word: '.doc,.docx',
                        pdf: '.pdf',
                        file: '*',
                    },
                    callback: {
                        afterChange: this.afterChange,
                        afterInit: this.afterInit,
                        beforeImageMounted: this.beforeImageMounted,
                        // 预览区域点击事件，previewer.enablePreviewerBubble = true 时生效
                        onClickPreview: this.onClickPreview,
                        // 复制代码块代码时的回调
                        onCopyCode: this.onCopyCode,
                        // 把中文变成拼音的回调，当然也可以把中文变成英文、英文变成中文
                        changeString2Pinyin: this.changeString2Pinyin
                    },
                    // 预览页面不需要绑定事件
                    isPreviewOnly: false,
                    // 预览区域跟随编辑器光标自动滚动
                    autoScrollByCursor: true,
                    // 外层容器不存在时，是否强制输出到body上
                    forceAppend: true,
                    // The locale Cherry is going to use. Locales live in /src/locales/
                    locale: "en_US",
                })
            },
            // 上传通用接口
            fileUpload(file) {
                let type = file.type
                console.log("upload", file.type)
                var formData = new FormData()
                formData.append('file', file)
                // axios post 请求                
                axios({
                    method: 'post',
                    url: '/upload', // 后端接口地址，需要根据实际情况修改
                    data: formData,
                    headers: {
                        'Content-Type': 'multipart/form-data' // 设置请求头部类型为 multipart/form-data
                    }
                })
                    .then(response => {
                        console.log('文件上传成功！');
                        console.log(response.data); // 输出服务器返回的数据（可选） 获取 返回的ID
                        axios({
                            method: 'get',
                            url: '/getMediaUrl/'+response.data,
                            // params: {
                            //     id: media_id
                            // }
                        }).then(response => {
                            console.log("请求media成功")
                            console.log()
                            let url = response.data.url
                            console.log(url)
                            // 返回文件地址的话需要
                           this.onloadCallback(type, url)                        
                        })
                    })
                    .catch(error => {
                        console.error('文件上传失败！');
                        console.error(error);
                    });
                    // this.onloadCallback(type)                        

                // var request = new XMLHttpRequest()
                // // 图片上传路径修改为自己连接
                // request.open('POST', 'localhost:80080' + '/upload')
                // // request.setRequestHeader('Authorization', "Bearer " + getToken())
                // request.onload = this.onloadCallback
                // request.send(formData)
            },
            onloadCallback(type, url) {
                console.log("call back")
                let imgMdStr = ""
                if (/mp4|avi|rmvb/i.test(type)) {
                    imgMdStr = `!video[${type}](${url})`;
                } else if (/mp3/i.test(type)) {
                    imgMdStr = `!audio[${type}](${url})`;
                } else if (/bmp|gif|jpg|jpeg|png/i.test(type)) {
                    imgMdStr = `![${type}](${url})`
                } else {
                    imgMdStr = `[${type}](${url})`
                } 
                this.cherrInstance.insert(imgMdStr)
               
                // test 代码 成功
                // console.log("call back")
                // let imgMdStr = ''
                // imgMdStr = `![${'image/jpg'}](https://up.deskcity.org/pic_source/d6/6a/c5/d66ac5159da4a416a910061f3df99add.jpg)`;
                
                // this.cherrInstance.insert(imgMdStr)
            },
            // 全局的URL处理器
            urlProcessor(url, srcType) {
                return url;
            },
            // 变更事件回调
            afterChange(text, html) {
                this.content = text
                // console.log("markdown", this.getCherryContent())
                // console.log("html", this.getCherryHtml())
                this.$emit('mdChange', html, text)
                this.$emit('input', text)
            },
            // 初始化事件回调
            afterInit(e) {},
            // 图片加载回调
            beforeImageMounted(e, src) {
                return {
                    [e]: src
                }
            },
            // 预览区域点击事件
            onClickPreview(event) {},
            // 粘贴事件
            onCopyCode(event, code) {
                // 阻止默认的粘贴事件
                // return false;
                // 对复制内容进行额外处理
                return code;
            },
            // 获取中文的拼音
            changeString2Pinyin(string) {
                /**
                 * 推荐使用这个组件：https://github.com/liu11hao11/pinyin_js
                 *
                 * 可以在 ../scripts/pinyin/pinyin_dist.js 里直接引用
                 */
                var pinyin = require("./pinyin/pinyin.js");
                return pinyin.pinyin(string, " ");
            },
            setMarkdown(content, keepCursor) {
                if (!this.cherrInstance) { // 未加载则重新初始化
                    this.initCherryMD(content)
                    return
                }
                this.cherrInstance.setMarkdown(content, keepCursor)
                // this.cherrInstance.setValue(content)

            },
            // setValue(content, keepCursor = false) {
            //     let editor = this.cherrInstance.editor
            //     editor.storeDocumentScroll();
            //     if (keepCursor === false) {
            //     return editor.editor.setValue(content);
            //     }
            //     const codemirror = editor.editor;
            //     const old = this.getCherryContent();
            //     const pos = codemirror.getDoc().indexFromPos(codemirror.getCursor());
            //     const newPos = getPosBydiffs(pos, old, content);
            //     codemirror.setValue(content);
            //     const cursor = codemirror.getDoc().posFromIndex(newPos);
            //     codemirror.setCursor(cursor);
            // },
            getCherryContent() {
                var result = this.cherrInstance.getMarkdown() // 获取markdown内容
                return result
            },
            getCherryHtml() {
                var result = this.cherrInstance.getHtml()
                return result
            },
            getData() {
                var result = this.cherrInstance.getHtml()
                return result
            },
            getToc() {
                var result = this.cherrInstance.getToc()
                return result
            },
            /**
             * type：{'pdf'|'img'}
             */
            exportMD(type = 'pdf') {
                this.cherrInstance.export(type)
            },
            /**
             * model{'edit&preview'|'editOnly'|'previewOnly'}
             */
            switchModel(model) {
                if (this.isInit()) {
                    this.cherrInstance.switchModel(model)
                }
            },
    
            insert(content, isSelect = false, anchor = [], focus = true) {
                this.cherrInstance.insert(content, isSelect, anchor, focus)
            },
            isInit() {
                if (this.cherrInstance) {
                    return true
                }
                this.$message.warning('编辑器未初始化，请检查')
                return false
            },
            addEle() {
                // var o=document.createElement("script");
                // o.src=t,null!=e&&o.setAttribute("data-style-url",e),document.body.appendChild(o)

                let newDiv = document.createElement("div");
                // 给它一些内容
                let newContent = document.createTextNode("Hi there and greetings!");
                // 添加文本节点 到这个新的 div 元素
                newDiv.appendChild(newContent);

                // 将这个新的元素和它的文本添加到 DOM 中
                let currentDiv = document.getElementById("div1");
                document.body.insertBefore(newDiv, currentDiv);
            }
        }
    }
  </script>
  
  <style lang="scss" >
  #markdown-container {
    position: absolute;
    left: 0vw;
    top: 0vh;
    width: 100vw;
  }

// .custom-syntax {
//   color: red;
// }

// // 这里样式要修改
// .language-go {
//     white-space: break-spaces;
// }


    
  </style>