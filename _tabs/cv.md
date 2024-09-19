---
layout: CV
icon: fas fa-archive
order: 5
---

<iframe src="https://docs.google.com/viewer?url=https://github.com/Black-Kamous/black-kamous.github.io/raw/main/_tabs/cv.pdf&embedded=true" style="width:100%; height:600px;" frameborder="0"></iframe>

<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/4.6.82/pdf.min.mjs"></script>

<div style="width: 100%; height: 600px;">
<canvas id="pdf-canvas" style="border: 1px solid;"></canvas>
</div>

<script>
var url = 'https://github.com/Black-Kamous/black-kamous.github.io/raw/main/_tabs/cv.pdf';

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

