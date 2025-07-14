# pixelmatch ライブラリ調査

## 1. 概要

`pixelmatch` は、2つの画像のピクセルレベルでの比較を行うための、軽量で高速なJavaScriptライブラリです。主にスクリーンショットのテストなどで、画像間の差異を検出するために使用されます。アンチエイリアスされたピクセルの検出や、知覚的な色差の測定といった機能を備えています。

## 2. インストール

npm を使用してインストールします。

```bash
npm install pixelmatch pngjs
```

`pngjs` はPNG画像の読み書きに必要です。

## 3. 基本的な使い方

### Node.js 環境での例

```javascript
import fs from 'fs';
import { PNG } from 'pngjs';
import pixelmatch from 'pixelmatch';

// 画像の読み込み
const img1 = PNG.sync.read(fs.readFileSync('image1.png'));
const img2 = PNG.sync.read(fs.readFileSync('image2.png'));
const { width, height } = img1;

// 差分画像を格納するためのPNGオブジェクトを作成
const diff = new PNG({ width, height });

// 画像を比較し、異なるピクセル数を取得
const numDiffPixels = pixelmatch(img1.data, img2.data, diff.data, width, height);

// 差分画像を保存
fs.writeFileSync('diff.png', PNG.sync.write(diff));

console.log(`異なるピクセル数: ${numDiffPixels}`);
```

### オプションの使用例

`pixelmatch` 関数は、最後の引数としてオプションオブジェクトを受け取ります。これにより、比較の挙動や差分画像の生成方法を細かく制御できます。

```javascript
import fs from 'fs';
import { PNG } from 'pngjs';
import pixelmatch from 'pixelmatch';

const img1 = PNG.sync.read(fs.readFileSync('image1.png'));
const img2 = PNG.sync.read(fs.readFileSync('image2.png'));
const { width, height } = img1;
const diff = new PNG({ width, height });

// オプションを指定して画像を比較
const numDiffPixelsWithOptions = pixelmatch(
    img1.data, img2.data, diff.data, width, height,
    {
        threshold: 0.05,      // 閾値を0.05に設定 (デフォルトは0.1)
        includeAA: false,     // アンチエイリアスされたピクセルを無視 (デフォルトはfalse)
        alpha: 0.5,           // 変更されていないピクセルのブレンド係数を0.5に設定 (デフォルトは0.1)
        aaColor: [0, 255, 0], // アンチエイリアスされたピクセルの色を緑に設定 (デフォルトは[255, 255, 0] - 黄色)
        diffColor: [0, 0, 255], // 異なるピクセルの色を青に設定 (デフォルトは[255, 0, 0] - 赤)
        diffMask: false       // 差分を透明な背景ではなく元の画像の上に描画 (デフォルトはfalse)
    }
);

fs.writeFileSync('diff_with_options.png', PNG.sync.write(diff));

console.log(`オプション付きで異なるピクセル数: ${numDiffPixelsWithOptions}`);
```

## 4. 要件定義との適合性評価

| 要件項目 | pixelmatch の適合性 |
|---|---|
| **機能要件** | |
| 差分検出 | 〇 (ピクセル単位で検出可能) |
| 差分数値化 | 〇 (異なるピクセル数を返却) |
| 差分画像生成 | 〇 (差分画像を生成可能) |
| **精度と柔軟性に関する要件** | |
| 閾値設定 | 〇 (`threshold` オプションで設定可能) |
| アンチエイリアシングの考慮 | 〇 (`includeAA` オプションで制御可能) |
| 無視領域（Ignore Regions）の指定 | △ (直接的なAPIはなし。ただし、`diffMask` を利用して差分マスクを生成し、そのマスクを加工することで間接的に実現可能。または、比較前に画像データを加工する必要がある) |
| 対象領域（Include Regions）の指定 | △ (直接的なAPIはなし。比較前に画像データを加工する必要がある) |
| **非機能要件** | |
| パフォーマンス | 〇 (軽量で高速と謳われている) |
| 環境 | 〇 (Node.js およびブラウザ環境で動作) |
| 依存性 | 〇 (軽量で依存性が少ない) |
| **差分画像の仕様** | |
| フォーマット | 〇 (PNG形式で出力可能) |
| ハイライト色 | 〇 (`diffColor`, `aaColor` オプションで設定可能) |
| 透明度 | 〇 (`alpha` オプションで設定可能) |

## 5. 長所と短所

### 長所

- **高速かつ軽量:** ピクセル単位の比較に特化しており、非常に高速に動作します。
- **シンプルなAPI:** 使い方がシンプルで導入が容易です。
- **アンチエイリアシング対応:** アンチエイリアシングによる偽陽性を減らすためのオプションがあります。
- **Node.js/ブラウザ対応:** 両方の環境で動作するため、幅広い用途に利用できます。

### 短所

- **無視領域/対象領域の直接指定不可:** 特定の領域を比較から除外したり、特定の領域のみを比較したりする直接的なAPIがありません。これは、動的なコンテンツを含むVRTにおいては課題となる可能性があります。実装するには、比較前に画像データを加工するなどの前処理が必要です。
- **高度な比較機能の欠如:** 知覚的な差分（人間の目には違いが分かりにくいがピクセルは異なる場合など）を考慮する機能は限定的です。より高度なVRTには、他のライブラリや自前実装との組み合わせが必要になる場合があります。

## 6. 結論

`pixelmatch` は、VRTにおける基本的な画像比較機能（差分検出、数値化、差分画像生成）を高速かつ軽量に実現できる優れたライブラリです。特に、ピクセル単位での厳密な比較が必要な場合に適しています。

しかし、「無視領域/対象領域の指定」といったVRTで頻繁に必要となる機能が直接サポートされていない点が課題です。この機能を実現するためには、`pixelmatch` を利用する前に画像データを加工するなどの追加の実装が必要になります。この点を考慮し、他のライブラリや自前実装の検討も引き続き重要です。
