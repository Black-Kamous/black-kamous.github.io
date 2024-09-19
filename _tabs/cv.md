---
layout: CV
icon: fas fa-archive
order: 5
---

<iframe src="https://docs.google.com/viewer?url=https://github.com/Black-Kamous/black-kamous.github.io/raw/main/_tabs/cv.pdf&embedded=true" style="width:100%; height:100%;" frameborder="0"></iframe>

<!--
<div style="width: 100%; height: 600px;">
<canvas id="pdf-canvas" style="border: 1px solid;"></canvas>
</div>

<script type="module">
var url = 'https://github.com/Black-Kamous/black-kamous.github.io/raw/main/_tabs/cv.pdf';

import pdfjsDist from 'https://cdn.jsdelivr.net/npm/pdfjs-dist@4.6.82/+esm'
pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdn.jsdelivr.net/npm/pdfjs-dist@4.6.82/build/pdf.worker.mjs'


// 使用pdf.js渲染和显示PDF
pdfjsLib.getDocument(url).promise.then(function(pdfDoc) {
 var canvas = document.getElementById('pdf-canvas');
 var context = canvas.getContext('2d');

 // 获取PDF的第一页
 pdfDoc.getPage(1).then(function(page) {
   var viewport = page.getViewport({scale: 1});
   canvas.height = viewport.height;
   canvas.width = viewport.width;

   // 渲染PDF页面到canvas
   page.render({canvasContext: context, viewport: viewport});
 });
});
</script>
-->
