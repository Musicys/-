<template>
    <div class="potopic">


        <div class="layout-demo">

            <!-- 提交题目头部 -->
            <div class="prot-top"
                style="height: 6%;width: 100%; background: var(--home-back);   display: flex; justify-content: space-between; align-items: center; padding: 0 20px;">

                <div style="display: flex; align-items: center; gap: 12px;">
                    <span style="color: var(--home-color); font-size: 16px; font-weight: 500;">#{{ data.id }}</span>
                    <span style="color: var(--home-color); font-size: 16px; font-weight: 500;">{{ data.title }}</span>
                    <a-tag color="blue" v-if="(data.acceptedNum / data.submitNum * 100) >= 70">简单</a-tag>
                    <a-tag color="orange"
                        v-if="(data.acceptedNum / data.submitNum * 100) >= 30 && (data.acceptedNum / data.submitNum * 100) < 70">中等</a-tag>
                    <a-tag color="red" v-if="(data.acceptedNum / data.submitNum * 100) < 30">困难</a-tag>
                </div>

                <div style="display: flex; gap: 8px; margin-right: 2em;">
                    <a-button class="but" @click="gohome"
                        style="background: var(--home-back); border: 1px solid var(--border); color: #6E6E6E;"
                        type="primary">返回</a-button>
                    <a-button class="but"
                        style="background: var(--home-back); color: #2B7839;border: 1px solid var(--border);"
                        type="dashed" @click="sumbit">{{
                            IsSumbit ? "判题中" : "提交" }}</a-button>
                </div>
                <div class="prot-top-user" style="display: flex; gap: 8px; margin-right: 3em; color: wheat;">
                    {{ state.state.user.user.userName }}
                </div>
            </div>

            <a-layout style="width: 100%; height: 94%; ">




                <a-layout-sider class="border"
                    style="max-width: 97vw;background: var(--home-back); color: var(--home-color); padding: 20px; width: 50vw;"
                    :resize-directions="['right']">


                    <a-tabs @change="setComentList" :active-key="agrnber" default-active-key="1" lazy-load>
                        <a-tab-pane key="1" title="题目描述">
                            <div class="top"
                                style="height: 50px; width: 200px; display: flex; justify-content: stretch; align-items: center;">

                                <a-tag style="margin-left: 1em;" v-for="i in data.tags">{{ i }}</a-tag>


                            </div>



                            <div class="contenx">


                                <Viewer :value="data.content"></Viewer>
                            </div>
                        </a-tab-pane>
                        <a-tab-pane key="2" title="题目解析">
                            <div class="two-box">

                                <!-- 题解 -->
                                <div class="comment-jx">
                                    <h1>
                                        题目解析

                                    </h1>

                                    <Viewer :value="'```' + data.answer"></Viewer>

                                </div>
                                <!-- 评论 -->
                                <div class="page2" @scroll="handleScroll">
                                    <CommentBox :commentid="CommentID" @PostCommentLsit="setComentList(2)"></CommentBox>

                                    <div class="comlisttop">
                                        <div> 评论：{{ Commentutil.tatiol }}</div>
                                        <div style="margin-left: 1em;">
                                            <span
                                                :style="{ 'color': CommentSolder.sortOrder == 'descend' ? '#57965C' : '#61A9DE', 'cursor': 'pointer' }"
                                                @click="setsolder(`descend`)">
                                                最新
                                            </span>

                                            <span style="margin: auto .5em; border-right:2px solid var(--border);">

                                            </span>

                                            <span
                                                :style="{ 'color': CommentSolder.sortOrder == 'ascend' ? '#57965C' : '#61A9DE', 'cursor': 'pointer' }"
                                                @click="setsolder(`ascend`)">
                                                最久
                                            </span>
                                        </div>
                                    </div>

                                    <div class=" loding" v-if="!IsComentList">
                                        <a-spin
                                            style="display: flex; justify-content: center; align-items: center; font-size: 26px; width: 100%;"
                                            :size="40" />
                                    </div>


                                    <div class="custom-scrollbar" ref="scrollContainer"
                                        v-else-if="IsComentList && !IsPostAut">
                                        <!-- 这里放置你的内容 -->
                                        <!-- 内容 -->
                                        <Comment :commentid="CommentID" @PostCommentLsit="setComentList(2)"
                                            v-for="i in ComeentList" :key="i.id" :data="i">
                                            <ComentSon :commentid="CommentID"
                                                @PostCommentLsit="GetComment(i.id, i.chiden.total)" :id="i.id"
                                                v-for="(e, index) in i.chiden.records" :key="e.id" :data="e" />
                                            <div style="text-align: center; color: white;">
                                                <span
                                                    v-if="i.chiden.total != 0 && i.chiden.records.length != i.chiden.total">
                                                    <IconDown />共 {{ i.chiden.total }} 条回复，
                                                    <span @click="GetComment(i.id, i.chiden.total)"
                                                        style="cursor: pointer;">点击查看</span>
                                                </span>
                                                <span
                                                    v-if="i.chiden.total != 0 && i.chiden.records.length == i.chiden.total && i.chiden.records.length > 2">
                                                    <IconUp />共 {{ i.chiden.total }} 条回复，

                                                    <span @click="GetComment(i.id, 2)"
                                                        style="cursor: pointer;">收起</span>

                                                </span>

                                            </div>
                                        </Comment>

                                        <a-spin
                                            style="display: flex; justify-content: center; align-items: center; font-size: 26px; width: 100%;"
                                            v-if="!CommentScrall" :size="40" />
                                        <div v-else style="display: flex; justify-content: center;">加载完成</div>


                                    </div>

                                    <div v-else>

                                        <!-- 加载失败 -->
                                        加载失败


                                    </div>
                                </div>

                            </div>

                        </a-tab-pane>
                        <a-tab-pane key="3" title="提交记录">



                            <a-table :stripe="true" :data="QuestionSumbitVoList" :pagination="false"
                                style="margin-top: 30px;max-width: 1000px; margin: auto;">
                                <template #columns>
                                    <a-table-column title="所有状态" data-index="tags">
                                        <template #cell="{ record }">

                                            <div>
                                                <a-tag :color="record.judgeInfo.message == '成功' ? '#304F36' : 'red'"
                                                    style="font-size: 1em; margin-right: .5em;   margin-top: .5em;">

                                                    {{ record.judgeInfo.message || "编译错误" }}
                                                </a-tag>

                                                <div><span style="font-size: .8em; ">{{
                                                    initialData(record.updateTime)
                                                        }}</span></div>
                                            </div>

                                        </template>
                                    </a-table-column>



                                    <a-table-column title="所有语言" data-index="tags">
                                        <template #cell="{ record }">

                                            <a-tag color="green" style="margin-left: .2em;">{{
                                                record.language }}</a-tag>

                                        </template>
                                    </a-table-column>

                                    <a-table-column title="执行时间" data-index="tags">
                                        <template #cell="{ record }">

                                            <a-tag color="green" style="margin-left: .2em;">{{
                                                record.judgeInfo.time + 'ms' }}</a-tag>

                                        </template>
                                    </a-table-column>
                                    <a-table-column title="消耗内存" data-index="tags">
                                        <template #cell="{ record }">

                                            <icon-settings /><span style="margin-left: 5px;">{{
                                                record.judgeInfo.memory }}Mb</span>

                                        </template>
                                    </a-table-column>

                                </template>
                            </a-table>
                            <div style="  text-align: center; font-size: .8em; color: #ffff; margin: 1em auto;">默认加载6条
                            </div>
                        </a-tab-pane>
                    </a-tabs>





                </a-layout-sider>
                <a-layout-content>
                    <a-layout-sider class="bk" style=" width: 100%;     max-height: 97vh; height: 70vh;"
                        :resize-directions="['bottom']">
                        <div class="top" style="height: 20px;">


                            <a-dropdown style="font-size: .5em; height: 15px; " @select="handleSelect"
                                :popup-max-height="false">

                                <a-button style="font-size: .5em; height: 15px; float: right; margin-right: 1em;">{{
                                    froms.language }}
                                    <icon-down /></a-button>
                                <template #content>
                                    <a-doption>java</a-doption>
                                    <a-doption>cpp</a-doption>

                                </template>
                            </a-dropdown>
                        </div>
                        <Monaco :value="froms.code" :language="froms.language" @handleChange="handleChange">
                        </Monaco>


                    </a-layout-sider>
                    <a-layout-content style="color: white;">

                        <div class="left-bottom"
                            style="display: flex; flex-direction: column; gap: 12px; padding: 16px;">
                            <div style="display: flex; gap: 16px;">
                                <a-tag color="blue">时间：{{ fromdata.judgeInfo.time }}ms</a-tag>
                                <a-tag :color="fromdata.judgeInfo.message === `答案错误` ? 'red' : 'green'">状态：{{
                                    fromdata.judgeInfo.message
                                }}</a-tag>

                                <a-tag :color="fromdata.status === 2 ? 'red' : 'green'">内存：{{ fromdata.judgeInfo.memory

                                    }}</a-tag>




                                <a-tag color=" orange">通过率：{{ (data.acceptedNum / data.submitNum * 100).toFixed(2) | 0
                                    }}%</a-tag>
                            </div>




                            <div style="display: flex; justify-content: start; margin-top: 2em;">
                                <router-link target="_blank" :to="{ path: `/sumbitQv/${Number(porps.id) - 1}` }">

                                    <a-button type="dashed" status="success" :disabled="Number(porps.id) - 1 <= 0">
                                        上一题</a-button>

                                </router-link>
                                <router-link style="margin-left: 2em;" target="_blank"
                                    :to="{ path: `/sumbitQv/${Number(porps.id) + 1}` }">
                                    <a-button type="dashed" status="success" :disabled="Number(porps.id) + 1 > total">
                                        下一题</a-button>

                                </router-link>
                            </div>

                        </div>
                    </a-layout-content>


                </a-layout-content>
            </a-layout>

        </div>
    </div>

