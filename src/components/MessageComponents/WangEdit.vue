<template>
    <div class="editor">
        <div class="input-group">
            <div class="input-group-btn">
                <button type="button" class="btn btn-success">标题</button>
            </div>
            <input
                type="text"
                v-model="messageTitle"
                class="form-control"
                placeholder="请输入标题... ..."
            />
        </div>

        <div>
            <p></p>
        </div>

        <div ref="toolbar" class="toolbar"></div>

        <div ref="editor" class="text"></div>

        <div>
            <p></p>
        </div>

        <Button class="btn btn-success pull-right" @click="submit">发布留言</Button>
    </div>
</template>

<script>
import E from "wangeditor";
export default {
    name: "editoritem",
    data() {
        return {
            messageTitle: "",
            messageContent: "",
            // uploadPath,
            editor: null,
            info_: null
        };
    },
    model: {
        // prop: "value",
        event: "publishMessage"
    },
    props: {
        // value: {
        //     type: String,
        //     default: ""
        // },
        isClear: {
            type: Boolean,
            default: false
        }
    },
    watch: {
        isClear(val) {
            // 触发清除文本域内容
            if (val) {
                this.editor.txt.clear();
                this.info_ = null;
            }
        }
        // value: function(value) {
        //     if (value !== this.editor.txt.html()) {
        //         this.editor.txt.html(this.value);
        //     }
        // }
        //value为编辑框输入的内容，这里我监听了一下值，当父组件调用得时候，如果给value赋值了，子组件将会显示父组件赋给的值
    },
    mounted() {
        this.initEditor();
    },
    methods: {
        initEditor() {
            this.editor = new E(this.$refs.toolbar, this.$refs.editor);
            this.editor.customConfig.uploadImgShowBase64 = false; // base 64 存储图片
            this.editor.customConfig.uploadImgServer = "http://proxy.mincox.club/api/message.upload_img/";
            this.editor.customConfig.uploadImgHeaders = {}; // 自定义 header
            this.editor.customConfig.uploadFileName = "file"; // 后端接受上传文件的参数名
            this.editor.customConfig.uploadImgMaxSize = 2 * 1024 * 1024; // 将图片大小限制为 2M
            this.editor.customConfig.uploadImgMaxLength = 6; // 限制一次最多上传 3 张图片
            this.editor.customConfig.uploadImgTimeout = 3 * 60 * 1000; // 设置超时时间

            // 配置菜单
            this.editor.customConfig.menus = [
                "head", // 标题
                "bold", // 粗体
                "fontSize", // 字号
                "fontName", // 字体
                "italic", // 斜体
                "underline", // 下划线
                "strikeThrough", // 删除线
                "foreColor", // 文字颜色
                "backColor", // 背景颜色
                "link", // 插入链接
                "list", // 列表
                "justify", // 对齐方式
                "quote", // 引用
                "emoticon", // 表情
                "image", // 插入图片
                "table", // 表格
                "video", // 插入视频
                "code", // 插入代码
                "undo", // 撤销
                "redo", // 重复
                "fullscreen" // 全屏
            ];

            this.editor.customConfig.uploadImgHooks = {
                fail: (xhr, editor, result) => {
                    // 插入图片失败回调
                    alert("失败");
                },
                success: (xhr, editor, result) => {
                    // 图片上传成功回调
                    alert("成功");
                },
                timeout: (xhr, editor) => {
                    // 网络超时的回调
                    alert("超时");
                },
                error: (xhr, editor) => {
                    // 图片上传错误的回调
                    alert("错误");
                },
                customInsert: (insertImg, result, editor) => {
                    // 图片上传成功，插入图片的回调
                    //result为上传图片成功的时候返回的数据，这里我打印了一下发现后台返回的是data：[{url:"路径的形式"},...]
                    console.log(result);
                    //insertImg()为插入图片的函数
                    //循环插入图片
                    // for (let i = 0; i < 1; i++) {
                    // console.log(result)
                    // let url = "http://otp.cdinfotech.top" + result.url;
                    let url = "http://proxy.mincox.club/" + result.url;
                    insertImg(url);
                    // }
                }
            };
            this.editor.customConfig.onchange = html => {
                this.messageContent = html; // 绑定当前逐渐地值
            };
            // 创建富文本编辑器
            this.editor.create();
        },
        submit() {
            this.$emit("publishMessage", {
                messageTitle: this.messageTitle,
                messageContent: this.messageContent
            });
        }
    }
};
</script>

<style lang="css">
.editor {
    width: 100%;
    margin: 0 auto;
    position: relative;
    z-index: 0;
}
.toolbar {
    border: 1px solid #ccc;
}
.text {
    border: 1px solid #ccc;
    min-height: 500px;
}
</style>