<template>
  <div id="app">
    <h4>Simulador Estimadores para o DFSA</h4>
    <h6>Sistemas de Comunicação 2021.1</h6>

    <div class="my-body mx-5 d-flex justify-content-center">
      <Filtro @simularEstimador="salvarFiltro($event)"/>
    </div>

    <!-- <div class="my-body mx-5 d-flex justify-content-center">
      <ul class="list-group list-group-flush">
        <li class="list-group-item" v-for="(item, index) in historico" :key="index">
          {{ index }} - {{ item }}
        </li>
      </ul>
    </div> -->

    <div class="my-body mx-5 d-flex justify-content-center mt-3">
      <div class="row w-100">
        <div class="col-sm" v-if="mostrarGraficoNTotalSlots">
          <GraficoNTotalSlots :series="serieNTotalSlots" />
        </div>

        <div class="col-sm" v-if="mostrarGraficoNTotalSlotsVazios">
          <GraficoNTotalSlotsVazios :series="serieNTotalSlotsVazios" />
        </div>

        <div class="col-sm" v-if="mostrarGraficoNTotalSlotsColisao">
          <GraficoNTotalSlotsColisao :series="serieNTotalSlotsColisao" />
        </div>

        <div class="col-sm" v-if="mostrarGraficoTempoMedioExecucao">
          <GraficoTempoMedioExecucao :series="serieTempoMedioExecucao" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Filtro from './components/Filtro';
import GraficoNTotalSlots from './components/GraficoNTotalSlots';
import GraficoNTotalSlotsVazios from './components/GraficoNTotalSlotsVazios';
import GraficoNTotalSlotsColisao from './components/GraficoNTotalSlotsColisao';
import GraficoTempoMedioExecucao from './components/GraficoTempoMedioExecucao';

