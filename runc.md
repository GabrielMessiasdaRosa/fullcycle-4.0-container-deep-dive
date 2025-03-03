var markdown = `

# Tutorial de Instalação e Execução do runC

## Instalação dos Componentes Necessários

### a) Instalando containerd e runC

```bash

# Atualize os pacotes do sistema

sudo apt-get update

# Instale o containerd

sudo apt-get install -y containerd curl

# Baixe e instale o runC

curl -L https://github.com/opencontainers/runc/releases/download/v1.2.0/runc.arm64 -o runc
chmod +x runc
sudo mv runc /usr/local/bin/
```

### b) Instalando o Docker (para Gerenciamento de Imagens)

```bash
sudo apt-get install -y docker-ce docker-ce-cli
docker --version
sudo usermod -aG docker $USER
sudo systemctl enable docker
sudo systemctl start docker
```

---

## Preparando o Root Filesystem (rootfs)

### a) Baixando uma Imagem de Exemplo

```bash

# Baixe a imagem do Alpine Linux

sudo docker pull alpine

# Crie um diretório para o rootfs

mkdir rootfs

# Extraia a imagem do Alpine para o diretório rootfs

CONTAINER_ID=$(sudo docker create alpine)
sudo docker export $CONTAINER_ID | sudo tar -C rootfs -xvf -
cd rootfs
```

---

## Criando o Arquivo de Configuração (config.json)

### a) Gerando o Arquivo de Configuração

```bash

# Crie um arquivo de especificação padrão

runc spec
```

### b) Exemplo de \`config.json\`

```json
{
  "ociVersion": "1.0.2",
  "process": {
    "terminal": true,
    "user": { "uid": 0, "gid": 0 },
    "args": ["sh"],
    "env": [
      "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
    ],
    "cwd": "/",
    "rlimits": [{ "type": "RLIMIT_NOFILE", "hard": 1024, "soft": 1024 }],
    "noNewPrivileges": true
  },
  "root": {
    "path": "rootfs",
    "readonly": true
  },
  "hostname": "meucontainer",
  "mounts": [
    { "destination": "/proc", "type": "proc", "source": "proc" },
    {
      "destination": "/dev",
      "type": "tmpfs",
      "source": "tmpfs",
      "options": ["nosuid", "strictatime", "mode=755", "size=65536k"]
    }
  ],
  "linux": {
    "namespaces": [
      { "type": "pid" },
      { "type": "network" },
      { "type": "ipc" },
      { "type": "uts" },
      { "type": "mount" }
    ]
  }
}
```

---

## Executando o Container com runC

### a) Iniciando o Container

```bash

# Execute o container com o runC

sudo runc run meucontainer
```

### b) Listando Containers em Execução

```bash

# Lista os containers em execução

sudo runc list
```

### c) Visualizando Processos do Container

```bash

# Veja o processo do container em execução

ps aux | grep meucontainer
```

### d) Parando o Container

```bash

# Pare o container em execução

sudo runc kill meucontainer KILL
```

---

## Fluxo Completo de Execução

| **Passo**              | **Comando**                                               | **Descrição**                     |
| ---------------------- | --------------------------------------------------------- | --------------------------------- |
| **Instalação**         | `sudo apt-get install containerd curl`                    | Instala dependências.             |
| **Preparar rootfs**    | `docker export CONTAINER_ID && sudo tar -C rootfs -xvf -` | Extrai sistema de arquivos.       |
| **Gerar config.json**  | `runc spec`                                               | Cria arquivo de configuração OCI. |
| **Executar Container** | `sudo runc run meucontainer`                              | Inicia o container.               |
| **Monitorar**          | `sudo runc list`                                          | Lista containers ativos.          |
| **Parar Container**    | `sudo runc kill meucontainer KILL`                        | Encerra o container.              |

---

## Dicas e Boas Práticas

### a) Segurança

- **Usuário Não-Root**: Configure \`"user": { "uid": 1000, "gid": 1000 }\` no \`config.json\`.
- **Read-Only Rootfs**: Mantenha \`"readonly": true\` para evitar alterações no sistema de arquivos.

### b) Otimização

- **Limites de Recursos**: Defina \`rlimits\` para controlar uso de CPU/memória.
- **Namespaces**: Use namespaces para isolamento de processos e rede.

---

## Recursos Adicionais

- **Documentação Oficial runC**: [GitHub](https://github.com/opencontainers/runc)
- **Especificação OCI**: [opencontainers.org](https://opencontainers.org)
- **Guia de Containerd**: [containerd.io](https://containerd.io)

---
