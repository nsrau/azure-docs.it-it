---
title: Interagire con contenitori Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Informazioni su come eseguire Spazi di sviluppo di Azure in un cluster esistente con contenitori di Windows
keywords: Spazi di sviluppo di Azure, spazi dev, docker, Kubernetes, Azure, AKS, servizio Azure Kubernetes, contenitori, contenitori WindowsAzure Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Windows containers
ms.openlocfilehash: 0b3f221c9e62343a02ba8742e4cf988c7cf26c12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240492"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interagire con i contenitori di Windows usando Gli spazi di sviluppo di AzureInteract with Windows containers using Azure Dev Spaces

È possibile abilitare gli spazi di sviluppo di Azure in spazi dei nomi Kubernetes nuovi ed esistenti. Azure Dev Spaces verrà eseguito e strumentazione dei servizi che vengono eseguiti in contenitori Linux.Azure Dev Spaces will run and instrument services that run on Linux containers. Tali servizi possono anche interagire con le applicazioni eseguite in contenitori Windows nello stesso spazio dei nomi. Questo articolo illustra come usare Spazi di sviluppo per eseguire servizi in uno spazio dei nomi con contenitori windows esistenti. Al momento, non è possibile eseguire il debug o connettersi a contenitori Windows con Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Configurare il cluster

In questo articolo si presuppone che si dispone già di un cluster con pool di nodi Linux e Windows.This article assumes you already have a cluster with both Linux and Windows node pools. Se è necessario creare un cluster con pool di nodi Linux e Windows, è possibile seguire le istruzioni [qui][windows-container-cli].

Connettersi al cluster utilizzando [kubectl][kubectl], il client della riga di comando Kubernetes. Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco dei nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

L'output di esempio seguente mostra un cluster con un nodo Windows e Linux.The following example output shows a cluster with both a Windows and Linux node. Assicurarsi che lo stato sia *Pronto* per ogni nodo prima di procedere.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Applicare una macchia ai nodi di Windows.Apply a [taint][using-taints] to your Windows nodes. La macchia nei nodi di Windows impedisce agli spazi dev di pianificare i contenitori Linux per l'esecuzione nei nodi di Windows. Il comando seguente consente di applicare una macchia al nodo Windows *aksnpwin987654* dell'esempio precedente.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Quando si applica una macchia a un nodo, è necessario configurare una torazione corrispondente nel modello di distribuzione del servizio per eseguire il servizio in tale nodo. L'applicazione di esempio è già configurata con una [torazione corrispondente][sample-application-toleration-example] alla macchia configurata nel comando precedente.

## <a name="run-your-windows-service"></a>Eseguire il servizio Windows

Eseguire il servizio Windows nel cluster AKS e verificare che sia in *esecuzione.* Questo articolo usa [un'applicazione di esempio][sample-application] per illustrare un servizio Windows e Linux in esecuzione nel cluster.

Clonare l'applicazione di esempio `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` da GitHub e passare alla directory:Clone the sample application from GitHub and navigate into the directory:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

L'applicazione di esempio utilizza [Helm 3][helm-installed] per eseguire il servizio Windows nel cluster. Passare alla `charts` directory e usare Helm per eseguire il servizio Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Il comando precedente usa Helm per eseguire il servizio Windows nello spazio dei nomi *dev.* Se non si dispone di uno spazio dei nomi denominato *dev*, verrà creato.

Usare `kubectl get pods` il comando per verificare che il servizio Windows sia in esecuzione nel cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Abilitare gli spazi di sviluppo di AzureEnable Azure Dev Spaces

Abilitare Dev Spaces nello stesso spazio dei nomi usato per eseguire il servizio Windows.Enable Dev Spaces in the same namespace you used to run your Windows service. Il comando seguente abilita gli spazi di sviluppo nello spazio dei nomi *dev:The* following command enables Dev Spaces in the dev namespace:

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Aggiornare il servizio Windows per Dev Spaces

Quando si abilita Spazi di sviluppo in uno spazio dei nomi esistente con contenitori già in esecuzione, per impostazione predefinita, Dev Spaces tenterà di instrumentare eventuali nuovi contenitori eseguiti in tale spazio dei nomi. Dev Spaces tenterà inoltre di instrumentare tutti i nuovi contenitori creati per il servizio già in esecuzione nello spazio dei nomi. Per impedire a Dev Spaces di instrumentare un contenitore in `deployment.yaml`esecuzione nello spazio dei nomi, aggiungere l'intestazione *no-proxy* all'oggetto .

Aggiungi `azds.io/no-proxy: "true"` al `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` file:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

Usare `helm list` per elencare la distribuzione per il servizio Windows:Use to list the deployment for your Windows service:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

Nell'esempio precedente, il nome della distribuzione è *windows-service*. Aggiornare il servizio Windows `helm upgrade`con la nuova configurazione utilizzando:

```cmd
helm upgrade windows-service . --namespace dev
```

Dal momento `deployment.yaml`che hai aggiornato il tuo , Dev Spaces non cercherà di instrumentare il tuo servizio.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Eseguire l'applicazione Linux con Azure Dev SpacesRun your Linux application with Azure Dev Spaces

Passare alla `webfrontend` directory e `azds prep` `azds up` usare i comandi e per eseguire l'applicazione Linux nel cluster.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

Il `azds prep --enable-ingress` comando genera il grafico Helm e dockerfiles per l'applicazione.

> [!TIP]
> Il [Dockerfile e il grafico Helm](../how-dev-spaces-works-prep.md#prepare-your-code) per il progetto vengono usati da Azure Dev Spaces per compilare ed eseguire il codice, ma è possibile modificare questi file se si vuole cambiare il modo in cui il progetto viene compilato ed eseguito.

Il `azds up` comando esegue il servizio nello spazio dei nomi.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

È possibile visualizzare il servizio in esecuzione aprendo l'URL pubblico, visualizzato nell'output del comando azds up. In questo esempio l'URL pubblico è `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Passare al servizio in un browser e fare clic su *Informazioni* in alto. Verificare che venga visualizzato un messaggio dal servizio *mywebapi* contenente la versione di Windows in uso del contenitore.

![App di esempio che mostra la versione di Windows di mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come Azure Dev Spaces consente di sviluppare applicazioni più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi.

> [!div class="nextstepaction"]
> [Sviluppo in team in Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
