# Setup_venv_linux
## 1. Tạo môi trường python trong miniconda3
### 1.1. Trên thiết bị có kết nối internet
- Tạo thư mục muốn dùng làm local channel
- `mkdir -p /home/username-1/my_local_channel`
- Conda sẽ tải các gói cần thiết và lưu chúng vào thư mục cache mặc định của Conda
(thường là thư mục pkgs nằm trong đường dẫn cài đặt của Conda). Tuy nhiên, nếu muốn
các gói được tải xuống được lưu vào một thư mục cụ thể để dễ tìm kiếm, có thể thay
đổi vị trí lưu cache của Conda bằng cách sau:
- `export CONDA_PKGS_DIRS=/home/username-1/my_local_channel`
- kiểm tra biến đã được đặt chưa bằng lệnh
- `echo $CONDA_PKGS_DIRS`
- Chạy lệnh tải gói với tùy chọn --download-only:
- `conda create --name temp_env python=3.11.11 --download-only`
### 1.2. Trên thiết bị không có kết nối 
- Sao chép thư mục `my_local_channel` đã tạo ở bước trên vào máy đích ví dụ `/home/username-2/my_local_channel`
- Tạo môi trường offline sử dụng local channel
- `conda create --offline --name my_offline_env python=3.11.11 -c file:///home/username-2/my_local_channel`
- Sau khi lệnh trên chạy xong, bạn có thể kích hoạt môi trường và kiểm tra phiên bản Python
- `conda activate my_offline_env`
- `python --version`
## 2. Cài đặt thư viện python thông qua pip
### 2.1. Trên thiết bị có kết nối internet
- Cài đặt thư viện (ví dụ `medaka` phiên bản pytorch+CPU)
- `pip install medaka-cpu --extra-index-url https://download.pytorch.org/whl/cpu`
- 


