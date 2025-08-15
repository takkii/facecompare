### Golden-Eagle

- We have subdivided the face recognition system.

※ Change_history: [2025/07/14]🆙

#### Getting started

```markdown
The lower the accuracy, the greater the possibility that it is the same person.
```

※ The accuracy will never be 0 even for the same photo.

> I did a face comparison based on the resume photos used in job hunting for people in their 20s to early 40s. 

> If the accuracy was not kept below 0.31, the results would look like someone else's face,

> even if it was my own face. However, this is my personal opinion.

#### v1.0.2

~~This image is of the person himself. (v1.0.0 ~ v1.0.1)~~

> Contents of the output message after face recognition comparison.

※ To avoid confusion, I changed it to ⭕️❎️.

#### v1.0.3

```markdown
Which face detection model to use.
“hog” is less accurate but faster on CPUs.
“cnn” is a more accurate deep-learning model,
which is GPU/CUDA accelerated (if available).
The default is “hog”.
```

```python
...
# The default is “hog”.
lo_before = face_recognition.face_locations(my_before, model='cnn')
lo_after = face_recognition.face_locations(my_after, model='cnn')
...
```

[face-recognition_documents](https://face-recognition.readthedocs.io/en/latest/face_recognition.html)

#### ChangeLog: 2025/07/07(七夕), v1.0.4

> The functions have been separated.

#### v1.0.5 ~ v1.0.5.1

- Accuracy evaluation: An exception will be thrown if the accuracy is greater than 0.32.

- print debug, ⭕️❎️ has been removed.

- Resolve, Dependent libraries during installation.

- The internal structure has been refactored.

※ Please use this library to evaluate the accuracy of facial photos and ensure their reliability.

> If it doesn't work, please fix the python code yourself.

※ Tested, run.py on WSL2. (check: v1.0.0 ~ v1.0.5.1)

#### v1.0.5.1 (Leave it in place)

```markdown
It can be used for unit testing.
Purpose, You can check if the person working on it is the same person.
```

> unit/facecompare.py

```python
import face_recognition
import golden_eagle as ga
import gc
import os
import threading


# face class
class Face(threading.Thread):

    # use thread
    def __init__(self):
        threading.Thread.__init__(self)

    # run method
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

        # Image Folder not found.
        else:
            raise ValueError("None, Please Check the Image Folder")

        # golden-eagle version.
        print(
            '-----------------------------------------------------------------'
        )
        print('\n')
        print("golden-eagle_version: " + ga.__version__)

        # Guarantee of reliability.
        ga.compare_before_after(my_before, my_after)


# try ~ except ~ finally.
try:
    thread = Face()
    thread.run()
# Custom Exception, raise throw.
except ValueError as ext:
    print(ext)
    raise RuntimeError from None

# Once Exec.
finally:
    # GC collection.
    gc.collect()
```

- Add it to your unit test folder, for example create unit/facecompare.py.

※ It is mainly intended to be used with CI such as GitHub Actions.

#### Usually used for operational verification. (run.py)

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
        # Windows Env.
        if os.path.exists(os.path.expanduser('~/images/')):
            # My Face picture in images folder.
            my_before = face_recognition.load_image_file(
                os.path.expanduser('~/images/myself.gif'))
            my_after = face_recognition.load_image_file(
                os.path.expanduser('~/images/myself2.gif'))
            # The default is “hog”.
            lo_before = face_recognition.face_locations(my_before, model='cnn')
            lo_after = face_recognition.face_locations(my_after, model='cnn')

            # A list of dicts of face feature locations (eyes, nose, etc)
            # model – Optional - which model to use.
            # “large” (default) or “small”.
            around_the_face_b = face_recognition.face_landmarks(my_before,
                                                                lo_before,
                                                                model='small')
            around_the_face_a = face_recognition.face_landmarks(my_after,
                                                                lo_after,
                                                                model='small')
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
            # The default is “hog”.
            lo_before = face_recognition.face_locations(my_before, model='cnn')
            lo_after = face_recognition.face_locations(my_after, model='cnn')

            # A list of dicts of face feature locations (eyes, nose, etc)
            # model – Optional - which model to use.
            # “large” (default) or “small”.
            around_the_face_b = face_recognition.face_landmarks(
                my_before, lo_before)
            around_the_face_a = face_recognition.face_landmarks(
                my_after, lo_after)

        # Image Folder not found.
        else:
            raise ValueError("None, Please Check the Image Folder")

        # golden-eagle version.
        print("golden-eagle_version: " + ga.__version__)

        # Guarantee of reliability.
        ga.compare_before_after(my_before, my_after)

        # The data is processed as a feature quantity.
        en_b = face_recognition.face_encodings(my_before)[0]
        en_a = face_recognition.face_encodings(my_after)[0]
        face_d: npt.NDArray = face_recognition.face_distance([en_b], en_a)
        hyoka: npt.DTypeLike = (np.floor(face_d * 1000).astype(int) / 1000)

        # Accuracy evaluation, no face photo editing.
        accuracy = "accuracy:" + str(hyoka)
        print(accuracy)

        # Face coordinate.
        print("Before Image, Get face coordinates  :" + str(lo_before))
        print("After Image, Get face coordinates :" + str(lo_after))

        # Get around the face
        print("Before Image, Get around the face :" + str(around_the_face_b))
        # print("After Image, Get around the face :" + str(around_the_face_a))

        # Use dlib, face recognition.
        cv2.startWindowThread()
        # Use face recognition my_after/my_before.
        cv2.imshow('Yourself before picture image.', my_before)
        cv2.imshow('Yourself after picture image.', my_after)
        # Window closes in 8 seconds
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

        # my_before load image, Plotting face recognition with matplotlib.
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

        # my_after load images, Plotting face recognition with matplotlib.
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
# Custom Exception, raise throw.
except ValueError as ext:
    print(ext)
    raise RuntimeError from None

# Once Exec.
finally:
    # GC collection.
    gc.collect()
```

> python run.py

※ Install the library and then create the above python file.

> We scientifically verify whether the two images are of the same person.

### When using facecompare on PyPi, there was a package name collision.

#### ENV

```markdown
# install
pip3 install golden-eagle

# home directory
cd $HOME

# command line downloads
curl --output requirements.txt https://raw.githubusercontent.com/takkii/facecompare/refs/heads/main/requirements.txt

# resolved dependency
pip3 install -r requirements.txt

# delete it as it is not necessary
rm -rf requirements.txt
```

> If you are using a pypi package and it doesn't work,

※ Try resolving the dependent libraries in requirements.txt.