</template>

<script setup lang="ts">
import { QuestionControllerService, QuestionCommentControllerService, QuestionSubmitAddRequest } from '@/generated';
import { onMounted, Ref, ref } from 'vue';
import { Viewer } from "@bytemd/vue-next";
import { Button, message } from 'ant-design-vue'
import Monaco from '@/components/Monaco.vue';
import CommentBox from './CommentBox.vue';
import { useRouter } from 'vue-router'
import Comment from './Comment.vue';
import ComentSon from './ComentSon.vue';
import { useStore } from 'vuex';
import { initialData } from '@/util/date';
const agrnber = ref("1")
const state = useStore()
const router = useRouter()
const porps = defineProps(["id"])
const IsComentList: Ref<Boolean> = ref(false)
const Commentutil = ref({
    tatiol: 99,

})
const CommentID = ref()
const CommentPage = ref({
    current: 1,
    pageSize: 10,


})
const CommentSolder = ref({
    "sortField": "createTime",
    "sortOrder": "descend",
})
const total = ref(0)
const setsolder = (str: string) => {
    CommentSolder.value.sortOrder = str
    setComentList(2)
}
const scrollContainer = ref(null);
//评论是否全部加载完成
const CommentScrall = ref(false)
async function handleScroll(event) {


    if (CommentScrall.value) {

        //全部评论加载完毕
        return

    }



    const element = event.target;

    if (Math.ceil(element.scrollTop + element.clientHeight) + 5 > element.scrollHeight) {
        //加载

        CommentPage.value.current += 1



        let res = await QuestionCommentControllerService.listPostCmentByPageUsingPost({
            ...CommentPage.value,
            questionid: CommentID.value
        })
        if (res.code == 0) {


            //添加数据
            if (res.data.records.length == 0) {
                //加载完成
                CommentScrall.value = true
            }



            ComeentList.value.push(...res.data.records)







        }
        else {

        }



    }
}
//评论
const ComeentList: Ref<Array<any>> = ref([])
//显示评论加载失败
const IsPostAut: Ref<Boolean> = ref(false)
const QuestionSumbitVoList = ref([])
const setComentList = async (e) => {
    agrnber.value = `${e}`
    if (e == 2) {
        IsComentList.value = false
        IsPostAut.value = false
        let res = await QuestionCommentControllerService.listPostCmentByPageUsingPost({
            current: 1,
            pageSize: 10,
            questionid: CommentID.value,
            ...CommentSolder.value
        })

        if (res.code == 0) {

            ComeentList.value = [...res.data.records]
            CommentScrall.value = false
            CommentPage.value.current = 1

            Commentutil.value.tatiol = res.data.total
            if (res.data.records.length == 0) {
                //加载完成




                CommentScrall.value = true
            }

            setTimeout(() => {
                IsPostAut.value = false;
                IsComentList.value = true;
            }, 500);



        }
        else {
            message.error("评论加载失败")
            IsPostAut.value = true
            IsComentList.value = true
        }
    }
    else if (e == 3) {


        let res = await QuestionControllerService.listQuestionSubmitByPageUsingPost({
            "current": 1,
            "pageSize": 6,
            "questionId": CommentID.value,
            "sortField": "updateTime",
            "sortOrder": "descend",
            "userId": state.state.user.user.id
        })
        if (res.code == 0) {
            QuestionSumbitVoList.value = res.data.records


        }
        else {
            QuestionSumbitVoList.value = []
        }




    }





}
// 子评论加载
const GetComment = async (id: any, size: any) => {


    const arr = {
        size: 0,
        data: []
    };
    // @ts-ignore
    let data: any = ComeentList.value;

    for (let index = 0; index < ComeentList.value.length; index++) {
        if (data[index].id == id) {
            console.log(data[index].chiden.current);
            const from = {
                "current": 1,
                "pageSize": size,
                "pid": id
            }
            //处理

            let res = await QuestionCommentControllerService.listPostComentByPageSonUsingPost(from);

            if (res.code == 0) {
                arr.data = res.data
                arr.size = index

            }
            else {
                message.error("加载失败")

            }
        }







    }

    ComeentList.value[arr.size].chiden = arr.data

}
const data: Ref<any> = ref({})

