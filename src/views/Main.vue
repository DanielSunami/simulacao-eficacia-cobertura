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
						<line-chart :height="150" :options="options" :chart-data="datacollection"></line-chart>
					</div>
				</div>
			</div>
			<div class="col-lg-4 grid-margin">
				<div class="row">
					<div class="col-lg-12 grid-margin stretch-card">
						<div class="card">
							<div class="card-body">
								<h4 class="card-title">Eficácia (%)</h4>
								<p class="card-description"></p>
								<div class="col col-12">
									<vue-slider class="pt-3" :marks="[0, 50, 70, 90, 100]" v-model="form.eficacia" :min="0" :max="100"/>
								</div>
							</div>
						</div>
					</div>
				</div>
				<div class="row">
					<div class="col-lg-12 grid-margin stretch-card">
						<div class="card">
							<div class="card-body">
								<h4 class="card-title">Taxa de Transmissão</h4>
								<p class="card-description"></p>
								<div class="col col-12">
									<vue-slider class="pt-3" :marks="[0, 0.5, 1, 1.5, 2]" v-model="form.r" @change="updateChart" :min="0" :max="2" :interval="0.1"/>
								</div>
							</div>
						</div>
					</div>
				</div>
				<div class="row">
					<div class="col-lg-12 grid-margin stretch-card">
						<div class="card">
							<div class="card-body">
								<h4 class="card-title">Taxa de Transmissão (%)</h4>
								<p class="card-description"></p>
								<form class="forms-sample">
									<b-form-group label="Número de habitantes"  label-for="input1">
										<b-form-input type="text" id="input1" class="claro" v-model="form.habitantes" @change="updateChart" placeholder="Exemplo: 46000000"></b-form-input>
									</b-form-group>
									<b-form-group label="Total de mortes em dez/20"  label-for="input2">
										<b-form-input type="text" id="input2" class="claro" v-model="form.mortesDez" @change="updateChart" placeholder="3000"></b-form-input>
									</b-form-group>
									<div class="d-flex">
										{{(( form.mortesDez / form.habitantes) * 100000).toFixed(2)}} mortes por 100mil habitantes em dez/20
									</div>
									<div class="d-none">
									{{baseMortes}}
									</div>
								</form>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
		<div class="row">
			<div class="col-lg-8 grid-margin stretch-card">
				<div class="card">
					<div class="card-body">
						<h4 class="card-title">Cobertura Vacinação (%)</h4>
						<p class="card-description"></p>
						<div class="d-flex flex-row">
							<div class="col" v-for="(item, index) in form.statusPorMes" :key="index">
								<div class="d-flex flex-column">
									<div class="mx-auto">
										<vue-slider class="pt-3 mx-auto" style="height: 180px" :marks="[0, 100]" @change="calc($event, index)" v-model="item.cobertura" :min="0" :max="100" direction="btt"/>
									</div>
									<div class="mx-auto mt-4">
										{{item.mes}}
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
import VueSlider from 'vue-slider-component'
import 'vue-slider-component/theme/default.css'
import lineChart from '../components/charts/chartjs/Line.vue';

export default {
	name: 'home',
	data() {
		return {
			form: {
				statusPorMes: [
					{ cobertura: 0, mes: "Jan" },
					{ cobertura: 0, mes: "Fev" },
					{ cobertura: 0, mes: "Mar" },
					{ cobertura: 0, mes: "Abr" },
					{ cobertura: 0, mes: "Mai" },
					{ cobertura: 0, mes: "Jun" }
				],
				eficacia: 50,
				r: 1.3,
				habitantes: 46000000,
				mortesDez: 3000
			},
			options: {
				scaleOverride: true,
				scaleStartValue: 0,
				scaleSteps: 10,
				scaleStepWidth: 0.1,
				scales: {
					yAxes: [
						{
							ticks: {
								max: 100,
								min: 0,
								stepSize: 20
							},
							scaleLabel: {
								display: true,
								labelString: 'Mortes por 100k/hab'
							}
						}
					]
				}
			},
			datacollection: {
				labels: ["Jan","Fev","Mar","Abr","Mai","Jun","Jul"],
				datasets: [
					{
						label: 'Mortes',
						borderColor: '#f87979',
						data: [8.48, 11.02, 14.33, 18.63, 24.22, 31.49, 40.94],
						fill: false,
						cubicInterpolationMode: 'monotone'
					}
				]
			}
		};
	},
	components: {
		lineChart,
		VueSlider
	},
	computed: {
		baseMortes() {
			const self = this;
			const arr = [];
			let i = parseFloat((( self.form.mortesDez / self.form.habitantes) * 100000).toFixed(2));
			for(let m = 0; m < 7; m++) {
				i = parseFloat((i * self.form.r).toFixed(2));
				arr.push(i);
			}

			return arr;
		}
	},
	methods: {
		addVacina() {
			let self = this;

			self.datacollection = Object.assign({}, self.datacollection);
		},
		calc(val, indexM) {
			let self = this;

			for(let i = indexM+1; i < self.form.statusPorMes.length; i++) {
				if(self.form.statusPorMes[i].cobertura < val)
					self.form.statusPorMes[i].cobertura = val;
			}

			for(let i = indexM-1; i >= 0; i--) {
				if(self.form.statusPorMes[i].cobertura > val)
					self.form.statusPorMes[i].cobertura = val;
			}

			if(indexM + 1 >= self.datacollection.datasets[0].data.length ) return;
			for(let i = indexM+1; i < self.datacollection.datasets[0].data.length; i++) {
				self.datacollection.datasets[0].data[i] = self.baseMortes[i] - (0.1 * val);
			}

			// atualiza grafico
			self.datacollection = Object.assign({}, self.datacollection);
		},
		updateChart(){
			for(let i = 0; i < self.datacollection.datasets[0].data.length; i++) {
				self.datacollection.datasets[0].data[i] = self.baseMortes[i];
			}

			// atualiza grafico
			self.datacollection = Object.assign({}, self.datacollection);
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
