

## Ros2 & Gazebo Kurulumu

- Her hangi bir kurulum yapmadan önce sistem ayarlarının utf8 desteklediğinden emin olunması gerekir

```
locale

```
- Kaynak repoları ayarlamak
    - Main (Ana Repo)
    - Universe (Topluluk tarafından sürdürülen yazılım)
    - Restricted (Kısıtlı yazılım)
    - Multiverse (Özel yazılım)
- ROS'u kurmak amacıyla, Ubuntu Universe deposunu etkinleştirmemiz gerekir

```
sudo apt install software-properties-common
sudo add-apt-repository universe

```
- ROS 2 deposunu kaynak listenize ekleyin
```
sudo apt update && sudo apt install curl gnupg lsb-release
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(source /etc/os-release && echo $UBUNTU_CODENAME) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null

```
- Kurulum dosyasının kaynağını oluşturma

```
source /opt/ros/humble/setup.bash

```
- Bu admı sürekli tetiklemek için bash kabuğunu kullanıyorsanız aşşağıdaki kodu çalıştırın

```
echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

- C++ ile bir örnek çalıştırın 

```
ros2 run demo_nodes_cpp talker
```

- Çıktı aşağıdaki gibi olmalıdır

```
tugrulkaya@ctchost:~$ ros2 run demo_nodes_cpp talker 
[INFO] [1665513752.890048250] [talker]: Publishing: 'Hello World: 1'
[INFO] [1665513753.889986038] [talker]: Publishing: 'Hello World: 2'
[INFO] [1665513754.890008889] [talker]: Publishing: 'Hello World: 3'
[INFO] [1665513755.889998250] [talker]: Publishing: 'Hello World: 4'
[INFO] [1665513756.889890685] [talker]: Publishing: 'Hello World: 5'
[INFO] [1665513757.889988211] [talker]: Publishing: 'Hello World: 6'
[INFO] [1665513758.889987653] [talker]: Publishing: 'Hello World: 7'
[INFO] [1665513759.889979977] [talker]: Publishing: 'Hello World: 8'
```

- Başka bir terminal penceresinde, **örnek Python dinleyicisini çalıştırın

```
ros2 run demo_nodes_py listener
```
- Çıktı aşağıdaki gibi olmalıdır
```
tugrulkaya@ctchost:~$ ros2 run demo_nodes_py listener
[INFO] [1665514083.298383639] [listener]: I heard: [Hello World: 4]
[INFO] [1665514084.279726269] [listener]: I heard: [Hello World: 5]
[INFO] [1665514085.279382641] [listener]: I heard: [Hello World: 6]
[INFO] [1665514086.279389254] [listener]: I heard: [Hello World: 7]

```

### Bu çıktıları aldıysanız Hem C++ hem de Python API'leri düzgün çalışıyor demektir.


> **_NOT:_** Burada sorun yaşıyorsanız, yayını etkinleştirdiğinizden emin olun – ağ arabiriminin DDS üzerinden iletişim kurabilmesi için buna ihtiyacı vardır

- Aşağıdaki komutları iki ayrı terminal penceresinde çalıştırın

```
ros2 multicast receive
ros2 multicast sen
```
- İlk komutun çıktısı ikinci komut çalıştırıldıktan sonra aşağıdaki gibi olmalıdır.

```
tugrulkaya@ctchost:~$ ros2 multicast receive
Waiting for UDP multicast datagram...
Received from 192.168.0.18:42980: 'Hello World!'
```

- İlk komut başarılı bir çıktı döndürmezse (yani Received from xx.xxx.xxx.xx:42980: 'Hello World!'), güvenlik duvarı yapılandırmanızı güncellemeyi deneyin

- Güvenlik duvarı yapılandırma

```
sudo apt install ufw 
sudo ufw allow in proto udp to 224.0.0.0/4
sudo ufw allow in proto udp from 224.0.0.0/4
```
- Ardından terminali kapatıp yeni terminalde tekrar deneyin

**_Önemli:_** Herhangi bir hata çıkacak olursa aşağıdaki paketleri sisteminize kurup tekrar deneyebilirsiniz.

```
sudo apt update
sudo apt install \
    build-essential \
    curl \
    libbz2-dev \
    libffi-dev \
    liblzma-dev \
    libncursesw5-dev \
    libreadline-dev \
    libsqlite3-dev \
    libssl-dev \
    libxml2-dev \
    libxmlsec1-dev \
    llvm \
    make \
    tk-dev \
    wget \
    xz-utils \
    zlib1g-dev
```

## Docker ile Ros2 Kurulumu

- Docker ve Docker-compose yüklenmesi

```
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
sudo apt install docker-ce
sudo systemctl status docker
```
- Adım 2 — Docker Komutunu Sudo Olmadan Yürütme (İsteğe Bağlı)

```
sudo usermod -aG docker ${USER}
su - ${USER}
sudo usermod -aG docker username
```
```
sudo apt install docker-compose
```

- Docker imajını indirme

```
docker pull osrf/ros:foxy-desktop
```
- Docker imajını çalıştırma 

```
docker run -it osrf/ros:foxy-desktop
```
- Ros2 paketlerini listeleme ve yürütülebilir dosyalırı listeleme

```
ros2 pkg list
ros2 pkg executables
```

- Örnek uygulamaları çalıştırma 

```
ros2 run demo_nodes_cpp listener
ros2 run demo_nodes_cpp talker

docker run -it --rm osrf/ros:foxy-desktop ros2 run demo_nodes_cpp talker

docker run -it --rm osrf/ros:foxy-desktop ros2 run demo_nodes_cpp listener
```

 **_NOT:_** docker tagı olmayan kodlarda interaktif terminal çalışırken yazmanız gerekiyor yani yukardaki docker run komutundan sonra.
 
 **_NOT_** Ros2 Dockerize etme yakında eklenecektir.

 ## Gazebo Kurulumu

 ```
 curl -sSL http://get.gazebosim.org | sh
 gazebo
 ```


## Sıradakiler


[Ortamınızı yapılandırmak](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Configuring-ROS2-Environment.html)

[verileri kaydetmek](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Recording-And-Playing-Back-Data/Recording-And-Playing-Back-Data.html)

[ROS 2 eğitimleri](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Recording-And-Playing-Back-Data/Recording-And-Playing-Back-Data.html)

## Yararlanılan Kaynaklar 

[Ros2 Dökümantasyonu](https://docs.ros.org/en/humble/index.html), [Ubuntu Repoları](https://help.ubuntu.com/community/Repositories/Ubuntu#What_are_Repositories.3F), [Ros2 Dağıtım Çeşitleri](https://www.ros.org/reps/rep-2001.html), [mcap dosya biçimi](https://mcap.dev/)

