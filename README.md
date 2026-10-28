# Projeto de Banco de Dados: Sistema de Oficina Mecânica 🛠️🚗

Este repositório contém o modelo conceitual de um banco de dados projetado para o controle e gerenciamento da execução de Ordens de Serviço (OS) em uma oficina mecânica. O projeto foi desenvolvido a partir de uma narrativa de regras de negócio, passando por refinamentos arquiteturais para suportar operações do mundo real, como histórico de preços, gestão de estoque e controle de equipes.

## 📖 Contexto e Regras de Negócio

O sistema foi modelado para atender ao seguinte fluxo operacional:
* Clientes levam veículos à oficina para consertos ou revisões.
* Os veículos são designados a equipes de mecânicos.
* A equipe identifica os serviços, consome peças do estoque e preenche uma Ordem de Serviço (OS) com datas e status.
* O valor da OS é calculado com base em catálogos de referência (peças e mão de obra).
* Um mesmo mecânico pode atuar em múltiplas equipes conforme a demanda.

## 🚀 Soluções Arquiteturais e Premissas Assumidas

Durante a modelagem, algumas premissas e expansões foram aplicadas ao escopo inicial para garantir um sistema escalável e normalizado, aproximando-o de um ERP (Enterprise Resource Planning) completo.

### 1. Padrão de Superclasse de Usuários (RBAC / Party-Role)
Adotou-se o modelo onde a entidade central `Usuario` concentra as informações de contato e credenciais de acesso (Endereço, Telefone, Email, Senha). As entidades `Cliente`, `Funcionario` (mecânicos) e `Fornecedor` são subclasses (relações 1:1 identificadoras) que herdam esses dados, garantindo que qualquer pessoa física ou jurídica que interaja com o sistema seja tratada de forma unificada.

### 2. Dupla Especialização de Clientes
A entidade `Cliente` atua como uma ponte herdando de `Usuario` e sofreu uma segunda especialização para separar rigorosamente Pessoas Físicas (`Cliente_PF`: CPF e Nome) de Pessoas Jurídicas (`Cliente_PJ`: CNPJ e Razão Social), eliminando valores nulos no banco de dados.

### 3. Histórico e Congelamento de Preços na OS (Regra Financeira)
Para atender à exigência de calcular a OS consultando o catálogo base sem corromper o histórico financeiro, foram criadas as tabelas associativas (N:M) `Ordem_de_Serviço_has_Produtos` e `Ordem_de_Serviço_has_Serviço`.
* Nessas tabelas de ligação, adicionamos atributos operacionais cruciais como `Quantidade`, `Valor_Cobrado` e `Valor_Mao_obra`.
* **Justificativa:** Se o preço de uma peça ou serviço aumentar no catálogo principal no ano seguinte, o valor gravado na OS antiga permanece intacto e auditável, refletindo exatamente o que o cliente pagou na época.

### 4. Flexibilidade de Alocação de Mecânicos
Foi identificada a necessidade de um mecânico (funcionário) pertencer a mais de uma equipe dependendo da demanda. A relação entre `Equipe` e `Funcionario` foi resolvida com a tabela associativa `Alocacao_Equipe`, que permite registrar a data de entrada e saída do mecânico em cada setor.

### 5. Cadeia de Suprimentos (Supply Chain)
Embora não estivesse explícito na narrativa inicial, um sistema de OS que consome peças exige o controle de sua origem e armazenamento. Foram incorporadas as entidades `Estoque` (para gerenciar almoxarifados) e `Fornecedor`, garantindo o ciclo de vida completo da peça na oficina.

## 🗂️ Estrutura de Entidades Principais

* **Usuario / Cliente / Funcionario / Fornecedor:** Gestão de identidades e papéis no sistema.
* **Veículo:** Cadastro da frota com vínculo de propriedade (1:N) com o Cliente.
* **Ordem_de_Serviço:** O coração da operação. Consolida Prazos, Status, Veículo e a Equipe responsável.
* **Produtos / Serviço:** Catálogos de referência com valores-base.
* **Equipe:** Agrupamentos lógicos de mecânicos que assumem as OSs.

## 🛠️ Ferramentas Utilizadas

* **MySQL Workbench:** Utilizado para a criação do Esquema Conceitual e Modelagem Relacional (DER).
