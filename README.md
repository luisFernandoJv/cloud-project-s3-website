# ☁️ Deploy de Site Estático na AWS com S3 + CLI

> Projeto prático de Cloud Computing — infraestrutura configurada 100% via AWS CLI, sem tocar no console gráfico.

![AWS](https://img.shields.io/badge/AWS-S3-FF9900?style=flat-square&logo=amazonaws&logoColor=white)
![CLI](https://img.shields.io/badge/AWS_CLI-Configurado-232F3E?style=flat-square&logo=amazonaws&logoColor=white)
![IAM](https://img.shields.io/badge/IAM-Usuário_%26_Políticas-DD344C?style=flat-square&logo=amazonaws&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-Script_de_Deploy-4EAA25?style=flat-square&logo=gnubash&logoColor=white)
![Status](https://img.shields.io/badge/Status-Concluído-success?style=flat-square)

---

## O que foi feito

Um site estático foi hospedado no **Amazon S3** com acesso público, usando apenas a **AWS CLI** a partir de uma instância EC2 — sem interface gráfica, sem atalhos.

O projeto cobre criação de bucket, gerenciamento de permissões com IAM, configuração de hospedagem estática e automação do deploy com script Bash.

---

## Arquitetura

![architecture](./diagrams/architecture-diagram.png)

> Fluxo completo: EC2 via Session Manager → AWS CLI → upload para bucket S3 → site acessível publicamente via endpoint S3.

---

## Tecnologias

| Serviço / Ferramenta      | Uso no projeto                                              |
| ------------------------- | ----------------------------------------------------------- |
| **Amazon S3**             | Hospedagem do site estático                                 |
| **AWS CLI**               | Toda a infraestrutura como código                           |
| **IAM**                   | Criação de usuário e anexo de política `AmazonS3FullAccess` |
| **EC2 + Session Manager** | Terminal remoto sem necessidade de SSH key                  |
| **Bash**                  | Script de deploy automatizado                               |

---

## Passo a passo técnico

### 1. Configuração da AWS CLI

```bash
aws configure
# Região: us-west-2 | Formato: json
```

### 2. Criação do bucket S3

```bash
aws s3api create-bucket \
  --bucket luis-alexandre-cafe-2026 \
  --region us-west-2 \
  --create-bucket-configuration LocationConstraint=us-west-2
```

### 3. Criação de usuário IAM com acesso ao S3

```bash
aws iam create-user --user-name awsS3user

aws iam attach-user-policy \
  --policy-arn arn:aws:iam::aws:policy/AmazonS3FullAccess \
  --user-name awsS3user
```

### 4. Ativação de hospedagem estática

```bash
aws s3 website s3://luis-alexandre-cafe-2026/ --index-document index.html
```

### 5. Upload dos arquivos com permissão pública

```bash
aws s3 cp /home/ec2-user/sysops-activity-files/static-website/ \
  s3://luis-alexandre-cafe-2026/ \
  --recursive \
  --acl public-read
```

### 6. Script de deploy automatizado

```bash
#!/bin/bash
aws s3 sync /home/ec2-user/sysops-activity-files/static-website/ \
  s3://luis-alexandre-cafe-2026/ \
  --acl public-read
```

> O `aws s3 sync` substitui o `aws s3 cp` — envia **somente os arquivos modificados**, tornando o deploy mais rápido e econômico.

---

## Estrutura do repositório

```
aws-s3-static-website-deployment/
│
├── scripts/
│   └── update-website.sh       # script de deploy
│
├── screenshots/
│   ├── s3-bucket-created.png
│   ├── static-hosting-enabled.png
│   ├── cli-upload.png
│   ├── website-live.png
│   └── architecture-diagram.png
│
└── README.md
```

---

## Resultado

Site publicado e acessível publicamente:

```
http://luis-alexandre-cafe-2026.s3-website-us-west-2.amazonaws.com
```

---

## Screenshots

### Bucket criado no S3

![bucket](./screenshots/s3-bucket-created.png)

> Bucket `luis-alexandre-cafe-2026` criado via CLI na região us-west-2 (Oregon).

---

### Hospedagem estática ativada

![hosting](./screenshots/static-hosting-enabled.png)

> Painel do S3 confirmando a hospedagem estática habilitada e o endpoint público gerado.

---

### Upload via CLI em execução

![cli](./screenshots/cli-upload.png)

> Terminal SSH no Session Manager exibindo o upload recursivo dos arquivos do site para o bucket S3.

---

### Site no ar

![site](./screenshots/website-live.png)

> Página do Café & Bakery acessível publicamente via endpoint S3.

---

## O que aprendi na prática

- Operar a AWS sem depender do console gráfico — só CLI
- Criar e gerenciar usuários IAM com políticas específicas por serviço
- Configurar hospedagem estática no S3 e entender o fluxo de permissões públicas (ACL)
- Automatizar deploys com Bash e entender a diferença real entre `cp` e `sync`
- Usar o Session Manager para acessar EC2 sem chave SSH

---

## Autor

**Luís Fernando Alexandre dos Santos**  
Desenvolvedor | Estudante de Cloud Computing

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Conectar-0A66C2?style=flat-square&logo=linkedin)](https://linkedin.com/in/luisfernando-eng)
[![GitHub](https://img.shields.io/badge/GitHub-Seguir-181717?style=flat-square&logo=github)](https://github.com/luisFernandoJv)

---

> _Projeto desenvolvido como parte de um laboratório prático AWS SysOps, simulando um pipeline real de deploy em nuvem._
