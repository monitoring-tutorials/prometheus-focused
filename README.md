## Objetivos
A ideia desse repositório é manter um estudo de algumas ferramentas de monitoramento e abordar os prinmcipais conceitos que envolve monitoramento e observabilidade, seja para ambiente on-premisses ou focado em [Cloud Native](https://www.cncf.io/). Vou mostrar o uso do Prometheus integrado com outras ferramentas com o Grafana, em ambientes para Kubernetes, infraestrutura local e outros cenários.

### Sumario
- [Objetivos](#objetivos)
  - [Sumario](#sumario)
  - [Porque monitorar?](#porque-monitorar)
  - [Monitoramento x Observabilidade](#monitoramento-x-observabilidade)
  - [Onde estudar?](#onde-estudar)
- [Prometheus](#prometheus)
- [Glossário](#glossário)
- [Arquitetura](#arquitetura)
- [Instalação do Prometheus](#instalação-do-prometheus)
  - [Prometheus no Linux](#prometheus-no-linux)
  - [Prometheus no Docker](#prometheus-no-docker)

### Porque monitorar?
O monitoramento dentro de tecnologia se refere à iniciativa de obter insights do seu ambiente, seja on-premisses ou cloud native. É o método de ler e entender o que está se passando dentro do contexto da sua infraestrutura/sistema da sua empresa. Existem MUITAS vantagens em manter um monitoramento dos seus ativos de TI, seja servidores, banco de dados, discos, sistemas de backup, aplicações, algumas eu cito abaixo:

- visibilidade da sua infraestrutura e dos sistemas envolvidos
- capacidade de fácil interpretação e leitura dos dados de aplicações
- entendimento fim a fim do que está ocorrendo e o que ocorreu no passado
- centralização lógica de logs e métricas dos sistemas e aplicações
- alarmes, alertas e notificações que podem demonstrar o que está ocorrendo
- uso de ferramentas poderosas suportadas pela comunidade, CNCF, Linux Foundation
- logs, métricas e ações em tempo real, com mínimos detalhes e porcentagens

### Monitoramento x Observabilidade
Aqui existem muitos pontos que é bom destacar, muitas pessoas ainda não tem a ideia da diferença entre as duas abordagens. Vamos começar citando o monitoramento, que é a base da observabilidade. As primeiras ferramentas de monitoramento lá atrás, `Nagios`, `Zabbix` e outras, tinham como foco o monitoramento mais raíz, ou seja, monitorar os principais `recursos de um servidor`, analisar se o `ping está down`, `gerar triggers de uso de disco`, `memória dos servidores`, analisar aquele switch, se  porta n está funcionando bem, se existe `mais portas livres e VLANS disponíveis` para aquele SD e muitos outros cenários. Isso é o monitoramento mais a baixo nível (que não deixa de ser importante, que isso fique claro).

Quando estamos mencionando a observabilidade, o foco muda totalmente. `Medir e garantir` são as palavras mais usadas, medir da forma correta os níveis de serviços e garantir que as aplicações e métricas estejam dentro do esperado, sem delays, atrasos, inconsistências. Aqui dentro do observabilidade, você precisa ir além para medir e observar, você precisa `codar se necessário`, criar um método que cobre todo o cenário de um serviço por exemplo, `integrar com outros sistemas de logs`. Ultimamente tem se usado MUITO a abordagem de `observability as code`, para observar usando código, abordagens baseadas em `IaC`, `GitOps` fazem parte do contexto de observabilidade.

### Onde estudar?
Para aquelas pessoas que estejam buscando conhecimentos sobre monitoramento, observabilidade e [Promtheus](https://prometheus.io/). Abaixo eu compartilho algumas fontes que são ótima e podem ajudar demais na sua jornada.

- [GitHub - Descomplicando o Prometheus](https://github.com/badtuxx/DescomplicandoPrometheus).
- [Udemy - Monitoramento de Aplicações com Prometheus e Grafana](https://www.udemy.com/course/monitorando-aplicacoes-com-prometheus-e-grafana/).
- 

## Prometheus
[Prometheus](https://prometheus.io/) é mais uma de tantas ferramentas existentes no mercado com foco em monitoramento e observabilidade de aplicações, serviços e infraestrutura. Mas ele se difere de MUITAS outras pelo fato de ser bem desenvolvido e receber centenas de milhares de contribuições da comunidade, é um projeto graduado da CNCF.

## Glossário
[Aqui](https://prometheus.io/docs/introduction/glossary/) você encontra termos que são usados dentro da arquitetura do Prometheus, ajuda demais no entendimento da ferramenta e seu modo de funcionamento.

## Arquitetura
Abaixo o exemplo de arquitetura que é usado no Prometheus e seus componentes, o desenho ajuda a esclarecer como e onde estão os componentes e com quais controladores eles se conectam.

![prometheus](images/prometheus1.png)

## Instalação do Prometheus 
Iremos abordar aqui algumas formas de instalar o Prometheus em diversos tipos de sistemas, seja diretamente no Linux, Docker, Kubernetes, via Helm e mais.

### Prometheus no Linux
Para que possamos ter uma ideia de como podemos partir com o Prometheus, vamos começar do zero com Linux, acho uma forma bem legal de demonstrar o seu uso.

- Para que seja possível a instalação, siga os passos para a instalação [aqui](https://prometheus.io/download/). E com alguns ajustes nos arquivos abaixo:

```bash
Installing Prometheus
Install wget in case you don't have it.
- yum install wget -y

Download the binaries and libraries.
- wget https://github.com/prometheus/prometh...

Extract content
- tar xvf prometheus-2.28.1.linux-amd64.tar.gz

 Create prometheus path
- mkdir -p /opt/prometheus

Move binaries to the new path.
- cp -rp . /opt/prometheus

Create Service file with the content below.
vim /etc/systemd/system/prometheus.service

[Unit]
Description=Prometheus Service
After=network.target

[Service]
Type=simple
ExecStart=/opt/prometheus/prometheus --config.file=/opt/prometheus/prometheus.yml

[Install]
WantedBy=multi-user.target

Start the application
- systemctl start prometheus
- systemctl stop prometheus 
- systemctl restart prometheus


Download node Exporter
- wget https://github.com/prometheus/node_ex...

Extract node exporter content
- tar xvf node_exporter-1.2.0.linux-amd64.tar.gz

Copy to the environment path.
- cp -p node_exporter /usr/local/bin/

Create the service file.
- vim /etc/systemd/system/node-exporter.service

[Unit]
Description=Prometheus Node Exporter Service
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target


Start the service
- systemctl start node-exporter
- systemctl stop node-exporter 
- systemctl restart node-exporter


Add the node to prometheus
- vim /etc/prometheus/prometheus.yml
  - job_name: 'node-exporter'
    static_configs:
    - targets: ['localhost:9100']

Installing Grafana
Download Grafana and install
- wget https://dl.grafana.com/oss/release/gr...
- yum install grafana-8.0.6-1.x86_64.rpm

Start Grafana
- systemctl start grafana-server

Login to your grafan page
http://192.168.56.11:3000/login
admin
admin

- Click add your first data source
- Add prometheus source
- Go back to the home page, click over "+" button and then click over import.
- Paste the node exporter code id  which is 1860 and then click over load button.
- Select Prometheus.
- Done.
```

### Prometheus no Docker





