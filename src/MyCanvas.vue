<template>
  <div>
    <div class="two-columns">
      <!-- 二つ目のカラム -->
      <div class="column tools">
        <!-- グリッド -->
        <button v-on:click="drawRectangle">draw</button>
        <!-- 削除ボタン -->
        <button v-on:click="deleteDraw">delete</button>
        <!-- 正方形 -->
        <button v-on:click="toSquare">正方形に</button>
        <div ref="pastearea" contenteditable="true" style="background:#fcc;">
          ここで右クリックを押して<br>
          貼り付けて下さい
        </div>
        <div><img id="pastearea" src=""></div>
        <label><input type="range" v-model="grid_divide" min="1" max="10">グリッドでかさ{{   grid_divide   }}</label>
      </div>
    </div>  
      <!-- 一つ目のカラム -->
      <div class="column">
        <div class="canvas-wrap">
          <canvas ref="_canvas_photo" width="1000" height="1000" class="canvas"></canvas>
          <canvas ref="_canvas_line" width="1000" height="1000" class="canvas"></canvas>
        </div>
      </div>
      
  </div>
</template>

<script>
export default {
  data() {
    return {
      grid_divide: 6,
      canvas_x: 0,
      canvas_y: 0,
      canvas_width: 1000,
      canvas_height: 1000,
      mouseX: 0,
      mouseY: 0,
      grid_width: 100,
      grid_height: 100,
      is_line_visible: true,
    }
  },
  props: {
    radius: {
      type: Number,
      default: 50
    }
  },
  watch: {
    radius() {
      this.draw(this.radius)
    }
  },
  methods: {
    // キー押下イベント
    onKeyDown(event) {
      console.log('a down')
      // Aボタン押下
      if (event.key == 'a') {
        console.log('a down > a' + this.mouseX + ' ' + this.mouseY)
        // window.onmousemove = ()
        this.first_x = this.mouseX
        this.first_y = this.mouseY

        this.deleteDraw()

        this.drawRectangle()
      } else if (event.key == 'f') {
       // Fボタン。終点
        this.deleteDraw()
        this.second_x = this.mouseX
        this.second_y = this.mouseY

        this.grid_width =  - this.first_x + this.second_x 
        this.grid_height =  - this.first_y + this.second_ya
        this.drawRectangle()

      } else if(event.key == 'd'){
        console.log('w push')
        this.toSquare()

      } else if(event.key == 's'){
        this.deleteDraw()
      }

    },
    deleteDraw() {
      console.log('delte > ' + this.canvas_width + ' :' + this.canvas_height)
      // this.ctx_line.clearRect(0, 0, this.canvas_width, this.canvas_height)
      this.ctx_line.clearRect(0, 0, 1000, 1000)
    },
    draw(radius) {
      // // this.paste_img()
      this.logx(radius)

    },
    logx() {
      console.log('きてるよー')
    },
    paste() {
      null
    },
    drawxx() {
      // 線を描画
      this.deleteDraw()
      this.ctx.beginPath();
      this.ctx.moveTo(0, 0);
      this.ctx.lineTo(200, 200);
      this.ctx.stroke();
    },
    // 正方形に
    toSquare(){
      this.deleteDraw()

      let x1 = this.first_x
      let y1 = this.first_y
      let width = this.grid_width
      let height = this.grid_width
      let x_divide = this.grid_divide
      let y_divide = this.grid_divide

      let x_plus = width / x_divide
      let y_plus = height / y_divide
      console.log('x_plus > ' + x_plus + ' ' + y_plus)
      for (let i = 0; i <= x_divide; i++) {
        this.drawVertical(x1 + i * x_plus, y1, height)
        this.drawHorizon(x1, y1 + i * y_plus, width)
      }

    },
    drawRectangle() {

      this.deleteDraw()
      // let x1 = 100;
      // let y1 = 100;
      // let x2=300;
      // let y2=400;
      let x1 = this.first_x
      let y1 = this.first_y
      let width = this.second_x - this.first_x 
      let height = this.second_y - this.first_y 
      let x_divide = this.grid_divide
      let y_divide = this.grid_divide

      let x_plus = width / x_divide
      let y_plus = height / y_divide
      console.log('x_plus > ' + x_plus + ' ' + y_plus)
      for (let i = 0; i <= x_divide; i++) {
        this.drawVertical(x1 + i * x_plus, y1, height)
        this.drawHorizon(x1, y1 + i * y_plus, width)
      }
    },
    drawVertical(x1, y1, height) {
      this.ctx_line.beginPath();
      this.ctx_line.moveTo(x1, y1);
      this.ctx_line.lineTo(x1, y1 + height);
      this.ctx_line.stroke();
    },
    drawHorizon(x1, y1, width) {
      this.ctx_line.beginPath();
      this.ctx_line.moveTo(x1, y1);
      this.ctx_line.lineTo(x1 + width, y1);
      this.ctx_line.stroke();
    },
    move(e) {
      // console.log('mouse.pos > ' + e.x + ' ' + e.y)
      this.getMousePosition(e)
    },
    getMousePosition(evt) {
      var rect = this.canvas.getBoundingClientRect()
      // console.log('rect >' + rect.left + ' xxx ' + rect.top)

      this.mouseX = evt.clientX - rect.left
      this.mouseY = evt.clientY - rect.top


      // this.mouseX = evt.clientX
      // this.mouseY = evt.clientY

      // console.log('mouse.pos 2> ' + this.mouseX + ' ' + this.mouseY)

    }
  },




  mounted() {
    // mounted 以降で canvas の DOM にアクセスできる
    // CreateJS などを使うときにも、ここで canvas と紐付ける
    console.log(this.$el)
    this.canvas = this.$refs._canvas_line
    this.ctx = this.$refs._canvas_photo.getContext('2d')
    this.ctx_line = this.$refs._canvas_line.getContext('2d')
    // this.draw(this.radius)

    // マウス移動イベント
    document.addEventListener('mousemove', this.move);


    // ドラッグの部分
    document.addEventListener('mouseadown', () => {
      document.addEventListener('mousemove', this.move);
    });

    // ドロップの部分
    document.addEventListener('mouseup', () => {
      // document.removeEventListener('mousemove', this.move);
    });

    document.addEventListener('keydown', this.onKeyDown)

    // pasteメソッド
    window.addEventListener('paste', (pasteEvent) => {
      pasteEvent.preventDefault();
      console.log('paste lister')
      console.log(pasteEvent)
      var item = pasteEvent.clipboardData.items[0];

      if (item.type.indexOf("image") === 0) {
        var blob = item.getAsFile();
        var bloburl = URL.createObjectURL(blob);

        var reader = new FileReader();
        reader.onload = function () {
          console.log('patste-----')
          // document.getElementById("pastearea").src = event.target.result;


          // $refs.pastearea.src = event.target.result
        };
        let img = new Image()
        img.src = bloburl;

        img.onload = () => {
          console.log('onload')

          var sw = img.naturalWidth;
          var sh = img.naturalHeight;
          this.canvas_width = img.naturalWidth
          this.canvas_height = img.naturalHeight
          this.ctx.drawImage(img, 0, 0, sw, sh);
          // thiFFs.ctx.drawImage(img, 0, 0)
        }

        reader.readAsDataURL(blob);
      }
    })

  }
}
</script>

<style scoped>
.canvas-wrap {
  width: 1000px;
  max-width: 100%;
  position: relative;
  padding: 0;
  box-sizing: content-box;
  border: solid
}

.canvas_warp:before {
  content: "";
  display: block;
  padding-top: 50%;
  border: solid
}

.canvas {
  position: absolute;
  left: 0;
  top: 0;
  border: 0;
  max-width: 100%;
  box-sizing: content-box;
  padding: 0;
  margin: 0;
  border: solid
}

.tools {
  /* position: absolute;
  top: 1200px; */
}

.two-columns {
    display: flex; /* flexboxを使用 */
  }

  .column {
    flex: 1; /* カラムの幅を均等に分割 */
    padding: 20px; /* 適切な間隔を設定 */
  }

  .canvas-wrap {
    /* 一つ目のカラムのスタイル */
  }
</style>