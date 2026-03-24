<div align="center">
<img src="https://user-images.githubusercontent.com/47891196/139104117-aa9c2943-37da-4534-a584-e4e5ff5bf69a.png" width="350px" />
</div>


# Estudo-K8S-IA
Estudo-K8S-IA

🚀 Criar o cluster com único comando
Execute:

```
bash
kind create cluster --name k8s-ia --config kind-config.yaml
```
--name k8s-ia → dá o nome ao cluster.

--config kind-cluster.yaml → usa o arquivo que define os nós.

🔍 Validar
Depois que terminar:

bash
kubectl get nodes
Você deve ver:

Código
NAME                  STATUS   ROLES           AGE   VERSION
k8s-ia-control-plane  Ready    control-plane   1m    v1.29.0
k8s-ia-worker         Ready    <none>          1m    v1.29.0
k8s-ia-worker2        Ready    <none>          1m    v1.29.0

🟡 Nível 2 – Semana 3

Deployments e Services
Pré-requisitos

Cluster Kubernetes funcional (pode ser Minikube, Kind ou um cluster gerenciado em nuvem).

kubectl instalado e configurado para acessar o cluster.

Conhecimento básico de Pods e Namespaces.

Passo 1 – Criar um Deployment Nginx com 3 réplicas
1. Crie um arquivo chamado nginx-deployment.yaml:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```
2. Aplique o manifesto:

```
kubectl apply -f nginx-deployment.yaml
```

3. Verifique os pods criados:

```
kubectl get pods
```

Passo 2 – Escalar Pods com kubectl scale

1. Escale para 5 réplicas:

```
kubectl scale deployment nginx-deployment --replicas=5
```

2. Confirme:

```
kubectl get pods
```

Passo 3 – Expor o serviço

ClusterIP (interno)

1. Crie o serviço:

```
kubectl expose deployment nginx-deployment --type=ClusterIP --port=80
```

2. Verifique:

```
kubectl get svc
```

NodePort (externo no nó)

1. Crie o serviço NodePort:

🔧 Opção 1 – Usar outro nome para o Service
Crie o serviço com um nome diferente:

```
bash
kubectl expose deployment nginx-deployment --name=nginx-service-nodeport --type=NodePort --port=80
```

🔧 Opção 2 – Alterar o Service existente
Se você já tem um Service ClusterIP e quer transformá-lo em NodePort, pode editar:

```
bash
kubectl edit svc nginx-deployment
```

Mude o campo spec.type de ClusterIP para NodePort.

Salve e saia.

O Kubernetes atualizará o serviço existente.

🔧 Opção 3 – Apagar e recriar
Se não precisa do Service atual:

```
bash
kubectl delete svc nginx-deployment
kubectl expose deployment nginx-deployment --type=NodePort --port=80
```

2. Descubra a porta atribuída:

```
kubectl get svc
```

→ Procure o campo NodePort (ex: 30080).

3. Acesse via navegador ou curl:

Pegue o IP de um nó do cluster (no Kind, geralmente é o próprio host Docker, ou você pode usar localhost):

```
kubectl get nodes -o wide
```

🔧 Como acessar o NodePort no Kind
1. Usar kubectl port-forward (mais simples)
Faça o forward da porta do serviço para o host:

```
kubectl port-forward svc/nginx-service-nodeport 8080:80
```

Agora você acessa pelo navegador ou curl:

curl http://localhost:8080

Passo 4 – Simular falha de Pod

1. Liste os pods:

```
kubectl get pods
```

2. Delete um pod manualmente:

```
kubectl delete pod <nome-do-pod>
```

3. Observe a recriação automática:

```
kubectl get pods -w
```
→ O ReplicaSet garante que o número de réplicas seja mantido.

Passo 5 – Rolling Update

1. Atualize a imagem do deployment:

```
kubectl set image deployment/nginx-deployment nginx=nginx:1.25
```

2. Acompanhe o rollout:
```
kubectl rollout status deployment/nginx-deployment
```

3. Se necessário, faça rollback:

```
kubectl rollout undo deployment/nginx-deployment
```

✅ Resultado esperado
Deployment criado com múltiplas réplicas.

Pods escalados dinamicamente.

Serviço exposto via ClusterIP e NodePort.

Pod recriado automaticamente após falha.

Rolling update aplicado e monitorado.
