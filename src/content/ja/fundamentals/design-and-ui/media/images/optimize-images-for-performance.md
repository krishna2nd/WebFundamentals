project_path: /web/_project.yaml
book_path: /web/fundamentals/_book.yaml
description: 画像は、ダウンロードするデータ量のほとんどを占めることが多く、また、ページ上の面積の多くを占めることもよくあります。

{# wf_review_required #}
{# wf_updated_on: 2014-04-29 #}
{# wf_published_on: 2000-01-01 #}

# 画像を最適化してパフォーマンスを向上させる {: .page-title }

{% include "_shared/contributors/TODO.html" %}



画像は、ダウンロードするデータ量のほとんどを占めることが多く、また、ページ上の面積の多くを占めることもよくあります。そのため、画像の最適化によって、かなりのデータ量を節約してウェブサイトのパフォーマンスを向上できる場合が少なくありません。ブラウザでダウンロードする必要のあるデータが減れば、クライアントの帯域幅の競合も減るため、すべてのアセットを素早くダウンロードして表示できるようになります。


## TL;DR {: .hide-from-toc }
- 画像形式は無作為に選択せず、利用可能な各形式を理解したうえで最適な形式を使用する。
- ワークフローに画像最適化ツールと圧縮ツールを組み込み、ファイルのサイズを小さくする。
- 利用頻度の高い画像をスプライト画像に置くことで、HTTP リクエストの回数を減らす。
- ページの初期読み込み時間を短縮して初期のページ容量を削減するために、スクロールして画像がビューに表示された時点で読み込むようにすることを検討する。


## 適切な形式を選ぶ

画像については、[ベクター画像](http://en.wikipedia.org/wiki/Vector_graphics)と[ラスター画像](http://en.wikipedia.org/wiki/Raster_graphics)の 2 種類を検討する必要があります。ラスター画像では、適切な圧縮形式（GIF、PNG、JPG など)を選ぶことも必要です。

**ラスター画像**とは、独立したドットやピクセルのグリッドとして表現される、写真などの画像です。ラスター画像は通常、カメラやスキャナで作成されますが、ブラウザ内で canvas 要素を使用して作成することもできます。画像サイズが大きくなると、ファイルのサイズも大きくなります。ラスター画像は元のサイズよりも大きくスケーリングすると不鮮明になるため、ブラウザ側で欠落しているピクセルを推測して補正する必要があります。

**ベクター画像**（ロゴやラインアートなど）は、曲線、直線、形状、塗りつぶし色のセットで定義されます。ベクター画像は、Adobe Illustrator や Inkscape などのプログラムを使用して作成され、[SVG](http://css-tricks.com/using-svg/)（リンク先は英語）などのベクター形式で保存されます。ベクター画像はシンプルなプリミティブで構成されているため、ファイルサイズを変えることなく、品質を低下させずにスケーリングできます。

適切な形式の選択にあたっては、画像の起源（ラスターまたはベクター）とコンテンツ（色、アニメーション、テキストなど）の両方を考慮することが重要です。すべての画像タイプに対応できる形式は存在しません。各形式にはそれぞれ長所と短所があります。

適切な形式を選ぶ際は、まず次のガイドラインを参考にします。

* 写真画像には JPG を使用します。
* ベクターアートや無地のグラフィック（ロゴやラインアートなど）には SVG を使用します。
  ベクターアートを利用できない場合は、WebP か PNG を使用します。
* GIF ではなく PNG を使用します。これは、PNG のほうが使用できる色数が多く、圧縮率も優れているためです。
* 長いアニメーションには、画質に優れユーザーが再生を制御できる <video> の使用を検討します。

## ファイルサイズを小さくする

画像ファイルのサイズは、保存後に後処理を行うことで大幅に削減できます。画像圧縮用のツールには、可逆と非可逆、オンライン、GUI、コマンドライン用など数多くのツールがあります。可能であれば、ワークフローにおける画像の最適化作業を自動化することをおすすめします。

JPG および PNG ファイルで画質を損なわずに、さらに可逆の圧縮を行うことのできるツールがいくつかあります。JPG の場合は、[jpegtran](http://jpegclub.org/) または [jpegoptim](http://freshmeat.net/projects/jpegoptim/)（Linux でのみ利用可能、--strip-all オプションを付けて実行）を利用できます（リンク先はすべて英語）。PNG の場合は、[OptiPNG](http://optipng.sourceforge.net/) または [PNGOUT](http://www.advsys.net/ken/util/pngout.htm) を利用できます（リンク先はすべて英語）。

## 画像スプライトを使用する

CSS スプライトは、複数の画像を 1 つの「スプライト シート」画像にまとめる手法です。個々の画像を使用するには、要素（スプライト シート）の背景画像とオフセットを指定して目的のパーツを表示します。

<a href="https://googlesamples.github.io/web-fundamentals/samples/../fundamentals/design-and-ui/media/images/image-sprite.html"><img src="img/sprite-sheet.png" class="center" alt="例で使用されている画像スプライト シート"></a>
<pre class="prettyprint">
{% includecode content_path="web..//fundamentals/design-and-ui/media/images/_code/image-sprite.html" region_tag="sprite" lang=css %}
</pre>

画像スプライトを使用すると、複数の画像を取得するために必要なダウンロードの数を減らせるメリットがありますが、キャッシュを有効にしておく必要があります。

## 遅延読み込みの使用を検討する

遅延読み込みを使用すると、未表示の部分に多数の画像を含んだ長いページの読み込みを大幅に短縮できます。必要に応じて画像を読み込むか、メイン コンテンツの読み込みとレンダリングの完了後に画像を読み込むことで時間を短縮します。遅延読み込みを使用すると、パフォーマンスの向上だけでなく、無限にスクロールするページの作成も可能になります。

無限にスクロールするページを作成する場合、コンテンツは表示される時点で読み込まれるため、検索エンジンがそのコンテンツを認識できない場合があることに注意する必要があります。また、ユーザーがフッターに掲載されている情報を探している場合、新しいコンテンツが常に読み込まれるため、フッターをなかなか表示できない可能性があります。

{% include shared/related_guides.liquid inline=true list=page.related-guides.optimize %}



