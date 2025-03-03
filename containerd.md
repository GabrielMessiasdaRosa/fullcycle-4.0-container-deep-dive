# Containerd e runC: Guia Detalhado

## Visão Geral

**Containerd** e **runC** são componentes fundamentais no ecossistema de containers, responsáveis por gerenciar o ciclo de vida e a execução de containers.

| **Componente** | **Função**                                         |
| -------------- | -------------------------------------------------- |
| **Containerd** | Gerencia o ciclo de vida dos containers.           |
| **runC**       | Executa containers seguindo as especificações OCI. |

---

## Containerd

### a) O que é Containerd?

- **Definição**: Runtime de baixo nível para gerenciamento de containers.
- **Mantido por**: Cloud Native Computing Foundation (CNCF).
- **Origem**: Extraído do Docker em 2017 para modularidade.

### b) Benefícios

- **Modularidade**: Pode ser usado independentemente do Docker.
- **Eficiência**: Menor overhead comparado ao Docker Daemon.
- **Integração**: Suporta Kubernetes via CRI (Container Runtime Interface).

### c) Componentes Principais

| **Componente**               | **Descrição**                                 |
| ---------------------------- | --------------------------------------------- |
| **Daemon Principal**         | Serviço gRPC para gerenciar containers.       |
| **Cliente**                  | Interface para interação com o daemon.        |
| **Armazenamento de Imagens** | Gerencia download e distribuição de imagens.  |
| **Execução de Containers**   | Inicia e supervisiona containers.             |
| **Network Primitives**       | Configura interfaces de rede para containers. |
| **Plugin CRI**               | Integração com Kubernetes.                    |

---

## runC

### a) O que é runC?

- **Definição**: Runtime de containers compatível com OCI.
- **Função**: Cria e executa containers usando namespaces e cgroups.
- **Origem**: Doado pela Docker à OCI em 2015.

### b) Funcionamento

1. **Configuração**: Lê um arquivo \`config.json\` com especificações OCI.
2. **Isolamento**: Aplica namespaces e cgroups para criar ambiente isolado.
3. **Execução**: Inicia o processo principal do container.

### c) Exemplo de Uso

```bash

# Criar container com runc

runc create my-container

# Iniciar container

