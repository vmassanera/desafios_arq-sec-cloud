# Desafio de Avaliação de Arquitetura – Segurança em AWS

## Contexto

Você recebeu um ambiente AWS que atende uma aplicação web em produção. O desenho atual **possui vulnerabilidades e más práticas** propositalmente inseridas para avaliação.

O objetivo do desafio é **identificar riscos**, **propor um redesenho seguro** e **justificar suas decisões** de forma clara, objetiva e alinhada às boas práticas.

> **Observação:** este desafio avalia **raciocínio arquitetural de segurança** (camadas, limites de confiança, identidade, dados, rede e operação), não implementação de código.

---

## Cenário Atual (As‑Is – com vulnerabilidades)

- **Tráfego de aplicação em HTTP/80** (sem TLS) do público para o **ALB**, e do ALB para **EC2**.
- **Duas instâncias EC2** (Web-1 e Web-2) em **subnets públicas**, aceitando **HTTP/80**.
- **SSH/22 exposto à Internet (0.0.0.0/0)** para administração.
- **RDS publicamente acessível** com porta **3306** exposta.
- **Backup de dados pessoais via FTP/21** para serviço externo (sem criptografia na transferência).
- **Administração da AWS por usuários IAM locais**, sem **SSO/MFA** com o **Entra ID**.
- **Ausência de segmentação adequada**: web tier todo em subnets públicas e banco fora de rede privada.

> Você receberá (ou já recebeu) um **arquivo `draw.io` (diagrams.net)** com o diagrama **As‑Is** contendo essas vulnerabilidades.

---

## Tarefas

### 1) Análise de Risco
- Identifique e **classifique os riscos** do cenário (probabilidade × impacto).
- Explique **por que** cada item é um problema no contexto de segurança e conformidade.
- (Opcional) Relacione controles a frameworks de referência que você utiliza em sua prática profissional (NIST CSF/800‑53, CIS AWS Foundations, OWASP ASVS/Top‑10, PCI DSS etc.).

### 2) Redesenho Seguro (To‑Be)
- Produza um **novo diagrama** (draw.io) com a **arquitetura corrigida**, cobrindo no mínimo:
  - **Tráfego TLS (HTTPS/443)** no **ALB** (ACM) e **redirecionamento 80 → 443**.
  - **Remoção de SSH/22 exposto**; adote **AWS Systems Manager Session Manager**  
    *(alternativa aceitável: bastion host com acesso just‑in‑time e origem restrita)*.
  - **RDS em subnets privadas**, **Publicly Accessible = false**, e **Security Group** permitindo apenas do **SG da aplicação**.
  - **Backup seguro**: substituir **FTP** por **S3**/**AWS Backup**, com **SSE (idealmente KMS)** e tráfego privado via **VPC Endpoint/PrivateLink**, **bucket policy** negando transporte inseguro.
  - **Identidade e Acesso**: substituir usuários IAM locais por **SSO (Entra ID ↔ AWS IAM Identity Center)**, **MFA** e **princípio do menor privilégio** (roles).
  - **Observabilidade/segurança gerenciada**: **CloudTrail**, **GuardDuty**, **Security Hub**, **Config** (no mínimo habilitados, mesmo que em alto nível).
  - **Segmentação de rede**: **web tier** em **subnets privadas** sem IP público (ALB na borda), **NAT** somente se necessário.

> **Não é necessário** incluir todos os serviços da AWS; foque no **essencial** para mitigar os riscos identificados.

### 3) Justificativas Técnicas
- Para cada mudança do To‑Be, explique:
  - **Qual risco** é mitigado,
  - **Qual controle/serviço** foi adotado,
  - **Benefícios** e **trade‑offs** (custo, complexidade, operação).

### 4) Roadmap de Implantação
- Proponha um **plano em fases**:
  - **Quick wins (até 30 dias)** – ex.: TLS no ALB, bloquear SSH público, desabilitar RDS público.
  - **60–90 dias** – ex.: integração SSO, backup imutável, automações.
  - **>90 dias** – ex.: multi‑conta/landing zone, centralização de logs, hardening avançado.
- Inclua um **plano de rollback** de alto nível.

---

## Entregáveis

1. **Diagrama As‑Is** (arquivo `draw.io`) – sem necessidade de alterar.
2. **Diagrama To‑Be** (arquivo `draw.io`) – arquitetura proposta.
3. **Documento curto (3–5 páginas) ou README** com:
   - Lista de **riscos** e **classificação**,
   - **Decisões de arquitetura** (o que mudou e por quê),
   - **Controles/serviços** adotados,
   - **Roadmap** (fases, quick wins, riscos residuais).
4. (Opcional) **Tabela de mapeamento** risco → controle (pode ser em Markdown).

> Se preferir, você pode **comentar diretamente no diagrama** (legendas/notes) e manter o texto bem objetivo no README.

---

## Critérios de Avaliação

### 1) Cobertura Técnica (30%)
- Aborda **rede, identidade, dados, aplicação e operação**.
- Retira **serviços expostos** desnecessariamente e aplica **TLS**.
- RDS privado, backup seguro, IAM com roles/SSO.

### 2) Boas Práticas de Segurança (25%)
- **Least privilege**, **MFA/SSO**, **TLS em trânsito**, **criptografia em repouso**,
- Logs/telemetria básicos habilitados,
- Redução de **superfície de ataque**.

### 3) Clareza Arquitetural (20%)
- Diagrama **legível**, com **limites de confiança** e **fluxos** claros,
- Nomenclatura, camadas e **Security Groups** bem representados.

### 4) Risco & Priorização (15%)
- Classificação coerente de riscos,
- **Quick wins** realistas e **roadmap** consistente.

### 5) Praticidade (10%)
- Propostas **viáveis** (custo/complexidade),
- Considerações operacionais (monitoramento, rotação de segredos, etc.).

---

## Orientações e Expectativas

- **Não é necessário** escrever código IaC; foco é **arquitetura e segurança**.
- Se alguma suposição for necessária (ex.: domínio, certificados), **documente**.
- Seja **objetivo**: diagramas e texto sucinto são suficientes.
- **Formato sugerido**:
  - `arquitetura-as-is.drawio`
  - `arquitetura-to-be.drawio`
  - `README.md` (ou `arquitetura.md`)

---

## Dicas (opcionais)

- ALB **HTTPS/443** com **ACM**; **redirect 80→443**.
- **AWS WAF** no ALB (regras gerenciadas) para camada 7.
- **Session Manager** (SSM) substitui SSH exposto; logs no CloudWatch/S3.
- **S3 (backup)** com **SSE‑KMS**, **versioning** e **bucket policy** negando sem TLS.
- **VPC Endpoints** para S3/logs; **NAT** apenas se necessário.
- **Secrets Manager** p/ credenciais (rotacionáveis).
- **CloudTrail/GuardDuty/Security Hub/Config** habilitados no mínimo.

---

## Como Entregar

- Envie um **ZIP** ou **link de repositório** contendo os entregáveis.
- Inclua no README:
  - **Resumo executivo** (3–5 parágrafos),
  - **Assunções**,
  - **Riscos residuais**.

---

## Tempo Estimado

- **2 a 4 horas**. Buscamos **clareza e consistência**, não exaustão.

---

### Pergunta-Chave (para reflexão)

> Se você fosse o responsável por aprovar essa arquitetura para **produção**, **o que precisaria mudar antes do go‑live** e **por quê**?

Boa avaliação!
