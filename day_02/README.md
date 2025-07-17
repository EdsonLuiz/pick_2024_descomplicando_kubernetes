# Day 02

## Pod
Menor unidade dentro de um cluster Kubernetes.
Um Pod pode conter um ou mais containers, que compartilham os mesmos recursos.

### Criando um Pod
Pod através de comando

```bash
kubectl run <pod_name> --image nginx --port 80
```

### Detalhes sobre Pods
Listar todos os Pods em execução no cluster, dentro do namespace default.

```bash
kubectl get pods
```

Por padrão o Kubernetes cria todos os objectos no namespace default.

Para ver os Pods em execução em todos os namespaces:

```bash
kubectl get pods [-A | --all-namespaces]
```

Para ver todos os Pods de uma namespace específica.

```bash
kubectl get pods -n <namespace>
```

Ver mais detalhes de um Pod em um formato específico ou com mais detalhes:

```bash
kubectl get pods <pod_name> -o <yaml | json | wide>
```

Mais detalhes do Pod com describe:

```bash
kubectl gdescribe pods <pod_name>
```

### Removendo Pods

```bash
kubectl delete pods <pod_name> 
```

### Criando Pod pelo arquivo de manifesto.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-giropops # nome do Pod que será criado
  labels: # utilizado para organização e identificação
    type: pod
    run: giropops # label run
    env: development
spec: # especificações do Pod
  containers: # containers dentro do pod
  - name: nginx # nome do container
    image: nginx # imagem base
    ports: # portas expostas por este container
    - containerPort: 80
```

### Criando o Pod especificando o arquivo de manifesto.

```shell
kubectl apply -f pod.yaml
```

Ao utilizar `apply` ele aplica o arquivo de manifesto ele cria o objeto descrito, caso o objeto já exista, ele será atualizado com as informações do arquivo de manifesto.

Ao utilizar `create` ele aplica o arquivo de manifesto criando o objeto somente se ele ainda não existir.

### Logs dos Pods.

Para verificar os logs dos containers dentro de um Pod:

```shell
kubectl logs <pod_name>
```

Ver os logs do container em tempo real:

```shell
kubectl logs -f <pod_name>
```

### Deletando o Pod

```shell
kubectl delete pods <pod_name>
```