export default {
  name: 'App',
  components: {
    Filtro,
    GraficoNTotalSlots,
    GraficoNTotalSlotsVazios,
    GraficoNTotalSlotsColisao,
    GraficoTempoMedioExecucao,
  },
  data() {
    return {
      estimador: undefined,
      nEtiquetas: undefined,
      incremento: undefined,
      maxEtiquetas: undefined,
      nRepeticoes: undefined,
      tamQuadroInicial: undefined,
      tamQuadro: undefined,
      limiteTamQuadro: undefined,
      historico: [],
      historicoGamas: [],
      beta: undefined,
      nTotalSlots: 0,
      serieNTotalSlots: [
        {
          name: 'Lower Bound',
          data: [],
        },
        {
          name: 'Shoute',
          data: [],
        },
        {
          name: 'Eom-lee',
          data: [],
        },
      ],
      mostrarGraficoNTotalSlots: false,
      nTotalSlotsVazios: 0,
      serieNTotalSlotsVazios: [
        {
          name: 'Lower Bound',
          data: [],
        },
        {
          name: 'Shoute',
          data: [],
        },
        {
          name: 'Eom-lee',
          data: [],
        },
      ],
      mostrarGraficoNTotalSlotsVazios: false,
      nTotalSlotsColisao: 0,
      serieNTotalSlotsColisao: [
        {
          name: 'Lower Bound',
          data: [],
        },
        {
          name: 'Shoute',
          data: [],
        },
        {
          name: 'Eom-lee',
          data: [],
        },
      ],
      mostrarGraficoNTotalSlotsColisao: false,
      tempoExecucaoLB: 0,
      tempoExecucaoShoute: 0,
      tempoExecucaoEomLee: 0,
      serieTempoMedioExecucao: [
        {
          name: 'Lower Bound',
          data: [],
        },
        {
          name: 'Shoute',
          data: [],
        },
        {
          name: 'Eom-lee',
          data: [],
        },
      ],
      mostrarGraficoTempoMedioExecucao: false,
    };
  },
  methods: {
    salvarFiltro(event) {
      this.estimador = parseInt(event.estimador, 10);
      this.nEtiquetas = parseInt(event.nEtiquetas, 10);
      this.incremento = parseInt(event.incremento, 10);
      this.maxEtiquetas = parseInt(event.maxEtiquetas, 10);
      this.nRepeticoes = parseInt(event.nRepeticoes, 10);
      this.tamQuadroInicial = parseInt(event.tamQuadroInicial, 10);
      this.limiteTamQuadro = event.limiteTamQuadro;
      // this.historico = [];

      this.simular();
    },

    estimarTamQuadroPot2(nEtiquetasConcorrentes) {
      if (nEtiquetasConcorrentes >= 1 && nEtiquetasConcorrentes <= 5) {
        return 4;
      } else if (nEtiquetasConcorrentes >= 6 && nEtiquetasConcorrentes <= 11) {
        return 8;
      } else if (nEtiquetasConcorrentes >= 12 && nEtiquetasConcorrentes <= 22) {
        return 16;
      } else if (nEtiquetasConcorrentes >= 23 && nEtiquetasConcorrentes <= 44) {
        return 32;
      } else if (nEtiquetasConcorrentes >= 45 && nEtiquetasConcorrentes <= 89) {
        return 64;
      } else if (nEtiquetasConcorrentes >= 90 && nEtiquetasConcorrentes <= 177) {
        return 128;
      } else if (nEtiquetasConcorrentes >= 178 && nEtiquetasConcorrentes <= 355) {
        return 256;
      } else if (nEtiquetasConcorrentes >= 356 && nEtiquetasConcorrentes <= 710) {
        return 512;
      } else if (nEtiquetasConcorrentes >= 711 && nEtiquetasConcorrentes <= 1420) {
        return 1024;
      } else if (nEtiquetasConcorrentes >= 1421 && nEtiquetasConcorrentes <= 2840) {
        return 2048;
      } else if (nEtiquetasConcorrentes >= 2841 && nEtiquetasConcorrentes <= 5680) {
        return 4096;
      } else if (nEtiquetasConcorrentes >= 5681 && nEtiquetasConcorrentes <= 11360) {
        return 8192;
      }

      return 0;
    },

    lowerBound(quadro) {
      const tam = 2 * (quadro.filter(el => el === 'C').length);

      if (this.limiteTamQuadro) {
        const nEtiquetasConcorrentes = (quadro.filter(el => el === 'S').length) + tam;
        this.tamQuadro = this.estimarTamQuadroPot2(nEtiquetasConcorrentes);
      } else {
        this.tamQuadro = tam;
      }
    },

    shoute(quadro) {
      const tam = Math.ceil(2.39 * (quadro.filter(el => el === 'C').length));

      if (this.limiteTamQuadro) {
        const nEtiquetasConcorrentes = (quadro.filter(el => el === 'S').length) + tam;
        this.tamQuadro = this.estimarTamQuadroPot2(nEtiquetasConcorrentes);
      } else {
        this.tamQuadro = tam;
      }
    },

    calcBeta(k, Ss, Sc) {
      const L = this.tamQuadro;

      if (k === 1) {
        return Infinity;
      }

      const gama = this.historicoGamas[k - 2];

      return L / (gama * (Sc + Ss));
    },

    calcGama(k, Ss, Sc) {
      if (k === 1) {
        return -2;
      }

      this.beta = this.calcBeta(k, Ss, Sc);

      const exp = Math.exp(-1 / this.beta);

      return (1 - exp) / (this.beta * (1 - ((1 + (1 / this.beta)) * exp)));
    },

    eomLee(quadro) {
      const Sc = quadro.filter(el => el === 'C').length;

      if (this.historicoGamas.length === 0) {
        let limiar = false;
        let k = 1;
        const Ss = quadro.filter(el => el === 'S').length;

        while (!limiar) {
          const gama = this.calcGama(k, Ss, Sc);
          this.historicoGamas.push(gama);

          if (k > 1) {
            const gamaAnterior = this.historicoGamas[k - 2];
            limiar = Math.abs(gamaAnterior - gama) < 0.001;
          }

          k += 1;
        }
      }

      const length = this.historicoGamas.length - 1;
      const tam = Math.ceil(this.historicoGamas[length] * Sc);

      if (this.limiteTamQuadro) {
        const nEtiquetasConcorrentes = Math.ceil(tam / this.beta);
        this.tamQuadro = this.estimarTamQuadroPot2(nEtiquetasConcorrentes);
      } else {
        this.tamQuadro = tam;
      }
    },

    estimarTamQuadroVogt(nEtiquetasConcorrentes) {
      if (nEtiquetasConcorrentes >= 1 && nEtiquetasConcorrentes <= 9) {
        return 16;
      } if (nEtiquetasConcorrentes >= 10 && nEtiquetasConcorrentes <= 27) {
        return 32;
      } if (nEtiquetasConcorrentes >= 17 && nEtiquetasConcorrentes <= 56) {
        return 64;
      } if (nEtiquetasConcorrentes >= 51 && nEtiquetasConcorrentes <= 129) {
        return 128;
      } if (nEtiquetasConcorrentes >= 112) {
        return 256;
      }

      return 0;
    },

    // Incompleto
    ivii(quadro) {
      const L = this.tamQuadro;
      const Sc = quadro.filter(el => el === 'C').length;
      let nEtiquetasConcorrentes = 1;

      if (L !== Sc) {
        nEtiquetasConcorrentes = 9;
      } else {
        nEtiquetasConcorrentes = 1;
      }

      if (this.limiteTamQuadro) {
        this.tamQuadro = this.estimarTamQuadroPot2(nEtiquetasConcorrentes);
      } else {
        this.tamQuadro = this.estimarTamQuadroVogt(nEtiquetasConcorrentes);
      }
    },

    simular() {
      try {
        let index = this.nRepeticoes;
        const indexSeries = this.estimador - 1;

        this.serieNTotalSlots[indexSeries].data = [];
        this.serieNTotalSlotsVazios[indexSeries].data = [];

        while (this.nEtiquetas <= this.maxEtiquetas) {
          while (index > 0) {
            console.log('+++++++++', index, '++++++++');
            this.dfsa();
            // this.historico.push(['-']);
            index -= 1;
          }

          const yNTotalSlots = Math.ceil(this.nTotalSlots / this.nRepeticoes);
          this.serieNTotalSlots[indexSeries].data.push({ x: `${this.nEtiquetas}`, y: yNTotalSlots });
          this.nTotalSlots = 0;
          this.mostrarGraficoNTotalSlots = true;

          const yNTotalSlotsVazios = Math.ceil(this.nTotalSlotsVazios / this.nRepeticoes);
          this.serieNTotalSlotsVazios[indexSeries].data.push({ x: `${this.nEtiquetas}`, y: yNTotalSlotsVazios });
          this.nTotalSlotsVazios = 0;
          this.mostrarGraficoNTotalSlotsVazios = true;

          const yNTotalSlotsColisao = Math.ceil(this.nTotalSlotsColisao / this.nRepeticoes);
          this.serieNTotalSlotsColisao[indexSeries].data.push({ x: `${this.nEtiquetas}`, y: yNTotalSlotsColisao });
          this.nTotalSlotsColisao = 0;
          this.mostrarGraficoNTotalSlotsColisao = true;

          let yTempoMedioExecucao = 0;

          if (this.estimador === 1) {
            yTempoMedioExecucao = Math.ceil(this.tempoExecucaoLB / this.nRepeticoes);
          } else if (this.estimador === 2) {
            yTempoMedioExecucao = Math.ceil(this.tempoExecucaoShoute / this.nRepeticoes);
          } else if (this.estimador === 3) {
            yTempoMedioExecucao = Math.ceil(this.tempoExecucaoEomLee / this.nRepeticoes);
          }

          this.serieTempoMedioExecucao[indexSeries].data.push({ x: `${this.nEtiquetas}`, y: yTempoMedioExecucao });
          this.tempoExecucaoLB = 0;
          this.tempoExecucaoShoute = 0;
          this.tempoExecucaoEomLee = 0;
          this.mostrarGraficoTempoMedioExecucao = true;

          this.nEtiquetas += this.incremento;
          index = this.nRepeticoes;
        }
      } catch (error) {
        console.log(error);
      }
    },

    dfsa() {
      let etiquetas = this.nEtiquetas;
      this.tamQuadro = this.tamQuadroInicial;

      while (etiquetas > 0) {
        console.log('e', etiquetas);
        const quadro = new Array(this.tamQuadro);
        this.nTotalSlots += this.tamQuadro;

        let colisaoDetectada = false;

        // eslint-disable-next-line no-plusplus
        for (let index = 0; index < quadro.length; index++) {
          const qntd = Math.floor(Math.random() * (etiquetas + 1));

          if (qntd > 1) {
            quadro[index] = 'C';
            colisaoDetectada = true;
            this.nTotalSlotsColisao += 1;
          } else if (qntd === 1) {
            quadro[index] = 'S';
            etiquetas -= 1;
          } else {
            quadro[index] = 'V';
            this.nTotalSlotsVazios += 1;
          }
        }

        // this.historico.push(quadro);

        if (colisaoDetectada) {
          const inicio = performance.now();

          if (this.estimador === 1) {
            this.lowerBound(quadro);
            this.tempoExecucaoLB += performance.now() - inicio;
          } else if (this.estimador === 2) {
            this.shoute(quadro);
            this.tempoExecucaoShoute += performance.now() - inicio;
          } else if (this.estimador === 3) {
            this.eomLee(quadro);
            this.tempoExecucaoEomLee += performance.now() - inicio;
          } else if (this.estimador === 4) {
            this.ivii();
          }
        }
      }
    },
  },
};
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.my-body{
  max-width: 1350px !important;
}
</style>
