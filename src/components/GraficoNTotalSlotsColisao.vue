<template>
  <div>
    <apexchart
      width="100%"
      height="350"
      type="line"
      :options="chartOptions"
      :series="series"
    />
  </div>
</template>

<script>
export default {
  props: {
    series: {
      type: Array,
      required: false,
    },
  },

  data() {
    return {
      chartOptions: {
        chart: {
          id: 'test',
        },
        grid: {
          show: true,
        },
        xaxis: {
          type: 'categories',
          title: {
            text: 'Número de Etiquetas',
          },
        },
        yaxis: {
          title: {
            text: 'Número de Slots em Colisão',
          },
        },
        markers: {
          size: 5,
        },
        dataLabels: {
          enabled: true,
        },
        toolbar: {
          show: false,
        },
      },
    };
  },
};
</script>
