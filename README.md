# LINUX - Install libs && venvs via PIP
## 1. Tạo môi trường Python trong miniconda3
### 1.1. Trên thiết bị có kết nối Internet
- Tạo thư mục muốn dùng làm *Local channel*
```linux
mkdir -p /home/username-1/my_local_channel
```
- *Conda* sẽ tải các gói cần thiết và lưu chúng vào thư mục *cache* mặc định của *Conda*
(thường là thư mục *pkgs* nằm trong đường dẫn cài đặt của *Conda*). Tuy nhiên, nếu muốn
các gói được tải xuống được lưu vào một thư mục cụ thể để dễ tìm kiếm, có thể thay
đổi vị trí lưu *cache* của *Conda* bằng cách sau:
```linux
export CONDA_PKGS_DIRS=/home/username-1/my_local_channel
```
- Kiểm tra biến đã được đặt chưa bằng lệnh
```linux
echo $CONDA_PKGS_DIRS
```
- Chạy lệnh tải gói với tùy chọn `--download-only`:
```linux
conda create --name temp_env python=3.11.11 --download-only
```
- Tạo các tệp *index* (như `repodata.json`) trong thư mục, giúp *Conda* nhận diện đó là
một kênh cục bộ *(Local channel)*, nếu chưa có lệnh `conda index` hãy chạy `conda install conda-build`
```linux
conda index /home/username-1/my_local_channel
```
- Nén thư mục `my_local_channel` thành tệp `my_local_channel.tar.gz` bằng lệnh
```linux
tar -zcf /home/username-1/my_local_channel.tar.gz /home/username-1/my_local_channel
```
- Đặt lại thư mục *cache* mặc định của *Conda*
```linux
export CONDA_PKGS_DIRS=/home/username-1/miniconda3/pkgs
```
### 1.2. Trên thiết bị không có kết nối Internet
- Sao chép tệp `my_local_channel.tar.gz` đã tạo ở bước trên vào máy đích ví dụ `/home/username-2/my_local_channel.tar.gz`
- Giải nén tệp  `my_local_channel.tar.gz` thành thư mục `my_local_channel` bằng lệnh
```linux
tar -zxf /home/username-2/my_local_channel.tar.gz
```
- Tạo môi trường *offline* sử dụng *Local channel*
```linux
conda create --offline --name my_offline_env python=3.11.11 -c file:///home/username-2/my_local_channel
```
- Sau khi lệnh trên thực thi xong, có thể kích hoạt môi trường và kiểm tra phiên bản *Python*
```linux
conda activate my_offline_env
```
```linux
python --version
```
- Sau đó kiểm tra đường dẫn *Python*, kết quả phải là `/home/username-1/miniconda3/envs/my_offline_env/bin/python`
```python
import sys
print(sys.executable)
```
- Cuối cùng kiểm tra đường dẫn *pip*, kết quả phải là `/home/username-1/miniconda3/envs/my_offline_env/lib/python3.11/site-packages/pip`,
nếu không phải, cần vô hiệu hóa *user site packages*, để tránh việc *pip* truy cập vào các gói được cài đặt ở cấp *user*,
có thể tạm thời đặt biến môi trường thành `export PYTHONNOUSERSITE=1`
```linux
python -m pip -V
```
## 2. Cài đặt thư viện Python thông qua Pip
### 2.1. Trên thiết bị có kết nối Internet
- Tạo một `venv` mới để cài đặt các gói *Python* (ví dụ `medaka`)
```linux
conda create --name medaka_env python=3.11.11
```
```linux
conda activate medaka_env
```
```linux
python -m pip install --upgrade pip
```
- Cài đặt thư viện `medaka`
```linux
pip install medaka
```
- Sử dụng thư viện `medaka` cần cài đặt thêm `pyabpoa`
```linux
pip install pyabpoa
```
- Tạo tệp `requirements.txt`
```linux
pip freeze >> requirements.txt
```
- Tải thư viện và các gói phụ thuộc vào `wheelhouse`
```linux
mkdir wheelhouse && pip download -r requirements.txt -d wheelhouse
```
- Sao chép tệp `requirements.txt` vào thư mục `wheelhouse`
```linux
cp requirements.txt wheelhouse
```
- Nén thư mục `wheelhouse` thành tệp `wheelhouse.tar.gz` bằng lệnh
```linux
tar -zcf wheelhouse.tar.gz wheelhouse
```
### 2.2. Trên thiết bị không có kết nối Internet
- Sao chép thư mục `wheelhouse.tar.gz` đã tạo ở bước trên vào máy đích ví dụ `/home/username-2/wheelhouse.tar.gz`
- Giải nén tệp  `wheelhouse.tar.gz` thành thư mục `wheelhouse` bằng lệnh
```linux
tar -zxf /home/username-2/wheelhouse.tar.gz
```
- Kích hoặt môi trường `my_offline_env` trong *Conda* đã tạo ở bước **1.2**
```linux
conda activate my_offline_env
```
- Đi đến thư mục `/home/username-2` chạy lệnh cài đặt các thư viện *Python*
```linux
pip install -r wheelhouse/requirements.txt --no-index --find-links wheelhouse
```
- Kiểm tra cài đặt
```linux
medaka --version
```

# LINUX - Create a Local channel && Install packages via Conda
