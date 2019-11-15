<template>
  <div class="app-container">
    <p>Instructions: Copy an small-size snapshoot to clipboard , click on the target textinput to paste the image into, and paste cmd+V or ctrl+v. </p>
    <el-form ref="form" :model="form" label-width="200px">
      <el-form-item label="input">
        <textarea @paste="onPaste"></textarea>
      </el-form-item>
      <el-form-item label="result
      ">
        <img v-bind:src="img_src">
      </el-form-item>
    </el-form>
    <el-table :data="result" ref="multipleTable">
      <el-table-column prop="name" label="">
        <template scope="scope">
          <span v-bind:style="{ color:scope.row.color  }">{{scope.row.name}}</span>
        </template>
      </el-table-column>
      <el-table-column prop="rgb" label="rgb"></el-table-column>
      <el-table-column prop="hex" label="HEX"></el-table-column>
    </el-table>
  </div>
</template>

<style>
.el-button {
  margin-top: 20px;
}
</style>

<script>
export default {
  data() {
    return {
      form: null,
      img_src: null,
      selected: false,
      picData: "",
      selected_config: null,
      result: [],
      options: [],
      tableData: [],
      itemData: {
        name: "",
        value: ""
      },
      dialogTableVisible: false,
      resultVisible: false,
      times: 1
    };
  },
  mounted() {},
  methods: {
    loadClipborad() {
      var data = ClipboardEvent;
    },
    onPaste(e) {
      if (!(e.clipboardData && e.clipboardData.items)) {
        return;
      }
      var _this = this;
      var reader = new FileReader();
      var f;
      for (var i = 0, len = e.clipboardData.items.length; i < len; i++) {
        var item = e.clipboardData.items[i];
        console.log(item);
        if (item.kind === "file") {
          f = item.getAsFile();
          console.log(f);
          break;
        }
      }
      var img = new Image();
      img.onload = function() {
        var canvas = document.createElement("canvas");
        canvas.width = img.width;
        canvas.height = img.height;
        canvas.getContext("2d").drawImage(img, 0, 0, img.width, img.height);
        var pixelData = canvas
          .getContext("2d")
          .getImageData(0, 0, img.width, img.height).data;
        var r = 0;
        var g = 0;
        var b = 0;
        for (var i = 0; i < pixelData.length; i += 4) {
          r += pixelData[i];
          g += pixelData[i + 1];
          b += pixelData[i + 2];
        }
        r /= img.width * img.height;
        g /= img.width * img.height;
        b /= img.width * img.height;

        // 将最终的值取整
        r = Math.round(r);
        g = Math.round(g);
        b = Math.round(b);
        console.log("rgb(" + r + "," + g + "," + b + ")");
        _this.result = [];
        var item = {};
        item.name = "██";
        item.rgb = r + "," + g + "," + b;
        item.color = "rgb(" + r + "," + g + "," + b + ")";
        item.hex = _this.rgb2Hex(item.color);
        _this.result.push(item);
      };

      reader.onprogress = function(e) {
        _this.img_src = e.target.result;
        img.src = e.target.result;
      };
      if (f) {
        reader.readAsDataURL(f);
      }
    },
    rgb2Hex(rgb) {
      if (/^rgb\((\d{1,3}\,){2}\d{1,3}\)$/i.test(rgb)) {
        //test RGB
        var hex = "#"; //定义十六进制颜色变量
        rgb.replace(/\d{1,3}/g, function(kw) {
          //提取rgb数字
          kw = parseInt(kw).toString(16); //转为十六进制
          kw = kw.length < 2 ? 0 + kw : kw; //判断位数，保证两位
          hex += kw; //拼接
        });
        return hex; //返回十六进制
      } else {
        console.log(`Input ${rgb} is wrong!`);
        return "#000"; //输入格式错误,返回#000
      }
    }
  },
  created() {}
};
</script>