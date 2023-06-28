<script setup>
import * as fabric from 'fabric';
import { onMounted, ref, shallowRef } from 'vue'; // v6
import QRCode from 'qrcode';
import FileSaver from 'file-saver';
import bg from './assets/share-bg-pc.56c5fc1b.png';
import bg2 from './assets/share-bg1.875eea06.png';
import bg3 from './assets/qr.80ad385f.png';

const canvasEl = ref(null);
const canvasIns = shallowRef();

onMounted(async () => {
  const canvas = new fabric.Canvas(canvasEl.value);

  canvasIns.value = canvas;

  await fabric.Image.fromURL(bg).then(pic => {
    pic.set({
      // 通过scale来设置图片大小，这里设置和画布一样大
      scaleX: canvas.width / pic.width,
      scaleY: canvas.height / pic.height,
    });

    canvas.backgroundImage = pic;
  });

  await fabric.Image.fromURL(bg2).then(img => {

    const imgWidth = 300;
    const imgHeight = 290;
    const scaleX = (imgWidth / img.width);

    img.set({
      scaleX,
      scaleY: (imgHeight / img.height),
      objectCaching: false,
      left: (canvas.width - img.width * scaleX) / 2,
      top: 50,
    });
    canvas.add(img);
      // 下移
    canvas.sendObjectBackwards(img) ;
  })

  await fabric.Image.fromURL(bg3).then(img => {
    const imgWidth = 126;
    const imgHeight = 126;
    const scaleX = (imgWidth / img.width);

    img.set({
      scaleX,
      scaleY: (imgHeight / img.height),
      objectCaching: false,
      // left: (canvas.width - img.width * scaleX) / 2,
      left: (canvas.width) / 2,
      top: 170,
      originX: 'center',
    });

    canvas.add(img);

  });

  const text = new fabric.Text('英雄联盟直播2023LOL', {
    left: canvas.width / 2,
    top: 100,
    fill: '#333',
    stroke: '#333',
    fontSize: 14,
    textAlign: 'center', // 设置文本居中对齐
    originX: 'center', // 设置originX为'center'以使文本以水平中心点为基准
  });

  canvas.add(text);
  canvas.bringObjectForward(text)

  QRCode.toDataURL('I am a pony!', function(err, url) {

    const i = new Image();
    i.src = url;
    i.width = 120;
    i.height = 120;

    const qrImg = new fabric.Image(i);

    const imgWidth = 120;
    const imgHeight = 120;
    const scaleX = (imgWidth / qrImg.width);

    qrImg.set({
      scaleX,
      scaleY: (imgHeight / qrImg.height),
      objectCaching: false,
      left: (canvas.width - qrImg.width * scaleX) / 2,
      top: 174,
    });

    canvas.add(qrImg);

    // 上移
    canvas.bringObjectForward(qrImg)

  });

});
const saveImg = () => {
  const dataUrl = fabric.util.toDataURL(canvasIns.value, 'jpeg', 1);
  FileSaver.saveAs(dataUrl, 'qrcode.jpeg');

};

</script>

<template>
  <div class="min-h-screen flex flex-col justify-center items-center">
    <canvas width="400" height="420" class="" ref="canvasEl" />
    <button @click="saveImg" class="mt-6 px-4 py-2 bg-blue-300/70 border-2 border-blue-400 rounded-lg">save</button>
  </div>

</template>

<style scoped>

</style>
