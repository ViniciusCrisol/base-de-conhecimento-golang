# Consumer Design
Este conteúdo visa demonstrar a implementação do padrão _Consumer Design_, descrito no artigo [SQS Consumer Design: Achieving High Scalability while managing concurrency in Go](https://towardsdatascience.com/sqs-consumer-design-achieving-high-scalability-while-managing-concurrency-in-go-d5a8504ea754 "SQS Consumer Design: Achieving High Scalability while managing concurrency in Go"). Em linhas gerais, este _design_ tem o objetivo de entregar alto desempenho a aplicações que lidam com cargas de trabalho altas e constantes, através da otimização de tarefas por mecanismos de concorrência/paralelismo fornecidos pela linguagem. O exemplo escolhido para demonstrar a implementação do _design_ proposto foi o consumo de uma fila SQS.

Como pode-se notar no fonte abaixo, foram criados 2 métodos, `workerpool` e `worker`. Sendo o primeiro deles, uma função auxiliar, responsável por invocar **N** workers, que por sua vez, são encarregados de executar isoladamente a regra de negócio da aplicação(no cenário proposto, recuperar e deletar mensagens de uma fila). Com isso, várias instâncias, dentro do mesmo programa estarão simultaneamente consumindo e processando mensagens da fila. Dessa maneira, otimizando o uso dos recursos providos pela máquina, consequentemente, aumentando o desempenho dos processos da aplicação.

```
func workerpool() {
    for w := 1; w <= N; w++ {
        go worker()
    }
}

func worker() {
    wg := sync.WaitGroup{}
    for {
        msgs, err := getSQSMsgs()
        if err != nil {
            // TODO
        }
        wg.Add(len(msgs))

        for _, msg := range msgs {
            go func(m *sqs.Message) {
                defer wg.Done()
                // TODO
                deleteSQSMsg(m)
            }(msg)
        }
        wg.Wait()
    }
}
```
