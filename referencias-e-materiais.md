# Referências e Materiais Adicionais

## Principais Referências

Para aprofundar seu conhecimento e verificar as informações apresentadas no guia, recomendo os seguintes links e fontes:

- **[Open Containers Initiative (OCI) - runc](https://github.com/opencontainers/runc)**: Página oficial do runc, ferramenta usada para executar containers OCI.
- **[Docker Documentation](https://docs.docker.com/)**: Documentação oficial do Docker, abrangendo instalação, uso de imagens e manipulação de containers.
- **[containerd Documentation](https://containerd.io/docs/)**: Documentação oficial do containerd, descrevendo como gerenciar containers e usar o CLI ctr.
- **[Linux Filesystem Hierarchy - tmpfs](https://man7.org/linux/man-pages/man5/tmpfs.5.html)**: Explicação detalhada sobre o tmpfs, que é utilizado para diretórios temporários.
- **[Linux Kernel Documentation - procfs](https://www.kernel.org/doc/Documentation/filesystems/proc.txt)**: Informações sobre o sistema de arquivos /proc, que fornece informações sobre o sistema e processos.
- **[Linux Kernel Documentation - sysfs](https://www.kernel.org/doc/html/latest/filesystems/sysfs.html)**: Informações sobre o sysfs, que fornece uma interface para o kernel expor dispositivos do sistema.
- **[cgroups - Control Groups](https://man7.org/linux/man-pages/man7/cgroups.7.html)**: Documentação oficial sobre cgroups, explicando como limitar e monitorar os recursos do sistema.
- **[POSIX Message Queues (mqueue)](https://man7.org/linux/man-pages/man7/mq_overview.7.html)**: Informações sobre as filas de mensagens mqueue usadas para comunicação entre processos.

## Referências Adicionais com Maior Especificidade

### Introdução aos Containers

- **[Docker](https://www.docker.com/)**: Uma das plataformas mais populares para trabalhar com containers.
- **[Kubernetes](https://kubernetes.io/)**: Sistema de orquestração de containers amplamente utilizado.
- **[O que são Containers?](https://www.redhat.com/pt-br/topics/containers/what-are-linux-containers)**: Uma explicação detalhada sobre o conceito de containers.

### Namespaces e Isolamento de Recursos

- **[Namespaces Linux](https://man7.org/linux/man-pages/man7/namespaces.7.html)**: Documentação do kernel sobre namespaces.
- **[Visão geral de Namespaces](https://lwn.net/Articles/531114/)**: Artigo com exemplos práticos.

### Cgroups e Controle de Recursos

- **[Documentação do Kernel sobre Cgroups](https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v2.html)**: Informações detalhadas sobre cgroups.
- **[Introdução aos Cgroups](https://www.linux.com/topic/desktop/introduction-cgroups/)**: Tutorial sobre o uso de cgroups.

### Criação de um Container de Forma Manual

- **[Artigo sobre criação manual de containers](https://medium.com/@teddyking/linux-containers-lxc-101-part-1-31961a90e971)**: Passo a passo detalhado. (Este artigo usa LXC, mas ilustra os princípios básicos)

### LXC - Linux Containers

- **[Site oficial do LXC](https://linuxcontainers.org/)**
- **[Tutorial LXC](https://linuxcontainers.org/lxc/getting-started/)**

### Open Container Initiative (OCI)

- **[Site oficial da OCI](https://opencontainers.org/)**
- **[Especificações da OCI](https://github.com/opencontainers/)**: Detalhes das especificações de runtime e imagem.

### Sistemas de Arquivos

- **[tmpfs](https://man7.org/linux/man-pages/man5/tmpfs.5.html)**
- **[proc](https://man7.org/linux/man-pages/man5/proc.5.html)**
- **[sysfs](https://man7.org/linux/man-pages/man5/sysfs.5.html)**
- **[mqueue](https://man7.org/linux/man-pages/man7/mq_overview.7.html)**
- **[debugfs](https://www.kernel.org/doc/Documentation/filesystems/debugfs.txt)**
- **cgroupsfs**: A documentação do cgroups em si geralmente cobre o sistema de arquivos associado. Veja a documentação do kernel linkada acima.

### Containerd e runc

- **[Containerd](https://containerd.io/)**
- **[runc](https://github.com/opencontainers/runc)**

### Container Runtimes - Populares

- **[CRI-O](https://cri-o.io/)**
- **[Podman](https://podman.io/)**

### Docker, OCI e CNCF

- **[CNCF (Cloud Native Computing Foundation)](https://www.cncf.io/)**
