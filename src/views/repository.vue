<template>
  <div id="repository">
	 <mu-list style="padding-bottom:50px">
			<mu-list-item v-for="(li,ind) in list" :key="li.id" titleClass="myitem">
				<div slot="title">
					{{li.title}}
					<mu-icon 
						value="border_color" 
						color="#2196f3" 
						:size="16"
						@click="showInput(ind)"			 
					/>
				</div>

				<mu-icon-button 
					touch
					@click="View(li.id)"
					iconClass="bgColor" 
					icon="remove_red_eye" 
					slot="left"
				/>

				<mu-icon 
					value="cancel" 
					color="red" 
					slot="right" 
					@click.self="showDialog(ind)"
				/>
			</mu-list-item>
		</mu-list>
		
		<FooterAdd @click="showAdd" />
		
		<InputDialog
			:open="inputDia"
			:showValue="activeLi.title"
			v-on:cancel="inputToggle"
			v-on:confirm="editor"
		/>
		
		<mu-dialog :open="dialog">
			确定删除{{activeLi.title}}吗?
			<mu-flat-button slot="actions" @click="toggle" primary label="取消"/>
			<mu-flat-button slot="actions" primary @click="deleteRepo" label="完成"/>
		</mu-dialog>
		
	</div>
</template>

<script>
	import InputDialog from "@/components/repository/inputDialog"
	import FooterAdd from "@/components/repository/footer"

	export default {
		data() {
			return {
				activeInd: 0,
				dialog: false,
				inputDia: false,
				isAdd: false
			}
		},
		methods: {
			showInput(ind) {
				this.activeInd = ind
				this.isAdd = false
				this.inputToggle()
			},
			showDialog(ind) {
				this.activeInd = ind
				this.toggle()
			},
			editor(val) {
				if (this.isAdd) {
					this.addList(val)
				} else {
					this.setTitle(val)
				}
				this.inputToggle()
			},
			View(id) {
				this.$store.commit("setDirection", 'slide-left')
				this.$router.push(`/repository/${id}`)
			},
			showAdd(e) {
				this.isAdd = true
				this.activeInd = null
				this.inputToggle()
			},
			inputToggle() {
				this.inputDia = !this.inputDia
			},
			toggle() {
				this.dialog = !this.dialog
			},
			deleteRepo() {
				this.$store.commit("deleteRepo", this.activeLi.id)
				this.toggle()
			},
			addList(title) {
				const listId = this.list.length
				this.$store.commit("addList", {
					id: listId,
					title: title,
					childrens: []
				})
				this.$store.commit("setDirection", 'slide-left')
				this.$router.push(`/repository/${listId}`)
			},
			setTitle(title) {
				const id = this.activeLi.id
				this.$store.commit("setTitle", {
					id: id,
					title: title
				})
			}
		},
		computed: {
			list() {
				return this.$store.state.repository.data
			},
			activeLi() {
				return this.list[this.activeInd] || {}
			}
		},
		components: {
			InputDialog,
			FooterAdd,
		}
	}

</script>

<style>
	.bgColor {
		color: #2196f3
	}
	
	.myitem{
		line-height: 35px
	}

</style>
