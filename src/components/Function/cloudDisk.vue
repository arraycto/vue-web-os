<template>
	<div>
		<Modal width="900" id="modalView" :fullscreen="fullscreen" v-model="isCloudDiskViewBool" footer-hide :draggable="draggable" :closable="false" >
			<div slot="header" style="width: 100%;display: flex;align-items: center;justify-content:space-between;">
				<div style="width: 50%;display: flex;align-items: center;">
					<Icon size="25" :type="menu.icon" style="margin: 0.5rem;" />
					<span style="color: white;"><b>{{menu.permissionsname}}</b></span>
				</div>
				<div style="width: 90px;display: flex;align-items: center;justify-content: space-between">
					<Button size="large" type="text" ghost @click="mini()" icon="md-remove"></Button>
					<Button size="large" type="text" ghost v-show="!fullscreen" @click="big()" icon="ios-expand"></Button>
					<Button size="large" type="text" ghost v-show="fullscreen" @click="small()" icon="md-expand"></Button>
					<Button size="large" type="text" ghost @click="closeView()" icon="md-close"></Button>
				</div>
			</div>
			<!-- 主体 -->
			<div style="width: 100%;display: flex;flex-direction: column;align-items: center;">
				<div class="BTL">
					<ButtonGroup>
						<Button @click="openMenu(item,index)" :ghost="indexButton==index?false:true"  v-for="(item,index) in permissionsList"  :type="item.color">
							<Icon :type="item.icon" />
							{{item.permissionsname}}
						</Button>
					</ButtonGroup>
				</div>
				<component @routerTo="openMenu2(arguments)"  :is="allComps[permissionsItem.permissionsenglish]" :menu="permissionsItem"></component>
			</div>
		</Modal>
	</div>
</template>

<script>
	import allComps from '../Menu/cloudDisk/index.js'
	export default {
		name: "cloudDisk",
		props: {
			menu: '',
		},
		data() {
			return {
				// 组件集合
				allComps: allComps,
				//当前组件
				permissionsItem:'',
				//全屏
				fullscreen:true,
				//移动
				draggable:false,
				//当前菜单按钮
				indexButton:null,
				//菜单
				permissionsList:[],
			}
		},
		computed: {
			//判断是否展示该面板
			isCloudDiskViewBool: {
				get() {
					return this.$store.state.control.cloudDiskViewBool;
				},
				set(v) {
					this.$store.commit("setFalseCloudDiskVB");
				}
			},
		},
		methods: {
			//子组件调用打开菜单
			openMenu2(values){
				this.indexButton=values[1];
				this.permissionsItem=values[0];
			},
			//打开菜单
			openMenu(item,index){
				this.indexButton=index;
				this.permissionsItem=item;
			},
			//关闭对话框
			closeView(){
				this.$store.commit("setFalseCloudDiskVB");
				this.$store.commit("deleteTaskList",this.menu);
			},
			//缩小对话框
			small(){
				this.fullscreen=false
				this.draggable=true
			},
			//放大对话框
			big(){
				this.fullscreen=true
				this.draggable=false
			},
			//最小化
			mini(){
				this.$store.commit("setFalseCloudDiskVB");
			},
			//查询所拥有的菜单菜单
			getMenuList() {
				let permissionsList = JSON.parse(localStorage.getItem("permissionsList"));
				this.permissionsList=[];
				for(var i=0;i<permissionsList.length;i++){
					if (permissionsList[i].type==2&&permissionsList[i].parentid==this.menu.id) {
						this.permissionsList.push(permissionsList[i]);
					}
				}
			}
		},
		mounted() {
			this.getMenuList();
		}
	}
</script>

<style scoped="scoped">
	.BTL {
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}
</style>
<style>
	#modalView .ivu-modal-header {
		background-color: #348DC1;
		padding: 5px;
	}
</style>
