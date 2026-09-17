# RELATÓRIO DE IMPLEMENTAÇÃO DE SERVIÇOS AWS

**Data:** [data de início do projeto]
**Empresa:** Abstergo Industries (Farmácia Virtual)
**Responsável:** José Wagner Blanco Júnior

---

## Introdução

Este relatório apresenta o processo de implementação de serviços de nuvem na empresa **Abstergo Industries**, realizado por **José Wagner Blanco Júnior**. O objetivo do projeto foi eleger **3 serviços AWS** com foco em **redução de custos imediatos**, aplicados à operação de uma plataforma virtual de farmácia — cobrindo o fluxo de entrada de notas fiscais/requisições de compra, armazenamento de documentos e automação do processamento desses dados.

Além dos 3 serviços de nuvem, este relatório documenta uma **camada complementar de otimização operacional**, já validada em projeto próprio (OpenOps), que ataca a segunda maior fonte de custo de uma farmácia: horas de equipe gastas em tarefas manuais repetitivas.

---

## Descrição do Projeto

O projeto foi dividido em 3 etapas, cada uma correspondendo a um serviço AWS, encadeadas em um único pipeline de baixo custo.

### Etapa 1 — Armazenamento e Arquivamento de Documentos

- **Nome da ferramenta:** Amazon S3 (Simple Storage Service), com regra de ciclo de vida para **Amazon S3 Glacier**
- **Foco da ferramenta:** Armazenamento central e durável de notas fiscais, requisições de compra e relatórios gerenciais
- **Descrição de caso de uso:** Toda nota fiscal de entrada e requisição de compra é enviada para um bucket S3 no momento do recebimento. Documentos com mais de 90 dias são movidos automaticamente para o tier Glacier, reduzindo o custo de armazenamento de histórico fiscal em mais de 80% frente ao S3 padrão, sem exigir servidor de arquivos dedicado nem rotina manual de backup.

### Etapa 2 — Automação de Extração de Dados (OCR)

- **Nome da ferramenta:** AWS Textract
- **Foco da ferramenta:** Extração automática de texto e dados estruturados de notas fiscais e requisições de compra (PDF/imagem)
- **Descrição de caso de uso:** No momento em que o documento chega ao bucket S3 (Etapa 1), o Textract extrai automaticamente fornecedor, itens, quantidades, valores e datas de validade dos produtos farmacêuticos. Isso elimina a digitação manual desses dados, que hoje consome horas de equipe administrativa por semana — a mesma lógica de redução de carga horária aplicada, aqui, à entrada de dados.

### Etapa 3 — Orquestração e Automação de Regras de Negócio

- **Nome da ferramenta:** AWS Lambda
- **Foco da ferramenta:** Orquestração serverless do pipeline (S3 → Textract → banco de dados operacional) e dos alertas de negócio
- **Descrição de caso de uso:** Uma função Lambda é acionada automaticamente a cada novo documento processado pelo Textract. Ela valida os dados extraídos, grava no sistema operacional (ver camada complementar abaixo) e dispara alertas de vencimento próximo e de baixo giro de estoque. Por ser um modelo *pay-per-execution*, não há custo de servidor ocioso — item essencial para uma operação de pequeno/médio porte, que é justamente o cenário de redução de custo imediato pedido neste projeto.

---

## Camada complementar: Otimização Operacional (OpenOps)

Os 3 serviços acima resolvem o custo de **infraestrutura de nuvem**. A maior fonte de custo evitável em uma farmácia, no entanto, costuma estar na **carga horária de equipe** dedicada a controles manuais de estoque, entrada/saída e conferência de recebíveis.

Para essa frente, este projeto reaproveita módulos já implementados e testados do projeto open source **OpenOps** (autoria própria, disponível publicamente):

- **Módulo Estoque** — controle de entrada/saída com eventos automáticos (`stock.changed`, `stock.low`), base direta para rodízio de validade e alerta de produtos parados.
- **Módulo Produtos** e **Módulo Clientes** — cadastro estruturado de itens e recebíveis, eliminando planilhas paralelas.

Essa combinação — nuvem para armazenamento/OCR/orquestração, OpenOps para regra de negócio e automação de estoque — é o que efetivamente reduz custo de forma sustentada, e não apenas na conta da AWS.

---

## Conclusão

A implementação dos serviços na empresa **Abstergo Industries** tem como resultado esperado a **redução do custo de armazenamento de documentos fiscais em mais de 80%** (via ciclo de vida S3 → Glacier), a **eliminação da digitação manual de notas fiscais e requisições de compra** (via Textract) e a **eliminação de custo de infraestrutura ociosa** no processamento desses dados (via Lambda serverless), o que aumentará a eficiência e a produtividade da empresa. Recomenda-se a continuidade da utilização dos serviços implementados, a evolução do pipeline com uma camada de banco de dados relacional gerenciado (Amazon RDS/Aurora) e de criptografia em repouso (AWS KMS/AES-256) para conformidade com a LGPD, e a busca contínua por novas tecnologias — incluindo IA para validação de dados — que possam melhorar ainda mais os processos da empresa.

---

## Anexos

- Modelo de relatório original fornecido pelo instrutor: [`modelo-relatorio-original.md`](modelo-relatorio-original.md)
- Arquitetura completa e roadmap de produto: ver seção "Arquitetura completa" no [README](../README.md) do repositório
- Projeto complementar de referência (OpenOps): https://github.com/josewagnerbljr-sys/openops

**Assinatura do Responsável pelo Projeto:**

José Wagner Blanco Júnior
