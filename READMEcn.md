# 使用手部识别进行简单的电脑操控
Estimate hand pose using MediaPipe (Python version).<br> 

<br> ❗ _️**这个程序大部分来自于这里 [original repo](https://github.com/Kazuhito00/hand-gesture-recognition-using-mediapipe) 以及它的英文翻译版本 [translated english version](https://github.com/kinivi/hand-gesture-recognition-mediapipe)**_ ❗
<br> 
![mqlrf-s6x16](https://user-images.githubusercontent.com/37477845/102222442-c452cd00-3f26-11eb-93ec-c387c98231be.gif)

# 安装要求
* mediapipe 0.8.1
* OpenCV 3.4.2 or Later
* Tensorflow 2.3.0 or Later<br>tf-nightly 2.5.0.dev or later (Only when creating a TFLite for an LSTM model)
* scikit-learn 0.23.2 or Later (Only if you want to display the confusion matrix) 
* matplotlib 3.3.2 or Later (Only if you want to display the confusion matrix)
* PyAutoGUI 0.9.53 and PyDirectInput 1.0.4 (For Computer Control)
  
## 安装方式
```bash
pip install mediapipe
pip install opencv-python
pip install pyautogui
pip install PyDirectInput
pip install --upgrade tensorflow
pip install -U scikit-learn
pip install matplotlib
```

# 添加新手势
安装完需要的程序之后用编译器跑original.py或者在terminal 里跑一下这个程序
```bash
python original.py
```
#### 1.输入数据
按 “k” 会进入录入模式 （「MODE:Logging Key Point」）<br>
<img src="https://user-images.githubusercontent.com/37477845/102235423-aa6cb680-3f35-11eb-8ebd-5d823e211447.jpg" width="60%"><br><br>
在录入模式底下，按 “0” 到 “9” 会把当前的手势节点信息(keypoints) 分别录入到对应的class_id 里面。这些信息会加入到"model/keypoint_classifier/keypoint.csv" 里面。 如果你要加个新的手势“笔芯”，想录入到class_id 8这个位置。 你可以先把“笔芯”的手势摆出来，然后按k 进入录入模式，然后做出“笔芯”手势 -> 按 8 -> 新的位置 -> 做出“笔芯”手势 -> 按 8 -> 循环。

新的手势添加完之后记得在 ["model/point_history_classifier/point_history_classifier_label.csv"](model/keypoint_classifier/keypoint_classifier_label.csv) 里面把这个手势的标签也给加上。

在"model/keypoint_classifier/keypoint.csv" 里面， 第一列对应的就是class_id 0-9，后面的都是手势节点信息<br>
<img src="https://user-images.githubusercontent.com/37477845/102345725-28d26280-3fe1-11eb-9eeb-8c938e3f625b.png" width="100%"><br><br>

在这个版本里面, class_id 0-7 都被占用了。你可以自行更改添加或者删除， 后面有说。如果要在这个版本上面添加的话可以从8 开始加起。
| class ID      | Gesture |
| ----------- | ----------- |
| 0   | Open       |
| 1   | Close      |
| 2   | Pointer(Q) |
| 3   | LeftClick  |
| 4   | RightClick |
| 5   | W          |
| 6   | E          |
| 7   | R          |

#### 2.模型训练
用Jupyter Notebook打开 "[point_history_classification.ipynb](point_history_classification.ipynb)"
如果需要改一下手势标签的话请自行在 ["model/point_history_classifier/point_history_classifier_label.csv"](model/keypoint_classifier/keypoint_classifier_label.csv) 里面进行更改<br>
在point_history_classification.ipynb 文件里面根据要训练的手势数量改一下变量 "NUM_CLASSES = 7" <br>
<img src="/images/modify_num_classes.png" width="69%">
<img src="/images/keypoint_csv.png" width="69%">
<br><br>

#### 3.删除手势
所有训练用的手势节点信息 都存在这里["keypoint.csv"](model/keypoint_classifier/keypoint.csv).
先进入以上的这个文件，删除掉目标的class_id的讯息。我写了个文本进行删除，可以跑一下这行代码，但是class_id 要换成你想删除的手势的class_id。
```bash
python .\remove_gesutre.py -i class_id
```
删除完训练用的手势节点，我们需要重新训练一个新的模型。就模型的label 不要删 不过你可以把label 改成NULL 或者直接无视掉也行。


#### 4.更改或者添加新的手势指令
首先你可以根据 步骤1 - 2 添加一个新的手势
手势添加成功之后，打开[desktopMotion.py](desktopMotion.py)， 然后你可以更改165 行 的内容。 你可以把'q' 换成 'a' 或者把hand_sign_id (也就是class_id) 改成别的手势。
```python
if isLeftHand:
    if hand_sign_id == 2 and prev_gesture != 2:  # Press Q
        pydirectinput.press('q')
    elif hand_sign_id == 5 and prev_gesture != 5:  # Press W
        pydirectinput.press('w')
    elif hand_sign_id == 6 and prev_gesture != 6:  # Press E
        pydirectinput.press('e')
    elif hand_sign_id == 7 and prev_gesture != 7:  # Press R
        pydirectinput.press('r')

else: 
    if hand_sign_id == 0:  #     Mouse gesture
        pydirectinput.moveTo(x, y - 200, duration=0.1, _pause=False)
        pass
    elif hand_sign_id == 3 and prev_gesture != 4 :  # Left Click gesture
        pydirectinput.click()
    elif hand_sign_id == 4 and prev_gesture != 5:  # Right Click gesture
        pydirectinput.click(button='right') 
```

# 游戏测试
跑这个文件 [file](desktopMotion.py), 打开英雄联盟并且请敬请享受痛苦。
```bash
python .\desktop_control.py 
```
<img src="/images/tmp1.png" >

## 游戏控制
在右手的情况下 [desktopMotion.py](desktopMotion.py), class_id 0 是用来控制鼠标移动的
<img src="/images/open.png" >
左手控制鼠标的点击 以及 Q, W, E, R 的技能释放 

class_id 3 鼠标点击左键
<img src="/images/leftclick.png" >
class_id 4 鼠标点击右键
<img src="/images/rightclick.png" >
class_id 2  Q 
<img src="/images/Q.png" >
class_id 5  W 
<img src="/images/W.png" >
class_id 6  E 
<img src="/images/E.png" >
class_id 7  空大 
<img src="/images/R.png" >

当前版本特别不完善，欢迎各位大佬完善一下。

# Reference
* [MediaPipe](https://mediapipe.dev/) 
* [original repo for gesture detection](https://github.com/Kazuhito00/hand-gesture-recognition-using-mediapipe)
* [translated english repo for gesture detection](https://github.com/kinivi/hand-gesture-recognition-mediapipe)
* [tutorial reference](https://youtu.be/a99p_fAr6e4)
  
# Author
Kazuhito Takahashi(https://twitter.com/KzhtTkhs)

# Translation and other improvements
Nikita Kiselov(https://github.com/kinivi)
 
# License 
hand-gesture-recognition-using-mediapipe is under [Apache v2 license](LICENSE).