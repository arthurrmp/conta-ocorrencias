<template>
  <div id="app">
    <section class="center hero is-primary">
      <div class="hero-body">
        <p class="title"> 🔍 Contagem de ocorrências</p>
        <p class="subtitle">github.com/arthurrmp/conta-ocorrencias</p>
      </div>
    </section>

    <section class="componentes">
      <b-field label="Valores">
        <b-input placeholder="Copie os valores separados por quebra de linha para contar as ocorrências." v-model="msg" type="textarea"></b-input>
      </b-field>
      <!--
        primary
        success
        warning
        danger
        -->
      <b-field>
        <b-checkbox v-model="removerLixo">Remover acentuação</b-checkbox>
        <b-checkbox v-model="lower">Converter para minúsculas</b-checkbox>
      </b-field>
      <b-field class="center">
        <b-button type="is-primary" @click="calcular">Calcular</b-button>
        <b-button
          type="is-success"
          @click="copy"
          v-if="map.length > 0"
          style="margin-left: 20px; transition: 300ms"
          >Copiar para excel</b-button
        >
      </b-field>
      <div v-if="map.length > 0">
        <b-table
          :bordered="true"
          :hoverable="true"
          :striped="true"
          :data="map"
          :columns="columns"
        ></b-table>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      removerLixo: false,
      lower: true,
      map: [],
      columns: [
        { field: "linha", label: "Linha" },
        { field: "contagem", label: "Contagem" },
        { field: "porcentagem", label: "Porcentagem" },
      ],
      msg: ``,
    };
  },
  methods: {
    modificar: function () {
      this.msg = "MODIFICADO";
    },
    calcular: function () {
      if (!this.msg) {
        alert("Campo não pode ser vazio");
        return;
      }

      if (!this.msg.includes("\n")) {
        alert("O texto inserido só possui uma linha");
        return;
      }

      const valoresTotais = this.msg.split("\n");
      console.log(valoresTotais);

      const valoresUnicos = [];

      for (let i = 0; i < valoresTotais.length; i++) {
        let valorAtual = valoresTotais[i];

        if (this.lower) {
          valorAtual = valorAtual.toLowerCase();
        }

        if (this.removerLixo) {
          valorAtual = valorAtual
            .replace(/\/\s{1,2}/g, "/")
            .normalize("NFD")
            .replace(/[\u0300-\u036f]/g, "");
        }

        const index = valoresUnicos.findIndex(
          (valor) => valor.linha === valorAtual
        );
        const existe = index !== -1;

        if (valorAtual.length <= 1) {
          continue;
        }

        if (!existe) {
          valoresUnicos.push({
            linha: valorAtual,
            contagem: 1,
            porcentagem: ((1 / valoresTotais.length) * 100).toFixed(2) + "%",
          });
        } else {
          const valorUnico = valoresUnicos[index];
          const novaContagem = valorUnico.contagem + 1;
          valoresUnicos[index] = {
            ...valorUnico,
            contagem: novaContagem,
            porcentagem:
              ((novaContagem / valoresTotais.length) * 100).toFixed(2) + "%",
          };
        }
      }

      valoresUnicos.sort((atual, proximo) =>
        atual.contagem < proximo.contagem ? 1 : -1
      );

      this.map = valoresUnicos;
    },
    copy: function () {
      let copy = "Valor\tContagem\tPorcentagem\n";
      for (const m of this.map) {
        copy += `${m.linha}\t${m.contagem}\t${m.porcentagem}\n`;
      }
      console.log(copy);

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
  max-width: 800px;
  margin-left: auto;
  margin-right: auto;
  color: #2c3e50;
  margin-top: 60px;
  margin-bottom: 60px;
}

.center {
  text-align: center;
}

.componentes {
  margin-top: 20px;
}

</style>
