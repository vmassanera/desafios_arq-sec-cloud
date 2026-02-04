# Desafio de Avaliação de Arquitetura – Segurança em AWS

## Contexto

Você recebeu um ambiente AWS que atende uma aplicação web em produção. O desenho atual **possui vulnerabilidades e más práticas** propositalmente inseridas para avaliação.

O objetivo do desafio é **identificar riscos**, **propor um redesenho seguro** e **justificar suas decisões** de forma clara, objetiva e alinhada às boas práticas.

> **Observação:** este desafio avalia **raciocínio arquitetural de segurança** (camadas, limites de confiança, identidade, dados, rede e operação), não implementação de código.

---

## Cenário Atual 

- **Tráfego de aplicação em HTTP/80** do público para o **ALB**, e do ALB para **EC2**.
- **Duas instâncias EC2** (Web-1 e Web-2) em **subnets públicas**, aceitando **HTTP/80**.
- **SSH/RDP** exposto à Internet para administração.
- **RDS publicamente acessível** com porta exposta.
- **Backup de dados pessoais via FTP/21** para serviço externo.
- **Administração da AWS por usuários IAM locais**.
- **Ausência de segmentação adequada**: web tier todo em subnets públicas e banco fora de rede privada.

> Você receberá (ou já recebeu) um **arquivo `draw.io` (diagrams.net)** com o diagrama **As‑Is** contendo essas vulnerabilidades.

---

## Tarefas

### 1) Análise de Risco
- Identifique e **classifique os riscos** do cenário (probabilidade × impacto).
- Explique **por que** cada item é um problema no contexto de segurança e conformidade.
- (Opcional) Relacione controles a frameworks de referência que você utiliza em sua prática profissional (NIST CSF/800‑53, CIS AWS Foundations, OWASP ASVS/Top‑10, PCI DSS etc.).

### 2) Redesenho Seguro
- Produza um **novo diagrama** (draw.io) com a **arquitetura corrigida**.
  
> **Não é necessário** incluir todos os serviços da AWS; foque no **essencial** para mitigar os riscos identificados.

### 3) Justificativas Técnicas
- Para cada mudança do To‑Be, explique:
  - **Qual risco** é mitigado,
  - **Qual controle/serviço** foi adotado,
  - **Benefícios** e **trade‑offs** (custo, complexidade, operação).

### 4) Roadmap de Implantação
- Proponha um **plano em fases**:
  - **até 30 dias**
  - **60–90 dias**.
  - **>90 dias**.
- Inclua um **plano de rollback** de alto nível.

---

## Entregáveis

1. **Diagrama To‑Be** (arquivo `draw.io`) – arquitetura proposta.
2. **Documento README** com:
   - Lista de **riscos** e **classificação**,
   - **Decisões de arquitetura** (o que mudou e por quê),
   - **Controles/serviços** adotados,
   - **Roadmap** (fases, quick wins, riscos residuais).

> Se preferir, você pode **comentar diretamente no diagrama** (legendas/notes) e manter o texto bem objetivo no README.

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

### Pergunta-Chave (para reflexão)

> Se você fosse o responsável por aprovar essa arquitetura para **produção**, **o que precisaria mudar antes do go‑live** e **por quê**?

Boa avaliação!
