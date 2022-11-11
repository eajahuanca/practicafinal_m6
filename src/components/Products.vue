<template>
	<div class="container">
		<div id="app">
			<h1>PRODUCTOS</h1>
			<input v-model="itemName" @keyup.enter="addItem" type="text" />
			<select v-model="itemCategory">
				<option v-for="catg in itemsCategory" v-bind:value="catg.categ" v-bind:key="catg.id" >{{ catg.categ }}</option>
			</select><br />

			<button class="button-add" @click="addItem()">Adicionar Producto</button>
		</div>
		<div class="table-border">

			<table width="100%">
				<tr>
					<th>ID</th>
					<th>NOMBRE DEL PRODUCTO</th>
					<th>CATEGORIA</th>
					<th>OPCIONES</th>
				</tr><tr><td colspan="4"><hr ></td></tr>
				<tr v-for="item of items" :key="item.id">
					<td>{{item.id}}</td>
					<td>{{item.name}}</td>
					<td>{{item.categ}}</td>
					<td>
						<button class="button-del" @click="removeItem(item.id)">Eliminar</button>
						<button class="button-edit" @click="editItem(item.id)">Editar</button>
					</td>
				</tr>
			</table>
		</div>
	</div>
</template>

<script>
import axios from "axios";
const BASE_URL = "http://localhost:3000/products";
const BASE_URL_CAT = "http://localhost:3000/categories";

export default {
	name: "ProductsView",
	data() {
		return {
			items: [],
			itemsCategory: [],
			itemName: "",
			itemCategory: "",
		};
	},
	async created() {
		try {
			const res = await axios.get(`${BASE_URL}`);
			this.items = res.data;
			const resCat = await axios.get(`${BASE_URL_CAT}`);
			this.itemsCategory = resCat.data;
		} catch (error) {
			console.log(error);
		}
	},
	methods: {
		async boughtItem(id) {
			await axios.patch(`${BASE_URL}/${id}`, {
				bought: true,
			});
			this.items = this.items.map((item) => {
				if (item.id === id) {
					item.bought = true;
				}
				return item;
			});
		},

		removeItem(id) {
			axios.delete(`${BASE_URL}/${id}`);
			this.items = this.items.filter((item) => item.id !== id);
		},
		
		async addItem() {
			const res = await axios.post(`${BASE_URL}/`, {
				name: this.itemName,
				categ: this.itemCategory,
			});
			this.items = [...this.items, res.data];
			this.itemName = "";
			this.itemCategory = "";
		},
	},
};
</script>

<style>
#app {
	text-align: center;
	color: #2c3e50;
}

.container {
	background-color: #bcdcfad2;
	max-width: 600px;
	margin: 0 auto;
	border-radius: 8px;
	padding-bottom: -20px;
	margin-bottom: 5em;
}

.table-border{
	border:1px solid #5e5d5e; 
	padding:1px; 
	width:580px; 
	margin-top:2em;
	margin-bottom:-2em;
	border-radius: 4px;
}

table {
  border-collapse: collapse;
}

.button-add {
	margin-top: 5px;
	background-color: #3498db;
	border: none;
	color: #ffffff;
	padding: 10px 20px;
	font-size: 14px;
	cursor: pointer;
	border-radius: 4px;
}

.button-del{
	background-color: #f0370d;
	color: #ffffff;
	padding: 5px;
	font-size: 12px;
	cursor: pointer;
	border-radius: 4px;
}


.button-edit{
	background-color: #f1df12;
	color: #ffffff;
	padding: 5px;
	font-size: 12px;
	cursor: pointer;
	border-radius: 4px;
}

input {
	margin-top: 5px;
	padding: 10px 20px;
	font-size: 14px;
	border-radius: 4px;
}

select {
	margin-left: 5px;
	background-color: #ffffff;
	margin-top: 5px;
	padding: 10px 20px;
	font-size: 14px;
	border-radius: 4px;
}

</style>
