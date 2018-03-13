<template>
  <div class="slidersecter">
    <canvas id="convasd" class="convasd" :width='canvasWidth' :height='canvasHeight'></canvas>
  </div>
</template>

<script>
export default {
  name: 'SliderSecter',
  data() {
    return {
      canvas: '',
      ctx: '',
      arrys: [],
      countDeg: [],
    };
  },
  props: {
    colorSecter: {
      type: Object,
      default: () => (
        { bg: 'rgba(0,100,200,0.3)',
          selectedBg: 'red',
          borderColor: '#aaa',
          selectedBorderColor: '#aaa',
          textColor: '#000' }
      ),
    },
    textList: {
      type: Array,
      default: () => ([]),
    },
    canvasWidth: {
      type: Number,
      default: 300,
    },
    canvasHeight: {
      type: Number,
      default: 300,
    },
    origin: {
      type: Object,
      default: () => ({ x: 150, y: 150, r: 100, startdeg: 240, opendeg: 25, count: 5 }),
      validator: (val) => {
        window.console.log(val);
        let flag = true;
        const { x, y, r, startdeg, opendeg, count } = val;
        if (x === undefined || y === undefined || r === undefined || startdeg === undefined
          || opendeg === undefined || count === undefined) {
          flag = false;
        }
        if (flag && opendeg * count > 360) {
          window.console.warn('开合度与数量的积不能大于360deg');
          flag = false;
        }
        return flag;
      },
    },
  },
  mounted() {
    this.draw();
  },
  methods: {
    draw() {
      this.canvas = document.getElementById('convasd');
      this.ctx = this.canvas.getContext('2d');
      this.drawSector(this.colorSecter.bg, this.colorSecter.borderColor);
      this.doClicke();
      this.countSecter();
      this.getTextOrigin(this.colorSecter.textColor);
    },
    getTextOrigin(color) {
      const obj = this.countDeg;
      const hypotenuse = this.origin.r / 2;
      for (let i = 0; i < obj.length; i += 1) {
        let deg = obj[i].max - (this.origin.opendeg / 2);
        window.console.log(deg);
        if (deg < 0) {
          deg = 360 + deg;
        }
        if (deg > 0 && deg < 90) {
          const degs = (deg * Math.PI) / 180;
          const onEdge = Math.sin(degs) * hypotenuse;
          const adjacentEdges = Math.sqrt((hypotenuse ** 2) - (onEdge ** 2));
          this.drawText(obj[i].id, this.origin.x + adjacentEdges, this.origin.x + onEdge, color);
        } else if (deg > 90 && deg < 180) {
          const degs = ((180 - deg) * Math.PI) / 180;
          const onEdge = Math.sin(degs) * hypotenuse;
          const adjacentEdges = Math.sqrt((hypotenuse ** 2) - (onEdge ** 2));
          window.console.log(onEdge);
          this.drawText(obj[i].id, this.origin.x - adjacentEdges, this.origin.y + onEdge, color);
        } else if (deg > 180 && deg < 270) {
          const degs = ((deg - 180) * Math.PI) / 180;
          const onEdge = Math.sin(degs) * hypotenuse;
          const adjacentEdges = Math.sqrt((hypotenuse ** 2) - (onEdge ** 2));
          this.drawText(obj[i].id, this.origin.x - adjacentEdges, this.origin.y - onEdge, color);
        } else if (deg > 270 && deg < 360) {
          const degs = ((360 - deg) * Math.PI) / 180;
          const onEdge = Math.sin(degs) * hypotenuse;
          const adjacentEdges = Math.sqrt((hypotenuse ** 2) - (onEdge ** 2));
          this.drawText(obj[i].id, this.origin.x + adjacentEdges, this.origin.y - onEdge, color);
        } else if (deg === 0 || deg === 360) {
          this.drawText(obj[i].id, this.origin.x + hypotenuse, this.origin.y, color);
        } else if (deg === 90) {
          this.drawText(obj[i].id, this.origin.x, this.origin.y + hypotenuse, color);
        } else if (deg === 180) {
          this.drawText(obj[i].id, this.origin.x - hypotenuse, this.origin.y, color);
        } else if (deg === 270) {
          this.drawText(obj[i].id, this.origin.x, this.origin.y - hypotenuse, color);
        }
      }
    },
    countSecter() {
      for (let i = 1; i <= this.origin.count; i += 1) {
        let start = this.origin.startdeg - (this.origin.opendeg * i);
        let end = this.origin.startdeg - (this.origin.opendeg * (i - 1));
        if (start > 0 && end > 0) {
          this.countDeg.push({ id: `${i}`, min: start, max: end, flag: true });
        } else if (start < 0 && end < 0) {
          start = 360 + start;
          end = 360 + end;
          this.countDeg.push({ id: `${i}`, min: start, max: end, flag: true });
        } else if (start < 0 && end > 0) {
          start = 360 + start;
          this.countDeg.push({ id: `${i}`, min: start, max: end, flag: false });
        } else {
          this.countDeg.push({ id: `${i}`, min: start, max: end, flag: true });
        }
      }
      window.console.log(this.countDeg);
    },
    drawSector(fillcolors, strokeStyle) {
      // this.drawText('上', 100, 100);
      for (let i = 1; i <= this.origin.count; i += 1) {
        const start = this.origin.startdeg - (this.origin.opendeg * i);
        const end = this.origin.startdeg - (this.origin.opendeg * (i - 1));
        this.sector(fillcolors, strokeStyle,
          this.origin.x, this.origin.y, this.origin.r, start, end, false);
      }
    },
    sector(fillcolors, strokeStyle, x, y, r, start, end, desction) {
      const ctx = this.ctx;
      ctx.strokeStyle = strokeStyle;
      ctx.fillStyle = fillcolors;
      ctx.beginPath();
      ctx.moveTo(x, y);
      ctx.arc(x, y, r, (Math.PI / 180) * start, (Math.PI / 180) * end, desction);
      ctx.lineTo(x, y);
      ctx.stroke();
      ctx.fill();
    },
    drawText(text, x, y, style) {
      this.ctx.beginPath();
      this.ctx.fillStyle = style;
      this.ctx.textBaseline = 'middle';
      this.ctx.textAlign = 'center';
      this.ctx.fillText(text, x, y);
      this.ctx.closePath();
    },
    doClicke() {
      this.canvas.addEventListener('touchstart', (e) => {
        e.preventDefault();
        const dont = this.getClickXy(e.touches[0].clientX, e.touches[0].clientY);
        const degrees = this.getDegrees(dont)[0];
        const angleEdgeC = this.getDegrees(dont)[1];
        window.console.log(degrees);
        if (angleEdgeC <= this.origin.r) {
          this.arrys = [];
          for (let i = 0; i < this.countDeg.length; i += 1) {
            const flag1 = this.countDeg[i].min < degrees && degrees < this.countDeg[i].max;
            const flag2 = (this.countDeg[i].max > degrees && degrees > 0)
             || (this.countDeg[i].min < degrees && degrees < 360);
            if (this.countDeg[i].flag && flag1) {
              this.arrys.push(`${this.countDeg[i].id}`);
            } else if (!this.countDeg[i].flag && flag2) {
              this.arrys.push(`${this.countDeg[i].id}`);
            }
          }
          this.controlDraw();
        }
      });
      this.canvas.addEventListener('touchmove', (e) => {
        e.preventDefault();
        const dont = this.getClickXy(e.touches[0].clientX, e.touches[0].clientY);
        const degrees = this.getDegrees(dont)[0];
        const angleEdgeC = this.getDegrees(dont)[1];
        if (angleEdgeC <= this.origin.r) {
          for (let i = 0; i < this.countDeg.length; i += 1) {
            const flag1 = this.countDeg[i].min < degrees && degrees < this.countDeg[i].max;
            const flag2 = (this.countDeg[i].max > degrees && degrees > 0)
             || (this.countDeg[i].min < degrees && degrees < 360);
            if (this.countDeg[i].flag && flag1) {
              if (this.arrys.indexOf(this.countDeg[i].id) === -1) {
                const lastNum = window.parseInt(this.arrys[this.arrys.length - 1]);
                const nowNum = window.parseInt(this.countDeg[i].id);
                const adsNum = Math.abs(lastNum - nowNum);
                if (adsNum === 1) {
                  this.arrys.push(this.countDeg[i].id);
                }
              } else {
                const str = this.arrys.join('');
                this.arrys = str.substr(0, str.indexOf(this.countDeg[i].id) + 1).split('');
              }
            } else if (!this.countDeg[i].flag && flag2) {
              if (this.arrys.indexOf(this.countDeg[i].id) === -1) {
                const lastNum = window.parseInt(this.arrys[this.arrys.length - 1]);
                const nowNum = window.parseInt(this.countDeg[i].id);
                const adsNum = Math.abs(lastNum - nowNum);
                if (adsNum === 1) {
                  this.arrys.push(this.countDeg[i].id);
                }
              } else {
                const str = this.arrys.join('');
                this.arrys = str.substr(0, str.indexOf(this.countDeg[i].id) + 1).split('');
              }
            }
          }
          this.controlDraw();
        }
      });
      this.canvas.addEventListener('touchend', () => {
        // window.console.log(this.arrys);
        this.$emit('change', this.arrys);
      });
    },
    getDegrees(dont) {
      const angleEdgeA = (this.origin.x - dont.x);
      const angleEdgeB = (this.origin.y - dont.y);
      const angleEdgeC = Math.sqrt((angleEdgeA ** 2) + (angleEdgeB ** 2));
      let degrees = (Math.asin((this.origin.y - dont.y) / angleEdgeC) / Math.PI) * 180;
      if (angleEdgeA > 0 && angleEdgeB > 0) {
        degrees = Math.abs(degrees) + 180;
      } else if (angleEdgeA > 0 && angleEdgeB < 0) {
        degrees = 180 - Math.abs(degrees);
      } else if (angleEdgeA < 0 && angleEdgeB < 0) {
        degrees = Math.abs(degrees);
      } else if (angleEdgeA < 0 && angleEdgeB > 0) {
        degrees = 360 - Math.abs(degrees);
      } else if (angleEdgeA === 0 && angleEdgeB > 0) {
        degrees = 270;
      } else if (angleEdgeA === 0 && angleEdgeB < 0) {
        degrees = 90;
      } else if (angleEdgeA > 0 && angleEdgeB === 0) {
        degrees = 180;
      } else if (angleEdgeA < 0 && angleEdgeB === 0) {
        degrees = 0;
      }
      return [degrees, angleEdgeC];
    },
    controlDraw() {
      const ctx = this.ctx;
      ctx.clearRect(0, 0, 300, 300);
      this.drawSector(this.colorSecter.bg, this.colorSecter.borderColor);
      for (let i = 0; i < this.arrys.length; i += 1) {
        const now = this.arrys[i];
        for (let j = 0; j < this.countDeg.length; j += 1) {
          const deg = this.countDeg[j];
          if (deg.id === now) {
            this.sector(this.colorSecter.selectedBg, this.colorSecter.selectedBorderColor,
              this.origin.x, this.origin.y,
              this.origin.r, deg.min, deg.max, false);
          }
        }
      }
      this.getTextOrigin(this.colorSecter.textColor);
    },
    getClickXy(x, y) {
      const bbox = this.canvas.getBoundingClientRect();
      return { x: x - bbox.left, y: y - bbox.top };
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.convasd{
 margin: 5% auto;
}

</style>
