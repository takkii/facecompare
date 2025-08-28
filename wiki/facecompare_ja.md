<div align="right">
    <p>※ 英文を加筆し、日本語化しました。</p>
</div>

### facecompare

※ GitHub公開レポジトリ版

#### 使い方

```markdown
# facecompareプロジェクトを複製します
git clone https://github.com/takkii/facecompare.git

# facecompareプロジェクトに移動します
cd facecompare

# wheelビルドフォルダーに移動します
cd dist

# インストール
pip3 install facecompare-1.0.4-py3-none-any.whl

# 一つ上の階層に移動します
cd ..

# 依存ライブラリを解消します
pip3 install -r requirements.txt
```

> run.py

```python
import facecompare as fc
import face_recognition
import gc
import os
import threading

from typing import Optional

class Face(threading.Thread):

    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        # Windows環境.
        if os.path.exists(os.path.expanduser('~/images/')):
            # My Face picture in images folder.
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
        # Imageフォルダーが見つかりません
        else:
            raise ValueError("None, Please Check the Image Folder")

        # facecompareバージョン
        print("facecompare_version: " + fc.__version__)

        # 精度評価
        fc.compare_before_after(my_before, my_after)


try:
    thread = Face()
    thread.run()
# カスタム例外
except ValueError as ext:
    print(ext)
    raise RuntimeError from None

# 一度だけ実行
finally:
    # GC collection.
    gc.collect()
```

> python run.py

※ 上記コマンドで動作を確認できます。