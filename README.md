# Go Server - Kubernetes

Servidor HTTP em Go com deploy no Kubernetes, utilizando ConfigMaps, Secrets e Services.

## Endpoints

| Rota         | Descrição                                                  |
|--------------|------------------------------------------------------------|
| `/`          | Retorna nome e idade a partir de variáveis de ambiente     |
| `/configmap` | Lê e exibe o conteúdo do arquivo `myfamily/family.txt`     |
| `/secret`    | Exibe as variáveis `USER` e `PASSWORD` injetadas via Secret|

## Estrutura

```
.
├── server.go                   # Aplicação Go
├── Dockerfile                  # Build da imagem
└── k8s/
    ├── deployment.yaml         # Deployment (2 réplicas)
    ├── configmap-env.yaml      # ConfigMap com variáveis de ambiente
    ├── configmap-family.yaml   # ConfigMap montado como volume
    ├── secrets.yaml            # Secret com USER e PASSWORD
    ├── service.yaml            # Service ClusterIP
    ├── service_node_port.yaml  # Service NodePort
    └── service_lb.yaml         # Service LoadBalancer
```

## Como usar

```bash
# Build da imagem
docker build -t goserver .

# Aplicar os manifests no cluster
kubectl apply -f k8s/
```

## AVISO: Secrets

> **Nunca suba o arquivo `k8s/secrets.yaml` para o repositório remoto.**
>
> Ele contém dados sensíveis (usuário e senha) codificados em base64.
> Antes de fazer push, certifique-se de que a linha abaixo está **descomentada** no `.gitignore`:
>
> ```
> k8s/secrets.yaml
> ```
>
> Caso precise compartilhar a estrutura do Secret, use um template sem valores reais:
>
> ```yaml
> apiVersion: v1
> kind: Secret
> metadata:
>   name: "goserver-secret"
> type: Opaque
> data:
>   USER: "<base64>"
>   PASSWORD: "<base64>"
> ```
