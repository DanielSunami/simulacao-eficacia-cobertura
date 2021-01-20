<template>
	<section class="home">
		<div class="page-header">
			<h3 class="page-title">
				Simulação
			</h3>
		</div>
		<div class="row">
			<div class="col-lg-8 grid-margin stretch-card">
				<div class="card">
					<div class="card-body">
						<!--<h4 class="card-title">Line chart</h4>-->
						<line-chart :height="250" :options="options" :chart-data="datacollection"></line-chart>
					</div>
				</div>
			</div>
			<div class="col-lg-4 grid-margin stretch-card">
				<div class="row">
					<div class="col-lg-12 grid-margin">
						<div class="card">
							<div class="card-body">
								<h4 class="card-title">Nova Vacina</h4>
								<p class="card-description"></p>
								<form class="forms-sample">
									<b-form-group label="Nome"  label-for="input1">
										<b-form-input type="text" id="input1" class="claro" v-model="form.novaVacina.nome.value" :state="form.novaVacina.nome.state" placeholder="Exemplo: CoronaVac"></b-form-input>
									</b-form-group>
									<b-form-group label="Cor"  label-for="input2">
										<b-form-input type="text" id="input2" class="claro" v-model="form.novaVacina.cor" placeholder="Cor em hex ou rgb, exemplo: #f87979"></b-form-input>
										<small>Opcional</small>
									</b-form-group>
									<b-form-group label="Dados"  label-for="input2">
										<b-form-input type="text" id="input2" class="claro" v-model="form.novaVacina.dados.value" :state="form.novaVacina.dados.state" placeholder="Exemplo: 0, 10, 50, 70, 70, 90"></b-form-input>
									</b-form-group>
									<div class="d-flex">
										<b-button variant="success" class="mr-2" @click="addVacina">Adicionar</b-button>
										<b-button variant="dark" @click="limparFormulario">Limpar</b-button>
									</div>
								</form>
							</div>
						</div>
					</div>
					<div class="col-lg-12">
						<div class="card">
							<div class="card-body">
								<h4 class="card-title">Remover Vacina</h4>
								<p class="card-description"></p>
								<div  class="d-flex vacina-list-item" v-for="(item, index) in datacollection.datasets" :key="item.label">
									<div class="col-2">
										<div class="color-box" :style="{backgroundColor: item.borderColor}"></div>
									</div>
									<div class="col-4">
										{{item.label}}
									</div>
									<div class="col-4">
										<b-button variant="danger" @click="removerVacina(index)">Remover</b-button>
									</div>
								</div>
								<div class="d-flex mt-4">
									<div class="col-4">
										<b-button variant="danger" @click="removerTodasVacinas">Remover Todas</b-button>
									</div>
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	</section>
</template>

<style>
.claro:focus {
	color: white;
}

.color-box {
	width: 100%;
	height: 25px;
}

.vacina-list-item + .vacina-list-item {
	margin-top: 10px;
}
</style>

<script>
import lineChart from '../components/charts/chartjs/Line.vue';

export default {
	name: 'home',
	data() {
		return {
			form: {
				novaVacina: {
					nome: {
						value: '',
						state: null
					},
					dados: {
						value: '',
						state: null
					},
					cor: ''
				}
			},
			options: {
				scales: {
					yAxes: [
						{
							scaleLabel: {
								display: true,
								labelString: 'Cobertura'
							}
						}
						/*,
						{
							ticks: {
								min: 0,
								max: 100,
								stepSize: 20,
							}
						}*/
					],
					xAxes: [
						{
							scaleLabel: {
								display: true,
								labelString: 'Disseminação'
							}
						}
					]
				}
			},
			datacollection: {
				labels: ["0","1","2","3","4","5","6"],
				datasets: [
					{
						label: 'CoronaVac',
						borderColor: '#f87979',
						data: [0, 0, 80, 85, 90, 100],
						fill: false,
						cubicInterpolationMode: 'monotone'
					}
				]
			}
		};
	},
	components: {
		lineChart
	},
	methods: {
		addVacina() {
			let self = this;

			if(!self.form.novaVacina.nome.value) {
				self.form.novaVacina.nome.state = false;
				self.$bvToast.toast('Campo "Nome" é obrigatório', {
					title: 'Campo obrigatório',
					autoHideDelay: 5000,
					appendToast: true,
					solid: true,
					variant: 'danger'
				});
			} else if(!self.form.novaVacina.dados.value) {
				self.form.novaVacina.dados.state = false;
				self.$bvToast.toast('Campo "Dados" é obrigatório', {
					title: 'Campo obrigatório',
					autoHideDelay: 5000,
					appendToast: true,
					solid: true,
					variant: 'danger'
				});
			}

			const vacina = {
				label: self.form.novaVacina.nome.value,
				// usa cor definida no formulario ou cor aleatoria
				borderColor: self.form.novaVacina.cor ? self.form.novaVacina.cor : "#" + Math.floor(Math.random()*16777215).toString(16),
				// converte de string para int
				data: self.form.novaVacina.dados.value.split(',').map(function(a) { return parseInt(a); }),
				fill: false,
				cubicInterpolationMode: 'monotone'
			};
 
			self.datacollection.datasets.push(vacina);
			self.datacollection = Object.assign({}, self.datacollection);
			self.limparFormulario();
		},
		limparFormulario() {
			let self = this;
			
			self.form.novaVacina.nome.value = '';
			self.form.novaVacina.nome.state = null;
			self.form.novaVacina.cor = '';
			self.form.novaVacina.dados.value = '';
			self.form.novaVacina.dados.state = null;

		},
		removerTodasVacinas() {
			let self = this;
			
			self.datacollection.datasets = [];
			self.datacollection = Object.assign({}, self.datacollection);
		},
		removerVacina(i) {
			let self = this;

			self.datacollection.datasets.splice(i,1);
			self.datacollection = Object.assign({}, self.datacollection);
		}
	}
}
</script>
