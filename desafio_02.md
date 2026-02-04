# Desafio Técnico – Júnior
## AWS + Infraestrutura como Código (IaC – Free Tier)

### Contexto

Você recebeu a missão de criar um **ambiente simples e seguro na AWS**, utilizando **Infraestrutura como Código (IaC)** e **versionamento em Git**.

O objetivo deste desafio é avaliar **fundamentos de Cloud e Segurança**, não soluções complexas ou ambientes de produção.  
Por isso, o cenário foi pensado para funcionar **dentro do AWS Free Tier**, evitando custos.

---

## Objetivo

Provisionar, usando **IaC (Terraform ou CloudFormation)**, um ambiente AWS contendo:

- ✅ Um **Servidor Web (EC2)**
- ✅ Um **Banco de Dados RDS** em **rede privada**
- ✅ **Acesso administrativo via Bastion Host**
- ✅ **Backup dos arquivos de configuração** do servidor Web em um **bucket S3 privado**
- ✅ Código versionado em **Git**
- ✅ Documentação clara (README)

---

## Requisitos Técnicos

### 1️⃣ Rede (VPC)

- Criar **1 VPC**
- Criar pelo menos:
  - **1 Subnet Pública**
    - Bastion Host
  - **1 Subnet Privada**
    - Servidor Web
    - RDS
- Pode usar **uma única AZ** para simplificar

---

### 2️⃣ Servidor Web (EC2)

- Tipo **Free Tier** (`t2.micro` ou `t3.micro`)
- Deve estar em **subnet privada**
- **Não deve ter IP público**
- Sistema operacional de sua escolha (Amazon Linux / Ubuntu)
- Instalar **Apache ou NGINX**
- Publicar uma página simples

---

### 3️⃣ Bastion Host (Acesso Administrativo)

- Tipo **Free Tier**
- Deve ficar em **subnet pública**
- Acesso **SSH (porta 22)** permitido:
- Apenas a partir do **seu IP** (ou variável configurável `my_ip`)
- O acesso SSH ao **Servidor Web deve ser feito somente via Bastion**
- ❌ **Não é permitido SSH direto da Internet para o Servidor Web**

---

### 4️⃣ Banco de Dados (RDS)

- Engine compatível com **Free Tier** (MySQL ou PostgreSQL)
- Deve estar em **subnet privada**
- **Publicly Accessible = false**
- Acesso permitido **somente** a partir do **Security Group do Servidor Web**

> Não é necessário criar aplicação ou estrutura de dados no banco.  
> Apenas o provisionamento correto do RDS é suficiente.

---

### 5️⃣ Backup dos Arquivos de Configuração (S3)

- Criar um **Bucket S3**
- Configurações obrigatórias:
- ✅ **Block Public Access habilitado**
- ✅ **Criptografia em repouso** (SSE‑S3 é suficiente)
- Criar um **script simples no Servidor Web** que:
- Copie ou compacte arquivos de configuração do Web Server  
  (ex.: `/etc/nginx` ou `/etc/apache2`)
- Envie esses arquivos para o **bucket S3**
- O backup pode ser:
- Manual (execução única do script), **ou**
- Automatizado (via `cron`) – opcional

---

## Ferramentas

- ✅ **IaC**: Terraform (preferencial) ou CloudFormation
- ✅ **AWS CLI**
- ✅ **Git** para versionamento do código

---

## O que **NÃO** é exigido

Para manter o desafio adequado ao nível Júnior, **não é necessário**:

- Certificado SSL (HTTPS / ACM)
- Load Balancer
- Auto Scaling
- Multi‑AZ
- KMS customizado
- CI/CD pipelines
- Remote state ou módulos avançados de Terraform

> Caso queira, você pode **descrever no README** como faria essas melhorias no futuro.

---

## Entregáveis

Um repositório Git contendo:

- Código IaC
- Arquivo `README.md` com:
- Pré‑requisitos
- Passo a passo para:
  - Criar o ambiente
  - Acessar o Bastion
  - Acessar o Servidor Web via Bastion
  - Executar o script de backup
  - Destruir o ambiente
- Breve explicação das decisões de segurança adotadas

---

## Critérios de Avaliação

### 🔐 Segurança (40%)
- Separação correta entre subnet pública e privada
- Servidor Web e RDS sem exposição direta à Internet
- SSH apenas via Bastion Host
- Bucket S3 privado

### ☁️ Infraestrutura como Código (30%)
- Código organizado e legível
- Uso de variáveis
- Estrutura clara

### 📄 Documentação (20%)
- README claro e objetivo
- Passos reproduzíveis

### ⭐ Bônus (10%) – Opcional
- Backup automatizado com `cron`
- Uso de variáveis para IP e nomes
- Comentários explicativos no código

---

## Tempo Sugerido

- ⏱️ **2 a 4 horas**
- Não é necessário criar um ambiente perfeito — foque em **clareza e fundamentos**

---

## Observação Importante

> Caso algum requisito não seja implementado, explique no README **como você faria** e **por quê**.

O mais importante será avaliar seu **raciocínio**, organização e entendimento dos conceitos básicos de Cloud e Segurança.

---

## Destruição do Ambiente

Ao final, garanta que seja possível executar:

```bash
terraform destroy

---

## ✅ Como usar
1. Crie um repositório no GitHub.
2. Crie um arquivo chamado **`README.md`**.
3. Cole **exatamente** o conteúdo acima.
4. (Opcional) Adicione o diagrama draw.io como referência.

Se quiser, posso:
- ✅ ajustar o texto para inglês
- ✅ criar uma versão **Pleno** a partir desse mesmo desafio
- ✅ alinhar o README com o **starter Terraform** que eu gerei antes
- ✅ criar uma **rubrica de avaliação interna** para você usar na entrevista

Você agora tem um desafio **bem estruturado, justo e profissional para nível Júnior** 👌
  

- 
