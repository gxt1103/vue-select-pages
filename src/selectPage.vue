<template>
    <div class="select-vue">
        <div class="select-box" :class="{'select-disabled':disabled}" ref="selectPageRef" :style="styles" @click="open">
            <div class="select-tips" v-if="selectData.length == 0">{{tips}}</div>
            <!--选中内容展示-->
            <div class="single-row" v-else>
                <div class="scroll">
                    <div class="label-content">
                        <template v-if="radio">
                            {{selectData.length>0?selectData[0][prop.name]:''}}
                        </template>
                        <template v-else>
                            <div class="label-block" :style="{background:theme?theme:'#409eff'}" v-for="(list, index) in selectData.slice(0, showCount)" :key="index">
                                <span>{{ list[prop.name] }}</span>
                                <i class="el-icon-close" @click.stop="removeSelect(index)"></i>
                            </div>
                            <div class="label-block" :style="{background:theme?theme:'#409eff'}" v-if="selectData.length>showCount">
                                + {{selectData.length - showCount}}
                            </div>
                        </template>
                    </div>
                </div>
            </div>
            <!--关闭-->
            <i class="select-close" :class="{'select-expend': show}"></i>
            <div class="close-pop-page" v-if="selectData.length" @click="clearAll">x</div>
        </div>
        
        <!--展开内容-->
        <div class="select-body" ref="selectOptionRef" :class="{'hide': !show}" :style="dropStyle">
            <div class="select-search" v-if="filterable">
                <span class="search-icon"><img src="data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBzdGFuZGFsb25lPSJubyI/PjwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+PHN2ZyB0PSIxNjkzNDQ2NjE1MTg5IiBjbGFzcz0iaWNvbiIgdmlld0JveD0iMCAwIDEwMjQgMTAyNCIgdmVyc2lvbj0iMS4xIiB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHAtaWQ9IjQwMjgiIHdpZHRoPSIyMDAiIGhlaWdodD0iMjAwIiB4bWxuczp4bGluaz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayI+PHBhdGggZD0iTTUxMS45NzU4OTIgMTkxLjI5Njk0NWEyMy4yMzQ2NjEgMjMuMjM0NjYxIDAgMSAwIDAgNDYuOTM0MDE0IDIwMi4zMTU4MDggMjAyLjMxNTgwOCAwIDAgMSAyMDIuMzE1ODA4IDIwMi4zMTU4MDggMjMuMjM0NjYxIDIzLjIzNDY2MSAwIDAgMCA0Ni45MzQwMTUgMCAyNDkuMTkxNzM2IDI0OS4xOTE3MzYgMCAwIDAtMjQ5LjI0OTgyMy0yNDkuMjQ5ODIyeiIgZmlsbD0iIzUxNTE1MSIgcC1pZD0iNDAyOSI+PC9wYXRoPjxwYXRoIGQ9Ik0xMDA5LjMxMzgwNSA5NTguMzg5MjY4bC0xNzIuODA3Nzg5LTE3Mi44MDc3ODlhNDc1LjAzMjYzOCA0NzUuMDMyNjM4IDAgMSAwLTU0LjM2OTEwNiA1My41NTU4OTNsMTczLjIxNDM5NSAxNzMuMjE0Mzk2YTM4LjE2MjkzIDM4LjE2MjkzIDAgMSAwIDUzLjk2MjUtNTMuOTYyNXogbS01MzMuMTE5MjktOTAuMDM0MzFhMzkzLjA3MjM3MyAzOTMuMDcyMzczIDAgMSAxIDM5My45NDM2NzItMzkyLjA4NDkgMzkzLjA3MjM3MyAzOTMuMDcyMzczIDAgMCAxLTM5My45NDM2NzIgMzkyLjA4NDl6IiBmaWxsPSIjNTE1MTUxIiBwLWlkPSI0MDMwIj48L3BhdGg+PC9zdmc+"></span>
                <input type="text" ref="filterInput" :placeholder="searchTips" v-model="keyword" @input="filters">
            </div>
            <div class="scroll-body" v-if="optionData.length>0">
                <div class="select-option" :class="{'selected': radio && selectIds.indexOf(list[prop.value]) != -1}" v-for="(list, index) in optionData.slice(isPage && !remote?(page-1)*pageSize:0, (isPage && !remote?(page*pageSize):optionData.length))" :key="index" @click="selectSingle(list)">
                    <div class="select-option-content" v-if="radio">
                        <slot name="title" v-bind:row="list">{{ texts(list) }}</slot>
                    </div>
                    <label class="select-checkbox" :style="{'background-color':theme,'border-color':theme}" v-else>
                        <span class="select-checkbox-input" :class="{'is-checked': selectIds.indexOf(list[prop.value]) != -1}"></span> <slot name="title" v-bind:row="list">{{ texts(list) }}</slot>
                    </label>
                </div>
                <div class="select-loading" v-if="loading">
                    <div class="canvasBox">
                        <div class="spinnerOneBox spinnerMaxBox">
                        <div class="spinnerOneBox spinnerMidBox">
                            <div class="spinnerOneBox spinnerMinBox"></div>
                        </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="select-none" v-else>
                暂无数据
            </div>
            <div class="select-page" v-if="isPage">
                <span :class="{'not-disabled': page == 1}" @click="prev">上一页</span>
                <span>{{page}}/{{totalPage}}</span>
                <span :class="{'not-disabled': page == totalPage}" @click="next">下一页</span>
            </div>
        </div>
    </div>
    

