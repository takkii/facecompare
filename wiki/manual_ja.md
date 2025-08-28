<div align="right">
    <p> ※ 英文を加筆し、日本語化しました。</p>
</div>

### イヌワシ

- [x] 顔認識システムを細分化しました。

- [x] ドキュメントを調整しました。

- [x] [hyokaproject](https://github.com/takkii/hyokaproject)、python-dotenvが[使用できる](https://github.com/takkii/hyokaproject/blob/main/hyokapp/views.py#L44)ことを確認しました。

#### 更新履歴: [2025/08/28]🆙

- [x] コメント内、なるべく日本語化をしました。

#### 始めに

```markdown
精度が低いほど同一人物である可能性が高くなります。
```

※ 同じ写真でも精度が0になることはありません。

> 私自身が20代～40代前半の就職活動で使った履歴書写真をもとに顔写真を比較してみました。 

> 精度が0.31以下に保つように比較します。同じ条件の顔写真でも別人として認識されてしまうことがわかりました。

※ ただし、これは私の個人的な意見です。

#### v1.0.0

> facecompare: v1.0.4

> golden-eagle: v1.0.0

- 上記パッケージは、名前のリファクタリング以外同じです。

※ facecompareの上記バージョンは、GitHubでパッケージを公開中です。

#### v1.0.1

- [x] READMEを更新し、メッセージを変更しました。

- [x] 解消する依存ライブラリを、requirements.txtに追加しました。

※ 大きな変更は以上です。

#### v1.0.2

~~この画像は本人です。(v1.0.0 ~ v1.0.1)~~

> 顔認識比較後の出力メッセージの内容を変更しました。

※ メッセージによる混乱を避けるため⭕️❎️に変更しました。

#### v1.0.3

```markdown
使用する顔検出モデル。
「hog」は精度は劣りますが、CPU では高速です。
「cnn」はより正確なディープラーニングモデルであり、
GPU/CUDA アクセラレーション (利用可能な場合)。
初期値は「hog」です。
```

```python
...
# 初期値は「hog」です。
lo_before = face_recognition.face_locations(my_before, model='cnn')
lo_after = face_recognition.face_locations(my_after, model='cnn')
...
```

[face-recognition ドキュメント](https://face-recognition.readthedocs.io/en/latest/face_recognition.html)

#### 変更履歴: 2025年七夕, v1.0.4

> 内包している機能を分離しました。

#### v1.0.5 ~ v1.0.5.1

- 精度評価: 精度が 0.32 より大きい場合は例外がスローされます。

- デバッグ出力、⭕️❎️ が削除されました。

- インストール中に依存するライブラリを解決します。

- 内部構造がリファクタリングされました。

> 動作しない場合は、Pythonコードを自分で修正してください。

※ WSL2でユーザテスト済み、run.py。(確認したバージョン: v1.0.0 ~ v1.0.5.1)

#### v1.0.5.1

```markdown
例えば、ユニットテストやGitHub Actionsなどに使用できます。
用途、現在作業している人物が同一人物であるかどうかを確認するためなどに使います。
```

#### v1.0.5.2

- [x] このライブラリをinstallする時に解消するライブラリを減らしました。

- [x] ~/golden_eagle_log/ にログを作成するようになりました。

- [x] ユニットテストを意識しました。ただ、ビルドする分数は変わらないと思います。

※ README、CIテストバッジを設置しましたがプライベートレポジトリなので私以外見れません。

#### v1.0.5.3 (しばらくそのままかずっとそのまま)

- [x] 精度評価を設定できるようにしました。

- [x] compare_before_afterの第三引数にfloat型の浮動小数点数を入れます。

- [x] '~/golden_eagle/d.log'とログファイルを出力するようにしました。

➙ golden_eagleフォルダがなければホームディレクトリに作成してください。

※ このライブラリは設定ファイルになりました。通常、使用者が数値をセットしてください。

> unit/facecompare.py

```python
import face_recognition
import golden_eagle as ga
import gc
import os
import threading


# faceクラス
class Face(threading.Thread):

    # スレッドを使用する
    def __init__(self):
        threading.Thread.__init__(self)

    # メソッドが走る
    def run(self):
        # Windows環境.
        if os.path.exists(os.path.expanduser('~/images/')):
            # 画像フォルダ内の私の顔写真
            my_before = face_recognition.load_image_file(
                os.path.expanduser('~/images/myself.gif'))
            my_after = face_recognition.load_image_file(
                os.path.expanduser('~/images/myself2.gif'))

        # WSL2環境.
        elif os.path.exists(
                os.path.expanduser(
                    '/mnt/c/Users/username/images')):
            my_before = face_recognition.load_image_file(
                os.path.expanduser(
                    '/mnt/c/Users/username/images/myself.gif'))
            my_after = face_recognition.load_image_file(
                os.path.expanduser(
                    '/mnt/c/Users/username/images/myself2.gif'))

        # 画像フォルダが見つかりません.
        else:
            raise ValueError("なし、画像フォルダを確認してください。")

        # イヌワシバージョン
        print(
            '-----------------------------------------------------------------'
        )
        print('\n')
        print("イヌワシ_バージョン: " + ga.__version__)

        # 初期値は0.6で、数値が低いほど顔の比較が厳密になります。
        ga.compare_before_after(my_before, my_after, 0.32)


# try ~ except ~ finally.
try:
    thread = Face()
    thread.run()
# カスタム例外をスローします。
except ValueError as ext:
    print(ext)
    raise RuntimeError from None

# 一度実行します
finally:
    # GC collection.
    gc.collect()
```

- これをユニットテストフォルダーに追加します(例: unit/facecompare.py を作成します)。

- [x] GitHub Actions、CIで検証済みです。但し、このライブラリを入れて依存解消すると8分くらいかかります。

※ GitHub Freeプラン、無料は2000分までの制限があります。この顔認証システムを利用したCIは、250回/月が目安です。

#### 通常は動作検証に使用します(run.py)。

```python
import cv2
import golden_eagle as ga
import face_recognition
import japanize_matplotlib
import gc
import matplotlib.pyplot as plt
import numpy as np
import os
import threading


class Face(threading.Thread):

    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        # Windows環境
        if os.path.exists(os.path.expanduser('~/images/')):
            # 画像フォルダ内の私の顔写真
            my_before = face_recognition.load_image_file(
                os.path.expanduser('~/images/myself.gif'))
            my_after = face_recognition.load_image_file(
                os.path.expanduser('~/images/myself2.gif'))
            # 初期値は「hog」
            lo_before = face_recognition.face_locations(my_before, model='cnn')
            lo_after = face_recognition.face_locations(my_after, model='cnn')

            # 顔の特徴の位置（目、鼻など）
            # model – オプション – 使用するモデル。
            # “large” (初期値) または “small”.
            around_the_face_b = face_recognition.face_landmarks(my_before,
                                                                lo_before,
                                                                model='small')
            around_the_face_a = face_recognition.face_landmarks(my_after,
                                                                lo_after,
                                                                model='small')
        # WSL2環境
        elif os.path.exists(
                os.path.expanduser(
                    '/mnt/c/Users/username/images')):
            my_before = face_recognition.load_image_file(
                os.path.expanduser(
                    '/mnt/c/Users/username/images/myself.gif'))
            my_after = face_recognition.load_image_file(
                os.path.expanduser(
                    '/mnt/c/Users/username/images/myself2.gif'))
            # 初期値は「hog」
            lo_before = face_recognition.face_locations(my_before, model='cnn')
            lo_after = face_recognition.face_locations(my_after, model='cnn')

            # 顔の特徴の位置（目、鼻など）
            # model – オプション – 使用するモデル。
            # “large” (初期値) または “small”.
            around_the_face_b = face_recognition.face_landmarks(
                my_before, lo_before)
            around_the_face_a = face_recognition.face_landmarks(
                my_after, lo_after)

        # 画像フォルダが見つかりません.
        else:
            raise ValueError("なし、画像フォルダを確認してください。")

        # イヌワシ バージョン
        print("イヌワシ_バージョン: " + ga.__version__)

        # 初期値は0.6で、数値が低いほど顔の比較が厳密になります。
        ga.compare_before_after(my_before, my_after, 0.32)

        # データは特徴量として処理します
        en_b = face_recognition.face_encodings(my_before)[0]
        en_a = face_recognition.face_encodings(my_after)[0]
        face_d: npt.NDArray = face_recognition.face_distance([en_b], en_a)
        hyoka: npt.DTypeLike = (np.floor(face_d * 1000).astype(int) / 1000)

        # 精度評価、顔写真編集なし
        accuracy = "accuracy:" + str(hyoka)
        print(accuracy)

        # 顔の座標
        print("Before Image, Get face coordinates  :" + str(lo_before))
        print("After Image, Get face coordinates :" + str(lo_after))

        # 顔周り
        print("Before Image, Get around the face :" + str(around_the_face_b))
        # print("After Image, Get around the face :" + str(around_the_face_a))

        # dlib, face recognitionを使用します
        cv2.startWindowThread()
        # face recognitionを使用し、my_after/my_beforeを評価します。
        cv2.imshow('Yourself before picture image.', my_before)
        cv2.imshow('Yourself after picture image.', my_after)
        # 8秒経過後、ウインドウを閉じます。
        cv2.waitKey(15000)
        cv2.waitKey(1)
        cv2.destroyAllWindows()
        cv2.waitKey(1)

        # 日本語訳
        jp_names = {
            'nose_bridge': '鼻筋',
            'nose_tip': '鼻先',
            'top_lip': '上唇',
            'bottom_lip': '下唇',
            'left_eye': '左目',
            'right_eye': '左目',
            'left_eyebrow': '左眉毛',
            'right_eyebrow': '右眉毛',
            'chin': '下顎'
        }

        # my_before画像を読み込み、matplotlibで顔認識を可視化します。
        fig = plt.figure('Yourself before picture image.',
                         figsize=(7, 7),
                         facecolor='lightskyblue',
                         layout='constrained')
        bx = fig.add_subplot()
        bx.imshow(my_before)
        bx.set_axis_off()
        for face in around_the_face_b:
            for name, points in face.items():
                points = np.array(points)

                bx.plot(points[:, 0],
                        points[:, 1],
                        'o-',
                        ms=3,
                        label=jp_names[name])
                bx.legend(fontsize=14)
                bx.set_title('Face Recognition Range')

        plt.show()

        # my_after画像を読み込み、matplotlibで顔認識を可視化します。
        fig = plt.figure('Yourself after picture image.',
                         figsize=(7, 7),
                         facecolor='deeppink',
                         layout='constrained')
        ax = fig.add_subplot()
        ax.imshow(my_after)
        ax.set_axis_off()
        for face in around_the_face_a:
            for name, points in face.items():
                points = np.array(points)
                ax.plot(points[:, 0],
                        points[:, 1],
                        'o-',
                        ms=3,
                        label=jp_names[name])
                ax.legend(fontsize=14)
                ax.set_title('Face Recognition Range')

        plt.show()

# try ~ except ~ finally.
try:
    thread = Face()
    thread.run()
# 顔写真の精度が高い例外を捕捉します。
except ValueError as ext:
    print("Use a recent photo of your face.")
    print(ext)
    raise RuntimeError from None

# 一度実行します
finally:
    # GC collection.
    gc.collect()
```

> python run.py

※ ライブラリをインストールしてから、上記のpythonファイルを作成してください。

> 2枚の画像が同一人物のものであるかどうかをデータ科学的に検証します。

### PyPiでfacecompareを登録しようとすると、パッケージ名の衝突が発生しました。

#### 環境構築

```markdown
# pypiからインストールします
pip3 install golden-eagle

# ホームディレクトリに移動
cd $HOME

# ダウンロード
curl --output requirements.txt https://raw.githubusercontent.com/takkii/facecompare/refs/heads/main/requirements.txt

# 依存ライブラリ解消
pip3 install -r requirements.txt

# 不要なので削除してください
rm -rf requirements.txt
```

> pypiパッケージを使用していて動作しない場合は、

※ requirements.txt 内の依存ライブラリを解決してみてください。

#### WSL2 / RockyLinux 公式版 10 (例)

```markdown
qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in 
"/home/username/.anyenv/envs/pyenv/versions/3.13.5/lib/python3.13/site-packages/cv2/qt/plugins" even though it was found.
This application failed to start because no Qt platform plugin could be initialized. Reinstalling the application may fix this problem.

Available platform plugins are: xcb.

[1]    1662 IOT instruction (core dumped)  python run.py
```

> sudo dnf install qt5-devel

※ 上記コマンドを実行してみてください。opencv-pythonはqt5に依存しているようです。

```markdown
# エラーメッセージ上の方にあるかも
qt.qpa.xcb: could not connect to display
```

```markdown
# ~/.zshrcに追加
export DISPLAY=:0.0
```

```markdown
# .wslconfigから削除
guiApplications=false
```

```markdown
# wslを再起動
wsl --shutdown
```

※ GUIアプリケーションをWSL2で実行できないとき、私の環境では上記コマンドで起動しました。