const gohome = (str) => {
    router.push(`/`)
}

const handleSelect = (e) => {

    froms.value.language = e

}

const froms: Ref<QuestionSubmitAddRequest> = ref({
    code: `
`,
    language: "java"

})

const handleChange = (str) => {


    froms.value.code = str
}
//获取
const gettopic = async () => {





    let res = await QuestionControllerService.getQuestionVoByIdUsingGet(CommentID.value)
    if (res.code == 0) {



        data.value = res.data
        // @ts-ignore
        if (res.data.codeinitial != undefined) {
            // @ts-ignore
            froms.value.code = res.data.codeinitial
        }


    }

    document.title = data.value.title
}
const fromdata = ref({
    "code": "public class Main {\n    public static void main(String[] args) {\n        \n    }\n}\n",
    "createTime": "2025-03-09T04:17:36.000+00:00",
    "id": "1898588957428518914",
    "judgeInfo": {
        "message": "未答题",
        "memory": "0",
        "time": "0"
    },

    "language": "java",
    "questionId": "1896165800906186753",
    "questionVO": null,
    "status": 2,
    "updateTime": "2025-03-09T04:17:37.000+00:00",
    "userId": "1895117906623131650",
    "userVO": null
})

const IsSumbit = ref(false)
const sumbitdg = async (res, index) => {

    setTimeout(async () => {
        console.log(index);

        if (index == 5) {
            message.error("编译出错，是否引入所有库")
            setComentList(3)
            IsSumbit.value = false
            return

        }
        let Resquset = await QuestionControllerService.getQuestionSumbitVoByIdUsingGet(res.data)
        if (Resquset.code == 0) {


            // @ts-ignore
            if (Resquset.data.judgeInfo != "{}") {
                // @ts-ignore
                fromdata.value = Resquset.data
                // @ts-ignore
                fromdata.value.judgeInfo = JSON.parse(Resquset.data.judgeInfo)


                IsSumbit.value = false
                agrnber.value = "3"

                setComentList(3)
                IsSumbit.value = false
            }
            else {
                message.loading("判题中")
                sumbitdg(res, ++index)

            }




        }



    }, 500);

}
const sumbit = async () => {
    if (froms.value.code == "") {
        return message.error("请输入代码")
    }
    IsSumbit.value = true
    let res = await QuestionControllerService.doQuestionSubmitUsingPost({ ...froms.value, questionId: CommentID.value })


    if (res.code == 0) {


        console.log();


        sumbitdg(res, 0)






    }
    else {
        message.error("错误")
        IsSumbit.value = false
    }

}
const initial = async () => {


    let res = await QuestionControllerService.listQuestionVoByPageUsingPost(


        {
            "pageSize": 1,
            "sortField": "updateTime",
            "sortOrder": "descend",
            "current": porps.id
        }
    )


    if (res.code == 0) {

        total.value = res.data.total
        if (res.data.records[0]) {
            CommentID.value = res.data.records[0].id || 1
            gettopic()
            setComentList(2)
            agrnber.value = "1"
        }
        else {
            CommentID.value = 1
            message.error("题目不存在")
        }


    }
    else {
        CommentID.value = 1
        message.error("请求错误")
    }


}

