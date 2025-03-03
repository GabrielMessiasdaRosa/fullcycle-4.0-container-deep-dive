# Tutorial de utilização do LXD e LXC

## Instalação e Uso do LXC e LXD no Ubuntu

### Introdução

#### O que são LXC e LXD?

LXC (Linux Containers) é uma tecnologia de virtualização a nível de sistema operacional que permite a criação e execução de múltiplos ambientes Linux isolados (containers) em um único host. O LXC fornece ferramentas e bibliotecas para gerenciar esses containers, oferecendo controle detalhado sobre seu comportamento e recursos.

LXD é um gerenciador de containers que se baseia no LXC para fornecer uma experiência mais amigável e rica em recursos. Pense no LXD como uma camada superior que torna o uso do LXC mais simples e eficiente, oferecendo funcionalidades adicionais como gerenciamento de imagens, redes e armazenamento.

#### Por que estamos usando o LXD?

- **Facilidade de Uso**: O LXD simplifica o processo de gerenciamento de containers, tornando-o mais acessível, especialmente para iniciantes.
- **Funcionalidades Avançadas**: Oferece recursos adicionais que não estão disponíveis diretamente no LXC, como perfis, gerenciamento de rede avançado e suporte a clusters.
- **Interface Unificada**: O LXD fornece uma API REST e uma ferramenta de linha de comando unificada para gerenciar containers locais e remotos.

Embora os comandos que usamos venham da ferramenta "lxc", é importante entender que:

- **lxd**: É o daemon (processo em segundo plano) que gerencia os containers, imagens, redes e outros recursos. Ele escuta solicitações e executa ações conforme necessário.
- **lxc**: É a ferramenta de linha de comando (CLI) que usamos para enviar comandos ao lxd. Quando você executa um comando lxc, ele se comunica com o lxd para realizar a ação solicitada.

**Resumindo**:

- **LXC**: Tecnologia subjacente que fornece a funcionalidade básica de containers.
- **LXD**: Gerenciador de containers que utiliza o LXC para fornecer recursos avançados.
- **lxc**: Interface de linha de comando usada para interagir com o lxd.

### Sobre o Snap

O Snap é um sistema de pacotes e implantação desenvolvido pela Canonical que permite instalar e atualizar aplicativos de forma fácil e segura no Ubuntu e em outras distribuições Linux.

### Pré-requisitos

É necessário ter:

- Sistema operacional Ubuntu instalado (versão 18.04 ou superior).
- Acesso a um usuário com privilégios de sudo.
- Conexão com a internet.

### Passo a Passo

#### Passo 1: Atualizar o Sistema

Antes de começar, atualize os pacotes do sistema com o comando:

```bash
sudo apt update && sudo apt upgrade -y
```

#### Passo 2: Instalar o Snapd

Se o Snapd não estiver instalado, instale-o com:

```bash
sudo apt install snapd -y
```

#### Passo 3: Instalar o LXD via Snap

Instalar o LXD via Snap garante que você tenha a versão mais recente, independentemente da arquitetura do sistema. Para isso, execute:

```bash
sudo snap install lxd
```

#### Passo 4: Adicionar Usuário ao Grupo LXD

Adicione seu usuário ao grupo lxd para evitar usar sudo em cada comando, usando:

```bash
sudo usermod -aG lxd $USER
```

e, em seguida, atualize a sessão com:

```bash
newgrp lxd
```

#### Passo 5: Inicializar o LXD

Inicialize o LXD com as configurações padrão com:

```bash
lxd init --auto
```

Obs.: Se preferir uma configuração personalizada, execute **lxd init** sem o **--auto** e siga as instruções interativas.

#### Passo 6: Verificar a Arquitetura do Sistema

É crucial saber a arquitetura do seu sistema para baixar a imagem correta. Execute:

```bash
uname -m
```

Possíveis saídas:

- **x86_64**: Arquitetura AMD64 (Intel/AMD de 64 bits).
- **aarch64** ou **arm64**: Arquitetura ARM64 (processadores ARM de 64 bits).

Exemplo de saída:

```bash
aarch64
```

Neste tutorial, usaremos um sistema com arquitetura ARM64 como exemplo.

#### Passo 7: Baixar e Executar uma Imagem do Alpine Linux

**Por que Alpine Linux?**

O Alpine Linux é uma distribuição leve e eficiente, com excelente suporte para diferentes arquiteturas, incluindo ARM64.

##### 7.1. Listar Imagens Disponíveis

Liste as imagens do Alpine Linux compatíveis com sua arquitetura.

- Para ARM64, execute:

```bash
lxc image list images:alpine arch=arm64
```

- Para AMD64, execute:

```bash
lxc image list images:alpine arch=amd64
```

##### 7.2. Lançar o Container usando a Imagem

Utilize o alias ou o fingerprint da imagem para lançar o container.

- Usando o Alias:
  - Para ARM64, execute:
  ```bash
  lxc launch images:alpine/3.18/arm64 meu-container
  ```
  - Para AMD64, execute:
  ```bash
  lxc launch images:alpine/3.18/amd64 meu-container
  ```
- Usando o Fingerprint, execute:

```bash
lxc launch images:<FINGERPRINT> meu-container
```

Exemplo:

```bash
lxc launch images:e1b3d2f5a8c7 meu-container
```

#### Passo 8: Verificar o Status do Container

Confira se o container está em execução com o comando:

```bash
lxc list
```

A saída esperada deve exibir o nome, estado, endereço e outras informações, por exemplo, mostrando que "meu-container" está em RUNNING.

#### Passo 9: Acessar o Container

Para acessar o shell do container, execute:

```bash
lxc exec meu-container -- /bin/sh
```

Obs.: O Alpine Linux utiliza **/bin/sh** por padrão. Você estará dentro do container e poderá executar comandos como em qualquer sistema Linux.

#### Passo 10: Verificar o Container em Execução com **ps aux**

No sistema host, para listar todos os processos relacionados ao LXD, use:

```bash
ps aux | grep lxd
```

Dentro do container, você pode verificar os processos em execução com:

```bash
ps aux
```

#### Passo 11: Parar e Remover o Container

##### 11.1. Parar o Container

Execute:

```bash
lxc stop meu-container
```

para parar o container.

##### 11.2. Verificar se o Container Parou

Utilize:

```bash
lxc list
```

para confirmar que o estado do container é STOPPED.

##### 11.3. Remover o Container

Execute:

```bash
lxc delete meu-container
```

para remover o container.
