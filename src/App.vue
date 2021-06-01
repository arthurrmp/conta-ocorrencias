<template>
  <div id="app">
    <section>
      <b-field label="Valores">
        <b-input v-model="msg" type="textarea"></b-input>
      </b-field>
      <b-button @click="clickMe">Calcular</b-button>
      <b-button
        type="is-success"
        style="margin-left: 30px"
        @click="copy"
        v-if="map.length > 0"
        >Copiar para excel</b-button
      >

      <div v-if="map.length > 0">
        <b-table :data="map" :columns="columns"></b-table>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      map: [],
      columns: [
        { field: "linha", label: "Linha" },
        { field: "contagem", label: "Contagem" },
        { field: "porcentagem", label: "Porcentagem" },
      ],
      msg: `Chronic Pain Grade (CPG)
RS-25
Davidson Trauma Scale (DTS)
RS-25
DSQ
Childhood Trauma Questionnaire (CTQ)
Athletic Coping Skills Inventory-28 (ACSI-28)
CD-RISC-25
Brief Symptom Inventory (BSI)
Humiliation Inventory
Resilience Scale for Adults (RSA)
Alcohol Use Disorder Identification Test (AUDIT)
RS-25
Adolescent/Adult Sensory Profile (SPA)
RS-25
Tobacco, Alcohol and Drug Attitude Scales (TUS)
Tobacco, Alcohol and Other Drug Use Scale(TADU)
Hospital Anxiety and Depression Scale (HADS)
RS-25
`,
    };
  },
  methods: {
    clickMe: function () {
      if (!this.msg) {
        alert("Campo não pode ser vazio");
        return;
      }

      if (this.msg.lastIndexOf("\n") === -1) {
        alert("O texto inserido só possui uma linha");
        return;
      }

      var arr = this.msg.split("\n");

      this.map = arr.reduce(
        (arr, e) => arr.set(e.trim(), (arr.get(e) || 0) + 1),
        new Map()
      );

      let arm = [];

      for (const m of this.map) {
        if (m[0] && m[1]) {
          let contagem = m[1];
          arm.push({
            linha: m[0],
            contagem,
            porcentagem: ((contagem / arr.length) * 100).toFixed(2) + "%",
          });
        }
      }

      arm = arm.sort((a, b) => (a.contagem < b.contagem ? 1 : -1));

      this.map = arm;
    },
    copy: function () {
      let copy = "Valor\tContagem\tPorcentagem\n";
      for (const m of this.map) {
        copy += `${m.linha}\t${m.contagem}\t${m.porcentagem}\n`;
      }
      console.log(copy)

      if (this.$clipboard(copy)) {
        alert("Copiado!");
      } else {
        alert(
          "Não foi possível copiar. Você pode copiar manualmente da tabela"
        );
      }
    },
  },
};
</script>

<style>
#app {
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;
  color: #2c3e50;
  margin-top: 60px;
  margin-bottom: 60px;
}
</style>
