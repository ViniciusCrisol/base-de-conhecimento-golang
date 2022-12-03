# Acesso ao banco de dados
A linguagem Go, através da biblioteca nativa `database/sql`, fornece diversos recursos para manipular bases de dados relacionais. Sua API disponibiliza diversos métodos que visam abstrair funcionalidades cotidianas. Entretanto, graças a abundância de métodos expostos pelo pacote, eventualmente, dúvidas acerca do assunto são geradas. Tendo isso em vista, esse pequeno post foi criado visando esclarecer algumas das questões mais comuns entre os programadores Go acerca do assunto.

## Query, Exec e Prepare
Essa seção visa sanar algumas dúvidas relacionadas ao uso dos métodos de consulta/manipulação mais comuns da API. Com fins de ilustração, imagens que demonstram as chamadas realizadas por baixo dos panos durante a execução dos métodos serão exibidas. O conteúdo da seção foi criado base no artigo [Query vs Exec vs Prepare in Go](https://aloksinhanov.medium.com/query-vs-exec-vs-prepare-in-golang-e7c49212c36c "Query vs Exec vs Prepare in Go").
 
### Prepare
O método prepare deve ser utilizado **apenas** nos casos nos quais deseja-se executar o mesmo statement N vezes durante o ciclo de vida do programa. Ao usar essa funcionalidade, deve-se manter atento aos seguintes detalhes:

1. A partir do ponto no qual o _prepared statement_ não for mais necessário, o próprio deve ser finalizado, para isso, o método `Close()` deve ser executado. Caso contrário, o espaço alocado para seu uso não será liberado.
2. Como pode-se notar no mapeamento realizado, ao executar o comando, serão realizadas duas chamadas TCP durante sua execução.

![Mapeamento PREPARE](mapeamento-prepare.webp "Mapeamento PREPARE")

**Sintaxe do comando:**
```
  stm, _ := db.Prepare("insert into items(Col1, Col2, Col3) values(?, ?, ?)”, “Val1”, "Val2", "Val3")
```