onMounted(() => {

    initial()

})


</script>

<style scoped>
.page2 {
    overflow-x: hidden;
    overflow-y: auto;
    width: 100%;
    height: 40vh;
    bottom: 0;

    border-top: .1px solid var(--border);
    /* 隐藏整个滚动条 */

    scrollbar-width: none;
    /* Firefox */
    -ms-overflow-style: none;
    /* Internet Explorer 10+ */


    /* WebKit浏览器专用 */
    &::-webkit-scrollbar {
        display: none;
    }
}

.comlisttop {

    display: flex;
    width: 90%;
    margin: 1em auto;

    justify-content: start;

}

.custom-scrollbar {

    width: 90%;
    padding: 15px;
    margin: auto;
    margin-bottom: 2em;


}

.loding {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-top: 1em;
}



.action {
    display: inline-block;
    padding: 0 4px;
    color: var(--color-text-1);
    line-height: 24px;
    background: transparent;
    border-radius: 2px;
    cursor: pointer;
    transition: all 0.1s ease;
}

.action:hover {
    background: var(--color-fill-3);
}

.bk {
    background: var(--home-back);
    color: var(--home-color);

    /* 定义滚动条轨道的样式 */
    ::-webkit-scrollbar {
        width: 10px;
        /* 滚动条宽度 */
    }

    /* 定义滚动条轨道的背景色 */
    ::-webkit-scrollbar-track {
        background-color: #2C2C2C;
        /* 背景色 */

        /* 边框颜色 */
    }

    /* 定义滚动条滑块的样式 */
    ::-webkit-scrollbar-thumb {
        background-color: #D1D1D1;
        /* 滑块颜色 */

        /* 边框颜色 */
    }

    /* 当鼠标悬停在滚动条上时改变滑块的颜色 */
    ::-webkit-scrollbar-thumb:hover {
        background-color: #C0C0C0;
        /* 鼠标悬停时滑块颜色 */
    }
}

