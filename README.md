# VagrantChallenge2
Vagrant Challenge Python Flask app Gunicorn

## Vagrant Nedir?

Vagrant, Yazılım geliştirme projeleri için hızlı ve tekrarlanabilir bir şekilde sanal makineler oluşturulmasını sağlayan araçtır.


## Proje Adımları

1. GitHub'da repo oluşturuldu ve lokale clone edildi.
2. Lokalde ilgili klasörün içine gidildi:

    ```bash
    cd Desktop/VagrantChallenge2/
    ```

3. Vagrant dosyası (`Vagrantfile`) oluşturuldu:

    ```bash
    vagrant init ubuntu/focal64
    ```
    vagrant init komutu, bulunduğunuz konumda örnek bir vagrantFile oluşturur. Bu örnekte, Ubuntu 20.04 LTS sanal makinesi kullanıldı.
    Bir diğer yöntem olarak Vagrantfile'ı kendiniz oluşturabilirsiniz, bunun için aşağıdaki komutları girebilirsiniz.

    ```ruby
    Vagrant.configure("2") do |config|
      config.vm.box = "ubuntu/focal64"
    end
    ```

## Vagrantfile Dosyası

```ruby
Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/focal64"

  config.vm.network "forwarded_port", guest: 8000, host: 8000

  config.vm.provision "file", source: "./app", destination: "~/app"
  
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    sudo apt-get install -y python3 python3-pip
    sudo pip3 install flask gunicorn
  SHELL
  
  config.vm.provision "shell", run: "always", inline: <<-SHELL
    cd app && gunicorn --bind 0.0.0.0:8000 app:app &
  SHELL
end

```
Sanal makinede çalıştırılan portun host makinede erişilebilmesi için ilgili port `guest` parametresine tanımlanır. Host makinede erişilmesi istenilen port ise `host` parametresine tanımlanır. Bu şekilde tanımlanan port, host makinede `localhost` üzerinden erişim sağlanır.

```ruby
config.vm.network "forwarded_port", guest: 8000, host: 8000
```
Aşağıdaki satır ile sanal makineye dosyaların kopyalanmasını sağlar. Kaynak (source) ve hedef (destination) dizinler belirtilir. 

```ruby
  config.vm.provision "file", source: "./app", destination: "~/app"
```

```ruby
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    sudo apt-get install -y python3 python3-pip
    sudo pip3 install flask gunicorn
  SHELL
```
- apt-get update: Paket yöneticisinin paket listesini günceller.

- sudo apt-get install -y python3 python3-pip: Python 3 ve pip'in kurulumunu yapar.

- sudo pip3 install flask gunicorn: Flask ve Gunicorn paketlerini kurar. 

Gunicorn, Python uygulamalarının web sunucusu olarak çalıştırılmasını sağlar ve bu durumda bir Flask uygulamasını çalıştırmak için kullanılmaktadır.

Sanal makine her ayağa kalktığında (`vagrant up`) uygulamanın çalışması için `run always` yazılır. Uygulamayı çalıştırmak için gunicorn komutu kullanılır. Sunucunun tüm ağ arayüzlerinden gelen istekleri kabul etmesi için `--bind 0.0.0.0:8000` parametresi eklenir. 

```ruby
  config.vm.provision "shell", run: "always", inline: <<-SHELL
    cd app && gunicorn --bind 0.0.0.0:8000 app:app &
  SHELL
```

1. Sanal makineyi başlatmak için bu komut çalıştırılır

    ```bash
    vagrant up
    ```
    
    ![](./doc/vm.png)

    ![Hello World](./doc/app.png)

2. Sanal makineyi kapatmak için:

    ```bash
    vagrant halt
    ```

3. Sanal makinayı tamamen silmek için:

    ```bash
    vagrant destroy
    ```

