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

===========================================================================================================

# Services

Exemplo completo de YAML para Services (ClusterIP e NodePort) alinhado exatamente com essa semana de estudos (Deployments + Services). Vou incluir também o Deployment do Nginx para você testar tudo no seu cluster com kind.

📦 1. Deployment (nginx com 3 réplicas)

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
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

🌐 2. Service - ClusterIP (comunicação interna)

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip
spec:
  type: ClusterIP
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

🌍 3. Service - NodePort (acesso externo)

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30007
```

👉 Agora você pode acessar via:

```
http://<IP_DO_NODE>:30007
```

🚀 Como aplicar tudo

```
kubectl apply -f deployment.yaml
kubectl apply -f service-clusterip.yaml
kubectl apply -f service-nodeport.yaml
```

🔍 Testes importantes (Lab)
1. Ver pods

```
kubectl get pods -o wide
```
2. Escalar

```
kubectl scale deployment nginx-deployment --replicas=5
```
3. Ver services

```
kubectl get svc
```
4. Testar dentro do cluster

```
kubectl run curl --image=curlimages/curl -it --rm -- sh
curl nginx-clusterip
```
💥 Simular falha de pod

```
kubectl get pods -w -o wide

kubectl delete pod <nome-do-pod>

```
👉 O Kubernetes recria automaticamente (ReplicaSet).

⚠️ Dica importante para KIND

O NodePort no kind pode não funcionar direto via localhost. Use:

```
kubectl port-forward svc/nginx-nodeport 8080:80
```
Em caso de erro ou uso da Porta:

🔍 Verificar quem está usando a porta
No Linux, você pode rodar:

```
sudo lsof -i :8080
```

ou

```
sudo netstat -tulnp | grep 8080
```

Isso mostra qual processo está ocupando a porta.

🛠️ Encerrar o processo que ocupa a porta
Depois de identificar o PID (Process ID), você pode encerrar com:

```
sudo kill -9 <PID>
```

🔄 Usar outra porta local
Se não quiser encerrar o processo, basta escolher outra porta livre:

```
kubectl port-forward svc/nginx-nodeport 9090:80
```

Isso vai mapear a porta 9090 da sua máquina para a porta 80 do serviço no cluster.

Depois acesse:

```
http://localhost:9090
```
===========================================================================================================

# 4 Semana
ConfigMaps e Secrets
Injeção de variáveis de ambiente e dados sensíveis
- Criar ConfigMap para configuração da aplicação.
- Criar Secret e montar no pod.
- Atualizar ConfigMap e observar mudanças sem redeploy.

```
```

```
```

```
```

```
```
```
```

```
```
```
```

```
```
```
```

```
```
```
```

```
```
```
```

```
```


```
```

```
```

```
```

```
```

```
```

```
```
```
```

```
```

```
```

```
```
```
```

```
```
```
```

```
```
```
```

```
```
```
```

```
```
```
```

```
```

