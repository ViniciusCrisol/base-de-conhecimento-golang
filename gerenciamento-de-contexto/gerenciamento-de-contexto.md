# Gerenciamento de contexto

O pacote context possibilita o cancelamento de processos em execução. Também é frequentemente utilizado para enriquecer o contexto das chamadas através de atributos personalizados. A API disponibiliza uma interface genérica para representar contextos. Os métodos WithCancel, WithTimeout e WithDeadline, são fornecidos para possibilitar a quebra de processos, tanto de forma manual, quanto agendada.

package main

import (
 "context"
 "fmt"
 "time"
)

func main() {
 ctx, cancel := context.WithCancel(context.Background())

 go func() {
  time.Sleep(time.Second * 3)
  cancel()
 }()

 select {
 case <-ctx.Done():
  fmt.Println("operation was cancelled")
 case <-time.After(time.Second * 5):
  fmt.Println("timed out")
 }
}

Neste exemplo, foi criando um contexto com o método context.WithCancel, o mesmo possui um context.Background como pai. Em seguida, foi disparada uma goroutine que por três segundos fica inativa e cancela a execução após a expiração. Finalmente, um select é utilizado para esperar que o contexto seja cancelado ou até que o tempo limite de cinco segundos, seja atingido. Se o contexto for cancelado antes do tempo limite, a operação será cancelada e a mensagem "operação foi cancelada" será impressa. Caso contrário, a mensagem "tempo esgotado" será exibida.
