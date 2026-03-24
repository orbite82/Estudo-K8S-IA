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