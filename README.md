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

## Instalar prometheus

### agregar usuario

sudo groupadd --system prometheus

sudo useradd -s /sbin/nologin --system -g prometheus prometheus

sudo mkdir /var/lib/prometheus

for i in rules rules.d files_sd; do sudo mkdir -p /etc/prometheus/${i}; done

sudo apt update

sudo apt -y install wget curl vim

### Crear archivos de configuracion

mkdir -p /tmp/prometheus && cd /tmp/prometheus

curl -s https://api.github.com/repos/prometheus/prometheus/releases/latest | grep browser_download_url | grep linux-amd64 | cut -d '"' -f 4 | wget -qi -

tar xvf prometheus*.tar.gz

cd prometheus*/

sudo mv prometheus promtool /usr/local/bin/

sudo mv prometheus.yml /etc/prometheus/prometheus.yml

sudo mv consoles/ console_libraries/ /etc/prometheus/

cd $HOME


### Editar el archivo

nano systemd/system/prometheus.service


[Unit]

Description=Prometheus

Documentation=https://prometheus.io/docs/introduction/overview/

Wants=network-online.target

After=network-online.target


[Service]

Type=simple

User=prometheus

Group=prometheus

ExecReload=/bin/kill -HUP \$MAINPID

ExecStart=/usr/local/bin/prometheus \

  --config.file=/etc/prometheus/prometheus.yml \
  
  --storage.tsdb.path=/var/lib/prometheus \
  
  --web.console.templates=/etc/prometheus/consoles \
  
  --web.console.libraries=/etc/prometheus/console_libraries \
  
  --web.listen-address=0.0.0.0:9090 \
  
  --web.external-url=


SyslogIdentifier=prometheus

Restart=always


[Install]

WantedBy=multi-user.target

### Asignar permisos


for i in rules rules.d files_sd; do sudo chown -R prometheus:prometheus /etc/prometheus/${i}; done

for i in rules rules.d files_sd; do sudo chmod -R 775 /etc/prometheus/${i}; done

sudo chown -R prometheus:prometheus /var/lib/prometheus/

sudo systemctl daemon-reload

sudo systemctl start prometheus

sudo systemctl enable prometheus

systemctl status prometheus

### Agregar node exporter a promehteus. Reemplace las viñetas por un guion -

nano /etc/prometheus/prometheus.yml


scrape_configs:

  #The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.

  - job_name: "prometheus"

    static_configs:

      - targets: ["localhost:9090"]

  - job_name: 'node_exporter'

    static_configs:

      - targets: ['localhost:9100']


### Reiniciar servicio

sudo systemctl restart prometheus

## Vía web agregar fuente de datos y crear tablero
