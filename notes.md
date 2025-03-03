# Introdução aos Containers
Containers são ambientes isolados que compartilham o kernel do sistema operacional, porém possuem suas próprias bibliotecas, binários e arquivos de configuração. Eles rodam como processos isolados, mas utilizando o mesmo kernel do sistema. Esse isolamento permite limitar recursos como CPU, memória, disco e rede, tornando os containers mais leves que máquinas virtuais, que necessitam de um sistema operacional completo.

# Criando Ambiente Isolado com unshare
O comando unshare é utilizado para criar novos namespaces, isolando diversos recursos do sistema. No exemplo, criamos um ambiente isolado que separa namespaces de PID, rede e sistema de arquivos e montagens, iniciando um bash no novo ambiente:

**Comando:**
```bash
sudo unshare -p -m -n -f --mount-proc bash
```
**Parâmetros:**

- **-p**: Cria um novo namespace de PID, fazendo com que os processos tenham identificadores independentes do sistema principal.
- **-m**: Cria um novo namespace de montagem, isolando o sistema de arquivos.
- **-n**: Cria um novo namespace de rede, isolando as interfaces de rede.
- **-f**: Força a criação do novo processo dentro do namespace.
- **--mount-proc**: Monta o sistema de arquivos proc, permitindo visualizar somente os processos do ambiente isolado.

# Identificando o Processo bash Criado com unshare
Após iniciar o bash com o unshare, ele passa a ser o PID 1 dentro do novo namespace. Para identificar o PID global (usado para mover o processo para o cgroup), basta executar:
```bash
ps -ef | grep unshare
```
Outra ferramenta útil é o pstree, que permite visualizar a árvore de processos. Basta substituir **<PID_DO_UNSHARE>**:
```bash
pstree -p <PID_DO_UNSHARE>
```
Assim, você pode identificar o PID do bash iniciado pelo comando unshare.

# Limitando Recursos com cgroup
Para controlar o uso de CPU e memória do processo, cria-se um cgroup. Siga os passos:

**Criação do cgroup:**
```bash
sudo mkdir /sys/fs/cgroup/mycontainer
```
**Configuração de limites:**
```bash
echo "50000 100000" | sudo tee /sys/fs/cgroup/mycontainer/cpu.max
```
- **CPU**: Limitar o uso a 50% de um núcleo:
- **Memória**: Limitar o uso a 100 MB:
```bash
echo "100M" | sudo tee /sys/fs/cgroup/mycontainer/memory.max
```
**Movendo o processo bash para o cgroup:**
Utilize o PID do bash identificado anteriormente:
```bash
echo <PID_DO_BASH> | sudo tee /sys/fs/cgroup/mycontainer/cgroup.procs
```
Dessa forma, o bash e seus processos filhos terão os recursos limitados conforme a configuração.

# Executando um Processo no Ambiente Isolado
Dentro do bash isolado (e já movido para o cgroup), é possível executar processos que estarão sujeitos às restrições definidas. Por exemplo, para simular carga na CPU e consumo de memória, pode-se utilizar o comando stress:
```bash
stress --cpu 2 --vm 1 --vm-bytes 80M --timeout 30
```
Esse comando gera carga na CPU e consome memória, permitindo testar os limites impostos pelo cgroup.

# Analisando o Uso de Recursos com cpu.stat
É possível verificar se as limitações estão sendo aplicadas corretamente ao monitorar o arquivo cpu.stat, que contém métricas de uso de CPU do cgroup. Para exibir o status:
```bash
cat /sys/fs/cgroup/mycontainer/cpu.stat
```
**Principais campos:**

- **usage_usec**: Tempo total de CPU utilizado pelos processos do cgroup (em microssegundos).
- **nr_throttled**: Número de vezes que o cgroup foi limitado.
- **throttled_usec**: Tempo total (em microssegundos) durante o qual os processos foram limitados.

# Cálculo da Porcentagem de Throttle com awk
Para entender a proporção do tempo em que os processos foram limitados, pode-se calcular a porcentagem de throttle utilizando a seguinte fórmula:
Porcentagem de Throttle = (throttled_usec / (throttled_usec + usage_usec)) * 100
Exemplo usando awk (que lê os valores do arquivo cpu.stat e realiza o cálculo):
```bash
awk '{if ($1 == "usage_usec") usage=$2; if ($1 == "throttled_usec") throttle=$2} END {print "Porcentagem de Throttle: " (throttle / (throttle + usage)) * 100 "%"}' /sys/fs/cgroup/mycontainer/cpu.stat
```
**Explicação do comando:**

- **$1** e **$2**: Representam o primeiro e segundo campo de cada linha do arquivo, respectivamente.
- Se o primeiro campo for **usage_usec**, o valor correspondente é armazenado em **usage**.
- Se for **throttled_usec**, o valor é armazenado em **throttle**.
- No bloco **END**, o comando calcula a porcentagem de throttle e exibe o resultado.

# Monitoramento em Tempo Real
Para observar as alterações nos valores do arquivo cpu.stat em tempo real, utilize o comando watch que atualiza a cada segundo:
```bash
watch -n 1 cat /sys/fs/cgroup/mycontainer/cpu.stat
```
Isso permite acompanhar dinamicamente como o consumo de CPU varia conforme os processos executados.

# Dicas e Cuidados
**Criação de novo cgroup para cada teste:**
É recomendado criar um novo cgroup para cada teste, garantindo que não haja valores acumulados dos testes anteriores.

**Remoção do cgroup:**
Após a conclusão do teste, verifique se não há processos ativos no cgroup e remova-o para limpar o sistema.
```bash
sudo rmdir /sys/fs/cgroup/mycontainer
```
