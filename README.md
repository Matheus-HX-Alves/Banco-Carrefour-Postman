# 🧪 Testes Automatizados — API Serverest

Projeto de testes automatizados para a API RESTful [Serverest](https://serverest.dev/), desenvolvido com **Postman** e executado via **Newman** com geração de relatório HTML interativo. A pipeline de CI/CD é gerenciada pelo **GitHub Actions**, garantindo a execução automática dos testes a cada alteração no repositório.

---

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Estrutura do Repositório](#estrutura-do-repositório)
- [Pré-requisitos](#pré-requisitos)
- [Como Rodar os Testes Localmente](#como-rodar-os-testes-localmente)
- [Casos de Teste Cobertos](#casos-de-teste-cobertos)
- [CI/CD com GitHub Actions](#cicd-com-github-actions)
- [Relatório de Testes](#relatório-de-testes)

---

## Sobre o Projeto

Este projeto implementa um plano de testes automatizados sobre o endpoint `/usuarios` da API Serverest, cobrindo o fluxo completo de **CRUD** (Create, Read, Update, Delete). Os testes validam status codes, tempo de resposta e a estrutura do corpo das respostas.

A execução é feita via **Newman** (CLI do Postman) com o reporter **htmlextra**, que gera um relatório visual detalhado ao final de cada execução.

---

## Tecnologias Utilizadas

| Ferramenta | Versão | Finalidade |
|---|---|---|
| [Postman](https://www.postman.com/) | — | Criação e organização da collection de testes |
| [Newman](https://www.npmjs.com/package/newman) | latest | Execução da collection via linha de comando |
| [newman-reporter-htmlextra](https://www.npmjs.com/package/newman-reporter-htmlextra) | latest | Geração do relatório HTML interativo |
| [GitHub Actions](https://github.com/features/actions) | — | Automação e CI/CD da execução dos testes |
| [Serverest API](https://serverest.dev/) | — | API alvo dos testes |

---

## Estrutura do Repositório

```
📦 Banco-Carrefour-Postman
├── 📄 Plano De Requisições Serverest.collection.json  # Collection Postman com todos os testes
├── 📄 Serverest Env.json                              # Arquivo de ambiente (variáveis de ambiente)
├── 📄 README.md                                       # Documentação do projeto
└── 📁 .github/
    └── 📁 workflows/
        └── 📄 newman.yml                              # Pipeline de CI/CD GitHub Actions
```

---

## Pré-requisitos

Antes de rodar os testes localmente, certifique-se de ter instalado:

- [Node.js](https://nodejs.org/) v16 ou superior
- npm (incluído com o Node.js)

---

## Como Rodar os Testes Localmente

**1. Clone o repositório:**

```bash
git clone https://github.com/Matheus-HX-Alves/Banco-Carrefour-Postman.git
cd Banco-Carrefour-Postman
```

**2. Instale o Newman e o reporter htmlextra:**

```bash
npm install -g newman
npm install -g newman-reporter-htmlextra
```

**3. Execute os testes:**

```bash
newman run "Plano De Requisições Serverest.collection.json" \
  -e "Serverest Env.json" \
  -r htmlextra,cli \
  --reporter-htmlextra-export ./newman-report/relatorio.html \
  --reporter-htmlextra-displayProgressBar \
  --reporter-htmlextra-title "Relatório De Testes Api Serverest"
```

**4. Acesse o relatório:**

Após a execução, abra o arquivo gerado no navegador:

```
./newman-report/relatorio.html
```

---

## Casos de Teste Cobertos

Os testes cobrem o fluxo completo de gerenciamento de usuários na rota `/usuarios`, seguindo a ordem de execução abaixo:

### 1. Listar Usuários Cadastrados — `GET /usuarios`

| # | Caso de Teste | Critério de Aceitação |
|---|---|---|
| 1.1 | Validar código de status da resposta | HTTP `200 OK` |
| 1.2 | Validar tempo de resposta | Abaixo de `1000ms` |
| 1.3 | Validar estrutura do corpo da resposta | JSON contendo `quantidade` (integer) e array `usuarios` com os campos `nome`, `email`, `password`, `administrador` e `_id` |

---

### 2. Cadastrar Novo Usuário — `POST /usuarios`

| # | Caso de Teste | Critério de Aceitação |
|---|---|---|
| 2.1 | Validar código de status da resposta | HTTP `201 Created` |
| 2.2 | Capturar e armazenar o ID do usuário criado | Variável de ambiente `userIdCreated` populada com o `_id` retornado |

**Payload enviado:**
```json
{
  "nome": "Teste De Automatização Usuário",
  "email": "Teste@teste.com.br",
  "password": "teste",
  "administrador": "true"
}
```

---

### 3. Listar Usuário pelo ID — `GET /usuarios/{{userIdCreated}}`

| # | Caso de Teste | Critério de Aceitação |
|---|---|---|
| 3.1 | Validar código de status da resposta | HTTP `200 OK` |

> O ID utilizado é o capturado dinamicamente no passo anterior, garantindo o isolamento e encadeamento dos testes.

---

### 4. Alterar Cadastro do Usuário — `PUT /usuarios/{{userIdCreated}}`

| # | Caso de Teste | Critério de Aceitação |
|---|---|---|
| 4.1 | Validar código de status da resposta | HTTP `200 OK` |

**Payload enviado:**
```json
{
  "nome": "Teste De Automatização Usuário Atualizado",
  "email": "TesteAtualizado@teste.com.br",
  "password": "teste",
  "administrador": "true"
}
```

---

### 5. Deletar Usuário Cadastrado — `DELETE /usuarios/{{userIdCreated}}`

| # | Caso de Teste | Critério de Aceitação |
|---|---|---|
| 5.1 | Validar código de status da resposta | HTTP `200 OK` |

> Este passo finaliza o fluxo garantindo que o usuário criado durante os testes seja removido, mantendo o ambiente limpo para execuções futuras.

---

## CI/CD com GitHub Actions

O workflow de CI/CD está configurado em `.github/workflows/newman.yml` e é disparado automaticamente nos seguintes eventos:

- **Push** para a branch `main`
- **Pull Request** para a branch `main`
- **Execução manual** pela aba *Actions* do repositório (`workflow_dispatch`)

**Etapas da pipeline:**

1. Checkout do código
2. Configuração do Node.js v20
3. Instalação do Newman e do reporter htmlextra
4. Execução da collection de testes
5. Upload do relatório HTML como artefato para download

---

## Relatório de Testes

Após cada execução pela pipeline, o relatório HTML gerado pelo **newman-reporter-htmlextra** fica disponível para download na aba **Actions** do GitHub:

1. Acesse a aba [Actions](https://github.com/Matheus-HX-Alves/Banco-Carrefour-Postman/actions)
2. Clique no workflow executado
3. Role até a seção **Artifacts**
4. Baixe o arquivo `relatorio-newman`

O relatório inclui: resumo de execução, resultado individual de cada teste, tempo de resposta, detalhes das requisições e respostas, e barra de progresso visual.
