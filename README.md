# KCD Brasil SP 2026

## Security by Design na Prática: Admission Controllers e mTLS Salvando a Madrugada do SRE

### Pré-requisitos
- Terminal (bash, ksh, zsh) se for Windows pode ser o [Git Bash](https://git-scm.com/install/windows)
- Docker
- Cluster Kubernetes (kind)
- kubectl
- Helm
- Kyverno
- Istio

### Como Instalar Localmente (lab)

#### Windows (10 ou 11 64-bits)
> Garanta que tenha permissão de Administrador
##### WSL
> Usar o PowerShell
```
wsl --install
```
##### Docker
> Usar o PowerShell
###### Baixar e executar o instalador
Acesse o site oficial do Docker ([docker.com/products/docker-desktop](https://docker.com/products/docker-desktop)) e baixe o instalador Docker Desktop for Windows. Execute o executável baixado (Docker Desktop Installer.exe).

###### Concluir a instalação
Certifique-se de marcar a opção **Use WSL 2 instead of Hyper-V** durante o assistente. Conclua o processo e reinicie a máquina caso seja solicitado.

![Alt text](./resources/imgs/Use%20WSL%202%20instead%20of%20Hyper-V.png)

###### Validar a instalação
```
docker --version
docker run hello-world
```

##### kubectl
> Usar o PowerShell
```
winget install Kubernetes.kubectl
```

###### Validar a instalação
```
kubectl version
```

##### Cluster Kubernetes (kind)
> Usar o PowerShell
```
winget install Kubernetes.kind
```

###### Criar o cluster padrão (1 nó)
```
kind create cluster --name kcd-brasil-sp-2026
```

###### Validar a comunicação e status dos nós
```
kubectl cluster-info --context kind-kcd-brasil-sp-2026
kubectl get nodes
```

###### Validar o funcionamento criando um pod
```
kubectl run nginx --image=nginx
kubectl get pods nginx
echo "Aguardar até o pod ficar com STATUS \"Running\""
sleep 60
kubectl exec -it nginx -- nginx -version
```

##### Helm
> Usar o PowerShell
```
winget install Helm.Helm
```
##### Validar a instalação do binário
```
helm version
```

##### Kyverno
> Usar o PowerShell
```
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
helm install kyverno kyverno/kyverno -n kyverno --create-namespace
```

###### Validar instalação do Kyverno
```
helm list -A
```

##### Istio
> Usar o PowerShell
##### Adicionar o repositório oficial do Istio
```
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
```

##### Instalar o chart base (CRDs)
```
helm install istio-base istio/base \
  -n istio-system \
  --create-namespace \
  --set defaultRevision=default \
  --wait
```

##### Instalar o Istiod
```
helm install istiod istio/istiod \
  -n istio-system \
  --wait
```

##### Habilitar injeção automática de sidecar
```
kubectl create namespace demo
kubectl label namespace demo istio-injection=enabled
```

##### Testando se a injeção está funcionando
```
kubectl run nginx-com-istio --image=nginx -n demo
kubectl get pod nginx-com-istio -n demo
```

###### Validar instalação do Istio
```
helm list -A
```
---
#### Linux (Debian, Ubuntu, Mint)
##### Docker
###### Limpar versões antigas
```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove -y $pkg; done
```
###### Instalar dependências e chave GPG oficial
```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
###### Configurar o repositório
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
###### Instalar a Docker Engine e plugins
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
###### Configurar permissões sem sudo
```bash
sudo usermod -aG docker $USER
newgrp docker
```
###### Validar a execução
```bash
docker run hello-world
```

##### kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

###### Validar a instalação
```
kubectl version
```
##### Cluster Kubernetes (kind)
###### Linux (AMD64)
```
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```


###### Criar o cluster padrão (1 nó)
```
kind create cluster --name kcd-brasil-sp-2026
```

###### Validar a comunicação e status dos nós
```
kubectl cluster-info --context kind-kcd-brasil-sp-2026
kubectl get nodes
```

###### Validar o funcionamento criando um pod
```
kubectl run nginx --image=nginx
kubectl get pods nginx
echo "Aguardar até o pod ficar com STATUS \"Running\""
sleep 60
kubectl exec -it nginx -- nginx -version
```

##### Helm
```
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
##### Validar a instalação do binário
```
helm version
```

##### Kyverno
```
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
helm install kyverno kyverno/kyverno -n kyverno --create-namespace
```

###### Validar instalação do Kyverno
```
helm list -A
```
##### Istio
##### Adicionar o repositório oficial do Istio
```
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
```

##### Instalar o chart base (CRDs)
```
helm install istio-base istio/base \
  -n istio-system \
  --create-namespace \
  --set defaultRevision=default \
  --wait
```

##### Instalar o Istiod
```
helm install istiod istio/istiod \
  -n istio-system \
  --wait
```

##### Habilitar injeção automática de sidecar
```
kubectl create namespace demo
kubectl label namespace demo istio-injection=enabled
```

##### Testando se a injeção está funcionando
```
kubectl run nginx-com-istio --image=nginx -n demo
kubectl get pod nginx-com-istio -n demo
```
---
#### MacOS
##### Docker
###### Verificar o processador
Clique no menu Apple > Sobre Este Mac para conferir se o Mac usa processador Apple Silicon (M1/M2/M3/M4) ou Intel.

###### Baixar o Docker Desktop
Acesse [docker.com/products/docker-desktop](https://docker.com/products/docker-desktop) e escolha a versão correspondente:
- Mac with Apple silicon (chips série M)
- Mac with Intel chip (processadores Intel)

###### Instalar a aplicação
Abra o arquivo .dmg baixado e arraste o ícone do Docker para a pasta Applications (Aplicativos).

###### Iniciar e conceder permissões
Abra o Docker a partir do Launchpad ou pasta Aplicativos. Aceite os termos de serviço e confirme com a senha de administrador quando o macOS solicitar permissão para instalar componentes de rede e socket.

###### Validar pelo Terminal
```bash
docker --version
docker run hello-world
```

##### kubectl
```
brew install kubectl
```

###### Validar a instalação
```
kubectl version
```

##### Cluster Kubernetes (kind)
```
brew install kind
```


###### Criar o cluster padrão (1 nó)
```
kind create cluster --name kcd-brasil-sp-2026
```

###### Validar a comunicação e status dos nós
```
kubectl cluster-info --context kind-kcd-brasil-sp-2026
kubectl get nodes
```

###### Validar o funcionamento criando um pod
```
kubectl run nginx --image=nginx
kubectl get pods nginx
echo "Aguardar até o pod ficar com STATUS \"Running\""
sleep 60
kubectl exec -it nginx -- nginx -version
```

##### Helm
```
brew install helm
```

##### Validar a instalação do binário
```
helm version
```
##### Kyverno
```
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update
helm install kyverno kyverno/kyverno -n kyverno --create-namespace
```

###### Validar instalação do Kyverno
```
helm list -A
```
##### Istio
##### Adicionar o repositório oficial do Istio
```
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
```

##### Instalar o chart base (CRDs)
```
helm install istio-base istio/base \
  -n istio-system \
  --create-namespace \
  --set defaultRevision=default \
  --wait
```

##### Instalar o Istiod
```
helm install istiod istio/istiod \
  -n istio-system \
  --wait
```

##### Habilitar injeção automática de sidecar
```
kubectl create namespace demo
kubectl label namespace demo istio-injection=enabled
```

##### Testando se a injeção está funcionando
```
kubectl run nginx-com-istio --image=nginx -n demo
kubectl get pod nginx-com-istio -n demo
```
---