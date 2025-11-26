# Monitoramento Distribuído com Prometheus, Thanos e Grafana

## 🔎 Visão Geral  
Este repositório oferece um exemplo de uma **solução completa de monitoramento distribuído** para ambientes com múltiplos sites e tenants. O sistema coleta métricas de infraestrutura e serviços de contêineres e bancos de dados, com **retenção histórica de 15 dias**, **compactação eficiente** e **alertas em tempo real via Telegram / Grafana**.

## 🧩 Tecnologias e Componentes  

- **Prometheus** — coleta de métricas via exporters (Node Exporter, cAdvisor, Redis Exporter, Mongo Exporter).  
- **Exporters** — para hardware, contêineres Docker, Redis e MongoDB.  
- **Thanos** — para armazenamento de longo prazo, compactação e agregação centralizada.  
- **Grafana** — visualização das métricas e dashboards.  
- **MinIO** — storage de objetos usado como backend pelo Thanos.  
- Suporte a múltiplos “edges” (servidores locais) e um nó “central” para agregação.  

## 📦 Arquitetura  

```
[Exporters] → [Prometheus Edge (scrape local)]  
                   ↓ remote_write  
             [Thanos Receive] → [MinIO + Thanos Store/Compactor/Sidecar]  
                                       ↑  
                                 [Prometheus Central] → [Grafana (dashboards + alertas)]  
```  

- Instâncias “edge” espalhadas por site/tenant fazem scraping local das métricas.  
- Dados enviados ao nó central via `remote_write`.  
- No central: Thanos + MinIO armazenam e compactam os dados.  
- Grafana consulta os dados centralizados, permitindo dashboards agregados por site ou tenant.  
- A utilização de labels (ex: site, tenant) permite segmentação e filtragem de métricas.  

## ✅ Benefícios  

- Monitoramento unificado de múltiplos sites/tenants.  
- Retenção de histórico e possibilidade de análise no longo prazo.  
- Redução de downtime potencial (~ 40%) graças a alertas e visibilidade proativa.  
- Segmentação por labels — ideal para ambientes corporativos / multi-cliente.  
- Scripts de automação (bash) para hardening e verificações periódicas, facilitando deploy e manutenção.  

## 🧰 Requisitos  

- Docker & Docker Compose (versão 3.8 ou superior).  
- Sistema Ubuntu 20.04+ (ou equivalente compatível).  
- Arquivo `.env` com variáveis de ambiente necessárias (ex.: `MINIO_ROOT_USER`, `TELEGRAM_BOT_TOKEN`, etc.).  
- Acesso às instâncias de Redis e MongoDB que você deseja monitorar (para configurar os exporters correspondentes).  

## 🚀 Instalação e Execução  

1. Clone este repositório:  
   ```bash
   git clone https://github.com/MWismeck/monitoring-devops-stack.git
   cd monitoring-devops-stack
   ```  
2. Crie um arquivo `.env` com suas variáveis de ambiente (MinIO, Telegram, etc.).  
3. Configure os exporters (Node, cAdvisor, Redis, Mongo) nos servidores “edge”.  
4. No nó central, execute:  
   ```bash
   docker compose up -d
   ```  
5. Acesse o painel do Grafana e importe dashboards pré-configurados ou crie os seus.  
6. Verifique se os dados estão sendo enviados corretamente via `remote_write` e armazenados no Thanos + MinIO.  

## ⚙️ Funcionamento  

- Cada servidor “edge” executa exporters + Prometheus local para coleta das métricas.  
- Os dados coletados são enviados via `remote_write` para o central.  
- No central, o Thanos + MinIO armazenam e compactam os dados.  
- O Grafana consome os dados do Thanos ou Prometheus Central, permitindo dashboards globais ou por site/tenant.  
- Regras de alertas podem ser configuradas para disparo de notificações (ex.: via Telegram).  

## 📁 Estrutura de Pastas (exemplo)  

```
/
├── docker-compose.yml        # define serviços centrais (MinIO, Thanos, Prometheus Central, Grafana)  
├── .env                      # variáveis de ambiente necessárias  
├── exporters/                # exemplos/configurações dos exporters (Node, cAdvisor, Redis, Mongo)  
├── docs/                     # documentação adicional, diagramas, guias de uso  
└── README.md                 # este arquivo  
```  

## 👥 Contribuições  

Contribuições são bem-vindas! Se quiser sugerir melhorias, adaptar para outros cenários ou adicionar funcionalidades — abra uma Issue ou um Pull Request.  

## 📄 Licença  

Este projeto está licenciado sob a licença **MIT** — sinta-se livre para usar, modificar e distribuir.  
