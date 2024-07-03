---
layout: page
title: project 2
description: a project with a background image and giscus comments
img: assets/img/3.jpg
importance: 2
category: work
giscus_comments: false
---
<head>
  <style>
    section#user {
      display: flex;
    }

    .hidden {
      display: none;
    }

	.stats-table {
      border-collapse: collapse;
      margin: 25px 0;
      font-size: 0.9em;
      font-family: sans-serif;
      min-width: 400px;
      box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
    }

    .stats-table thead tr {
      background-color: #c5ddd9;
      color: #ffffff;
    }

    .stats-table th,
    .stats-table td {
      padding: 12px 15px;
      min-width: 60px;
      text-align: left;
    }

    .stats-table tbody tr {
      border-bottom: 1px solid #dddddd;
    }

    .stats-table tbody tr:last-of-type {
      border-bottom: 2px solid #c5ddd9;
    }
  </style>
</head>

<body>
  <section id="user">
    <section>
      <div style="float:left;margin-right:5px;">
        <label for="original-year">Original Year</label>
        <input id="original-year" name="original-year" type="text" size="10" value="">
      </div>
      <div style="float:left;">
        <label for="converted-year">Converted Year</label>
        <input id="converted-year" name="converted-year" type="text" size="10" value="">
      </div>
      <br style="clear:both;" />
    </section>
    <section>
      <div style="float:left;margin-right:5px;">
        <label for="games">Games</label>
        <input id="games" name="games" type="text" size="10" value="">
      </div>
      <div style="float:left;margin-right:5px;">
        <label for="completions">Completions</label>
        <input id="completions" name="completions" type="text" size="10" value="">
      </div>
      <div style="float:left;margin-right:5px">
        <label for="attempts">Attempts</label>
        <input id="attempts" name="attempts" type="text" size="10" value="">
      </div>
      <div style="float:left;margin-right:5px">
        <label for="yards">Yards</label>
        <input id="yards" name="yards" type="text" size="10" value="">
      </div>
      <div style="float:left;margin-right:5px">
        <label for="touchdowns">Touchdowns</label>
        <input id="touchdowns" name="touchdowns" type="text" size="10" value="">
      </div>
      <div style="float:left;">
        <label for="interceptions">Interceptions</label>
        <input id="interceptions" name="interceptions" type="text" size="10" value="">
      </div>
      <br style="clear:both;" />
    </section>
    <section>
      <div style="float:left;margin-right:5px;">
        <input id="calculate" type="button" value="Calculate" onclick="calc()">
      </div>
    </section>
  </section>
  <div id="result" class="hidden">
    <table class="stats-table">
      <thead>
        <tr>
          <th>YR</th>
          <th>G</th>
          <th>CMP</th>
          <th>ATT</th>
          <th>CMP%</th>
          <th>YDS</th>
          <th>Y/A</th>
          <th>AY/A</th>
          <th>Y/C</th>
          <th>Y/G</th>
          <th>TD</th>
          <th>TD%</th>
          <th>INT</th>
          <th>INT%</th>
          <th>RATE</th>
        </tr>
      </thead>
      <tbody>
        <tr id="realStats"></tr>
        <tr id="hypStats"></tr>
      </tbody>
    </table>
  </div>
  <script>
    function loadCoeff() {
      return fetch('/assets/json/passer-rating.json')
        .then(response => response.json())
        .catch(error => {
          console.error('Error:', error);
          throw error;
        });
    }
    async function calc() {
      try {
        let coeff = await loadCoeff();
        const realYear = String(document.getElementById('original-year').value); const hypYear = String(document.getElementById('converted-year').value); const hashYear = Number(realYear + hypYear);
        const gameAdj = coeff[hashYear][0]; const cmpAdj = coeff[hashYear][1]; const tdAdj = coeff[hashYear][2];
        const cepAdj = coeff[hashYear][3]; const ydsAdj = coeff[hashYear][4]; const PAttAdj = coeff[hashYear][5];
        const game = Number(document.getElementById('games').value); const cmp = Number(document.getElementById('completions').value); const att = Number(document.getElementById('attempts').value);
        const yds = Number(document.getElementById('yards').value); const td = Number(document.getElementById('touchdowns').value); const cep = Number(document.getElementById('interceptions').value);
        const cmpPct = cmp / att;
        const hypGame = game * gameAdj; const hypCmpPct = cmpPct * cmpAdj; const hypAtt = att * PAttAdj * gameAdj; const hypYdsPA = (yds / att) * ydsAdj; const hypTdPct = (td / att) * tdAdj;
        const hypCepPct = (cep / att) * cepAdj; const hypCmp = hypCmpPct * hypAtt; const hypYds = hypYdsPA * hypAtt; const hypTd = hypTdPct * hypAtt; const hypCep = hypCepPct * hypAtt;
        let prAR = ((cmp / att) - 0.3) * 5; if (prAR > 2.375) { prAR = 2.375; } else if (prAR < 0) { prAR = 0; }
        let prBR = ((yds / att) - 3) * 0.25; if (prBR > 2.375) { prBR = 2.375; } else if (prBR < 0) { prBR = 0; }
        let prCR = (td / att) * 20; if (prCR > 2.375) { prCR = 2.375; } else if (prCR < 0) { prCR = 0; }
        let prDR = 2.375 - ((cep / att) * 25); if (prDR > 2.375) { prDR = 2.375; } else if (prDR < 0) { prDR = 0; }
        let prAH = ((hypCmp / hypAtt) - 0.3) * 5; if (prAH > 2.375) { prAH = 2.375; } else if (prAH < 0) { prAH = 0; }
        let prBH = ((hypYds / hypAtt) - 3) * 0.25; if (prBH > 2.375) { prBH = 2.375; } else if (prBH < 0) { prBH = 0; }
        let prCH = (hypTd / hypAtt) * 20; if (prCH > 2.375) { prCH = 2.375; } else if (prCH < 0) { prCH = 0; }
        let prDH = 2.375 - ((hypCep / hypAtt) * 25); if (prDH > 2.375) { prDH = 2.375; } else if (prDH < 0) { prDH = 0; }
        const arrReal = [realYear, game, cmp, att, (cmpPct * 100).toFixed(1), yds, (yds / att).toFixed(1),
          ((yds + (20 * td) - (45 * cep)) / att).toFixed(1), (yds / cmp).toFixed(1), (yds / game).toFixed(1), td,
          ((td / att) * 100).toFixed(1), cep, ((cep / att) * 100).toFixed(1), (((prAR + prBR + prCR + prDR) / 6) * 100).toFixed(1)];
        const arrHyp = [hypYear, hypGame.toFixed(0), hypCmp.toFixed(0), hypAtt.toFixed(0),
          (hypCmpPct * 100).toFixed(1), hypYds.toFixed(0), hypYdsPA.toFixed(1), ((hypYds + (20 * hypTd) - (45 * hypCep)) / hypAtt).toFixed(1),
          (hypYds / hypCmp).toFixed(1), (hypYds / hypGame).toFixed(1), hypTd.toFixed(0), (hypTdPct * 100).toFixed(1), hypCep.toFixed(0),
          (hypCepPct * 100).toFixed(1), (((prAH + prBH + prCH + prDH) / 6) * 100).toFixed(1)];
        popRow('realStats', arrReal);
        popRow('hypStats', arrHyp);
        document.getElementById('result').classList.remove('hidden');
      }
      catch (error) {
        console.error('Error: ', error);
      }
    }
    function popRow(rowId, arr) {
      const row = document.getElementById(rowId);
      row.innerHTML = arr.map(i => `<td>${i}</td>`).join('');
    }
  </script>
