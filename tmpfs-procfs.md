# Sistemas de Arquivos

## Introdução aos Sistemas de Arquivos

Um **sistema de arquivos** é uma estrutura lógica que define como dados são armazenados, organizados e acessados em dispositivos de armazenamento (HDs, SSDs, memória RAM). Ele gerencia:

- **Metadados**: Nomes, permissões, datas de criação.
- **Operações**: Leitura, escrita, exclusão.
- **Hierarquia**: Diretórios e subdiretórios.

Neste guia, focaremos em dois sistemas de arquivos especiais: **tmpfs** e **procfs**.

---

## TMPFS (Temporary File System)

### a) O que é o TMPFS?

- **Definição**: Sistema de arquivos temporário armazenado **em memória RAM**.
- **Características**:
  - **Volátil**: Dados são perdidos após desligamento.
  - **Rápido**: Acesso em velocidade de RAM.
  - **Uso comum**: Diretórios temporários (ex: **/tmp**, **/run**).

### b) Casos de Uso

- **Cache efêmero**: Dados que não precisam persistir.
- **Arquivos temporários de aplicações**: Sessões de usuário, logs voláteis.

### c) Como Montar um TMPFS

```bash

# Montar tmpfs em /mnt/tmp

sudo mount -t tmpfs -o size=512M tmpfs /mnt/tmp
```

- **Parâmetros**:
  - **size=512M**: Limita o uso de memória a 512 MB.
  - **mode=1777**: Define permissões (ex: **drwxrwxrwt**).

### d) Configuração no **/etc/fstab**

```
tmpfs /mnt/tmp tmpfs defaults,size=512M,noexec 0 0
```

- \***\*noexec\*\***: Impede execução de binários no diretório (melhora segurança).

---

## PROCFS (Process File System)

### a) O que é o PROCFS?

- **Definição**: Sistema de arquivos virtual que expõe **informações do kernel e processos**.
- **Montagem padrão**: **/proc**.
- **Funcionalidades**:
  - Visualizar dados de processos (ex: **/proc/1234** para PID 1234).
  - Acessar informações do sistema (ex: **/proc/cpuinfo**).

### b) Estrutura do **/proc**

| **Arquivo/Diretório**  | **Descrição**                    |
| ---------------------- | -------------------------------- |
| **/proc/cpuinfo**      | Detalhes da CPU                  |
| **/proc/meminfo**      | Estatísticas de memória          |
| **/proc/<PID>/status** | Status de um processo específico |
| **/proc/sys**          | Configurações do kernel (sysctl) |

### c) Exemplo de Uso

```bash

# Verificar uso de memória

cat /proc/meminfo

# Listar processos em execução

ls /proc | grep -E '^[0-9]+$'
```

---

## Comparação: TMPFS vs PROCFS

| **Característica**     | **TMPFS**                    | **PROCFS**                          |
| ---------------------- | ---------------------------- | ----------------------------------- |
| **Armazenamento**      | Memória RAM                  | Virtual (dados do kernel)           |
| **Persistência**       | Volátil                      | Volátil (dados em tempo real)       |
| **Uso Principal**      | Arquivos temporários         | Monitoramento do sistema            |
| **Montagem**           | Manual ou via **/etc/fstab** | Automática (gerenciada pelo kernel) |
| **Exemplo de Caminho** | **/tmp**, **/run**           | **/proc**, **/proc/sys**            |

---

## Configurações de Segurança

### a) TMPFS

- **Restrições**:
  ```bash
  sudo mount -t tmpfs -o size=512M,noexec,nosuid tmpfs /mnt/tmp
  ```
  - **nosuid**: Ignora bits SUID/SGID.
  - **noexec**: Bloqueia execução de binários.

### b) PROCFS

- **Restrições**:
  - Limitar acesso ao **/proc** com permissões:
    ```bash
    sudo chmod 700 /proc/[PID]/
    ```
  - Usar namespaces para isolamento de processos.

---

## Melhores Práticas

### a) TMPFS

- **Limite de Tamanho**: Evite consumir toda a RAM com **size=**.
- **Dados Críticos**: Não armazene dados persistentes.

### b) PROCFS

- **Monitoramento**: Use ferramentas como **top**, **htop**, ou **ps**.
- **Edição Cuidadosa**: Modificar arquivos em **/proc/sys** pode alterar comportamentos do kernel.

---

## Exemplo Prático: Criar um TMPFS para Cache

### Passo a Passo:

1. **Criar Diretório**:

   ```bash
   sudo mkdir /mnt/cache
   ```

2. **Montar TMPFS**:

   ```bash
   sudo mount -t tmpfs -o size=1G,nosuid tmpfs /mnt/cache
   ```

3. **Verificar**:
   ```bash
   df -h | grep tmpfs
   # Saída: tmpfs 1.0G 0 1.0G 0% /mnt/cache
   ```

---

## TLDR:

- **TMPFS**: Ideal para dados temporários de alta velocidade.
- **PROCFS**: Essencial para depuração e monitoramento do sistema.
