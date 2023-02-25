### YOLOv5 5.0-based Face-Mask Detection System

1. 模型选择
2. 输入选择(本地文件、摄像头、RTSP)；在检测RTSP视频流的时候，尽量不要启用帧间延时，否则会出现很高的延时，用yolo5x模型时，rtsp会很卡，建议抽帧检测, 把main.py中的133-135行注释取消
```python
# jump_count += 1
# if jump_count % 5 != 0:
#     continue
```

3. IoU调整
4. 置信度调整
5. 帧间延时调整
6. 边框大小调整
7. 播放/暂停/结束
8. 统计检测结果（显示边框时，支持中文标签）


**本地编译使用：**
```bash
conda create -n yolov5_pyqt5 python=3.8
conda activate yolov5_pyqt5
cd ./
pip install -r requirements.txt
# 训练模型放在pt文件夹下
python main.py
```
运行*main.py*开启检测界面后**会自动检测已有模型**

**使用过程中如果遇到问题请Issue**

**问题汇总**

**Q:** 把模型替换为yolov5最新版本的模型后，界面左下角有错误提示，但是没有报错，请问怎么解决？<br />
**A:** 最新版本的界面，用最新的yolov5模型不会报错或者闪退，这是因为加了异常捕获，避免闪退。如果你想看详细错误报告，可以把DetThread类中的异常捕获取消，或者调用cgit模块。
<br /><br />
**Q:** 点击摄像头按钮后，再检测，为什么还是检测上一次的文件？<br />
**A:** 点击摄像头按钮后，会自动检测电脑连接了几个摄像头（除了电脑自带摄像头，有些人还会连接usb摄像头），检测完成后，摄像头按钮下方会出现数字序号，你需要手动点击出现的数字选择摄像头。
<br /><br />
**Q:** 怎么更改背景图片、背景颜色？<br />
**A:** PyQt5+Qss+QtDesigner。
<br /><br />
**Q:** 我将摄像头的默认分辨率640x480修改为1920x1080后，画面就很卡顿，FPS从30变为了7。<br />
**A:** 为了快速启动摄像头，代码中opencv是使用direct show模式打开摄像头的，这种模式下摄像头捕获分辨率调高之后，帧率可能会下降。改一下datasets.py中LoadWebcam类，大概242行，把括号中“, cv2.CAP_DSHOW”删除，括号里就留一个“eval(pipe)”就行了。但是启动摄像头需要的时间会比之前长一些。<br />或者，依旧使用direct show模式，将244行的`self.cap.set(cv2.CAP_PROP_BUFFERSIZE, 3)`替换为以下代码
```python
        self.cap.set(cv2.CAP_PROP_FRAME_WIDTH, 1920)
        self.cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 1080)
        self.cap.set(cv2.CAP_PROP_BUFFERSIZE, 3)  # set buffer size
        self.cap.set(cv2.CAP_PROP_FOURCC, cv2.VideoWriter_fourcc('M', 'J', 'P', 'G'))
```
第二种方法可能对有些电脑不适用。
