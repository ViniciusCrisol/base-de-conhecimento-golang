# Acesso ao banco de dados
A linguagem Go, através da biblioteca nativa `database/sql`, fornece diversos recursos para manipular bases de dados relacionais. Sua API disponibiliza vários métodos que visam abstrair funcionalidades cotidianas. Entretanto, graças a enorme abundância de funções expostas pelo pacote, eventualmente, dúvidas acerca do assunto são geradas. Tendo isso em vista, esse pequeno texto visa esclarecer algumas dessas questões que são tão comuns entre os programadores Go.

## Query, Exec e Prepare
Essa seção visa sanar alguns questionamentos relacionadas ao uso dos métodos de consulta/manipulação mais comuns da API. Com fins ilustrativos, imagens que demonstram as chamadas realizadas por baixo dos panos serão exibidas.

Em suma, o conteúdo do bloco foi traduzido do artigo [Query vs Exec vs Prepare in Go](https://aloksinhanov.medium.com/query-vs-exec-vs-prepare-in-golang-e7c49212c36c "Query vs Exec vs Prepare in Go").

### Exec
O método `Exec` deve ser utilizado **apenas** nos casos nos quais deseja-se executar operações que possuam retornos irrelevantes. Essa característica é geralmente atribuída as chamadas **insert**, **update** e **delete**.

1. Após a execução do método, a conexão utilizada será liberada automaticamente.
2. O número de chamadas ao banco de dados difere, dependendo de como a invocação do método for realizada. Como pose-se notar nos mapeamentos anexados, ao executar o método sem inferir parâmetros, será realizada apenas uma chamada TCP ao serviço de banco de dados(ignorando a autenticação). Entretanto, ao atribuir parâmetros, serão realizadas 3 chamadas TCP.

**Mapeamento EXEC com parâmetros:**
![Mapeamento EXEC com parâmetros](mapeamento-exec-com-params "Mapeamento EXEC com parâmetros")

**Mapeamento EXEC sem parâmetros:**
![Mapeamento EXEC sem parâmetros](mapeamento-exec-sem-params "Mapeamento EXEC sem parâmetros")

**Sintaxe do comando:**
```
  stm, _ := db.Exec("insert into items(Col1, Col2, Col3)...”, PARÂMETROS)
```

### Prepare
O método `Prepare` deve ser utilizado **apenas** nos casos nos quais deseja-se executar o mesmo statement N vezes durante o ciclo de vida do programa. Ao usar essa funcionalidade, deve-se manter atento aos seguintes detalhes:

1. A partir do ponto que o _prepared statement_ não for mais necessário, o próprio deve ser finalizado. Para isso, o método `Close()`, contido no próprio statement deve ser executado. Caso contrário, os recursos alocados para seu funcionamento não serão liberados.
2. Como pode-se notar no mapeamento, ao invocar a função, serão realizadas duas chamadas TCP para sua execução(ignorando a autenticação).

![Mapeamento PREPARE](mapeamento-prepare.webp "Mapeamento PREPARE")

**Sintaxe do comando:**
```
  stm, _ := db.Prepare("insert into items(Col1, Col2, Col3) values(?, ?, ?)”, “Val1”, "Val2", "Val3")
```