runc start my-container
```

---

## Relação entre Containerd e runC

### Fluxo de Trabalho

1. **Comando Docker**: \`docker run\` é enviado ao Docker Daemon.
2. **Containerd**: Recebe a solicitação e prepara o ambiente.
3. **runC**: Cria e executa o container seguindo as especificações OCI.

### Comparação

| **Aspecto**        | **Containerd**                          | **runC**                       |
| ------------------ | --------------------------------------- | ------------------------------ |
| **Nível**          | Alto (gerenciamento de ciclo de vida)   | Baixo (execução de containers) |
| **Interação**      | Comunica com Docker Daemon e Kubernetes | Executa containers diretamente |
| **Especificações** | Compatível com CRI                      | Compatível com OCI             |

---

## Docker Daemon vs Containerd

### Docker Daemon

- **Função**: Interface de alto nível para gerenciamento de containers.
- **Componentes**: Gerencia imagens, redes, volumes e containers.
- **Integração**: Usa containerd como runtime subjacente.

### Containerd

- **Função**: Gerencia o ciclo de vida de containers.
- **Componentes**: Executa containers via runC.
- **Integração**: Pode ser usado independentemente do Docker.

### Resumo

| **Aspecto**     | **Docker Daemon**          | **Containerd**                      |
| --------------- | -------------------------- | ----------------------------------- |
| **Nível**       | Alto (orquestração)        | Médio (gerenciamento de containers) |
| **Dependência** | Depende de containerd      | Independente do Docker Daemon       |
| **Uso**         | Desenvolvimento e produção | Kubernetes e outros orquestradores  |

---

## Exemplo de Configuração OCI (\`config.json\`)

```json
{
  "ociVersion": "1.0.2",
  "process": {
    "terminal": true,
    "user": { "uid": 0, "gid": 0 },
    "args": ["/bin/sh"]
  },
  "root": { "path": "rootfs" },
  "linux": {
    "namespaces": [
      { "type": "pid" },
      { "type": "network" },
      { "type": "ipc" },
      { "type": "uts" },
      { "type": "mount" }
    ],
    "cgroups": {
      "memory": { "limit": 536870912 }
    }
  }
}
```

---

## Casos de Uso

### a) Kubernetes

- **Containerd**: Runtime padrão para gerenciar pods.
- **runC**: Executa containers dentro dos pods.

### b) Serverless

- **Containerd**: Gerencia execução de funções em plataformas como AWS Lambda.

### c) CI/CD

- **Containerd**: Executa pipelines de build em containers.

---

## Recursos Oficiais

- **Containerd**: [GitHub](https://github.com/containerd/containerd)
- **runC**: [GitHub](https://github.com/opencontainers/runc)
- **OCI**: [opencontainers.org](https://opencontainers.org)

---

## Resumo

- **Containerd**: Gerencia o ciclo de vida de containers de forma eficiente.
- **runC**: Executa containers seguindo padrões OCI.
- **Docker Daemon**: Fornece uma interface amigável para desenvolvedores.
- **Integração**: Containerd e runC são componentes essenciais em ambientes de containers.

# Tutorial de Execução do Containerd

## Introdução

Recapitulando **containerd** é um runtime de containers que gerencia o ciclo de vida de containers de forma eficiente e modular.

---

## Iniciando o Containerd

### a) Iniciar o Serviço

```bash
sudo systemctl start containerd
```

### b) Verificar Status

```bash
sudo systemctl status containerd
```

---

## Gerenciando Containers

### a) Listar Containers em Execução

```bash
sudo ctr tasks list
```

- **Saída Esperada**:
  ```
  TASK     PID    STATUS
  meucontainer 1234   RUNNING
  ```

### b) Baixar uma Imagem

```bash
sudo ctr image pull docker.io/library/alpine:latest
```

- **Detalhes**:
  - Baixa a imagem do Alpine do Docker Hub.
  - Armazena a imagem localmente para uso futuro.

### c) Criar e Executar um Container

```bash
sudo ctr run --rm -t docker.io/library/alpine:latest meucontainer /bin/sh
```

- **Opções**:
  - \`--rm\`: Remove o container após a execução.
  - \`-t\`: Aloca um terminal interativo.
  - \`/bin/sh\`: Comando a ser executado no container.

### d) Verificar Containers em Execução

```bash
sudo ctr tasks list
```

### e) Parar um Container

```bash
sudo ctr task kill meucontainer
```

`

- **Alternativa**: Para forçar a parada, use:
  ```bash
  sudo ctr task kill -s SIGKILL meucontainer
  ```

---

## Comandos Adicionais do Containerd

### a) Listar Imagens Locais

```bash
sudo ctr images list
```

### b) Remover uma Imagem

```bash
sudo ctr images remove docker.io/library/alpine:latest
```

### c) Executar um Container em Segundo Plano

```bash
sudo ctr run -d docker.io/library/alpine:latest meucontainer /bin/sh
```

### d) Anexar a um Container em Execução

```bash
sudo ctr tasks attach meucontainer
```

---

## Fluxo Completo de Execução

| **Passo**              | **Comando**                                                 | **Descrição**                |
| ---------------------- | ----------------------------------------------------------- | ---------------------------- |
| **Iniciar Containerd** | \`sudo systemctl start containerd\`                         | Inicia o serviço containerd. |
| **Baixar Imagem**      | \`sudo ctr image pull alpine:latest\`                       | Baixa a imagem do Alpine.    |
| **Executar Container** | \`sudo ctr run --rm -t alpine:latest meucontainer /bin/sh\` | Cria e executa um container. |
| **Listar Containers**  | \`sudo ctr tasks list\`                                     | Lista containers ativos.     |
| **Parar Container**    | \`sudo ctr task kill meucontainer\`                         | Encerra o container.         |

---

## Dicas e Boas Práticas

### a) Segurança

- **Usuário Não-Root**: Execute containers como usuário não-privilegiado.
- **Imagens Confiáveis**: Use apenas imagens de fontes confiáveis (ex: Docker Hub oficial).

### b) Otimização

- **Limites de Recursos**: Use \`cgroups\` para limitar CPU/memória.
- **Imagens Leves**: Prefira imagens baseadas em Alpine ou distroless.

---

## Recursos Adicionais

- **Documentação Oficial**: [containerd.io](https://containerd.io)
- **Repositório GitHub**: [github.com/containerd](https://github.com/containerd/containerd)
- **Especificação OCI**: [opencontainers.org](https://opencontainers.org)

---
