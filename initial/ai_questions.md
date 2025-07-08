# Questões criadas por IA

## Threads API

1. A maioria das APIs de threads, como pthreads, oferece uma função pthread_join(). Esta função permite que uma thread aguarde o término de outra. Se uma thread principal cria várias threads de trabalho e não chama pthread_join() para nenhuma delas antes de encerrar sua própria execução, que problemas podem surgir? Qual é o propósito de "desanexar" (detaching) uma thread e como isso se relaciona com pthread_join()?

> R: A thread chamadora finalizar vai causar que todas as suas threads 'filhas' encerrem abruptamente, causando resuktados imprecisos e incompletos.
>
> Desanexar uma thread a desvincula da thread que a criou. Isso informa ao sistema que os recursos daquela thread podem ser liberados automaticamente assim que ela terminar sua execução. Isso é útil quando a thread principal não precisa do resultado da thread de trabalho e não quer ficar bloqueada esperando por ela.

2. As threads podem ser implementadas em nível de usuário (User-Level Threads - ULTs) ou em nível de núcleo (Kernel-Level Threads - KLTs). Descreva as principais diferenças entre esses dois modelos. Quais são as vantagens de um sobre o outro em termos de performance na criação e troca de contexto, e em termos de aproveitamento de sistemas com múltiplos processadores?

> R: À nível de usuário, o gerenciamento da thread é feito por uma biblioteca no processo do usuário, sem o conhecimento o kernel. Para o kernel, o processo inteiro é uma única thread de execução.
>
> Vantagens: Criação de threads e troca de contexto são extremamente rápidas, pois não exigem uma chamada de sistema (operação muito custosa).
>
> Desvantagens: Se uma ULT realiza uma chamada de sistema bloqueante, o kernel bloqueia o processo inteiro, incluindo todas as outras ULTs - mesmo que elas estejam protas para executar. Além disso, ULTs não podem ser executadas em paralelo por múltiplos processadores, pois o kernel só enxerga uma única thread por processo.
>
> À nível de kernel, o kernel tem conheicmento de cada thread e as gerencia individualmente. Cada KLT é uma unidade de escalonamento para o istema operacional.
>
> Vantagens: Permite paralelismo real em sistema com múltiplos processadores.
>
> Desvantagens: A criação, o término e a troca de contexto são mais lentos que nas ULTs, devido a chamadas de sistema e intervenção do kernel.

3. Uma thread pode ser cancelada de duas maneiras principais: assíncrona ou adiada (deferred). Explique a diferença entre elas e por que o cancelamento adiado é, na maioria dos casos, a abordagem preferível e mais segura.

> 