# 飲み込めていないメソッドを調べる

- TopoJSONを扱う際の文法
- datumメソッドが何をしているか
- topojson.meshとは何か？
- d3.map()は何か？

# TopoJSONを扱う際の文法

d3ではTopoJSONのままでは使えない。GeoJSONに変換し、featureのみSVGのデータを作り使用する事で地図を作成出来る。

```js
svg.append("g")
  .attr("class","countries")
  .selectAll("path")
  .data(topojson.feature(us, us.objects.counties).features)
```

読み込んだTopoJSONをGeoJSONに変換。TopoJSONの`feature`と言うメソッドを使いPolygonなど様々な種類のデータの型からFeatureのみを抽出。

```js
topojson.feature(us, us.objects.counties)
```

出力すると下記のようなオブジェクトになる。

```
{type: "FeatureCollection", features: Array(3142)}
```

featuresのみが必要。

```js
topojson.feature(us, us.objects.counties).features
```

# datumメソッドが何をしているか

dataは配列を引数に取り複数のSVG要素を作成、datumは1つのSVG要素を作成する。下記２つは同じ意味。

```js
  ~ 略 ~
  .datum(topojson.mesh(us,us.objects.states, (a,b) => {
    return a !== b;
  }))
  ~ 略 ~

  ~ 略 ~
  .data([topojson.mesh(us,us.objects.states, (a,b) => {
    return a !== b;
  })])
  ~ 略 ~
```

# topojson.meshとは何か？

州同士の境界線は重なる。各州の重なりった境界線が１つになった線を作成する。幾つも枝分かれした複雑な線になる。

```js
svg.append("path")
  .datum(topojson.mesh(us,us.objects.states, (a,b) => {
    return a !== b;
  }))
  .attr("class","states")
  .attr("d",path);
```

`!==`とすると境界線（重なるもの）で`===`とすると他の州と共有していない１本の境界線が取得出来る。

[参考：BorderMesh](https://bl.ocks.org/mbostock/8ba407a7a53d80be62a8d54774663c2f)

# d3.map()は何か？

ES6のmapと同じ。基本的にキー・バリューのオブジェクトと同じだが、バリューの設定や取得などのためにメソッドが用意されている。

```js
const memberList = new Map();

memberList.set(20,'鈴木');
memberList.set(23,'田中');
memberList.set(37,'高橋');

console.log(memberList.get(23)); // 田中
console.log(memberList.has(37)); // true
```