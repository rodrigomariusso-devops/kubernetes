# Documentação da Configuração do Cluster Kind

## Visão Geral

Este documento descreve a configuração de um cluster Kubernetes criado usando o **kind** (Kubernetes IN Docker). A configuração permite a criação de um cluster com um nó de controle e dois nós de trabalho, além de definir mapeamentos de porta para facilitar o acesso a serviços rodando no cluster.

## Estrutura do Código

### 1. **Definições Básicas**

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
```

- **kind**: Este campo especifica que o objeto a ser criado é um cluster.
- **apiVersion**: Define a versão da API a ser utilizada para a configuração do cluster. Neste caso, `kind.x-k8s.io/v1alpha4`.

### 2. **Definição dos Nós do Cluster**

A seção `nodes` especifica os diferentes nós que farão parte do cluster.

#### Nó de Controle

```yaml
- role: control-plane
  kubeadmConfigPatches:
  - |
    kind: InitConfiguration
    nodeRegistration:
      kubeletExtraArgs:
        node-labels: "ingress-ready=true"
```

- **role: control-plane**: Indica que este nó será o nó de controle do cluster, responsável pela gestão do estado do cluster.
- **kubeadmConfigPatches**: Permite aplicar patches de configuração adicionais ao `kubeadm` durante a inicialização do nó.
  - **InitConfiguration**: Define a configuração inicial do nó.
    - **nodeRegistration**: Configurações relacionadas ao registro do nó.
      - **kubeletExtraArgs**: Parâmetros adicionais para o Kubelet (o agente que executa em cada nó).
        - **node-labels**: Define uma etiqueta personalizada para o nó, neste caso, `ingress-ready=true`. Essa etiqueta pode ser utilizada para indicar que o nó está pronto para gerenciar recursos de ingressos.

#### Mapeamento de Portas

```yaml
  extraPortMappings:
  - containerPort: 80
    hostPort: 80
    listenAddress: "0.0.0.0"
    protocol: TCP
  - containerPort: 443
    hostPort: 443
    listenAddress: "0.0.0.0"
    protocol: TCP
  - containerPort: 8080
    hostPort: 8080
    listenAddress: "0.0.0.0"
    protocol: TCP
```

Esta seção define como as portas do contêiner serão mapeadas para as portas do host:

- **containerPort**: A porta dentro do contêiner Kubernetes.
- **hostPort**: A porta do host que será mapeada para a porta do contêiner.
- **listenAddress**: O endereço IP onde o host escutará. `0.0.0.0` significa que o host irá escutar em todas as interfaces de rede disponíveis.
- **protocol**: O protocolo de comunicação utilizado, que neste caso é TCP.

As portas mapeadas são:

- **Porta 80**: Usada para tráfego HTTP.
- **Porta 443**: Usada para tráfego HTTPS.
- **Porta 8080**: Usada frequentemente para aplicações web.

#### Nós de Trabalho

```yaml
- role: worker
- role: worker
```

Esta parte da configuração define dois nós de trabalho. Esses nós serão responsáveis por executar as cargas de trabalho e os pods que são criados no cluster.

## Conclusão

Esta configuração do cluster `kind` permite a criação de um ambiente Kubernetes local com um nó de controle que pode gerenciar recursos de ingressos e dois nós de trabalho. O mapeamento de portas para o host garante que os serviços rodando no cluster possam ser acessados facilmente através das portas definidas (80, 443 e 8080).

### Criação do Cluster

Para criar o cluster utilizando esta configuração, execute o seguinte comando:

```bash
kind create cluster --config config.yaml --name estudo-devops
```

Certifique-se de que o Docker esteja instalado e em execução, pois o `kind` utiliza contêineres Docker para criar o cluster Kubernetes.