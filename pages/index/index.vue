<template>
	<view class="content">
		<!-- 状态栏 -->
		<view v-if="list.length !==0" class="todo-header">
			<!-- 状态栏的左侧 -->
			<view class="todo-header_left">
					<text class="active-text">{{text}}</text>
					<text>{{listData.length}}条</text>
			</view>
			<!-- 状态栏的右侧 -->
			<view class="todo-header_right">
				  <view class="todo-header_right-item" @click="tab(0)" :class="{'active-tab':activeIndex===0}">全部</view>
				  <view class="todo-header_right-item" @click="tab(1)" :class="{'active-tab':activeIndex===1}">待办</view>
				  <view class="todo-header_right-item" @click="tab(2)" :class="{'active-tab':activeIndex===2}">已完成</view>
			</view>
		</view>
		<!-- 没有数据的状态	 -->
		<view v-if="list.length===0" class="default"> 
			<view class="default-info">
				<view class="default-info_text">
					您还没有创建任何待办事项
				</view>
				<view class="default-info_text">点击下方➕号创建一个吧</view>
			</view>
		</view>
		<view  v-else class="todo-content">
			<view class="todo-list" v-for="(item,index) in listData" :key="index" @click="finish(item.id)" :class="{'todo--finish':item.checked}">
				<view class="todo-list_checkbox">
					<view class="checkbox">
						
					</view>
				</view>
				<view class="todo-list_content">
					{{item.content}}
				</view>
			</view>
		</view>
		<!-- 创建按钮 -->
		<view class="create-todo" @click="create" :class="{'create-todo-active':textShow}">
			<text class="iconfont icon-hao"></text>
		</view>
		<!-- 输入框 -->
		<view class="create-content" v-if="active" :class="{'create-show':textShow}">
			<view class="create-content-box">
				<!-- input输入框 -->
				<view class="create-input">
					<input type="text"  v-model="value" placeholder="请输入您要创建的todo">
				</view>
				<!-- 发布按钮 -->
				<view class="create-button" @click="add">
					创建
				</view>
			</view>	
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello',
				active:false,
				value:'',
				list:[],
				activeIndex:0,
				text:'',
				textShow:false
			}
		},
		computed:{
			listData(){
				// 复制拷贝
				let list = JSON.parse(JSON.stringify(this.list))
				let newList = []
				// 全部
				if(this.activeIndex==0){
					this.text = '全部'
					return list;
					
				}
				// 待完成
				if(this.activeIndex==1){
					list.forEach((item)=>{
						if(!item.checked){
							newList.push(item);
						}
					})
					this.text = '待办';
					return newList;
				}
				// 已完成
				if(this.activeIndex==2){
					list.forEach((item)=>{
						if(item.checked){
							newList.push(item);
						}
					})
					this.text = '已完成';
					return newList;
					return []
				}
			}
		},
		onLoad() {

		},
		methods: {
			// 点击+号
			create(){
				if(this.active){
					this.close()
				}else{
					this.open();
				}	
			},
			// 打开动画
			open(){
				this.active=true;
				this.$nextTick(function(){
					setTimeout(()=>{
						this.textShow = true;
					},50)	
				})
			},
			// 关闭动画
			close(){
				this.textShow = false;
				this.$nextTick(()=>{
					setTimeout(()=>{
						this.active = false;
					},350)
				})
			},
			// 发布
			add(){
				console.log(this.value);
				if(this.value===''){
					uni.showToast({
						title:"请输入内容",
						icon:'none'
					})
					return
				}
				this.list.unshift({
					id:'id'+new Date().getTime(),
					content:this.value,
					checked:false
				});
				this.value='';
				// this.active=false;
				this.close();
			},
			//点击列表触发
			finish(id){
				let index = this.list.findIndex((item)=>item.id === id)
				console.log(this.list[index]);
				this.list[index].checked= !this.list[index].checked;
			},
			tab(index){
				this.activeIndex = index;
			}
		}
	}
</script>