</template>
<script>
export default {
    props:{
        data:{
            type: Array,
            default:()=>{return []}
        },
        filterable: {
            type: Boolean,
            default: true
        },
        searchTips:{
            type: String,
            default: '输入进行搜索'
        },
        tips: {
            type: String,
            default: '请选择'
        },
        isPage:{//是否分页
            type: Boolean,
            default: false
        },
        pageSize:{
            type: Number,
            default: 10
        },
        radio:{//是否单选
            type: Boolean,
            default: false
        },
        clickClose: { //是否点击后关闭
            type: Boolean,
            default: false
        },
        prop:{
            type: Object,
            default: ()=>{return {
                name: 'name',
                value: 'value'
            }}
        },
        showTitle:{ //显示那些标题组合
            type: Array,
            default:()=>{return []}
        },
        showCount: {
            type: Number,
            default: 1
        },
        remote:{ //是否支持远程调用
            type: Boolean,
            default: false
        },
        remoteMethod: Function,
        initValue: [Array, Number, String], //初始化选中的数据, 需要在data中存在
        styles:{ //组件显示style
            type:Object,
            default: ()=>{ return {}}
        },
        theme:{ //主题配置
            type:String,
            default: ''
        },
        disabled:{
            type: Boolean,
            default: false
        }
    },
    watch:{
        data: {
            handler(val){
                this.initData(val, 'watch')
            },
            deep: true
        },
        initValue(){
            this.setValue()
        }
    },
    data(){
        return {
            keyword: '',
            page: 1,
            totalPage: 1,
            show: false,
            selectData: [],//选中的数据
            selectIds:[], //选中的id
            sourceData: [],
            optionData: [], //下拉数据
            dropStyle: {},
            loading: false,
            initLeft: '',
            isComposing: false,
            positionY: 0
        }
    },
    mounted(){
        if(this.data.length) this.initData(this.data);
        window.addEventListener('click', (e)=>{
            if(!this.$el.contains(e.target)){
                this.closed();
            }
        })
        this.loadData();
        this.$nextTick(() => {
            if(this.$refs.filterInput) {
                this.$refs.filterInput.addEventListener('compositionstart', e => {
                    this.isComposing = true;
                })
                this.$refs.filterInput.addEventListener('compositionend', e => {
                    this.isComposing = false;
                    this.filters();
                })
            }
        })
    },
    methods:{
        setValue(value){
            let initValue = this.initValue;
            if(value) initValue = value;
            if(Object.prototype.toString.call(initValue) === '[object Array]'){
                if(initValue.length>0 && initValue[0][this.prop.value]){ //如果传入数组对象直接显示
                    this.selectIds = [];
                    initValue.forEach(d => {
                        this.selectIds.push(d[this.prop.value]);
                    })
                    this.selectData = [...initValue];
                } else {
                    this.selectIds = initValue;
                    this.selectData = [];
                    this.optionData.forEach(d => {
                        if(initValue.indexOf(d[this.prop.value]) !== -1){
                            this.selectData.push(d);
                        }
                    })
                }
                
            } else {
                if(initValue && this.selectIds.indexOf(initValue) == -1){ //设置初始值
                    this.selectIds = [initValue];
                    this.selectData = [];
                    this.optionData.forEach(d => {
                        if(initValue === d[this.prop.value]){
                            this.selectData.push(d);
                        }
                    })
                }
            }
        },
        initData(data, type){
            this.sourceData = JSON.parse(JSON.stringify(data));
            this.optionData = JSON.parse(JSON.stringify(data));
            this.totalPage = Math.ceil(this.optionData.length/this.pageSize);
            this.setValue();
        },
        filters(){
            if(this.isComposing) return;
            if (this.remote && typeof this.remoteMethod === 'function') {
                this.page = 1;
                this.loadData();
            } else {
                this.page = 1;
                const newData = JSON.parse(JSON.stringify(this.sourceData));
                let filterData = [];
                if(!this.keyword) {
                    filterData = newData;
                } else {
                    if(this.showTitle.length){ //针对显示多个标题
                    
                        this.showTitle.forEach(s =>{
                            let newFilter = newData.filter(d => d[s].toLowerCase().indexOf(this.keyword.toLowerCase()) > -1);
                            newFilter.forEach(d =>{
                                let index = filterData.filter(f => f[this.prop.value] == d[this.prop.value])
                                if(index == -1) filterData.push(d);
                            })
                        })
                    } else {
                        filterData = newData.filter(d => d[this.prop.name].toLowerCase().indexOf(this.keyword.toLowerCase()) > -1);
                    }
                }
                
                this.optionData = filterData;
                this.totalPage = Math.ceil(this.optionData.length/this.pageSize);
            }
        },
        texts(list){
            let txts = '';
            if(this.showTitle.length){
                this.showTitle.forEach((d, index) => {
                    if(index == 0) {
                        txts += list[d];
                    } else {
                        txts += '--' + list[d];
                    }
                })
            } else {
                txts = list[this.prop.name]
            }
            return txts
        },
        open(e){
            if(this.disabled) return;
            this.show = !this.show;
            if(!this.show) this.keyword = '';
            this.$nextTick(()=>{
                this.positionY = e.clientY;
                this.getPosition();
                if(this.show) this.$refs.filterInput.focus();
            })
        },
        closed(){
            this.show = false;
            this.keyword = '';
        },
        removeSelect(index){
            if(this.disabled) return;
            this.selectIds.splice(index, 1);
            this.selectData.splice(index, 1);
            this.$emit('selectChange', this.radio?this.selectIds[0]:this.selectIds)
        },
        selectSingle(list){
            if(this.radio){
                if(list[this.prop.value] == this.selectIds[0]){
                    this.selectIds = [];
                    this.selectData = [];
                }else {
                    this.selectIds = [list[this.prop.value]];
                    this.selectData = [list];
                }
            } else {
                let index = this.selectIds.findIndex(d => d == list[this.prop.value]);
                if(index == -1){
                    this.selectIds.push(list[this.prop.value]);
                    this.selectData.push(list);
                } else {
                    this.selectIds.splice(index, 1);
                    this.selectData.splice(index, 1);
                }
            }
            if(this.clickClose || this.radio) this.closed();
            this.$emit('selectChange', this.radio?this.selectIds[0]:this.selectIds)
        },
        getSelect(){
            let selected = this.selectIds;
            if(this.radio){
                selected = this.selectIds[0];
            }
            return selected;
        },
        getSelectArray(){
            let selectedArray = this.selectData;
            if(this.radio){
                selectedArray = this.selectData[0];
            }
            return selectedArray;
        },
        clearSelect(){
            this.selectIds = [];
            this.selectData = [];
        },
        prev(){
            if(this.page>1) this.page--;
            this.loadData();
        },
        next(){
            if(this.page<this.totalPage) this.page++;
            this.loadData();
        },
        getPosition(){ //边界处理
            if(!this.$refs.selectOptionRef && !this.$refs.selectPageRef) return
            if(this.positionY+this.$refs.selectOptionRef.clientHeight > document.body.clientHeight){
                this.dropStyle = {
                    marginTop: (-(this.$refs.selectPageRef.clientHeight + this.$refs.selectOptionRef.clientHeight + 10))+'px',
                    width: this.$refs.selectPageRef.clientWidth+'px'
                }
            } else{
                this.dropStyle = {
                    marginTop: '10px',
                    width: this.$refs.selectPageRef.clientWidth+'px'
                }
            }
            let popWidth = this.$refs.selectOptionRef.offsetWidth;
            let selectWidth = this.$refs.selectPageRef.offsetWidth;
            let screenWidth = document.body.clientWidth;
            if(!this.initLeft) this.initLeft = this.$refs.selectOptionRef.offsetLeft;
            if(this.initLeft + popWidth > screenWidth){
                this.$set(this.dropStyle, 'right', screenWidth - selectWidth - this.initLeft + 'px');
            } else {
                this.$set(this.dropStyle, 'right', 'auto');
            }
        },
        loadData(){ //远程数据加载
            if (this.remote && typeof this.remoteMethod === 'function') {
                this.loading = true;
                this.remoteMethod(this.keyword, (data, totalPage)=>{
                    this.optionData = data;
                    this.totalPage = totalPage?totalPage:1;
                    this.loading = false;
                    this.$nextTick(()=> {
                        this.getPosition();
                    })
                }, this.page)
            } else {
                this.$nextTick(()=> {
                    this.getPosition();
                })
            }
        },
        clearAll(){
            this.selectIds = [];
            this.selectData = [];
            this.$emit('selectChange', this.radio?this.selectIds[0]:this.selectIds)
        }
    }
}
</script>
<style lang="scss" scoped>
    .select-vue{
        .select-box{
            background: #fff;
            border: 1px solid #E6E6E6;
            cursor: pointer;
            display: block;
            font-size: 12px;
            height: 33px;
            line-height: 33px;
            min-height: 33px;
            outline: none;
            position: relative;
            text-overflow: ellipsis;
            width:200px;
            &:hover{
                .close-pop-page{
                    display: inline-block;
                }
            }
        }
        .select-disabled{
            cursor: not-allowed;
        }
       
        .select-tips{
            color: #999;
            padding: 0 10px;
            position: absolute;
            display: flex;
            height: 100%;
            align-items: center;
        }
        .single-row{
            position: absolute;
            top: 0;
            bottom: 0px;
            left: 0px;
            right: 20px;
            overflow: auto hidden;
            .scroll{
                overflow-y: hidden;
                .label-content{
                    display: flex;
                    flex-wrap: nowrap;
                    line-height: 30px;
                    padding:1px 0 3px 5px;
                }
                .label-block{
                    align-items: baseline;
                    background: rgb(64, 158, 255);
                    border-radius: 3px;
                    color: #FFF;
                    display: flex;
                    flex: 1;
                    height: 26px;
                    line-height: 26px;
                    margin: 2px 5px 2px 0;
                    max-width: max-content;
                    position: relative;
                    padding: 0px 5px;
                    width: 50%;
                    span{
                        display: inline-block;
                        color: #FFF;
                        overflow: hidden;
                        text-overflow: ellipsis;
                        white-space: nowrap;
                    }
                    i{
                        color: #FFF;
                        font-size: 12px;
                        cursor: pointer;
                        display: flex; 
                    }
                }
            }
        }
        .select-disabled{
            .single-row{
                .scroll{
                    .label-block{
                        background:rgb(83, 169, 255);
                    }
                }
            }
        }
        .select-close{
            color:#999;
            display: inline-block;
            font-size: 14px;
            height:12px;
            overflow: hidden;
            position: absolute;
            right: 7px;
            top: 50%;
            margin-top: -6px;
            cursor: pointer;
            transition: all 0.3s;
            -webkit-transition: all 0.3s;
            width:12px;
            &::after{
                border-left:1px solid #999;
                border-bottom: 1px solid #999;
                content:'';
                height:8px;
                left:50%;
                position: absolute;
                transform: translate(-60%) rotate(-45deg);
                width:8px;
            }
        }
        .close-pop-page{
            background: #fff;
            border: 1px solid #bbb;
            border-radius: 100%;
            color: #bbb;
            cursor: pointer;
            display: none;
            font-size: 10px;
            line-height: 12px;
            position: absolute;
            right: 7px;
            top: 5px;
            text-align: center;
            width: 12px;
        }
        .select-expend{
            margin-top: -4px;
            transform: rotate(180deg);
        }
        .hide{
            display: none;
        }
        .select-body{
            position: fixed;
            padding: 5px 0;
            z-index: 999;
            min-width: fit-content;
            border: 1px solid #E6E6E6;
            background-color: #fff;
            border-radius: 2px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.12);
            animation-name: xm-upbit;
            animation-duration: 0.3s;
            animation-fill-mode: both;
            .select-search{
                background-color: #FFF !important;
                cursor: pointer;
                margin-bottom: 5px;
                position: relative;
                padding: 0 10px;
                input{
                    border:0;
                    border-bottom:1px solid #DCDFE6;
                    box-shadow: none;
                    box-sizing: border-box;
                    font-size: 12px;
                    height: 30px;
                    line-height: 30px;
                    outline: none;
                    padding:5px 5px 5px 15px;
                    width:100%;
                }
                .search-icon{
                    line-height:initial;
                    position: absolute;
                    left:10px;
                    top:5px;
                    img{
                        height:11px;
                        width:11px;
                        vertical-align: middle;
                    }
                }
            }
            .scroll-body{
                max-height: 200px;
                overflow-x: hidden;
                overflow-y: auto;
                position: relative;
                .select-option{
                    display: flex;
                    align-items: center;
                    font-size: 12px;
                    line-height: 32px;
                    position: relative;
                    padding: 0 10px;
                    cursor: pointer;
                    .select-option-content{
                        display: flex;
                        position: relative;
                        // overflow: hidden;
                        white-space: nowrap;
                        // text-overflow: ellipsis;
                        color: #666;
                        width: calc(100% - 20px);
                    }
                    .select-checkbox{
                        color: #666;
                        cursor: pointer;
                        display: inline-block;
                        font-size: 12px;
                        position: relative;
                        font-weight: 400;
                        white-space: nowrap;
                        user-select: none;
                        .select-checkbox-input{
                            background-color: #fff;
                            box-sizing: border-box;
                            border: 1px solid #dcdfe6;
                            cursor: pointer;
                            display: inline-block;
                            height:14px;
                            line-height: 1;
                            margin-right:5px;
                            margin-top:-3px;
                            outline: none;
                            position: relative;
                            vertical-align: middle;
                            width: 14px;
                            transition:border-color .25s cubic-bezier(.71,-.46,.29,1.46),background-color .25s cubic-bezier(.71,-.46,.29,1.46);
                            &::after{
                                box-sizing: content-box;
                                content: "";
                                border: 1px solid #fff;
                                border-left: 0;
                                border-top: 0;
                                height: 7px;
                                left: 4px;
                                position: absolute;
                                top: 1px;
                                transform: rotate(45deg) scaleY(0);
                                width: 3px;
                                transition: transform .15s ease-in .05s;
                                transform-origin: center;
                            }
                        }
                        .is-checked{
                            background-color: #409eff;
                            border-color: #409eff;
                            &::after{
                                transform: rotate(45deg) scaleY(1);
                            }
                        }
                    }
                    
                    &:hover{
                        background: rgb(220, 223, 230);
                    }
                    &.selected{
                        background: #409eff;
                        .select-option-content{
                            color: #fff;
                        }
                    }
                }
                .select-loading{
                    align-items: center;
                    background: rgba(0, 0, 0, 0.4);
                    bottom:34px;
                    display: flex;
                    left:0;
                    justify-content: center;
                    position: fixed;
                    right:0;
                    top:40px;
                    .canvasBox {
                        align-items: center;
                        border-radius: 50%;
                        display: flex;
                        justify-content: center;
                        margin: 1em;
                        width: 5em;
                        height: 5em;
                        .spinnerOneBox {
                            align-items: center;
                            border: 0.3em solid transparent;
                            border-top: 0.3em solid #fff;
                            border-right: 0.3em solid #fff;
                            border-radius: 100%;
                            display: flex;
                            justify-content: center;
                        }
                        .spinnerMaxBox {
                            animation: spinnerOne 3s linear infinite;
                            height: 3em;
                            width: 3em;
                            .spinnerMidBox {
                                animation: spinnerOne 5s linear infinite;
                                height: 2.4em;
                                width: 2.4em;
                                .spinnerMinBox {
                                    animation: spinnerOne 5s linear infinite;
                                    height: 1.8em;
                                    width: 1.8em;
                                }
                            }
                        }
                    }
                }
                &::-webkit-scrollbar{
                    width: 8px;
                }
                &::-webkit-scrollbar-thumb{
                    border-radius: 2em;
                    background: #d5d5d5;
                }
                &::-webkit-scrollbar-track{
                    border-radius: 2em;
                    background: #fff;
                }
            }
            .select-none{
                color: #999;
                line-height: 100px;
                text-align: center;
            }
            .select-page{
                padding: 0 10px;
                display: flex;
                margin-top: 5px;
                span{
                    cursor: pointer;
                    display: flex;
                    height: 26px;
                    line-height: 26px;
                    flex: auto;
                    justify-content: center;
                    vertical-align: middle;
                    margin: 0 -1px 0 0;
                    background-color: #fff;
                    color: #333;
                    font-size: 12px;
                    border: 1px solid #e2e2e2;
                    flex-wrap: nowrap;
                    width: 100%;
                    overflow: hidden;
                    min-width: 50px;
                    user-select: none;
                }
                .not-disabled{
                    cursor: no-drop;
                    color: rgb(210, 210, 210); 
                }
            }
        }
        @-webkit-keyframes xm-upbit {
            from {
                -webkit-transform: translate3d(0, 30px, 0);
                opacity: 0.3;
            }
            to {
                -webkit-transform: translate3d(0, 0, 0);
                opacity: 1;
            }
        }
        @keyframes xm-upbit {
            from {
                transform: translate3d(0, 30px, 0);
                opacity: 0.3;
            }
            to {
                transform: translate3d(0, 0, 0);
                opacity: 1;
            }
        }
        @keyframes spinnerOne {
            0% {
                transform: rotate(0deg);
            }
            100% {
                transform: rotate(360deg);
            }
        }
    }
</style>