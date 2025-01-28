# 手把手实战项目1：用MediaPipe Pose检测居家康复训练动作标准度（GitHub开源代码修改）。
以下是一个基于修改MediaPipe Pose开源代码来检测居家康复训练动作标准度的实战项目指南：

### 1. 环境搭建
1. **安装Python**：确保你已安装Python，推荐使用Python 3.7及以上版本。
2. **安装MediaPipe**：
   - 打开终端（Windows下为命令提示符或PowerShell），运行以下命令安装MediaPipe：
   ```bash
   pip install mediapipe
   ```
3. **安装其他依赖**：
   - 可能还需要安装`numpy`、`opencv - python`等库，运行以下命令安装：
   ```bash
   pip install numpy opencv - python
   ```

### 2. 下载MediaPipe Pose开源代码
1. 访问MediaPipe的GitHub仓库：[https://github.com/google/mediapipe](https://github.com/google/mediapipe)。
2. 找到与Pose相关的示例代码，例如`mediapipe/examples/desktop/pose_tracking`。你可以将整个MediaPipe仓库克隆到本地：
   ```bash
   git clone https://github.com/google/mediapipe.git
   ```

### 3. 理解原始代码
1. **导入部分**：
   - 原始代码开头会导入必要的库，如`mediapipe`、`cv2`（OpenCV）等。
   ```python
   import mediapipe as mp
   import cv2
   ```
2. **初始化MediaPipe Pose**：
   - 创建一个`mp_pose.Pose`对象，设置相关参数，如是否静态图像模式、检测置信度等。
   ```python
   mp_pose = mp.solutions.pose
   pose = mp_pose.Pose(static_image_mode=False, min_detection_confidence=0.5, min_tracking_confidence=0.5)
   ```
3. **处理图像/视频帧**：
   - 读取图像或视频帧，将其转换为RGB格式（MediaPipe通常需要RGB输入），然后通过`pose.process`方法进行姿态检测。
   ```python
   cap = cv2.VideoCapture(0)
   while cap.isOpened():
       ret, frame = cap.read()
       if not ret:
           break
       image = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
       results = pose.process(image)
   ```
4. **绘制关键点**：
   - 使用`mp_drawing`模块绘制检测到的人体关键点和连接。
   ```python
   mp_drawing = mp.solutions.drawing_utils
   if results.pose_landmarks:
       mp_drawing.draw_landmarks(image, results.pose_landmarks, mp_pose.POSE_CONNECTIONS)
   ```
5. **显示结果**：
   - 将处理后的图像转换回BGR格式并显示。
   ```python
   image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
   cv2.imshow('MediaPipe Pose', image)
   if cv2.waitKey(5) & 0xFF == 27:
       break
   cap.release()
   cv2.destroyAllWindows()
   ```

### 4. 修改代码以检测动作标准度
1. **定义动作标准关键点关系**：
   - 例如，对于某个康复训练动作（如站立位体前屈），我们可能关注腰部、膝盖、手部等关键点的位置关系。假设标准动作中，腰部到膝盖的距离与腰部到手部的距离应该在一定比例范围内。
   ```python
   def check_pose_accuracy(results):
       if results.pose_landmarks:
           landmarks = results.pose_landmarks.landmark
           # 获取腰部、膝盖、手部关键点的索引，具体索引可参考MediaPipe文档
           waist_index = mp_pose.PoseLandmark.LEFT_HIP.value
           knee_index = mp_pose.PoseLandmark.LEFT_KNEE.value
           hand_index = mp_pose.PoseLandmark.LEFT_WRIST.value
           waist_x = landmarks[waist_index].x
           waist_y = landmarks[waist_index].y
           knee_x = landmarks[knee_index].x
           knee_y = landmarks[knee_index].y
           hand_x = landmarks[hand_index].x
           hand_y = landmarks[hand_index].y
           # 计算距离
           waist_knee_dist = ((waist_x - knee_x) ** 2+(waist_y - knee_y) ** 2) ** 0.5
           waist_hand_dist = ((waist_x - hand_x) ** 2+(waist_y - hand_y) ** 2) ** 0.5
           ratio = waist_knee_dist / waist_hand_dist
           # 设定标准比例范围
           if 0.8 <= ratio <= 1.2:
               return True
       return False
   ```
2. **在主循环中调用检测函数**：
   - 在处理完每一帧图像并获取姿态检测结果后，调用上述定义的`check_pose_accuracy`函数。
   ```python
   cap = cv2.VideoCapture(0)
   while cap.isOpened():
       ret, frame = cap.read()
       if not ret:
           break
       image = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
       results = pose.process(image)
       if results.pose_landmarks:
           is_accurate = check_pose_accuracy(results)
           if is_accurate:
               cv2.putText(image, '动作标准', (10, 30), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 255, 0), 2)
           else:
               cv2.putText(image, '动作不标准', (10, 30), cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 0, 255), 2)
       mp_drawing.draw_landmarks(image, results.pose_landmarks, mp_pose.POSE_CONNECTIONS)
       image = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)
       cv2.imshow('MediaPipe Pose', image)
       if cv2.waitKey(5) & 0xFF == 27:
           break
   cap.release()
   cv2.destroyAllWindows()
   ```

### 5. 测试与优化
1. **测试**：
   - 运行修改后的代码，进行居家康复训练动作的演示。观察检测结果，看是否能准确判断动作标准度。
2. **优化**：
   - **参数调整**：如果检测结果不准确，可以调整MediaPipe的初始化参数，如`min_detection_confidence`和`min_tracking_confidence`，以提高检测的稳定性和准确性。
   - **动作标准定义优化**：进一步细化动作标准关键点关系的定义，考虑更多的关键点和更复杂的几何关系，以适应不同的康复训练动作。
   - **异常处理**：添加更多的异常处理代码，例如当摄像头无法打开或图像读取失败时的处理，使程序更加健壮。 
