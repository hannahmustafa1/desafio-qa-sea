# Desafio QA — Sea Tecnologia | Testes automatizados da API `/employees`

Testes automatizados da API REST de cadastro de funcionários, feitos com **Postman** e executados por linha de comando com o **Newman**.

## O que é testado

| Pasta | ID | Cenário | Esperado |
|---|---|---|---|
| 01 - Fluxo principal | AUT-01 | Listar funcionários | 200, JSON, lista, tempo < 2 s |
| | AUT-02 | Cadastrar funcionário válido (CPF gerado a cada execução) | 201, id retornado, dados iguais aos enviados |
| | AUT-03 | Consultar o funcionário criado pelo id | 200 e mesmo id |
| | AUT-04 | Funcionário criado aparece na listagem | id presente na lista |
| 02 - Validações | AUT-05 | CPF com letras | 400 |
| | AUT-06 | CPF com dígito verificador inválido | 400 |
| | AUT-07 | CPF duplicado | 409 ou 400 |
| | AUT-08 | Corpo vazio `{}` | 400 |
| | AUT-09 | Nome com números | 400 |
| | AUT-10 | Consultar id inexistente | 404 |
| 03 - Edição | AUT-11 | Editar só o nome (PATCH) sem autenticação | 401 ou 403 |
| | AUT-12 | Edição parcial não apaga os outros campos | CPF preservado após o PATCH |
| 04 - Segurança | SEC-01 | Acesso à lista sem autenticação | 401 ou 403 |
| | SEC-03 | Cabeçalhos: CORS, Cache-Control, X-Powered-By | sem `*`, sem cache público, sem X-Powered-By |
| 99 - Limpeza | — | Apaga todos os registros criados na execução | — |

### Sobre os testes que falham

Os testes com um ID de defeito entre colchetes (ex.: `[BUG-005 / API-03]`) **falham de propósito**: eles verificam o comportamento correto, que a API ainda não tem. Assim, a automação documenta os defeitos e serve como teste de regressão: quando o defeito for corrigido, o teste passa a passar sem mudar nada.

### Decisões de projeto

- **Dados únicos a cada execução:** o CPF é gerado com dígitos verificadores válidos e o nome leva um carimbo de tempo. Isso evita conflito com execuções anteriores ou com dados de outras pessoas.
- **Limpeza ao final:** todos os ids criados, inclusive os aceitos indevidamente nos testes negativos, são apagados no fim, para não deixar lixo no ambiente compartilhado.
- **Nomes dos campos configuráveis:** como a API não tem documentação, o caminho dos campos (ex.: `state.employee.cpf`) e o modelo do corpo (`baseEmployee`) ficam no arquivo de ambiente. Se a API mudar, só esse arquivo é alterado.
- **Sem testes de carga nem destrutivos em massa**, por se tratar de um ambiente compartilhado.

## Resultado esperado hoje

22 verificações: **11 passam** (fluxo principal e consulta de id inexistente) e **11 falham**, documentando os defeitos de validação, edição e segurança.

## Como executar

Pré-requisito: [Node.js](https://nodejs.org) 18 ou superior.

```bash
npm install
npm test
```

O resultado aparece no terminal, e um relatório HTML é gerado em `reports/relatorio.html`.

Para rodar contra outro endereço:

```bash
npm test -- --env-var baseUrl=https://outro-endereco.com
```

### No Postman

1. Importe `collections/desafio-qa.postman_collection.json` e `environments/sea.postman_environment.json`.
2. Selecione o ambiente **Sea - analista-teste** no canto superior direito.
3. Clique com o botão direito na coleção e escolha **Run collection**.

### No GitHub Actions

O workflow `.github/workflows/api-tests.yml` roda os testes a cada push e também manualmente pela aba **Actions**. O relatório HTML fica disponível em **Artifacts** na página da execução.

## Estrutura

```
collections/   coleção do Postman com os testes
environments/  URL base e configuração dos campos
.github/       pipeline de execução automática
reports/       relatório gerado (não versionado)
```

## Uso de IA

O detalhamento de onde usei IA e o que precisei corrigir está no diário de uso de IA, entregue junto com o relatório do desafio.
