# Estrategia de Testes - API Serverest

## 1. Objetivo

Definir a estrategia de testes para a API Serverest, com foco na validacao dos fluxos de usuarios disponiveis na collection Postman do projeto.

O objetivo e garantir que os principais comportamentos da API estejam funcionando conforme esperado, cobrindo cadastro, consulta, autenticacao, alteracao e exclusao de usuarios, alem de validar respostas de erro, estrutura dos contratos e tempo de resposta das requisicoes.

## 2. Contexto do Projeto

Este projeto utiliza Postman para criacao dos cenarios de teste e Newman para execucao automatizada via linha de comando e pipeline CI/CD no GitHub Actions.

A API alvo e a Serverest, uma API publica utilizada para estudos e praticas de testes de software.

### Tecnologias e ferramentas

| Item | Uso |
| --- | --- |
| Postman | Criacao e manutencao da collection de testes |
| Newman | Execucao automatizada da collection |
| GitHub Actions | Execucao dos testes em pipeline |
| newman-reporter-htmlextra | Geracao de relatorio HTML |
| Serverest API | Sistema alvo dos testes |

## 3. Escopo

Estao dentro do escopo desta estrategia:

- Validacao da rota `POST /usuarios` para cadastro de usuarios comuns e administradores.
- Validacao de cadastro com email duplicado.
- Validacao de cadastro e alteracao com email invalido.
- Validacao da rota `GET /usuarios` para listagem de usuarios.
- Validacao da rota `GET /usuarios/{id}` para consulta por identificador.
- Validacao da rota `POST /login` para autenticacao de usuario.
- Validacao da rota `PUT /usuarios/{id}` para alteracao cadastral.
- Validacao da rota `DELETE /usuarios/{id}` para exclusao de usuario.
- Validacao de status code, corpo da resposta, campos obrigatorios, tipo de conteudo e tempo de resposta.
- Execucao automatizada da suite via Newman em ambiente local e CI/CD.

## 4. Fora de Escopo

Nao fazem parte do escopo atual:

- Testes de interface web.
- Testes mobile.
- Testes de carga, estresse ou volume em larga escala.
- Testes de seguranca ofensiva, como pentest.
- Validacao direta em banco de dados, pois a API publica nao disponibiliza acesso ao banco.
- Validacao de infraestrutura interna da Serverest.
- Testes de outros dominios da API que nao estejam implementados na collection atual.

## 5. Ambientes

| Ambiente | URL | Finalidade |
| --- | --- | --- |
| Publico Serverest | `https://serverest.dev` | Execucao dos testes automatizados contra a API publica |
| Local | Maquina do QA/desenvolvedor | Execucao manual via Postman ou automatizada via Newman |
| CI/CD | GitHub Actions | Execucao automatizada em push, pull request ou workflow manual |

## 6. Massa de Dados

A massa de dados deve ser preferencialmente dinamica para evitar conflito entre execucoes.

| Massa | Finalidade | Estrategia |
| --- | --- | --- |
| Usuario comum valido | Cadastro, login, consulta, alteracao e exclusao | Gerar nome, email e senha dinamicamente na pre-request |
| Usuario administrador valido | Validar cadastro com perfil administrativo | Gerar dados dinamicos e enviar `administrador` como `true` |
| Email ja cadastrado | Validar regra de duplicidade | Reutilizar email criado previamente na execucao |
| Email invalido | Validar mensagem de erro e status `400` | Enviar valor sem formato de email valido |
| ID inexistente | Validar comportamento para recurso nao encontrado | Utilizar identificador valido em formato, mas sem registro associado |
| ID invalido | Validar regra de formato do identificador | Utilizar identificador fora do padrao esperado pela API |

Os dados sensiveis nao devem ser versionados. Tokens, senhas reais ou credenciais permanentes nao devem ser mantidos em arquivos da collection ou do ambiente.

## 7. Criterios de Entrada

Para iniciar a execucao dos testes, os seguintes criterios devem ser atendidos:

