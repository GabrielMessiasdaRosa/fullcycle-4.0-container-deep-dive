# Sistemas de Arquivos Especiais: Sys, Mqueue e Debugfs

## Sistemas de Arquivos Especiais

Sistemas de arquivos especiais são usados para interagir com funcionalidades do kernel e do sistema operacional. Eles não armazenam dados tradicionais, mas fornecem interfaces para acessar informações e controlar comportamentos do sistema. Neste guia, exploraremos três sistemas de arquivos especiais: **sysfs**, **mqueue** e **debugfs**.

---

## SYSFS (System File System)

### a) O que é o SYSFS?

- **Definição**: Sistema de arquivos virtual que expõe **estruturas de dados do kernel** para o espaço do usuário.
- **Montagem padrão**: **/sys**.
- **Funcionalidades**:
  - Gerenciar dispositivos e drivers.
  - Acessar informações de hardware (ex: CPU, memória, dispositivos PCI).

### b) Estrutura do **/sys**

| **Diretório**    | **Descrição**                           |
| ---------------- | --------------------------------------- |
| **/sys/class**   | Classes de dispositivos (ex: rede, USB) |
| **/sys/devices** | Dispositivos físicos e virtuais         |
| **/sys/kernel**  | Configurações e informações do kernel   |
| **/sys/module**  | Módulos do kernel carregados            |

### c) Exemplo de Uso

```bash

# Verificar dispositivos de rede

ls /sys/class/net

# Verificar informações da CPU

cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_cur_freq
```

---

## MQUEUE (Message Queue File System)

### a) O que é o MQUEUE?

- **Definição**: Sistema de arquivos para **filas de mensagens POSIX**.
- **Montagem padrão**: **/dev/mqueue**.
- **Funcionalidades**:
  - Criar e gerenciar filas de mensagens entre processos.
  - Usado para comunicação interprocessos (IPC).

### b) Como Montar o MQUEUE

```bash
sudo mount -t mqueue none /dev/mqueue
```

### c) Exemplo de Uso

```bash

# Criar uma fila de mensagens

mq_open /my_queue

# Listar filas de mensagens

ls /dev/mqueue

# Enviar uma mensagem

echo "Hello" > /dev/mqueue/my_queue
```

---

## DEBUGFS (Debug File System)

### a) O que é o DEBUGFS?

- **Definição**: Sistema de arquivos para **depuração e diagnóstico do kernel**.
- **Montagem padrão**: **/sys/kernel/debug**.
- **Funcionalidades**:
  - Acessar informações internas do kernel.
  - Realizar testes e ajustes em tempo real.

### b) Como Montar o DEBUGFS

```bash
sudo mount -t debugfs none /sys/kernel/debug
```

### c) Exemplo de Uso

```bash

# Verificar eventos de bloqueio

cat /sys/kernel/debug/tracing/trace

# Ajustar parâmetros do kernel

echo 1 > /sys/kernel/debug/sched_features
```

---

## Comparação: SYSFS vs MQUEUE vs DEBUGFS

| **Característica**  | **SYSFS**                        | **MQUEUE**                       | **DEBUGFS**                       |
| ------------------- | -------------------------------- | -------------------------------- | --------------------------------- |
| **Propósito**       | Gerenciar dispositivos e drivers | Comunicação interprocessos (IPC) | Depuração e diagnóstico do kernel |
| **Montagem Padrão** | **/sys**                         | **/dev/mqueue**                  | **/sys/kernel/debug**             |
| **Uso Comum**       | Informações de hardware          | Filas de mensagens               | Testes e ajustes do kernel        |
| **Persistência**    | Volátil                          | Volátil                          | Volátil                           |

---

## Configurações de Segurança

### a) SYSFS

- **Restrições**:
  - Limitar acesso a **/sys** com permissões adequadas.
  - Usar namespaces para isolamento de processos.

### b) MQUEUE

- **Restrições**:
  - Definir permissões de leitura/escrita para usuários específicos.
  - Usar **mq_setattr** para configurar limites de tamanho.

### c) DEBUGFS

- **Restrições**:
  - Montar apenas quando necessário.
  - Evitar expor informações sensíveis.

---

## Melhores Práticas

### a) SYSFS

- **Monitoramento**: Use ferramentas como **udev** para gerenciar dispositivos.
- **Edição Cuidadosa**: Modificar arquivos em **/sys** pode alterar comportamentos do sistema.

### b) MQUEUE

- **Limites de Tamanho**: Configure limites para evitar estouro de memória.
- **Gerenciamento de Filas**: Remova filas não utilizadas para liberar recursos.

### c) DEBUGFS

- **Uso Temporário**: Desmonte após a depuração.
- **Acesso Restrito**: Limite o acesso a usuários privilegiados.

---

## TL;DR

- **SYSFS**: Gerencia dispositivos e drivers, montado em **/sys**.
- **MQUEUE**: Usado para filas de mensagens IPC, montado em **/dev/mqueue**.
- **DEBUGFS**: Para depuração do kernel, montado em **/sys/kernel/debug**.
- **Segurança**: Restrinja acesso e monte apenas quando necessário.
