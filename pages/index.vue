<template>
  <div class="contenedor-flex">
    <div class="instrucciones">
      <h3>Selecciona una fecha para ver la imagen de la tierra en ese momento</h3>
    </div>

    <div class="contenedor-calendario" :style="{ border: '1px solid #d9d9d9', borderRadius: '4px' }">
      <a-calendar :fullscreen="false" @panelChange="onPanelChange" @select="ondia" />
    </div>
    <div class="imagentierra">
      <img :src="imagensrc" class="imagen">
    </div>
  </div>
</template>

<script>
import moment from 'moment'
import axios from 'axios'

export default {
  data () {
    return {
      imagenid: null,
      imagensrc: null
    }
  },
  methods: {
    onPanelChange (value, mode) {
      // eslint-disable-next-line no-console
      console.log(value, mode)
    },

    async ondia (dia) {
      // selecciona fecha y transforma a formato para solicitud a api
      const fechaseleccionada = dia._d
      const fechamoment = moment(fechaseleccionada)
      const fechaaxios = fechamoment.format('YYYY-MM-DD')
      const fechaimg = fechamoment.format('YYYY/MM/DD')
      // eslint-disable-next-line no-console
      console.log(fechaaxios)
      // key para solicitudes a EPIC api
      const key = '?api_key=L3nncLqxy28c3v5x6d2DD2kFvlk2xjYosYFeePd0'
      // solicita array de imagenes disponibles
      try {
        const imgDisponibles = await axios.get('https://api.nasa.gov/EPIC/api/natural/date/' + fechaaxios + key)
        // eslint-disable-next-line no-console
        console.log('respuesta axios', imgDisponibles)
        const imagenid = imgDisponibles.data[0].image
        // eslint-disable-next-line no-console
        console.log(imagenid)
        this.imagenid = imagenid
      } catch (error) {
        // eslint-disable-next-line no-console
        console.error('ontener imagenes', error)
      }
      const urlimagen = 'https://api.nasa.gov/EPIC/archive/natural/' + fechaimg + '/jpg/' + this.imagenid + '.jpg' + key
      // eslint-disable-next-line no-console
      console.log(urlimagen)
      this.imagensrc = urlimagen
    }
  }
}
</script>
<style scoped>
.instrucciones{
 text-align: center;
 width: 100vw;
 border-style: solid;
 border-color: rgb(212, 106, 198);
 border-radius: 5px;
}
.contenedor-flex {
 justify-content: center;
 display: flex;
 flex-wrap: wrap;
}
.contenedor-calendario{
 width: 400px;
}

.imagen {
 width: 50vw;

}

@media screen and (max-width: 800px) {
.contenedor-calendario{
 max-width: 400px;
}
.imagen {
 width: 90vw;

}
}
</style>