- API Serverest disponivel.
- Arquivo de ambiente configurado com a variavel `baseUrl`.
- Collection Postman atualizada no repositorio.
- Newman instalado localmente ou configurado na pipeline.
- Massa dinamica de usuarios configurada nas pre-requests.
- Pipeline do GitHub Actions disponivel para execucao automatizada.

## 8. Criterios de Saida

A execucao pode ser considerada concluida quando:

- Todos os cenarios planejados forem executados.
- As evidencias forem geradas por meio do relatorio Newman.
- Falhas criticas forem corrigidas ou registradas para analise.
- O resultado da pipeline estiver disponivel no GitHub Actions.
- Nao houver falhas em fluxos classificados como smoke.

## 9. Criterios de Aceite

A entrega sera considerada aceita quando:

- Os endpoints principais retornarem os status codes esperados.
- As respostas estiverem em formato `application/json`.
- Os contratos das respostas possuirem os campos obrigatorios esperados.
- Os fluxos positivos de cadastro, login, consulta, alteracao e exclusao forem executados com sucesso.
- Os fluxos negativos retornarem mensagens de erro coerentes com a regra validada.
- As requisicoes responderem dentro do limite definido na collection.
- A execucao via CI/CD gerar relatorio de testes sem falhas impeditivas.

## 10. Matriz de Risco

| Risco | Impacto | Probabilidade | Prioridade | Tipo de teste recomendado |
| --- | --- | --- | --- | --- |
| Falha no cadastro de usuario | Alto | Media | Alta | API, smoke, regressao |
| API permitir cadastro com email duplicado | Alto | Media | Alta | API, regressao, teste negativo |
| Login nao retornar token de autorizacao | Alto | Media | Alta | API, smoke, regressao |
| Consulta de usuarios retornar contrato incorreto | Medio | Media | Media | API, contrato, regressao |
| Alteracao de usuario nao persistir corretamente | Alto | Baixa | Media | API, regressao |
| Exclusao de usuario nao remover ou nao sinalizar corretamente o registro | Medio | Media | Media | API, regressao |
| Mensagens de erro divergentes para dados invalidos | Medio | Alta | Media | API, teste negativo |
| Tempo de resposta acima do limite esperado | Medio | Media | Media | Performance basica, regressao |
| Massa de dados conflitar entre execucoes | Medio | Alta | Alta | API, estabilidade da automacao |
| Token ou dados sensiveis versionados por engano | Alto | Baixa | Alta | Revisao de repositorio, seguranca |
| Indisponibilidade da API publica durante a pipeline | Alto | Media | Alta | Smoke, monitoramento da pipeline |

## 11. Suites de Teste

### Smoke

Entram na suite smoke os cenarios minimos para confirmar que a API esta apta para validacoes mais completas:

- `GET /usuarios` deve retornar `200`.
- `POST /usuarios` deve cadastrar usuario valido com `201`.
- `POST /login` deve autenticar usuario cadastrado com `200`.
- `GET /usuarios/{id}` deve consultar usuario existente com sucesso.
- `DELETE /usuarios/{id}` deve remover usuario criado durante o teste.

### Regressao

Entram na suite de regressao os cenarios que validam o comportamento completo do dominio de usuarios:

- Cadastro de usuario comum.
- Cadastro de usuario administrador.
- Cadastro com email ja existente.
- Cadastro com email invalido.
- Listagem de usuarios.
- Consulta por ID.
- Consulta com ID invalido.
- Alteracao de usuario existente.
- Alteracao com email invalido.
- Alteracao usando ID inexistente.
- Exclusao de usuario existente.
- Exclusao de usuario inexistente.
- Validacao de estrutura e campos obrigatorios das respostas.
- Validacao de tempo de resposta.

### API

A suite de API representa o foco principal deste projeto:

- Validacao de status code.
- Validacao de headers.
- Validacao de `Content-Type`.
- Validacao de schema/contrato da resposta.
- Validacao de mensagens de sucesso e erro.
- Encadeamento entre requisicoes por variaveis da collection.
- Validacao de regras de negocio expostas pelos endpoints.