.border {

    border: 1px solid var(--border);
    border-radius: 15px 0px 0px 15px;
}

.contenx {
    background: var(--home-back);
    color: var(--home-color);
}

.top {
    background: var(--home-back);
    color: var(--home-color);

    /* 定义滚动条轨道的样式 */
    ::-webkit-scrollbar {
        width: 10px;
        /* 滚动条宽度 */
    }

    /* 定义滚动条轨道的背景色 */
    ::-webkit-scrollbar-track {
        background-color: #2C2C2C;
        /* 背景色 */
        border: 1px solid var(--border);
        /* 边框颜色 */
    }

    /* 定义滚动条滑块的样式 */
    ::-webkit-scrollbar-thumb {
        background-color: #D1D1D1;
        /* 滑块颜色 */
        border: 1px solid white;
        /* 边框颜色 */
    }

    /* 当鼠标悬停在滚动条上时改变滑块的颜色 */
    ::-webkit-scrollbar-thumb:hover {
        background-color: #C0C0C0;
        /* 鼠标悬停时滑块颜色 */
    }
}

.layout-demo {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
    background: var(--home-back);
    color: var(--home-color);

}

.comment-jx {
    overflow-x: hidden;
    overflow-y: auto;
    max-height: 40vh;

}

.comment-jx::-webkit-scrollbar {
    display: none;
    /* 隐藏滚动条（适用于 WebKit 浏览器，如 Chrome 和 Edge） */
}

.comment-jx {
    -ms-overflow-style: none;
    /* 隐藏滚动条（适用于 IE 和 Edge Legacy） */
    scrollbar-width: none;
    /* 隐藏滚动条（适用于 Firefox） */
}

.two-box {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    --color-text-2: white;
    --color-text-1: white;
    --color-text-3: white;
}
</style>