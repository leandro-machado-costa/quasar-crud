<template>
  <q-page class="flex flex-center">
    <div class="q-pa-md">
      <q-btn label="Novo " color="secondary" @click="openDialog('')"></q-btn>
      <q-table title="Lista empresa" :rows="rows" :columns="columns">
        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td key="nomeFantasia" :props="props">
              {{ props.row.nomeFantasia }}
            </q-td>
            <q-td key="cnpj" :props="props">
              {{ props.row.cnpj }}
            </q-td>
            <q-td key="valorFaturamentoMensal" :props="props">
              {{ props.row.valorFaturamentoMensal }}
            </q-td>
            <q-td key="dataInicio" :props="props">
              {{ props.row.dataInicio }}
            </q-td>
            <q-td key="editar" :props="props">
              <q-btn
                label="Editar"
                color="primary"
                @click="openDialog(props.row)"
              ></q-btn>
            </q-td>
            <q-td key="excluir" :props="props">
              <q-btn
                label="Excluir"
                color="deep-orange"
                @click="remove(props.row)"
              ></q-btn>
            </q-td>
          </q-tr>
        </template>
      </q-table>
    </div>

    <q-dialog v-model="editRow">
      <q-card>
        <q-card-section>
          <div class="text-h6">Empresa</div>
        </q-card-section>

        <q-card-section class="q-pt-none">
          <q-input
            filled
            v-model="editRowItem.nomeFantasia"
            label="Nome Fantasia"
            :dense="dense"
          ></q-input>
          <q-input
            filled
            v-model="editRowItem.cnpj"
            label="CNPJ" mask="xx.xxx.xxx/xxxx-xx"
            :dense="dense"
          ></q-input>
          <q-input
            filled
            v-model="editRowItem.valorFaturamentoMensal"
      type="number" mask="#,##"
            label="Faturamento Mensal"
            :dense="dense"
          ></q-input>
          <q-input
            filled
            v-model="editRowItem.dataInicio"
          >
            <template v-slot:append>
              <q-icon name="event" class="cursor-pointer">
                <q-popup-proxy
                  ref="qDateProxy"
                  cover
                  transition-show="scale"
                  transition-hide="scale"
                >
                  <q-date v-model="editRowItem.dataInicio"  mask="DD/MM/YYYY" >
                    <div class="row items-center justify-end">
                      <q-btn
                        label="Fechar"
                        color="primary"
                        flat v-close-popup
                      ></q-btn>
                    </div>
                  </q-date>
                </q-popup-proxy>
              </q-icon>
            </template>
          </q-input>
        </q-card-section>

        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Salvar" @click="saveRow()"></q-btn>
          <q-btn flat label="Fechar" v-close-popup></q-btn>
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>






<script>
import { defineComponent } from "vue";

export default defineComponent({
  name: "IndexPage",
  data() {
    return {
      editRow: false,
      editRowItem: {},
      oldRowItem: {},
      columns: [
        {
          name: "nomeFantasia",
          required: true,
          label: "Nome Fantasia",
          align: "left",
          field: "nomeFantasia",
          sortable: true,
        },
        {
          name: "cnpj",
          align: "center",
          label: "CNPJ",
          field: "cnpj",
          sortable: true,
        },
        {
          name: "valorFaturamentoMensal",
          label: "Faturamento Mensal",
          field: "valorFaturamentoMensal",
          sortable: true,
        },
        { name: "dataInicio", label: "Data Inicio", field: "dataInicio" },
        { name: "editar", label: "Editar", field: "editar" },
        { name: "excluir", label: "Excluir", field: "excluir" },
      ],
      rows: [],
    };
  },
  created: function () {
    if (localStorage.bd)
    this.rows = JSON.parse(localStorage.bd);
    console.log(this.rows);
  },
  methods: {
    openDialog: function (row) {
      this.clearVariables();
      this.editRow = true;

      if (row != "") {
        this.editRowItem = Object.assign({}, row);
        this.oldRowItem = Object.assign({}, row);
      }
    },

    saveRow: function () {
      this.editRow = false;

      if (this.oldRowItem){
        for (let index = 0; index < this.rows.length; index++) {
          if (
            JSON.stringify(this.rows[index]) == JSON.stringify(this.oldRowItem)
          ) {
            this.rows[index] = this.editRowItem;
          }
        }
      }else{
        console.log(this.editRowItem)
        this.rows.push(this.editRowItem)
      }
      this.clearVariables();
      localStorage.bd = JSON.stringify(this.rows);
    },
    clearVariables: function () {
      this.editRowItem = {nomeFantasia:"",cnpj:"",valorFaturamentoMensal:0.0,dataInicio:""};
      this.oldRowItem = undefined;
    },
    remove: function(row){
      this.rows = this.rows.filter(item => JSON.stringify(item) !== JSON.stringify(row));
      localStorage.bd = JSON.stringify(this.rows);
    }
  },
});
</script>