<style>
	@import "../../common/icon.css";
	.todo-header{
		position: fixed;
		top:0;
		left:0;
		width: 100%;
		z-index: 99;
		box-sizing: border-box;
		display: flex;
		height: 45px;
		box-shadow: -1px 1px 5px 0 rgba(0,0,0,.1 );
		background-color: #fff;
		align-items: center;
		color: #333333;
		font-size: 12px;
		padding: 0 15px;
	}
	.todo-header_left{
		width: 100%;
	}
	.active-text{
		color: #279ABF;
		font-size: 14px;
		padding-right: 10px;
	}
	.todo-header_right{
		flex-shrink: 0;
		display: flex;
	}
	.todo-header_right-item{
		padding: 0 5px;
	}
	.active-tab{
		color:#279abf	
	}
	.todo-content{
		position: relative;
		padding-top: 50px;
		padding-bottom: 100px;
	}
	.todo-list{
		display: flex;
		align-items: center;
		position: relative;
		padding: 15px;
		margin: 15px;
		border-radius: 10px;
		font-size: 14px;
		color:#0c3854;
		background: #cfebfd;
		box-shadow: -1px 1px 5px 1px rgba(0,0,0,.1),
		-1px 2px 1px 0 rgba(255,255,255) inset;
		overflow: hidden;
	}
	.todo-list:after{
		position: absolute;
		content:'';
		top:0;
		left:0;
		bottom: 0;
		width: 5px;
		background: #91d1e8;
	}
	.todo-list_checkbox{
		padding-right: 15px;
	}
	.checkbox{
		width: 20px;
		height: 20px;
		border-radius:50%;
		background: #fff;
		box-shadow: 0 0 5px 1px rgba(0,0,0,.1);
	}
	.todo--finish .checkbox{
		position: relative;
		background: #eee;
	}
	.todo--finish .checkbox:after{
		content: "";
		position: absolute;
		width: 10px;
		height: 10px;
		top:0;
		left: 0;
		right: 0;
		bottom: 0;
		margin: auto;
		border-radius: 50%;
		background: #CFEBFD;
		box-shadow: 0 0 2px 0 rgba(0,0,0,.2) inset;
	}
	.todo--finish .todo-list_content{
		color: #999;
	}
	.todo--finish.todo-list:before{
		content: "";
		position: absolute;
		top:0;
		bottom: 0;
		left: 0;
		right: 0;
		height: 2px;
		margin: auto 0;
		background: #bdcdd8;
	}
	.todo--finish.todo-list:after{
		background: #ccc;
	}
	.create-todo{
		display: flex;
		justify-content: center;
		align-items: center;
		position: fixed;
		bottom: 20px;
		left: 0;
		right: 0;
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background-color: #deeff5;
		margin: 0 auto;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,.1),
		-1px 1px 1px 0 rgba(255,255,255) inset
		;
	}
	.icon-hao{
		font-size: 30px;
		color:#add8e6;
	}
	.create-content{
		position: fixed;
		bottom: 95px;
		left: 20px;
		right: 20px;
		transition: all 0.3s;
		opacity: 0;
		transform: scale(0) translateY(200%);
	}
	.create-show{
		opacity: 1;
		transform: scale(1) translateY(0);
	}
	.create-content-box{
		display: flex;
		align-items: center;
		padding: 0 15px;
		padding-right: 0;
		border-radius: 50px;
		background: #DEEFF5;
		box-shadow: -1px 1px 5px 2px rgba(0,0,0,.1),
		-1px 1px 1px  0 rgba(255,255,255) inset;
		z-index: 0;
	}
	.create-input{
		width: 100%;
		padding-right: 15px;
		color: #ADD8E6;
	}
	.create-button{
		display: flex;
		justify-content: center;
		align-items: center;
		flex-shrink: 0;
		width: 80px;
		height: 50px;
		border-radius: 50px;
		font-size: 16px; 
		color: #88d4ec;
		box-shadow: -2px 0px 2px 1px rgba(0,0,0,.1);
	}
	.create-content:after{
		content: "";
		position: absolute;
		right: 0;
		left: 0;
		bottom:-8px;
		margin: 0 auto;
		width: 20px;
		height: 20px;
		background: #DEEFF5;
		transform: rotate(45deg);
		box-shadow:1px 1px 5px 2px rgba(0,0,0,.1) ;
		z-index: -1;
	}
	.create-content-box:after{
		content: "";
		position: absolute;
		right: 0;
		left: 0;
		bottom:-8px;
		margin: 0 auto;
		width: 20px;
		height: 20px;
		background: #DEEFF5;
		transform: rotate(45deg);	
	}
	.default{
		padding-top: 100px;
	}
	.default-info_text{
		text-align: center;
		color: #ccc;
		font-size: 14px;
	}
	.icon-hao{
		transition:transform 0.5s;
	}
	.create-todo-active{
		transform: rotate(135deg);
	}
	
</style>
