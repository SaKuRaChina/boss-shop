<template>
	
	<div class='chanceCenterWrap'>
		<h3 class="pageTitle">
			<span>项目问答</span>
			<em>Project Question and Answer</em>
		</h3>
		
		<div class="pageTableBox">
			<div class="pageSearch">
				<div class="eachSearch">
					<el-input size='small' placeholder="搜索关键字" v-model='keyWord'></el-input>
				</div>
				<button class="commonBtn" @click='searchPage'>查询</button>
			</div>
			<table class="pageTable" border='0' cellpadding="0" cellspacing="0">
				<thead>
					<tr>
						<th>用户</th>
						<th>评论内容</th>
						<th>商机</th>
						<th>显示</th>
						<th>评论时间</th>
						<th>状态</th>
						<th>操作</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for='item in pageArr' :key='item.id'>
						<td>{{ item.userName }}</td>
						<td>{{ item.comment }}</td>
						<td>{{ item.businessName }}</td>
						<td class="tableState" :class="[item.isShow == 0 ? 'stateDeny' : 'stateSure']">
							<p><span>是</span><span>否</span></p>
						</td>
						<td>{{ item.createTime }}</td>
						<td>{{ item.isReply == 0 ? '未回复' : '已回复'}}</td>
						<td>
							<button :disabled="item.isReply == 1" type="button">回复</button>
							<button type="button">隐藏</button>
							<button type="button">删除</button>
						</td>
					</tr>
					<tr v-if='pageArr.length == 0'>
						<td colspan="10">暂无数据...</td>
					</tr>
				</tbody>
			</table>
			<div class="pagination">
				<el-pagination
				    @current-change="changeSize"
				    :current-page="paginationObj.currentPage"
				    :page-size="paginationObj.pageSize"
				    layout="total, prev, pager, next, jumper"
				    :total="paginationObj.totalCount">
			    </el-pagination>
			</div>
		</div>
		
		<Replay :replayShow='replayShow' :id='itemId'></Replay>


	</div>

</template>


<script>

	import Replay from '../../jumper/replay'

	export default{
		name:'ProgramAsk',
		data(){
			return{
				replayShow:true,
				keyWord:'',
				pageArr:[],
				paginationObj:{

				},
				itemId:0
			}
		},
		components:{
			Replay
		},
		mounted(){
			this.initPage()
		},
		methods:{
			initPage(arr = {}){
				var that = this
				console.log(arr)
				$.ajax({
					type:'post',
					url:that.Global.router + '/business/getQAList',
					data:arr,
					xhrFields:{withCredentials:true},
					success(res){
						console.log(res)

						if(res.code === "0000"){

							that.pageArr = [];
							for(var i=0; i<res.result.detail.length; i++){
								that.pageArr.push(res.result.detail[i])
							}


							that.paginationObj = {
								currentPage:res.result.currentPage,
								nextPage:res.result.nextPage,
								pageSize:res.result.pageSize,
								prePage:res.result.prePage,
								totalCount:res.result.totalCount,
								totalPage:res.result.totalPage
							}

						}

					},
					error(){}
				})

			},
			changeSize(val){
				//console.log(val)
				var sendArr = {
					param:this.keyWord,
					currentPage:val
				}

				//console.log(sendArr)
				this.initPage(sendArr)
			},
			searchPage(){
				var sendArr = {
					param:this.keyWord,
					currentPage:1
				}
				console.log(this.sendArr)
				this.initPage(sendArr)
			}

		}
	}

</script>


<style>

</style>