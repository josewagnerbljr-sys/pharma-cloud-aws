# 🏥 Pharma Cloud AWS — Plataforma Virtual de Farmácia na AWS

> Projeto de portfólio inspirado no desafio da trilha AWS da [Digital Innovation One](https://www.dio.me/) — evoluído além do escopo original do curso e integrado ao [OpenOps](https://github.com/josewagnerbljr-sys/openops), plataforma open source de gestão empresarial já em desenvolvimento por este autor.

[![AWS](https://img.shields.io/badge/AWS-S3%20%7C%20Textract%20%7C%20Lambda-orange)](#-serviços-aws-escolhidos)
[![OpenOps](https://img.shields.io/badge/base%20operacional-OpenOps-blue)](https://github.com/josewagnerbljr-sys/openops)
[![Status](https://img.shields.io/badge/status-em%20desenvolvimento-yellow)]()
[![License](https://img.shields.io/badge/license-MIT-green)]()

---

## 📌 Sobre o projeto

Este repositório documenta o desenho de uma **plataforma virtual para uma farmácia fictícia**, usando a infraestrutura da AWS como camada de nuvem e o **OpenOps** como camada operacional (estoque, produtos, clientes, automação por eventos).

O desafio original pedia a escolha de **3 serviços AWS com foco em redução de custo imediato** — o relatório completo, no formato exigido, está em [`docs/relatorio-implementacao-aws.md`](docs/relatorio-implementacao-aws.md). O modelo original fornecido pelo instrutor está preservado, sem alterações de conteúdo, em [`docs/modelo-relatorio-original.md`](docs/modelo-relatorio-original.md).

Este README vai além do entregável mínimo: aqui está a arquitetura completa que este projeto pode assumir quando deixa de ser um exercício de curso e passa a ser um produto real.

---

## ☁️ Serviços AWS escolhidos (entregável oficial do desafio)

| Serviço | Papel | Ganho de custo |
|---|---|---|
| **Amazon S3** | Armazenamento de notas fiscais, requisições de compra e relatórios, com regra de ciclo de vida para arquivar dados antigos em **S3 Glacier** | Armazenamento a fração do custo de um servidor de arquivos próprio |
| **AWS Textract** | OCR automático das notas fiscais e requisições de compra no momento da entrada | Elimina digitação manual — reduz diretamente horas de equipe administrativa |
| **AWS Lambda** | Orquestra o pipeline (S3 → Textract → banco de dados) e dispara alertas de vencimento/giro de estoque | Modelo *pay-per-execution* — sem servidor ligado 24h para uma operação de pequeno/médio porte |

Detalhamento completo, com justificativa técnica e financeira de cada etapa, está no relatório oficial (`docs/relatorio-implementacao-aws.md`).

---

## 🧩 Camada complementar: OpenOps (redução de custo por operação, não só por infraestrutura)

A escolha dos 3 serviços acima resolve o lado **"nuvem"** do custo. Mas a maior alavanca de redução de custo em uma farmácia não é só armazenamento barato — é **carga horária de equipe** gasta em tarefas manuais e repetitivas (conferência de estoque, controle de vencimento, lançamento de entrada/saída).

É exatamente isso que os módulos já implementados do [OpenOps](https://github.com/josewagnerbljr-sys/openops) resolvem hoje:

- **Módulo Produtos** — API REST completa (FastAPI) para cadastro de itens, adaptável para incluir lote/validade farmacêutica.
- **Módulo Estoque (Inventory)** — já integrado ao EventBus com eventos `stock.changed` e `stock.low`, base direta para controle de rodízio e alerta de vencimento sem intervenção manual.
- **Módulo Clientes** — base para o controle de recebíveis e requisições de compra descrito na visão do projeto.
- **Segurança de nível produção** — JWT + RBAC (Argon2id), observabilidade (OpenTelemetry/Prometheus), containerização Docker, SBOM assinado (Sigstore/cosign) — infraestrutura que uma farmácia real precisaria e que aqui já está pronta e testada, não é promessa.

**Próxima fase deste repositório:** portar o módulo Estoque do OpenOps para um vertical `Pharmacy`, adicionando campo de validade/lote e a regra de "produtos de alto giro antecedendo a compra" (busca de mercado por período, citada na visão original deste projeto).

---

## 🗺️ Arquitetura completa (visão de produto, além do mínimo do desafio)

```
 Nota Fiscal / Requisição (PDF/imagem)
            │
            ▼
       [Amazon S3]  ── arquivamento (Glacier) ──►  histórico fiscal
            │
            ▼
     [AWS Textract]  ── extração automática de dados ──►
            │
            ▼
      [AWS Lambda]  ── valida e grava ──►
            │
            ▼
   [OpenOps · Módulo Estoque/Produtos/Clientes]
            │
    ┌───────┴────────┐
    ▼                ▼
 EventBus        Regras de negócio
 (stock.low,     (rodízio de vencimento,
  stock.changed)  giro de produto, recebíveis)
```

**Roadmap de arquitetura (fora do escopo dos 3 serviços oficiais, mas planejado):**
- **Amazon RDS/Aurora** — banco relacional para os dados estruturados do OpenOps em produção
- **AWS KMS (AES-256)** — criptografia de dados sensíveis em repouso, alinhado à LGPD
- **Amazon Comprehend / SageMaker** — camada de IA interna para validação de dados extraídos e classificação de risco de vencimento

---

## 🚀 Rodando o demo via GitHub Actions

Este repositório inclui um workflow (`.github/workflows/demo-api.yml`) que sobe a API do OpenOps em um runner do GitHub e executa um *smoke test* dos endpoints de Produtos/Estoque — uma forma de demonstrar o funcionamento do sistema sem custo de infraestrutura real, direto pela aba **Actions** deste repositório.

---

## 📁 Estrutura do repositório

```
pharma-cloud-aws/
├── README.md
├── docs/
│   ├── relatorio-implementacao-aws.md
│   └── modelo-relatorio-original.md
└── .github/
    └── workflows/
        └── demo-api.yml
```

---

## 🙏 Créditos

Projeto inspirado no desafio de projeto da trilha AWS da [Digital Innovation One](https://www.dio.me/). Este repositório é uma evolução autoral, não um fork direto de repositório de instrutor — construído como peça de portfólio próprio, com integração ao projeto open source [OpenOps](https://github.com/josewagnerbljr-sys/openops), do mesmo autor.

---

## 📇 Contato

**José Wagner Blanco Júnior** — Principal AI Systems Architect | Executive Tech Consultant

- 💼 LinkedIn: [linkedin.com/in/blancoconsultoria](https://linkedin.com/in/blancoconsultoria)
- 💻 GitHub: [github.com/josewagnerbljr-sys](https://github.com/josewagnerbljr-sys)
- 📧 E-mail: consultoriablanco8@gmail.com
- 🔗 Todos os links: [beacons.ai/blanco.sys](https://beacons.ai/blanco.sys)