</body>

Every project has a beautiful feature showcase page.
It's easy to include images in a flexible 3-column grid format.
Make your photos 1/3, 2/3, or full width.

To give your project a background in the portfolio page, just add the img tag to the front matter like so:

    ---
    layout: page
    title: project
    description: a project with a background image
    img: /assets/img/12.jpg
    ---

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/1.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/3.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Caption photos easily. On the left, a road goes through a tunnel. Middle, leaves artistically fall in a hipster photoshoot. Right, in another hipster photoshoot, a lumberjack grasps a handful of pine needles.
</div>
<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/5.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    This image can also have a caption. It's like magic.
</div>

You can also put regular text between your rows of images.
Say you wanted to write a little bit about your project before you posted the rest of the images.
You describe how you toiled, sweated, _bled_ for your project, and then... you reveal its glory in the next row of images.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-4 mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    You can also have artistically styled 2/3 + 1/3 images, like these.
</div>

The code is simple.
Just wrap your images with `<div class="col-sm">` and place them inside `<div class="row">` (read more about the <a href="https://getbootstrap.com/docs/4.4/layout/grid/">Bootstrap Grid</a> system).
To make images responsive, add `img-fluid` class to each; for rounded corners and shadows use `rounded` and `z-depth-1` classes.
Here's the code for the last row of images above:

{% raw %}

```html
<div class="row justify-content-sm-center">
  <div class="col-sm-8 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/6.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-4 mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/11.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
```

{% endraw %}
