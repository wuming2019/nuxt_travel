<template>
    <div class="main">
        <div class="air-column">
            <h2>乘机人</h2>
            <el-form class="member-info">
                <div 
				class="member-info-item" 
				v-for="(item,index) in users"
				:key="index">

                    <el-form-item label="乘机人类型">
                        <el-input placeholder="姓名" 
						class="input-with-select"
						v-model="users[index].username">
                            <el-select 
                            slot="prepend" 
                            value="1" 
                            placeholder="请选择">
                                <el-option label="成人" value="1"></el-option>
                            </el-select>
                        </el-input>
                    </el-form-item>

                    <el-form-item label="证件类型">
                        <el-input 
                        placeholder="证件号码"  
						class="input-with-select"
						v-model="users[index].id">
                            <el-select 
                            slot="prepend" 
                            value="1"           
                            placeholder="请选择">
                                <el-option label="身份证" value="1" :checked="true"></el-option>
                            </el-select>
                        </el-input>
                    </el-form-item>

                    <span class="delete-user" @click="handleDeleteUser(index)">-</span>
                </div>
            </el-form>

            <el-button class="add-member" type='primary' @click="handleAddUsers">添加乘机人</el-button>
        </div>

        <div class="air-column">
            <h2>保险</h2>
            <div>
                <div 
				class="insurance-item"
				v-for="(item,index) in infoData.insurances"
				:key="index">
                    <el-checkbox 
                    :label="`${item.type}：￥${item.price}/份×1  最高赔付${item.compensation}`" 
                    border
					@change="handleChange(item.id)">
                    </el-checkbox> 
                </div>
            </div>
        </div>

        <div class="air-column">
            <h2>联系人</h2>
            <div class="contact">
                <el-form label-width="60px">
                    <el-form-item label="姓名">
                        <el-input v-model="contactName"></el-input>
                    </el-form-item>

                    <el-form-item label="手机">
                        <el-input placeholder="请输入内容" v-model="contactPhone">
                            <template slot="append">
                            <el-button @click="handleSendCaptcha">发送验证码</el-button>
                            </template>
                        </el-input>
                    </el-form-item>

                    <el-form-item label="验证码">
                        <el-input v-model="captcha"></el-input>
                    </el-form-item>
                </el-form>   
                <el-button type="warning" class="submit" @click="handleSubmit">提交订单</el-button>
            </div>
        </div>

        <!-- 模板中英语总价格触发计算 -->
        <span v-show="false">{{allPrice}}</span>
    </div>
</template>

