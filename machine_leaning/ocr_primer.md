#### 教材
https://youtu.be/b-GBxllmiwQ

---

## Colab (Google Colaboratory)
- ブラウザ上でPythonを動かせるサービス
- 環境構築不要

https://colab.research.google.com/


## pip
- Pythonのパッケージマネージャー
  - `apt`はUbuntuとか `yum`はCentOSとか(`yum`は`dnf`に代替される？)
 
## `Tesseract(sh)`でOCR
> Tesseractは、オープンソースの光学文字認識（OCR）エンジンです。OCRは、画像やスキャンされたドキュメントに含まれるテキストを読み取り、それを編集可能なテキスト形式に変換する技術です

![image](https://github.com/naon708/til/assets/77439261/b661f760-09e6-4bd4-8542-d31fc5d5badd)


## `tesseract_layout`
```py
txt2 = tool.image_to_string(
    img2,
    lang='eng+jpn',
    builder=pyocr.builders.TextBuilder(tesseract_layout=7)
)
txt2
```
### レイアウト解析のオプション
```
0:　テキストの傾斜角度や言語の種類を検知（OSD）して出力
1:　OSDありでOCR（回転した画像にも対応してOCR可）
2:　OSDなしでテキストの傾斜角度情報を標準出力（OCRなし）
3:　OSDなしでOCR（デフォルトの設定はこれ）
4:　単一列にさまざまなテキストサイズが入り混じったものと想定してOCR
5:　縦書きのまとまった文章と想定してOCR
6:　横書きのまとまった文章と想定してOCR
7:　一行の文章と想定してOCR
8:　一単語と想定してOCR
9:　円の中に一単語がある想定でOCR（①、➁など）
10:　一文字と想定してOCR
11:　順序を気にせずできるだけ画像内に含まれる文章をOCRで取得
12:　OSDありでできるだけ画像内に含まれる文章をOCRで取得
13:　Tesseract固有の処理を飛ばして一行の文章としてOCR処理
```

## 認識のしやすさ
フルカラー画像がきつい (OCRの精度は入力元の画像の状態に大きく依存する)</br>
↓</br>
前処理を加えてOCRが認識しやすくする (e.g. グレースケール)

```py
img_gray = cv2.imread('sample02.png', 0)
cv2.imwrite('sample03.png', img_gray)
```

## 認識部分の可視化
```py
# 画像を読み込む
out = cv2.imread('sample01.png')

for box in results:
  # box内のpositionには矩形情報が入っていて、それを元に画像に矩形を書き込む
  cv2.rectangle(out, box.position[0], box.position[1], (0, 0, 255), 2)

# 書き込んだ画像を保存する(書き出す)
cv2.imwrite('output.png', out)
```
![image](https://github.com/naon708/til/assets/77439261/e9e2c0a7-038f-4e48-9c1a-1ac4b42fa1a2)

## `!apt install`
プリフィックスの`!`　→　ColabやJupyter等のPythonの実行環境で、shellコマンドであることを明示的に示すためのプリフィックス

---

## Output
https://colab.research.google.com/drive/1dwlR1YY2NaYjdhoAISQxKGJuEw5294YJ?usp=sharing

---

*fin*
