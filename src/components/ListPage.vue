<script setup>
import { reactive, ref, onMounted, onUpdated } from 'vue'
const options = reactive(JSON.parse(localStorage.getItem('data')) || []) // 定义一个响应式数组并赋值
console.log(options)
const state = ref(false)
const str = ref("")
const ModityTime = ref("")
const ModityDisc = ref("")
const Index = ref(-1)
const msg = ref("")
const flag = ref(false)

const formItem = ref(null) // 使用ref来定义formItem
const ModifyWindow = ref(null) // 使用ref来定义ModifyWindow
const mc = ref(null)

onMounted(() => {
    formItem.value = document.querySelector('.opt') // 在onMounted函数里面给formItem赋值
    ModifyWindow.value = document.querySelector('.popup') // 在onMounted函数里面给ModifyWindow赋值
    mc.value = document.querySelector('.mc')
})

onUpdated(() => {
    formItem.value.style.pointerEvents = flag.value ? "none" : "auto"
    ModifyWindow.value.style.pointerEvents = flag.value ? "auto" : "none"
    ModifyWindow.value.style.opacity = flag.value ? 1 : 0
    mc.value.style.backgroundColor = flag.value ? "rgba(0, 0, 0, .5)" : "rgba(0, 0, 0, 0)"
})

const AddText = ref("请添加描述")

function func() {
    state.value = !state.value
    AddText.value = state.value ? "取消添加" : "请添加描述"
}

function add() {
    if (!str.value) {
        return alert("输入内容不能为空")
    }
    options.push({
        id: new Date().toLocaleString(),
        value: str.value,
        date: new Date().toLocaleString()
    })
    state.value = !state.value
    AddText.value = state.value ? "取消添加" : "请添加描述"
    localStorage.setItem('data', JSON.stringify(options))
    str.value = ""
}

function removeItem(index) {
    options.splice(index, 1)
    localStorage.setItem('data', JSON.stringify(options))
}

function modifyOpt(index) {
    Index.value = index
    ModityTime.value = options[Index.value].date
    ModityDisc.value = options[Index.value].value
    flag.value = true
}

function modifyItem() {
    if (!msg.value) {
        return alert("输入内容不能为空")
    }
    options[Index.value].date = new Date().toLocaleString()
    options[Index.value].value = msg.value
    msg.value = ""
    flag.value = false
    localStorage.setItem('data', JSON.stringify(options))
}

</script>



<template>
<div class="mc">
    <div class="opt">
        <h1>操作日志</h1>
        <button @click="func" >{{ AddText }}</button>
        <!-- 添加栏 -->
        <div v-if="state">
            <input type="text" v-model="str">
            <button @click="add">确认添加</button>
        </div>
        <!-- 表格渲染 --> 
        <table>
            <thead>
                <tr>
                    <th>操作时间</th>
                    <th>操作描述</th>
                    <th>事项操作</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(i, index) in options">
                    <td>{{ i.date }}</td>
                    <td>{{ i.value }}</td>
                    <td><button @click="removeItem(index)">删除</button> <button @click="modifyOpt(index)">修改</button></td>
                </tr>
            </tbody>
        </table>
    </div>
</div>



<div class="popup">
    <header>
        <h2>修改描述</h2>
    </header>
    <table>
        <thead>
            <tr>
                <th>操作时间</th>
                <th>操作描述</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>{{  ModityTime }}</td>
                <td>{{  ModityDisc }}</td>
            </tr>
        </tbody>
    </table>
    <div class="content">
        <div class="field">
            <input type="text" v-model="msg">
            <button @click="modifyItem" >确认修改</button>
        </div>
    </div>
</div>
</template>

<style scoped>
table {
margin: 0 auto;
width: 950px;
border-collapse: collapse;
color: #3c3637;
}
th {
padding: 10px;
background: #f2f2f2;
font-size: 18px;
text-align: left;
}

td,
th {
border: 1px solid #ebebeb;
padding: 15px;
}

td {
color: #666;
font-size: 16px;

}

tbody tr {
background: #fff;
}

tbody tr:hover {
background: rgb(237, 203, 165);
}
.opt table {
margin-top: 20px;
}
.popup {
color: #fff;
position: absolute;
top:50%;
left: 50%;
transform: translate(-50%,-50%);
background: #7d2ae8; 
padding:25px;
border-radius: 15px;
max-width: 380px;
width: 100%;
opacity: 0;
pointer-events: none;
}
.popup h2 {
text-align: center;
transform: translate(100%);
}
.popup :is(header, .icon, .field) {
display: flex;
align-items: center;
justify-content: space-between;
}
.content .field {
margin: 12px 0 -5px 0;
height: 45px;
border: 1px solid #e1e1e1;
border-radius:  4px;
padding: 0 5px;
}
.field i {
width: 50px;
text-align: center;
font-size: 18px;
}
.field input {
border: none;
outline: none;
height: 100%;
width: 100%;
font-size: 15px;

}
.field button {
padding: 5px 18px;
color: #fff;
border-radius: 4px;
cursor: pointer;
border: 2px solid transparent;
}
.popup table {
width: 100%;
}

</style>
