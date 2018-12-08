# HandsonTableをハンズオン

[HandsonTable](https://handsontable.com/)でHTMLテーブルを生成するというHTMLソース。サーバ側には何も依存していないので、HandsonTableのJavaScriptのソースを取ってきてローカルでリンクさせれば、完全にネットワークなしで動作させることはできます。

### HTMLソース
```HTML
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Handsontable</title>
    <script src="https://cdn.jsdelivr.net/npm/handsontable@6.2.0/dist/handsontable.full.min.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/handsontable@6.2.0/dist/handsontable.full.min.css" rel="stylesheet" media="screen">
  </head>
  <body>

		<div id="grid">
		</div>
		<p>
		<textarea id="output" rows="4" cols="60"></textarea>
		</p>
		<p>
		<button id="getButton">タグをコピー</button>
		</p>

		<script src="./script.js"></script>

  </body>
</html>
```
pro版だと色々とライセンスが絡んでくるみたいなので、フリー版でやってみました。フリーの方もサーバに上げておいてくれているので問題なしです。"grid", "output", "getButton"というidをキーにしてJavaScriptでいじくります。

---
### script.js
HTMLに直書きしても良かったんだけど、お手本的に切り離してみました。
```JavaScript
var grid = document.getElementById('grid');
var output = document.getElementById('output');
var getButton = document.getElementById('getButton');

var table = new Handsontable(grid, {
    startCols: 10,
    startRows: 20,
    width: 400,
    height: 400,
    outsideClickDeselects: false,
    selectionMode: 'range',
});

getButton.addEventListener('click', function(event) {
  var selected = table.getSelected();
  var data = [];
  for (var i = 0; i < selected.length; i += 1) {
    var item = selected[i];
    data.push(table.getData.apply(table, item));
  }

  //console.log(JSON.stringify(data));
  //output.value = JSON.stringify(data);

  var htmlstr = "<table>\n";

  for ( var keyA in data ) {
    for ( var keyB in data[keyA] ) {
	  htmlstr = htmlstr + "<tr>\n";
      for ( var keyC in data[keyA][keyB] ) {
        htmlstr = htmlstr + "<td>";
	    if(data[keyA][keyB][keyC]!=null){
		  htmlstr = htmlstr + data[keyA][keyB][keyC];
		}
        htmlstr = htmlstr + "</td>\n";
	  }
	  htmlstr = htmlstr + "</tr>\n"
    }
  }

  htmlstr = htmlstr + "</table>"

  output.value = htmlstr;
  output.select();
  var retVal = document.execCommand('copy');

});
```
document.getElementById()でそれぞれHTMLからポイントして、配列をJSONにして出さずに、HTMLテーブルとして吐き出しています。コピーされるだけでも良いとは思ったけど、分かりやすいようにテキストボックスにも入るようにしました。まぁ行きがかり上というのもあるんですが…。

