# Desafio 02 – Infraestrutura como Código (IaC) em AWS

## Contexto

Neste desafio, você deverá **provisionar um ambiente AWS seguro e organizado**, utilizando **Infraestrutura como Código (IaC)**, com ênfase em **boas práticas de segurança**, **fundamentos de alta disponibilidade** e **automação**.

> **Objetivo do processo**: avaliar **planejamento, qualidade técnica e clareza**.  
> Não buscamos um ambiente “enterprise‑grade”, e sim **fundamentos sólidos**, **decisões justificadas** e **execução consistente**.

---

## Objetivo

Criar, com **IaC (preferencialmente Terraform)**, um ambiente contendo:

- ✅ **Servidor Web** em **subnet privada**, **sem IP público**  
- ✅ **RDS** em **subnet privada**  
- ✅ **Acesso administrativo via Bastion Host**  
- ✅ **Backup de arquivos de configuração** do Web Server em **S3**  
- ✅ **Código versionado em Git** + **README claro**  
- ✅ **Alta disponibilidade mínima** no web tier (**duas AZs + ALB + ASG**)  
- ✅ **Hardening** de SO e servidor web (NGINX ou Apache) **automatizado**  
- ✅ **Criptografia com KMS (rotação habilitada)** e **segredos no Secrets Manager**  
- ✅ **VPCE S3** e **bucket policy** restringindo acesso via **`aws:sourceVpce`**

> **Custos**: Sempre que possível, **priorize recursos compatíveis com o AWS Free Tier**, de forma a **minimizar ou evitar custos** durante a execução do desafio.

---

## Escopo Base (Obrigatório)

### 1) Rede (VPC)
- 1 **VPC** dedicada
- Subnets:
  - **Pública**: **Bastion Host**
  - **Privada**: **Servidor Web** e **RDS**
- **Security Groups** com **menor privilégio**

### 2) Bastion Host (pública)
- **SSH (22)** permitido **apenas** do **seu IP** (ou variável `my_ip`)
- SSH do Bastion → Web (privado)
- ❌ **Sem SSH direto** da Internet para o Web

### 3) Servidor Web (privado)
- Instância **sem IP público**
- **NGINX** ou **Apache** servindo **Hello World**
- Acesso HTTP interno apenas para teste (ex.: via Bastion usando `curl`)

### 4) Banco de Dados (RDS)
- MySQL/PostgreSQL
- **Publicly Accessible = false**
- SG do RDS **aceita somente** tráfego do **SG do Web**

### 5) Backup em S3 
- **Bucket S3** com:
  - **Block Public Access** habilitado
  - **Criptografia em repouso** (SSE‑S3 ou SSE‑KMS)
- **Script** no Web **(manual ou cron)** para enviar configs  
  (ex.: `/etc/nginx` ou `/etc/apache2`) para o bucket

---

## Requisitos Adicionais 

### 1) Alta disponibilidade mínima (web tier)
- **Duas AZs** (ex.: `us-east-1a` e `us-east-1b`)
- Duas **subnets privadas** (uma por AZ) para o web tier
- **Application Load Balancer (ALB)** público
- **Auto Scaling Group (ASG)** para o web (ex.: `min=1, desired=2, max=2`)
- **Health checks** no Target Group/ALB

### 2) Hardening automatizado
- **SO** (mínimo): desabilitar serviços desnecessários, permissões básicas, `auditd` (mínimo), `logrotate`, `umask` apropriado
- **Servidor Web**:  
  - NGINX/Apache com **config segura por padrão** (ex.: `server_tokens off`, headers como `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy` etc.)  
  - Se optar por HTTPS, pode apenas **documentar** como faria (ACM, TLS 1.2+)
- **Automação** via **`user_data`**, **Ansible chamado pelo `user_data`** ou **AWS SSM (State Manager/Document)**

