<template>
  <div class="page">
     <canvas v-for="page in pages" :id="'the-canvas'+page" :key="page"></canvas>
  </div>
</template>

<script>
import PDFJS from "pdfjs-dist";
  export default {
    data () {
      return {
        pdfDoc:null,
        pages:0,
        ischange:false,
      }
    },
    components: {

    },
    methods: {
                 _renderPage(num) {
      this.pdfDoc.getPage(num).then(page => {
        let canvas = document.getElementById("the-canvas" + num);
        let ctx = canvas.getContext("2d");
        let dpr = window.devicePixelRatio || 1;
        let bsr =
          ctx.webkitBackingStorePixelRatio ||
          ctx.mozBackingStorePixelRatio ||
          ctx.msBackingStorePixelRatio ||
          ctx.oBackingStorePixelRatio ||
          ctx.backingStorePixelRatio ||
          1;
        let ratio = dpr / bsr;
        let viewport = page.getViewport(
          screen.availWidth / page.getViewport(1).width
        );
        canvas.width = viewport.width * ratio;
        canvas.height = viewport.height * ratio;
        canvas.style.width = viewport.width + "px";
        canvas.style.height = viewport.height + "px";
        ctx.setTransform(ratio, 0, 0, ratio, 0, 0);
        let renderContext = {
          canvasContext: ctx,
          viewport: viewport
        };
        page.render(renderContext);
        if (this.pages > num) {
          this._renderPage(num + 1);
        }
      });
    },
    _loadFile(url) {
      PDFJS.getDocument(url).then(pdf => {
        this.pdfDoc = pdf;
        this.pages = this.pdfDoc.numPages;
        this.$nextTick(() => {
          this._renderPage(1);
        });
      });
    }
    },
    computed: {

    },
    mounted(){
      let getpdf = 'https://static-oss-shengqianxiong-produce.oss-cn-hangzhou.aliyuncs.com/FILE19092820068537/MER19101581917608contract.pdf';
      // let getpdf='./vendor/test.pdf'
    let obj = {};
    obj.url = `${getpdf}?`;
    obj.httpHeaders = {
      token: localStorage.getItem("token")
    };
    const CMAP_URL = 'https://cdn.jsdelivr.net/npm/pdfjs-dist@2.0.943/cmaps/';
    obj.cMapUrl = CMAP_URL
    obj.cMapPacked = true
    this._loadFile(obj);
    },
  }
</script>

<style scoped >

 
</style>
