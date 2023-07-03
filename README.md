# grafana

### Actualizar linux

sudo apt update

### Agregar llaves de grafana al repo de linux

sudo apt install -y gnupg2 curl software-properties-common

curl -fsSL https://packages.grafana.com/gpg.key|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/grafana.gpg

sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"

### Instalar grafana y activar el servicio

sudo apt update

sudo apt -y install grafana

sudo systemctl enable --now grafana-server

systemctl status grafana-server.service 


### Ingresar vía web http://IP:3000

Username: admin

Password: admin

## Node exporter

### Descargar node exporter y descomprimir
wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz

tar xvf node_exporter-1.3.1.linux-amd64.tar.gz

cd node_exporter-1.3.1.linux-amd64

sudo cp node_exporter /usr/local/bin

cd ..

rm -rf ./node_exporter-1.3.1.linux-amd64

### Agregar usuario
sudo useradd --no-create-home --shell /bin/false node_exporter

sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter

### Editar el archivo
sudo nano /etc/systemd/system/node_exporter.service

[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target

### Iniciar servicios

sudo systemctl daemon-reload
sudo systemctl start node_exporter
