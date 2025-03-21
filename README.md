# **LINUX - Use conda-pack to clone the entire environment**
## **1. Trên thiết bị có kết nối Internet**
- Tạo môi trường biotools trong conda
```linux
conda create --name biotools python pip
conda activate biotools
```
- Cài đặt các gói cần thiết qua conda, nếu các thư viện nào bị lỗi có thể cài đặt chúng bằng pip
```linux
conda install fastqc bwa gatk bcftools snpeff samtools bedtools deeptools picard tabix
```
```linux
pip install fastqc bwa gatk bcftools snpeff samtools bedtools deeptools picard tabix
```
- Cài đặt conda-pack để đóng gói môi trường
```linux
conda install -c conda-forge conda-pack
```
- Đóng gói môi trường, lệnh conda pack sẽ tạo ra một file nén biotools.tar.gz chứa toàn bộ môi trường conda 
```linux
conda pack -n biotools -o biotools.tar.gz
```
- Sao chép file biotools.tar.gz đến máy đích
## **2. Trên thiết bị không có kết nối Internet**
- Giải nén môi trường trong máy đích
```linux
mkdir -p ~/.conda/envs/biotools
tar -xzf biotools.tar.gz -C ~/.conda/envs/biotools
```
- Kích hoạt môi trường trên máy đích
```linux
source /usr/miniconda3/bin/activate
conda activate biotools
```
## **3. Cài đặt biến môi trường cho JAVA và chạy GATK**
- Đặt biến JAVA PATH và GATK PATH
 ```linux
echo 'export JAVA_HOME=/usr/' >> ~/.bashrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc
echo 'export GATK=/usr/GATK/gatk-4.5.0.0/gatk-package-4.5.0.0-local.jar' >> ~/.bashrc
source ~/.bashrc
```
- Kiểm tra mô trường JAVA, nên là `/usr/bin/java`
```linux
which java
java --version
```
- Chạy GATK
```linux
java -jar $GATK
```
# **LINUX - Create a Local Channel && Install Packages via Conda**
## **1. Trên thiết bị có kết nối Internet**
- Tạo thư mục muốn dùng làm *Local channel*, ví dụ đối với `linux-64`
```linux
mkdir -p /home/username/my_local_channel/linux-64
```
- Tùy chỉnh cấu hình trong tệp `.condarc`, Nếu không thấy tệp `~/.condarc` hoặc `/etc/conda/.condarc` hãy
tự tạo tệp bằng lệnh `vi /home/username/.condarc`. Trong tệp `.condarc`, thứ tự các kênh rất quan trọng. Trong ví dụ bên dưới,
*Conda* sẽ tìm kiếm các gói trong kênh `conda-forge` trước, sau đó là `bioconda`, và cuối cùng là `defaults`.
- Đặt lại `pkgs_dirs` mỗi lần sử dụng *Conda* sẽ ưu tiên lưu các gói vào thư mục `/home/username/my_local_channel`.
```linux
channels:
  - conda-forge
  - bioconda
  - defaults
pkgs_dirs:
  - /home/username/my_local_channel/linux-64
```
- Kích hoặt `Conda`
```linux
source ~/miniconda3/bin/activate
```
- Tải các gói của `python 3.10` vào *Local channel* 
```linux
conda create --name temp_env python=3.10 --download-only
```
- Tải các gói của `medaka` vào *Local channel*
```linux
conda install medaka=1.11.3 --download-only
```
- Tải các gói của `pyabpoa` vào *Local channel*
```linux
conda install pyabpoa --download-only
```
- Tạo *index* cho *local channel*, lệnh bên dưới sẽ tạo tệp `/home/username/my_local_channel/linux-64/repodata.json`,
và tệp `/home/username/my_local_channel/noarch/repodata.json` giúp *Conda* nhận diện đó là một *channel* hợp lệ.
- Nếu chưa có lệnh `index`, hãy cài đặt `conda-build` bằng lệnh `conda install conda-build`
```linux
conda index /home/username/my_local_channel
```
- Nén thư mục `my_local_channel` thành tệp `my_local_channel.tar.gz` bằng lệnh
```linux
tar -zcf my_local_channel.tar.gz my_local_channel
```
## **2. Trên thiết bị không có kết nối Internet**
- Sao chép thư mục `my_local_channel.tar.gz` đã tạo ở bước trên vào máy đích ví dụ `/home/username/my_local_channel.tar.gz`
- Giải nén tệp  `my_local_channel.tar.gz` thành thư mục `my_local_channel` bằng lệnh
```linux
tar -zxf /home/username/my_local_channel.tar.gz
```
- Đặt `my_local_channel` thành `Channel` ưu tiên bằng cách chỉnh sửa tệp cấu hình người dùng `.condarc`
```linux
channels:
  - file:///home/username/my_local_channel
  - conda-forge
  - bioconda
  - defaults
```
- Kích hoặt `Conda`
```linux
source ~/miniconda3/bin/activate
```
- Tạo môi trường mới với `python 3.10` sử dụng `Local channel`
```linux
conda create --name medaka_env python=3.10 -c file:///home/username/my_local_channel/ --offline --override-channels
```
```linux
conda activate medaka_env
```
- Kiểm tra phiên bản *Python*
```linux
python --version
```
- Sau đó kiểm tra đường dẫn *Python*, kết quả phải là `/home/username/miniconda3/envs/medaka_env/bin/python`
```python
import sys
print(sys.executable)
```
- Kiểm tra đường dẫn *pip*, kết quả phải là `/home/username/miniconda3/envs/medaka_env/lib/python3.10/site-packages/pip`,
nếu không phải, cần vô hiệu hóa *user site packages*, để tránh việc *pip* truy cập vào các gói được cài đặt ở cấp *user*,
có thể tạm thời đặt biến môi trường thành `export PYTHONNOUSERSITE=1`
```linux
python -m pip -V
```
- Cài đặt thư viện `medaka`
```linux
conda install medaka -c file:///home/username/my_local_channel/ --offline --override-channels
```
- Kiểm tra cài đặt
```linux
medaka --version
```

# **LINUX - Install Libs via PIP**
## **1. Trên thiết bị có kết nối Internet**
- Tạo một `venv` mới để cài đặt các gói *Python* (ví dụ `medaka`)
```linux
conda create --name medaka_env python=3.10
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
## **2. Trên thiết bị không có kết nối Internet**
- Sao chép thư mục `wheelhouse.tar.gz` đã tạo ở bước trên vào máy đích ví dụ `/home/username/wheelhouse.tar.gz`
- Giải nén tệp  `wheelhouse.tar.gz` thành thư mục `wheelhouse` bằng lệnh
```linux
tar -zxf /home/username/wheelhouse.tar.gz
```
- Kích hoạt môi trường `medaka_env` trong `Conda` đã tạo ở mục trên
```linux
source ~/miniconda3/bin/activate
```
```linux
conda activate medaka_env
```
- Đi đến thư mục `/home/username/` thực thi lệnh cài đặt các thư viện *Python*
```linux
pip install -r wheelhouse/requirements.txt --no-index --find-links wheelhouse
```
- Kiểm tra cài đặt
```linux
medaka --version
```