### E2E

Mesmo sendo um projeto de API, ha um fluxo E2E em nivel de servico que pode ser validado:

1. Cadastrar usuario.
2. Realizar login com o usuario cadastrado.
3. Consultar usuario por ID.
4. Alterar dados do usuario.
5. Consultar novamente para validar alteracao.
6. Excluir usuario.
7. Confirmar comportamento apos exclusao.

Esse fluxo garante que as operacoes principais funcionem de forma integrada.

### Mobile

Nao ha testes mobile automatizados neste repositorio, pois o projeto atual valida diretamente a API.

Caso exista futuramente um aplicativo mobile consumindo a Serverest, recomenda-se validar:

- Login no aplicativo usando usuario cadastrado pela API.
- Tratamento de erro para credenciais invalidas.
- Listagem ou consumo de dados retornados pela API.
- Comportamento offline ou falhas de conexao, se aplicavel.

### Validacao de Banco

Nao ha validacao direta de banco no escopo atual, pois a API publica Serverest nao disponibiliza acesso ao banco de dados.

Em um ambiente controlado, a validacao de banco poderia incluir:

- Conferir se o usuario criado foi persistido corretamente.
- Conferir se alteracoes foram refletidas na base.
- Conferir se a exclusao removeu ou inativou o registro conforme regra.
- Validar integridade entre dados de usuario e operacoes dependentes.

## 12. Decisoes de Qualidade

Os fluxos de usuario foram priorizados porque representam a base de autenticacao e controle de acesso da API. Quando cadastro, login, consulta, alteracao ou exclusao falham, os demais fluxos dependentes tendem a ser impactados.

O cadastro de usuarios foi tratado como prioridade alta por ser a porta de entrada para criacao de massa de dados e execucao dos demais cenarios. Sem um usuario valido, nao e possivel validar login nem fluxos encadeados de consulta, alteracao e exclusao.

O login foi priorizado por envolver autenticacao e retorno de token. Mesmo que a collection atual foque no dominio de usuarios, a autenticacao e um ponto critico para qualquer evolucao futura envolvendo endpoints protegidos.

Os testes negativos foram incluidos para validar robustez da API e garantir que entradas invalidas sejam recusadas com mensagens claras. Isso inclui email invalido, email duplicado, ID inexistente e ID fora do formato esperado.

A validacao de contrato foi priorizada porque mudancas inesperadas em campos, tipos ou estrutura da resposta podem quebrar consumidores da API mesmo quando o status code permanece correto.

A validacao de tempo de resposta foi mantida como uma verificacao basica de performance. O objetivo nao e substituir testes de carga, mas identificar degradacoes evidentes durante execucoes locais ou em pipeline.

Os testes mobile e de banco foram documentados como fora do escopo pratico do projeto atual, mas mantidos como referencia estrategica. Essa decisao evita criar uma falsa cobertura e deixa claro quais validacoes exigiriam outro tipo de ambiente ou aplicacao consumidora.

## 13. Evidencias e Relatorios

As evidencias devem ser obtidas por meio do relatorio gerado pelo Newman com o reporter `htmlextra`.

O relatorio deve conter:

- Resumo da execucao.
- Quantidade de testes aprovados e reprovados.
- Tempo de resposta por requisicao.
- Detalhes das requisicoes executadas.
- Mensagens de erro em caso de falha.

Em ambiente CI/CD, o relatorio deve ser publicado como artefato da pipeline para facilitar rastreabilidade.

## 14. Recomendacoes de Evolucao

- Remover tokens ou valores sensiveis versionados na collection.
- Separar suites por finalidade, como `smoke`, `regressao` e `contrato`.
- Adicionar validacao mais estruturada de schema JSON.
- Criar dados dinamicos para todos os cenarios que dependem de email unico.
- Incluir cenarios para endpoints de produtos e carrinhos, caso sejam adicionados ao projeto.
- Configurar limpeza de massa apos execucao para reduzir interferencia entre rodadas.
- Definir limites de tempo de resposta por criticidade do endpoint.
