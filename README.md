# grafana

## Actualizar linux

sudo apt update

## Agregar llaves de grafana al repo de linux

sudo apt install -y gnupg2 curl software-properties-common
curl -fsSL https://packages.grafana.com/gpg.key|sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/grafana.gpg
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"

## Instalar grafana y activar el servicio

sudo apt update
sudo apt -y install grafana
sudo systemctl enable --now grafana-server
systemctl status grafana-server.service 


## Ingresar vía web http://IP:3000

Username: admin
Password: admin

