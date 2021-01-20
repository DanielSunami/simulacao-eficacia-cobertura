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
									<vue-slider class="pt-3" :marks="[0, 100]" v-model="form.eficacia" :min="0" :max="100"/>
								</div>
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
				eficacia: 50
			},
			baseMortes: [30, 40, 60, 50, 40, 30, 30],
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
						data: [30, 40, 60, 50, 40, 30, 30],
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
	methods: {
		addVacina() {
			let self = this;

			self.datacollection = Object.assign({}, self.datacollection);
		},
		calc(val, indexM){
			let self = this;

			if(indexM + 1 >= self.datacollection.datasets[0].data.length ) return;
			for(let i = indexM+1; i < self.datacollection.datasets[0].data.length; i++) {
				self.datacollection.datasets[0].data[i] = self.baseMortes[i] - (0.1 * val);
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
