<script>
    export default{
        data(){
            return{
                    // 放入資料
                    list:[
                        {id:1,title:'事件1',isDone:false},
                        {id:2,title:'事件2',isDone:false},
                        {id:3,title:'事件3',isDone:false},
                        {id:4,title:'事件4',isDone:false},
                        {id:5,title:'事件5',isDone:false},
                        {id:6,title:'事件6',isDone:false},
                    ],
                    newItem:'',
                }
            },
            created() {
                // 從localStorage中取得資料
                const storedData = localStorage.getItem('list');
                if (storedData) {
                    // 將取得的資料轉換回物件格式
                    this.list = JSON.parse(storedData);
                }
            },
            watch:{ //使用 watch 屬性，用來監看某個值，值改變時就會執行
                list:{
                    handler(list){ //handler(newValue, oldValue){ 值改變時執行這裡的程式 }
                        localStorage.setItem('list',JSON.stringify(list))
                    },
                    deep:true //deep屬性：設定成 true 就能監看物件中的屬性是否改變
                }
            },
            computed:{
                // 待辦事項：過濾出是true的
                // 原理 → 檢查當前元素的isDone屬性是否為false（即未完成狀態）。如果回調函數返回true，則該元素將被保留在結果數組中；如果回調函數返回false，則該元素將被過濾（排除）
                todo(){
                    return this.list.filter(a => !a.isDone);
                },
                // 完成事項：過濾出是false的
                // 原理 → 檢查當前元素的isDone屬性是否為true（即已完成狀態）。如果回調函數返回true，則該元素將被保留在結果數組中；如果回調函數返回false，則該元素將被過濾（排除）
                done(){ 
                    return this.list.filter(a => a.isDone);
                }
            },
            methods:{
                // 設定刪除的條件
                removeItem(id){
                    const index = this.list.findIndex(item => item.id === id);
                    if (index !== -1) {
                        this.list.splice(index, 1);
                    }
                },
                addItem(){
                    const idNumber = this.list.length; // 將data()中list的數量長度放入變數中

                    const newItemObj = {
                        // id: (idNumber+1), // 設定新增的項目id為list的數量長度+1
                        id: this.list[this.list.length-1].id+1,
                        title: this.newItem, // 設定新增的項目title為data()中newItem的空字串
                        isDone: false, // 設定新增的項目isDone為false
                    };
                    console.log(newItemObj.id);
                    this.list.push(newItemObj); // 將新增的項目添加到list陣列中
                    this.newItem='';
                },
                editBtn(index) {
                    const editedItem = this.list[index];
                    const newTitle = prompt('請輸入修改後的標題', editedItem.title);
                    if (newTitle) {
                        editedItem.title = newTitle;
                    }
                },
            }
    }
</script>

<template>
    <div class="container mt-5">

<h1 class="text-center title mb-3">計畫清單</h1>

<div class="row">
    <div class="col-12 col-lg-6 box">
        <div class="todo card p-2">
            <h3 class="todo-title text-center py-2">待辦事項</h3>
            <div class="mb-2 position-relative">
                <input type="text" placeholder="請輸入欲新增的事項" class="w-100 p-2 purple" @keydown.enter="addItem" v-model.trim="newItem">
                <div @click="addItem" class="addBtn d-flex justify-content-center align-items-center"><i class="fa-solid fa-plus"></i></div>
            </div>
            <ul class="p-0">
                <li v-for="(b, index) in todo" :key="b.id" class="purple p-2 mb-2 d-flex align-items-center"> <!-- 抓取key值，來區分每個都是不同項目 -->
                    <input type="checkbox" v-model="b.isDone" class="checkbox-css">{{b.title}} 
                    <div @click="editBtn(index)" class="editBtn d-flex justify-content-center align-items-center"><i class="fa-solid fa-pen"></i></div>
                    <div @click="removeItem(b.id)" class="deleteBtn d-flex justify-content-center align-items-center p-2"><i class="fa-solid fa-xmark"></i></div> <!-- 綁定@click事件，並呼叫methods，帶入key值 -->
                </li>
            </ul>
        </div>
    </div>

    <div class="col-12 col-lg-6 box">
        <div class="done card p-2">
            <h3 class="done-title text-center py-2">完成事項</h3>
            <ul class="p-0">
                <li v-for="c in done" :key="c.id" class="yellow p-2 mb-2 d-flex align-items-center delete-line"> <!-- 抓取key值，來區分每個都是不同項目 -->
                    <input type="checkbox" v-model="c.isDone" class="checkbox-css">{{c.title}} 
                </li>
            </ul>
        </div>
    </div>
