Vagrant.configure("2") do |config|
    # Configuración de la primera máquina: Servidor Apache 1
    config.vm.define "web1" do |web1|
      web1.vm.box = "ubuntu/bionic64"
      web1.vm.network "private_network", ip: "192.168.33.10"
      
      web1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
  
      # Compartir directorio local con Apache
      web1.vm.synced_folder "./html", "/var/www/html"
  
      # Provisionar Apache
      web1.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        echo "<h1>Servidor Web 1</h1>" | sudo tee /var/www/html/index.html
        sudo systemctl enable apache2
        sudo systemctl start apache2
      SHELL
    end
  
    # Configuración de la segunda máquina: Servidor Apache 2
    config.vm.define "web2" do |web2|
      web2.vm.box = "ubuntu/bionic64"
      web2.vm.network "private_network", ip: "192.168.33.11"
      
      web2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
  
      # Compartir directorio local con Apache
      web2.vm.synced_folder "./html", "/var/www/html"
  
      # Provisionar Apache
      web2.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        echo "<h1>Servidor Web 2</h1>" | sudo tee /var/www/html/index.html
        sudo systemctl enable apache2
        sudo systemctl start apache2
      SHELL
    end
  
    # Configuración de la tercera máquina: Servidor Nginx (Load Balancer)
    config.vm.define "nginx" do |nginx|
        nginx.vm.box = "ubuntu/bionic64"
        nginx.vm.network "private_network", ip: "192.168.33.12"
        nginx.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
        end
        nginx.vm.provision "shell", inline: <<-SHELL
        sudo apt update
        sudo apt install -y nginx
        sudo tee /etc/nginx/sites-available/load_balancer <<EOF
    server {
        listen 80;
        location / {
            proxy_pass http://backend;
        }
    }

    upstream backend {
        server 192.168.33.10;
        server 192.168.33.11;
    }
    EOF
        sudo ln -sf /etc/nginx/sites-available/load_balancer /etc/nginx/sites-enabled/
        sudo systemctl restart nginx
        SHELL
    end
end
  