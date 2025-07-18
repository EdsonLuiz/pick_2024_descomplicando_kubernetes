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

### Pod com mais de um container

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
  - name: alpine
    image: alpine
    args:
    - sleep
    - "1800"
```

Crie o Pod com base no arquivo de manifesto.

```shell
kubectl apply -f pod-multi.yaml
```

Verifique o estado do pod depois de criado.

```shell
kubectl get pods
kubectl describe pods pod-giropops
```

### Attach e Exec

Utilizados para entrar no container ou executar algum comando diretamente no container.

Conectando ao container alpine:

```shell
kubectl attach <pod_name> -c <container_name_inside_pod>
```

Como o `attach` se conecta ao processo com `PID 1`, em nosso caso do container alpine com sleep, nada aparece, pois o sleep não aceita input.

Alterando o container alpine do Pod.

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
  - name: alpine
    image: alpine
    command: ["/bin/sh"] # Use /bin/sh as the entrypoint
    args: ["-c", "while true; do /bin/sh; done"] # Keep the container running
    stdin: true # Keep stdin open
    tty: true # Allocate a TTY
```

Agora é possível utilizar o `attach` em modo interativo, mas ficaremos presos ao processo no PID 1.
Utilize o `Attach` somente para se conectar ao container.

```shell
kubectl attach -ti <pod_name> -c <container_name_inside_pod>
```

Para executar comandos dentro do containe utilize o `exec`.

```shell
kubectl exec <pod_name> -c <container_name> -- ls # executa ls dentro do container
kubectl exec <pod_name> -c <container_name> -ti -- sh
```

Aqui o `-ti` é utilizado para que crie um processo com interatividade pelo terminal, tendo o comportamento do comando `attach`, mas neste caso foi criado um processo que não existia na criação do container. Isto torna possível a conexão com container que não tenham o `bash / sh` no seu `PID 1`. Agora é possível utilizar `ctrl + d` para sair do container sem ficar preso no processo principal.

