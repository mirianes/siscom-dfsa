<template>
  <div class="mt-3">
    <div class="row">
      <div class="col-sm">
        <label for="estimador">Estimadores</label>
        <div class="input-group mb-3">
          <select
            class="form-select"
            aria-label="Default select example"
            id="estimador"
            v-model="estimador"
          >
            <option selected value="1">Lower Bound</option>
            <option value="2">Shoute</option>
            <option value="3">Eom-Lee</option>
            <option value="4">IV-II</option>
          </select>
        </div>
      </div>
      <div class="col-sm">
        <div class="mb-3">
          <label for="n-etiquetas" class="form-label"
            >Quantidade Inicial de Etiquetas</label
          >
          <input
            type="number"
            class="form-control"
            id="n-etiquetas"
            v-model="nEtiquetas"
          />
        </div>
        <div class="mb-3">
          <label for="incremento" class="form-label">Passo de Incremento</label>
          <input
            type="number"
            class="form-control"
            id="incremento"
            v-model="incremento"
          />
        </div>
        <div class="mb-3">
          <label for="max-etiquetas" class="form-label"
            >Quantidade Máxima de Etiquetas</label
          >
          <input
            type="number"
            class="form-control"
            id="max-etiquetas"
            v-model="maxEtiquetas"
          />
        </div>
      </div>
      <div class="col-sm">
        <div class="mb-3">
          <label for="n-repeticoes" class="form-label"
            >Número de Repetições</label
          >
          <input
            type="number"
            class="form-control"
            id="n-repeticoes"
            v-model="nRepeticoes"
          />
        </div>
        <div class="mb-3">
          <label for="tam-quadro-inicial" class="form-label"
            >Tamanho do Quadro Inicial</label
          >
          <input
            type="number"
            class="form-control"
            id="tam-quadro-inicial"
            v-model="tamQuadroInicial"
          />
        </div>
        <div class="form-check">
          <input
            class="form-check-input"
            type="checkbox"
            value=""
            id="limite-tam-quadro"
            v-model="limiteTamQuadro"
          />
          <label class="form-check-label" for="limite-tam-quadro">
            Tamanho do quadro limitado a uma potência de 2
          </label>
        </div>
      </div>
    </div>

    <div class="row mt-3">
      <div class="col-sm">
        <button type="button" class="btn btn-success" @click="simular()">Simular</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      estimador: 1,
      nEtiquetas: undefined,
      incremento: undefined,
      maxEtiquetas: undefined,
      nRepeticoes: undefined,
      tamQuadroInicial: undefined,
      limiteTamQuadro: undefined,
    };
  },
  methods: {
    simular() {
      this.$emit('simularEstimador', {
        estimador: this.estimador,
        nEtiquetas: this.nEtiquetas,
        incremento: this.incremento,
        maxEtiquetas: this.maxEtiquetas,
        nRepeticoes: this.nRepeticoes,
        tamQuadroInicial: this.tamQuadroInicial,
        limiteTamQuadro: this.limiteTamQuadro,
      });
    },
  },
};
</script>
