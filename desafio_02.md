# Desafio 02 – Infraestrutura como Código (IaC) em AWS

## Contexto
Neste desafio, o objetivo é avaliar a capacidade do candidato de **provisionar um ambiente AWS seguro**, utilizando **Infraestrutura como Código (IaC)**, respeitando **boas práticas de segurança**, **organização de código** e **uso consciente de custos**.

---

## Objetivo
Criar, utilizando **IaC (Terraform preferencialmente)**, um ambiente AWS contendo:

- ✅ Um **Servidor Web**
- ✅ Um **Banco de Dados RDS** em **rede privada**
- ✅ **Acesso administrativo (SSH / RDP) restrito**
- ✅ **Backup dos arquivos de configuração** do servidor web em um **bucket S3**
- ✅ Código versionado em **Git**
- ✅ Documentação clara (README)

Sempre que possível, priorize recursos compatíveis com o **AWS Free Tier**, evitando a geração de custos na sua conta AWS.

---

## Requisitos Técnicos Obrigatórios

### 1️⃣ Rede (VPC)
- Criar uma **VPC dedicada**
- Criar ao menos:
  - **1 Subnet Pública** (Bastion Host)
  - **1 Subnet Privada** (Servidor Web + RDS)
- Uso de **uma única AZ** é aceitável
- **Security Groups** restritivos (menor privilégio)

### 2️⃣ Servidor Web (EC2)
- Tipo **Free Tier** (`t2.micro` ou `t3.micro`)
- Em **subnet privada**, **sem IP público**
- Instalar **Apache ou NGINX**
- Página simples (ex.: `Hello World`)

### 3️⃣ Bastion Host (Acesso Administrativo)
- Tipo **Free Tier** em **subnet pública**
- **SSH (22)** apenas do **seu IP** (ou variável)
- **SSH ao Web** somente via Bastion
- ❌ Sem SSH direto da Internet para o Web

### 4️⃣ Banco de Dados (RDS)
- Engine **Free Tier** 
- **Subnet privada**, `Publicly Accessible = false`
- SG do RDS permitindo acesso **somente** do SG do Web

### 5️⃣ Backup dos Arquivos de Configuração
- **Bucket S3 privado** 
- **Criptografia em repouso** (SSE‑S3 ou SSE‑KMS)
- **Script** no Web para enviar configs (ex.: `/etc/nginx` ou `/etc/apache2`) ao S3
- Execução pode ser **manual** ou via **cron** (automatizado)

---

## **não obrigatórios*, mas fortemente considerados na avaliação*

- **IAM Role** para EC2 (evitar credenciais estáticas)
- **Variáveis Terraform** para CIDR, IP de SSH, nomes de recursos
- Organização em `main.tf`, `variables.tf`, `outputs.tf`
- **Outputs úteis** (endpoint do RDS, IP do Bastion)
- **Backup automatizado** com `cron`
- **SSE‑KMS** no S3 (em vez de SSE‑S3)

---

## ⭐ Desejável – Pipelines de **CI/CD**
*(integração contínua e entrega/implantação contínua — OPCIONAL)*

Você pode escolher **Jenkins** **ou** **GitHub Actions** (ou outro de sua preferência) para implementar um pipeline simples de **CI** (validação) e, opcionalmente, **CD** (deploy controlado). Exemplos do que valorizamos:

### CI (Integração Contínua)
- **Qualidade e conformidade**:
  - `terraform fmt -check` e `terraform validate`
  - `terraform plan` em Pull Requests
- **Segurança do código** (ferramentas à sua escolha):
  - **SAST** (ex.: `semgrep`, `bandit`, etc.)
  - **Secret scanning** (ex.: `gitleaks`, `trufflehog`)
  - **IaC scanning** (ex.: `tfsec`, `checkov`)
- **Pre-commit hooks** (opcional): enforce de formatação/validadores locais

### CD (Entrega/Implantação Contínua) — **opcional**
- Execução de `terraform apply` **manual** (gate) em branch principal
- Uso de **assume role com OIDC** (no GitHub Actions) para evitar chaves estáticas
- Proteções mínimas: aprovação manual, ambiente protegido

> **Observação:** Mantenha o pipeline simples; o objetivo é demonstrar **higiene de código**, **checagens automáticas** e **boas práticas de segurança**.

---

## Itens Bônus (Opcional)
- **AWS Systems Manager Session Manager** (em vez de SSH)
- **VPC Endpoint** para acesso privado ao S3 (elimina saída pública no backup)
- Pequeno **diagrama** da arquitetura
- Explicação de **evolução para produção** (alto nível)

---

## O que NÃO é exigido
- HTTPS/ACM, Load Balancer, Auto Scaling, Multi‑AZ
- Pipelines complexos
- Ambientes multi‑account

---

## Entregáveis
- Repositório Git com:
  - Código IaC
  - `README.md` com:
    - Pré‑requisitos
    - Como **criar** e **destruir** o ambiente
    - Como **acessar** (Bastion → Web)
    - Como **executar o backup**
    - **Decisões de segurança** e **Assunções**
  - *(Desejável)* Arquivos de **pipeline** (`Jenkinsfile` ou `.github/workflows/*.yml`)

---

## Observação Final
Caso algum requisito não seja implementado, explique no README **como faria** e **por quê**.  
A clareza do raciocínio e a capacidade de justificar decisões técnicas são tão importantes quanto a implementação.
