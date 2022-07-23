# hand-gesture-recognition-using-mediapipe
Estimate hand pose using MediaPipe (Python version).<br> 
This is a program that recognizes and utilize hand gestures to interact with computer. The demo is used to play league of legends with hand gestures. Welcome for improvements.
<br> ❗ _️**Majority of the code is based on this [original repo](https://github.com/Kazuhito00/hand-gesture-recognition-using-mediapipe) and its [translated english version](https://github.com/kinivi/hand-gesture-recognition-mediapipe)**_ ❗
<br> 
![mqlrf-s6x16](https://user-images.githubusercontent.com/37477845/102222442-c452cd00-3f26-11eb-93ec-c387c98231be.gif)

# Requirements
* mediapipe 0.8.1
* OpenCV 3.4.2 or Later
* Tensorflow 2.3.0 or Later<br>tf-nightly 2.5.0.dev or later (Only when creating a TFLite for an LSTM model)
* scikit-learn 0.23.2 or Later (Only if you want to display the confusion matrix) 
* matplotlib 3.3.2 or Later (Only if you want to display the confusion matrix)
* PyAutoGUI 0.9.53 and PyDirectInput 1.0.4 (For Computer Control)
  
## Installation Requirements
```bash
pip install mediapipe
pip install opencv-python
pip install pyautogui
pip install PyDirectInput
pip install --upgrade tensorflow
pip install -U scikit-learn
pip install matplotlib
```

# Add new gestures
Run the demo app
```bash
python original.py
```
#### 1.Input Data
Press "k" to enter the mode to save key points（displayed as 「MODE:Logging Key Point」）<br>
<img src="https://user-images.githubusercontent.com/37477845/102235423-aa6cb680-3f35-11eb-8ebd-5d823e211447.jpg" width="60%"><br><br>
If you press "0" to "9", the key points will be added to "model/keypoint_classifier/keypoint.csv" as shown below.<br>
1st column: Pressed number (used as class ID), 2nd and subsequent columns: Key point coordinates<br>
<img src="https://user-images.githubusercontent.com/37477845/102345725-28d26280-3fe1-11eb-9eeb-8c938e3f625b.png" width="100%"><br><br>
After you have added new key points for a new gesture, remember to add its label in ["model/point_history_classifier/point_history_classifier_label.csv"](model/keypoint_classifier/keypoint_classifier_label.csv)
For this version, the number 0-7 has been used. You can add a new gesture by removing previous gestures or start from 8.
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

#### 2.Model training
Open "[point_history_classification.ipynb](point_history_classification.ipynb)" in Jupyter Notebook and execute from top to bottom.<br>
To change the number of training data classes, change the value of "NUM_CLASSES = 7" and <br>modify the label of ["model/point_history_classifier/point_history_classifier_label.csv"](model/keypoint_classifier/keypoint_classifier_label.csv) as appropriate. 

<img src="/images/modify_num_classes.png" width="69%">
<img src="/images/keypoint_csv.png" width="69%">
<br><br>

#### 3.Remove gesture
All the keypoints are stored in ["keypoint.csv"](model/keypoint_classifier/keypoint.csv).
Go into this file and remove all rows that are associated with the class_id that you want to delete. I wrote a helper script to do this. 
```bash
python .\remove_gesutre.py -i class_id
```
Replace the class_id with the number you want to delete and run the script. For example
```bash
python .\remove_gesutre.py -i 0
```
This will remove all the keypoint data for the first gesture with class_id = 0

Re-train the model but don't delete the label in keypoint_classifier_label.csv, you can rename the deleted class_id label to NULL if you like, or keep it as is.

#### 4.Change or add custom gesture input
You can add a new custom gesture by following step 1 - 2.
To modify or create new "gesture to computer command" mapping, you can open up [desktopMotion.py](desktopMotion.py) and modify line 165
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

# Game Demo
You can run this [file](desktopMotion.py), open up league and enjoy a depressing day
```bash
python .\desktop_control.py 
```
<img src="/images/tmp1.png" >

## Game Controls
Currently in [desktopMotion.py](desktopMotion.py), the class_id 0 is being used to move mouse.
<img src="/images/open.png" >
For now, mouse control is binded to Right Hand, and Q, W, E, R keys are binded to Left hand. 

class_id 3 is for LeftClick
<img src="/images/leftclick.png" >
class_id 4 is for RightClick
<img src="/images/rightclick.png" >
class_id 2 is for Q 
<img src="/images/Q.png" >
class_id 5 is for W 
<img src="/images/W.png" >
class_id 6 is for E 
<img src="/images/E.png" >
class_id 7 is for R 
<img src="/images/R.png" >

Welcome everyone to try this out and contribute to this or the orignal repo!

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