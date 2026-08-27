# Processo consumindo CPU excessiva — diagnóstico e resolução

## Sintoma
Sistema apresentando lentidão geral, com suspeita de algum processo consumindo
recursos de CPU de forma anormal.

## Diagnóstico

**1. Listar processos em tempo real, ordenados por uso de CPU:**
```bash
top
```
`top` exibe uma lista dinâmica dos processos em execução, atualizada em tempo real,
ordenada por padrão pelo consumo de CPU do maior para o menor.

Colunas principais:
- `PID`: identificador único do processo (usado para referenciá-lo em outros comandos)
- `%CPU`: percentual de uso do processador
- `%MEM`: percentual de uso de memória RAM
- `COMMAND`: nome do processo/comando em execução

Para sair do `top`, pressionar `q`.

**Resultado obtido (teste prático):**
```
PID     USER    PR  NI  VIRT   RES   SHR S  %CPU  %MEM  TIME+    COMMAND
530886  aaron   20   0  8628   2344  2220 R  94,4   0,1  0:17.22  yes
```

## Causa raiz
O processo `yes` (PID 530886) estava consumindo 94,4% de CPU de forma contínua,
sendo o principal responsável pela lentidão do sistema.

## Solução

**1. Encerrar o processo de forma "educada" (permite que ele finalize com segurança):**
```bash
kill 530886
```
Envia o sinal padrão (SIGTERM), solicitando que o processo se encerre normalmente.

**2. Se o processo não responder ao comando acima (travado ou ignorando o sinal):**
```bash
kill -9 530886
```
O `-9` envia o sinal SIGKILL, forçando o encerramento imediato do processo,
sem chance dele finalizar tarefas pendentes.

**Verificação pós-correção:**
```bash
top
```
Confirma que o processo não aparece mais na lista e o uso de CPU voltou ao normal.

## Prevenção
- Monitorar uso de CPU periodicamente com `top` ou `htop`
- Investigar processos com consumo anormalmente alto antes que afetem outros serviços
- Em ambientes de produção, configurar alertas automáticos de uso de CPU acima de um limite (ex: 90%)

## Nota
Sempre priorizar `kill` (sinal padrão) antes de `kill -9`, pois o encerramento forçado
pode causar perda de dados caso o processo estivesse gravando algo no momento da interrupção.