</div>

</div>
</template>

<style scoped>
/* ------------- 移除bootstrap預設樣式 ------------- */
.card{
    border-radius: 0;
    border: 0;
}

/* ------------- 大標 ------------- */
.title{
    color: gray;
    font-weight: 600;
}

/* ------------- li去除預設點點 ------------- */
li{
    list-style: none;
    position: relative;
}

/* ------------- 待辦事項 ------------- */
.todo-title{
    color: #fff;
    background: #975aad;
}
.purple{
    color: #975aad;
    font-weight: 600;
    font-size: 1.2rem;
    border: 1px solid;
}

/* ------------- 完成事項 ------------- */
.done-title{
    color: #fff;
    background: #eab32a;
}
.yellow{
    color: #eab32a;
    font-weight: 600;
    font-size: 1.2rem;
    border: 1px solid;
}

/* ------------- 桌機區塊靠上對齊 ------------- */
.box{
    position: relative;
}
@media screen and (min-width:768px){
    .todo{
        position: relative;
    }
    .done{
        position: relative;
    }
}
@media screen and (min-width:992px){
    .todo{
        position: absolute;
        top: 0;
        width: 100%;
    }
    .done{
        position: absolute;
        top: 0;
        width: 100%;
    }
}

/* ------------- checkbox ------------- */
.checkbox-css {
    -webkit-appearance: none;
    -moz-appearance: none;
    -ms-appearance: none;
    -o-appearance: none;
    appearance: none;
    position: relative;
    height: 20px;
    width: 20px;
    background: #ffffff;
    border: 1px solid gray;
    color: gray;
    cursor: pointer;
    display: inline-block;
    margin: 0 0.5rem;
    outline: none;
    position: relative;
    z-index: 1000;
    border-radius: 3px;
}
.checkbox-css:checked::before {
    width: 20px;
    height: 20px;
    content: '\f00c';
    font-size: 13px;
    font-weight:bold;
    display:flex;
    align-items:center;
    justify-content:center;
    font-family:'Font Awesome 5 Free';
}

/* ------------- 完成事項刪除線 ------------- */
.delete-line{
    position: relative;
}
.delete-line:after{
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 90%;
    height: 1px;
    background: #eab32a;
}

/* ------------- 刪除按鈕 ------------- */
.deleteBtn{
    background: #e31717;
    color: #fff;
    width: 25px;
    height: 25px;
    border-radius: 50px;
    position: absolute;
    top: 50%;
    right: 0;
    transform: translate(-50%, -50%);
    cursor: pointer;
}

/* ------------- 新增按鈕 ------------- */
.addBtn{
    background: #975aad;
    color: #fff;
    width: 25px;
    height: 25px;
    border-radius: 50px;
    position: absolute;
    top: 50%;
    right: 0;
    transform: translate(-50%, -50%);
    cursor: pointer;
}

/* ------------- 刪除按鈕 ------------- */
.editBtn{
    background: #975aad;
    color: #fff;
    width: 25px;
    height: 25px;
    font-size: 12px;
    border-radius: 50px;
    position: absolute;
    top: 50%;
    right: 4%;
    transform: translate(-100%, -50%);
    cursor: pointer;
}
</style>