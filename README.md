# 🌦️ Real-Time Weather Alert System com AWS e API Tomorrow.io

Este projeto simula a ingestão, tratamento e análise de dados climáticos em tempo real e em batch com notificações automáticas. Ele foi desenvolvido como um **laboratório completo de Engenharia de Dados na AWS**, sendo uma ótima base para estudos, portfólio ou até mesmo aplicações reais de monitoramento ambiental.

> 📚 Este repositório serve como um **mini-curso prático**. Você vai aprender a construir arquiteturas de dados com foco em ingestão, transformação, persistência e orquestração na nuvem, usando os principais serviços gerenciados da AWS.

---

## 📌 Índice

- [📖 Visão Geral](#visão-geral)
- [📊 Fluxo do Processo](#fluxo-do-processo)
- [🏗️ Arquitetura da Solução](#arquitetura-da-solução)
- [🗂️ Estrutura de Pastas](#estrutura-de-pastas)
- [🧰 Tecnologias Utilizadas](#tecnologias-utilizadas)
- [🚀 Etapas do Projeto](#etapas-do-projeto)
- [✅ Resultado Final](#resultado-final)
- [🧠 O Que Você Aprende](#o-que-você-aprende)
- [🧪 Como Reproduzir](#como-reproduzir)
- [🙋‍♂️ Autor](#autor)

[🔝 Voltar ao topo](#real-time-weather-alert-system-com-aws-e-api-tomorrowio)

---

## 📖 Visão Geral

Este sistema consome dados da API do Tomorrow.io, envia em tempo real para um stream do Kinesis, processa com AWS Lambda e grava os dados brutos em um bucket S3. Esses dados são depois transformados e catalogados com Glue, permitindo consultas via Athena. Além disso, alertas meteorológicos são enviados por SMS ou e-mail caso certas condições climáticas sejam atingidas.

---

## 📊 Fluxo do Processo

![Fluxo do Processo](arquitetura/fluxo-processo.png)

---

## 🏗️ Arquitetura da Solução

![Arquitetura](arquitetura/arquitetura.png)

- **Camada de Ingestão:** Requisições à API Tomorrow.io executadas por uma função Lambda (Producer) e ingestão de dados em tempo real pelo Kinesis Data Stream.
- **Camada de Processamento em Tempo Real:** Lambda (Consumer) processa os dados do Kinesis, avalia parâmetros e dispara alertas via SNS.
- **Camada de Persistência:** Lambda (Batch) armazena os dados em S3 (camada raw).
- **Camada de Transformação:** AWS Glue transforma os dados em formato parquet e organiza em partições ano/mês/dia.
- **Camada de Análise:** AWS Athena realiza consultas SQL sobre os dados da camada gold.
- **Orquestração:** CloudWatch Events dispara a Lambda de ingestão a cada 3 minutos.

---

## 🗂️ Estrutura de Pastas

```text
aws-weather-realtime-etl/
├── README.md                        # Documentação completa do projeto
├── imagens/                         # Prints de cada etapa do processo
│   ├── 01_api_key_tomorrow.png
│   ├── 02_kinesis_stream.png
│   ├── ...
├── arquitetura/                    # Diagramas e fluxogramas
│   ├── arquitetura.png
│   └── fluxo-processo.png
├── lambda/                         # Funções Lambda (Producer, Consumer, Batch)
│   ├── lambda_function_producer.py
│   ├── lambda_function_alert.py
│   └── lambda_function_batch.py
├── glue/                           # Scripts de ETL com PySpark
│   └── jobglue.py
└── .env.example                    # Exemplo de variáveis de ambiente
```

Essa estrutura permite fácil navegação, reprodutibilidade e clareza na documentação do pipeline completo.

---

## 🧰 Tecnologias Utilizadas

| Serviço         | Descrição |
|----------------|-----------|
| **Tomorrow.io** | Fonte dos dados climáticos via API REST |
| **AWS Lambda** | Processamento serverless de dados: ingestão, transformação e alertas |
| **Amazon Kinesis** | Stream de dados em tempo real (broker de eventos) |
| **Amazon S3** | Armazenamento escalável em camadas (raw/gold) |
| **AWS Glue** | ETL e catalogação de dados com PySpark |
| **Amazon Athena** | Consultas SQL diretamente nos dados do S3 |
| **Amazon SNS** | Serviço de notificação via SMS/E-mail |
| **CloudWatch Events** | Agendamento (cron) para execuções automatizadas |

---

## 🚀 Etapas do Projeto

### 1️⃣ Obtenção da API Key
- Crie uma conta em [Tomorrow.io](https://app.tomorrow.io/)
- Gere uma API Key e configure como variável de ambiente `TOMORROW_API_KEY` <p>



### 2️⃣ Criação do Kinesis Data Stream
- Nome: `broker`
- Serve como canal de ingestão para dados climáticos em tempo real

### 3️⃣ Lambda Producer
- Nome: `producer`
- Código: [lambda_function_producer.py](lambda/lambda_function_producer.py)
- Objetivo: Consumir a API e enviar para o Kinesis

### 4️⃣ Tópico SNS para Alertas
- Nome: `snsalerta`
- Envia notificações para e-mail e SMS configurados

### 5️⃣ Lambda Consumer (Realtime)
- Nome: `consumer_realtime`
- Código: [lambda_function_alert.py](lambda/lambda_function_alert.py)
- Objetivo: Ler Kinesis, aplicar lógica de alerta e disparar mensagens via SNS

### 6️⃣ CloudWatch Events
- Nome: `producer_event`
- Cron: Executa a Lambda Producer a cada 3 minutos

### 7️⃣ Lambda Consumer (Batch)
- Nome: `consumer_batch`
- Código: [lambda_function_batch.py](lambda/lambda_function_batch.py)
- Objetivo: Persistir os dados recebidos no S3 em formato JSON (camada raw)

### 8️⃣ Glue Crawler (raw)
- Nome: `raw_crawler`
- Cria catálogo de dados a partir dos arquivos em `s3://alertarealtime/raw/`

### 9️⃣ Glue Job (ETL)
- Nome: `weather_job`
- Código: [jobglue.py](glue/jobglue.py)
- Objetivo: Transformar dados JSON para Parquet particionado

### 🔟 Glue Crawler (gold)
- Nome: `gold_crawler`
- Atualiza o catálogo a partir dos arquivos tratados (camada gold)

### 1️⃣1️⃣ Amazon Athena
- Permite consulta analítica SQL em cima dos dados armazenados no S3 (camada gold)

---

## ✅ Resultado Final

- Dados climáticos organizados e processados automaticamente
- Alertas por SMS e e-mail em tempo real
- ETL completo usando serviços gerenciados AWS
- Consultas SQL eficientes com Athena

---

## 🧠 O Que Você Aprende

- 🛠️ Como construir pipelines em tempo real e em batch
- ☁️ Prática com serviços AWS (Lambda, Kinesis, S3, Glue, Athena, SNS)
- 🧩 Montagem de Data Lake escalável e consultável
- 📈 Tratamento de dados e automação com eventos
- 🔐 Princípios de segurança com IAM Roles específicas por função

---

## 🧪 Como Reproduzir

```bash
# Clone o projeto
git clone https://github.com/seuusuario/aws-weather-realtime-etl.git
cd aws-weather-realtime-etl

# Configure sua variável de ambiente
export TOMORROW_API_KEY="sua-chave-da-api"
```

A seguir, siga cada etapa numerada da seção [🚀 Etapas do Projeto](#etapas-do-projeto).

---

## 🙋‍♂️ Autor

Projeto desenvolvido por **Seu Nome Aqui**  
🔗 [LinkedIn](https://www.linkedin.com/in/Adrianogvs)  
💻 [GitHub](https://github.com/Adrianogvs)

Se curtir, ⭐ o repositório e compartilhe com a comunidade!

[🔝 Voltar ao topo](#real-time-weather-alert-system-com-aws-e-api-tomorrowio)

