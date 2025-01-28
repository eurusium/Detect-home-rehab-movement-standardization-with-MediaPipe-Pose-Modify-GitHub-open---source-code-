```markdown
# 用 MediaPipe Pose 检测居家康复训练动作标准度

## 项目简介
本项目利用 MediaPipe Pose 技术，对居家康复训练动作的标准度进行实时检测。

## 安装步骤
1. 克隆本仓库：
```bash
git clone https://github.com/your_username/home_rehab_movement_detection.git
cd home_rehab_movement_detection
```
2. 创建并激活虚拟环境（可选但推荐）：
```bash
python -m venv venv
source venv/bin/activate  # 在 Windows 上使用 `venv\Scripts\activate`
```
3. 安装依赖库：
```bash
pip install -r requirements.txt
```

## 使用方法
在项目根目录下运行以下命令启动程序：
```bash
python src/main.py
```

## 注意事项
- 确保你的电脑连接了摄像头。
- 可根据实际情况调整 `check_pose_accuracy` 函数中的标准比例范围。
```

#### 5. 创建 GitHub 仓库并上传代码
1. 登录 GitHub，创建一个新的仓库，仓库名可设为 `home_rehab_movement_detection`。
2. 在本地项目根目录下，初始化 Git 仓库：
```bash
git init