<script>
export default {
	data () {
		return {
			users: [{
				username: '',
				id: ''
			}],
			// 机票的数据
			infoData: {},
			// 保险数据id
			insurances: [],

			contactName: '', //联系人名字
			contactPhone: '', //联系人电话
			captcha: '', //验证码
			invoice: false, //发票字段
			seat_xid: '', //座位id，来自url的参数
			air: '' //航班id，来自url的id
		}
    },
    
    computed: {
        //总价格
        allPrice(){
            // 如果请求未完成，暂时不需要计算，返回0
            if(!this.infoData.seat_infos){
                return 0
            }

            let price = 0

            // 机票单机，取座位信息的第一个价格
            price += this.infoData.seat_infos.org_settle_price

            // 燃油费
            price += this.infoData.airport_tax_audlet

            // 保险数据
            price += 30 * this.insurances.length

            price *= this.users.length

            // 把值存到store
            this.$store.commit("air/setAllPirce", price)

            return price
        }  
    },

	mounted () {
		const {id,seat_xid}	= this.$route.query

		// 请求机票数据
		this.$axios({
			url: '/airs/' + id,
			params: {
				seat_xid
			}
		}).then(res => {
			// 保存机票的数据
            this.infoData = res.data
            
            // 调用store的方法，把infoData存到store中
            this.$storestore.commit('air/setInfoData', this.infoData)
		})
	},
    methods: {
        // 添加乘机人
        handleAddUsers(){
			// 添加的话就push到数组
			this.users.push({
				username: '',
				id: ''
			})
        },
        
        // 移除乘机人
        handleDeleteUse(){
			// 把user的某一项移除掉
			// 用数组的splice方法，slice方法这是截取，创建新的数组
			this.users.splice(index,1)
		},
		
		// 选中保险是触发
		handleChange(){
			// 先判断数组中是否已经包含该id
			const index = this.insurances.indexOf(id)

			// 如果包含了应该删除
			if(index !== -1){
				this.insurances.splice(index,1)
			}else{
				// 添加id到数组
				this.insurances.push(id)
			}

			// console.log(this.insurances)
		},
        
        // 发送手机验证码，复制registerForm表单的功能
        handleSendCaptcha(){
			// 如果手机号码为空，则不请求
			if(!this.contactPhone){
				this.$message.error('请输入手机号码')
				return
			}

			// 发送验证码
			this.$axios({
				url: 'captchas',
				method: 'POST',
				data: {
					tel: this.contactPhone //手机号码
				}
			}).then(res => {
				// 解构出code属性
				const {code} = res.data

				this.$alert(`模拟手机验证码是: ${code}`,'提示')
			})
        },

        // 提交订单
        handleSubmit(){
			console.log(this.users)
			// 提交给后台接口的字段
			const data = {
			    users: this.users,
			    insurances: this.insurances,
			    contactName: this.contactName,
			    contactPhone: this.contactPhone,
			    invoice: this.invoice,
			    captcha: this.captcha,
			    seat_xid: this.$route.query.seat_xid,
			    air: this.$route.query.id
			}
			// console.log(data)

			// 判断乘机人
			if(!this.users[0].username || !this.users[0].id){
				this.$message.error('乘机人不能为空')
				return
			}

			// 判断联系人
			if(!this.contactName){
				this.$message.error('联系人不能为空')
				return
			}

			// 判断手机号码
			if(!this.contactPhone){
				this.$message.error('手机号码不能为空')
				return
			}

			// 判断验证码
			if(!this.captcha){
				this.$message.error('验证码不能为空')
				return
			}

			// 提交
			this.$axios({
				url: '/airorders',
				method: 'POST',
				// 直接这样提交会报错，显示没有token值
				// 可以给接口单独加上请求头
				headers: {
                    Authorization: `Bearer ${this.$store.state.user.userInfo.token}`
				},
				data
			}).then(res => {
				console.log(res)
			})
        }
    }
}
</script>

<style lang="less" scoped>
    .air-column{
        border-bottom:1px #ddd dashed;
        padding-bottom: 20px;   
        margin-bottom: 20px;
    }

    .air-column h2{
        margin-bottom: 20px;
        font-size: 22px;
        font-weight: normal;
    }

    /deep/ .el-select .el-input {
        width: 130px;
    }

    .input-with-select{
        width:590px;
    }

    .input-with-select /deep/  .el-input-group__prepend {
        background-color: #fff;
    }
    .member-info /deep/ .el-form-item{
        margin-bottom:0;
    }

    .member-info-item{
        border-bottom:1px #eee dashed;
        padding-bottom: 20px;
        position: relative;

        &:first-child{
            .delete-user{
                display: none;
            }
        }
    }

    .add-member{
        margin-top:20px;
    }

    .delete-user{
        display: block;
        background:#ddd;
        width:16px;
        height:16px;
        font-size:14px;
        text-align: center;
        line-height: 16px;
        color:#fff;
        cursor: pointer;
        border-radius: 50px;
        position:absolute;
        right:-30px;
        top:50%;
    }

    .insurance{
        > div{
            margin-top:10px;
        }
    }

    .insurance-item{
        margin-bottom: 20px;
    }

    .contact{
        /deep/ .el-input{
            width:50%;
        }
    }

    .submit{
        margin: 50px auto;
        display: block;
        width:250px;
        height:50px;
    }
</style>