### 3) Criptografia, rotação e segredos
- **KMS (CMK)** com **rotação automática habilitada**
- **S3 (backup)** com **SSE‑KMS** usando a CMK
- **Segredos do RDS** em **AWS Secrets Manager** (ou **SSM Parameter Store – SecureString**)  
  - *(Opcional)* rotação automatizada do secret

### 4) Tráfego privado e egress controlado
- **VPC Endpoint (S3)** (Gateway) para backups **sem egress público**
- **Bucket policy** restringindo a origem ao **VPCE** via `aws:sourceVpce`
- **NAT Gateway** **opcional** (custo!); considere **SSM Session Manager** para reduzir dependência de NAT

### 5) Observabilidade e postura (mínimo viável)
- **CloudWatch Logs**: enviar logs do webserver (NGINX/Apache) e do sistema
- **Alarmes**: CPU, StatusCheckFailed, HealthyHostCount do ALB/Target Group
- **CloudTrail** habilitado (escopo mínimo é suficiente para o desafio)

---

## ⭐ Desejável – **Pipelines de CI/CD** (opcional)

Você pode escolher **Jenkins** **ou** **GitHub Actions** (ou outro) para implementar:

### CI (Integração Contínua)
- `terraform fmt -check`, `terraform validate`, `terraform plan` em PR
- Varreduras de segurança (ferramentas **à sua escolha**):
  - IaC: `tfsec`, `checkov`
  - Segredos: `gitleaks`, `trufflehog`
- (Opcional) Pre‑commit hooks

### CD (Entrega/Implantação Contínua) – *opcional*
- `terraform apply` **manual** via **gate** (ex.: `workflow_dispatch` / aprovação)
- **Assume Role por OIDC** (no GitHub Actions) — evita chaves estáticas
- Branch protegida, ambientes de aprovação

> Objetivo: demonstrar **higiene de código**, **checagens automáticas** e **postura DevSecOps**.

---

## Reprodutibilidade e Documentação

Espera-se que o código entregue seja **reprodutível por qualquer pessoa com conhecimento básico em AWS**, sem depender de informações implícitas ou ajustes manuais não documentados.

A documentação deve permitir que **qualquer integrante da equipe** consiga:
- Entender a arquitetura proposta;
- Criar o ambiente a partir do código;
- Executar testes básicos;
- Destruir o ambiente ao final.

Esse requisito avalia a **clareza, organização e maturidade técnica** da solução.

---

## Entregáveis

- Repositório **Git** contendo:
  - Código **IaC**
  - `README.md` com:
    - Pré‑requisitos
    - Como **criar** e **destruir** o ambiente
    - Como **acessar** (Bastion → Web)
    - Como **executar o backup**
    - **Decisões de segurança**
    - **Assunções** (premissas adotadas)
    - **Instruções de reprodutibilidade** (passo a passo claro)
  - *(Se implementado)* arquivos de pipeline (`Jenkinsfile` ou `.github/workflows/*.yml`)
  - *(Se desejar)* diagrama simples (PNG e/ou `.drawio`)

---

## Restrições e Custos

- Utilize, sempre que possível, **recursos compatíveis com o AWS Free Tier**, a fim de **minimizar ou evitar custos**.
- **Web tier** com 2 instâncias pode passar do Free Tier dependendo do tempo de execução — você pode usar `min=1, desired=1, max=2` para demonstrar a arquitetura e **documentar** como ativaria `desired=2`.
- **RDS**: mantenha **single‑AZ** para conter custos (o objetivo de HA aqui é o **web tier**).
- Evite **NAT Gateway** se possível (custos). Prefira **SSM Session Manager** + **VPCE**.


## Assunções (template)

Inclua no README as premissas que você adotou, por exemplo:

```markdown
## Assunções
- O ambiente é de pequena escala e não possui requisitos regulatórios específicos.
- O uso restringe-se ao período do desafio (deverá ser destruído ao final).
- A região utilizada suporta Free Tier (ex.: us-east-1).
- O acesso SSH ao Bastion será feito a partir de um IP fixo informado.
