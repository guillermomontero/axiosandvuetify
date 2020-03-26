<template>
  <div>
    <v-row>
      <v-col cols="12">
        <v-card>
          <v-date-picker
            v-model="fecha"
            full-width
            locale="es-es"
            :min="minimo"
            :max="maximo"
            color="blue-grey darken-3"
            @change="getDolar(fecha)"
          >
          </v-date-picker>
        </v-card>
        <v-card color="blue-grey darken-3" dark>
          <v-card-text class="display-1 text-center" v-if="valor !== 'Sin resultados'">
            1€ = {{ valor }} Pesos
          </v-card-text>
          <v-card-text class="display-1 text-center" v-else>
            {{ valor }}
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import axios from 'axios'
import { mapMutations } from 'vuex'

export default {
  name: 'Home',
  data() {
    return {
      fecha: new Date().toISOString().substr(0, 10),
      minimo: '1999',
      maximo: new Date().toISOString().substr(0, 10),
      valor: null
    }
  },
  methods: {
    async getDolar(dia) {
      let formatFecha = dia.split('-').reverse().join('-');
      try {
        this.mostrarLoading({ titulo: 'Obteniendo datos', color: 'blue-grey darken-3' })
        let datos = await axios.get(`https://mindicador.cl/api/euro/${formatFecha}`)
        if(datos.data.serie.length > 0) {
          this.valor = await datos.data.serie[0].valor
        } else {
          this.valor = 'Sin resultados'
        }
      } catch(error) {
        // console.log(error)
      }
      finally {
        this.ocultarLoading()
      }
    },

    ...mapMutations(['mostrarLoading', 'ocultarLoading'])
  },

  created() {
    this.getDolar(this.fecha)
  }
}
</script>
