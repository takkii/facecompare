 [![MIT License](http://img.shields.io/badge/license-MIT-blue.svg?style=flat)](LICENSE) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![PyPI Downloads](https://static.pepy.tech/badge/golden-eagle)](https://pepy.tech/projects/golden-eagle)

### Face Compare Project

- This project is Face Recognition Console Application.

#### How to use

```markdown
# Clone the facecompare project
git clone https://github.com/takkii/facecompare.git

# Go to facecompare project
cd facecompare

# wheel build folder.
cd dist

# Install case wheel.
pip3 install facecompare-1.0.4-py3-none-any.whl

# Move up one folder level.
cd ..

# please apply requirements.txt.
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
        # Windows Env.
        if os.path.exists(os.path.expanduser('~/images/')):
            # My Face picture in images folder.
            my_before = face_recognition.load_image_file(
                os.path.expanduser('~/images/myself.gif'))
            my_after = face_recognition.load_image_file(
                os.path.expanduser('~/images/myself2.gif'))
        # WSL2 Env.
        elif os.path.exists(
                os.path.expanduser(
                    '/mnt/c/Users/username/images')):
            my_before = face_recognition.load_image_file(
                os.path.expanduser(
                    '/mnt/c/Users/username/images/myself.gif'))
            my_after = face_recognition.load_image_file(
                os.path.expanduser(
                    '/mnt/c/Users/username/images/myself2.gif'))
        # Config Folder not found.
        else:
            raise ValueError("None, Please Check the Config Folder")

        # facecompare version.
        print("facecompare_version: " + fc.__version__)

        # Accuracy assessment.
        fc.compare_before_after(my_before, my_after)


try:
    thread = Face()
    thread.run()
# Custom Exception.
except ValueError as ext:
    print(ext)
    raise RuntimeError from None

# Once Exec.
finally:
    # GC collection.
    gc.collect()
```

> python run.py

Please, README [here](https://github.com/takkii/facecompare/wiki/manual).

