---
title: Interagire con contenitori Windows
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Informazioni su come eseguire Azure Dev Spaces in un cluster esistente con i contenitori di Windows
keywords: Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori, contenitori di Windows
ms.openlocfilehash: 131f69d42795b857a53fc21b760a7275a6826bb8
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212472"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Interagire con i contenitori di Windows usando Azure Dev Spaces

È possibile abilitare Azure Dev Spaces per gli spazi dei nomi Kubernetes nuovi ed esistenti. Azure Dev Spaces eseguirà e instrumenterà i servizi eseguiti in contenitori Linux. Tali servizi possono anche interagire con le applicazioni eseguite in contenitori Windows nello stesso spazio dei nomi. Questo articolo illustra come usare gli spazi di sviluppo per eseguire i servizi in uno spazio dei nomi con i contenitori di Windows esistenti. A questo punto, non è possibile eseguire il debug o la connessione a contenitori Windows con Azure Dev Spaces.

## <a name="set-up-your-cluster"></a>Configurare il cluster

Questo articolo presuppone che sia già presente un cluster con pool di nodi Linux e Windows. Se è necessario creare un cluster con pool di nodi Linux e Windows, è possibile seguire le istruzioni riportate [qui][windows-container-cli].

Connettersi al cluster usando [kubectl][kubectl], il client da riga di comando Kubernetes. Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco di nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

L'output di esempio seguente mostra un cluster con un nodo Windows e Linux. Prima di procedere, verificare che lo stato sia *pronto* per ogni nodo.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Applicare una [macchia][using-taints] ai nodi di Windows. Il guasto nei nodi di Windows impedisce agli spazi di sviluppo di pianificare i contenitori Linux da eseguire nei nodi Windows. Il comando di esempio seguente consente di applicare una macchia al nodo *aksnpwin987654* di Windows dell'esempio precedente.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Quando si applica una macchia a un nodo, è necessario configurare una tolleranza corrispondente nel modello di distribuzione del servizio per eseguire il servizio in tale nodo. L'applicazione di esempio è già configurata con una [tolleranza corrispondente][sample-application-toleration-example] al Tainio configurato nel comando precedente.

## <a name="run-your-windows-service"></a>Eseguire il servizio Windows

Eseguire il servizio Windows nel cluster AKS e verificare che sia in uno stato di *esecuzione* . Questo articolo usa un' [applicazione di esempio][sample-application] per illustrare un servizio Windows e Linux in esecuzione nel cluster.

Clonare l'applicazione di esempio da GitHub e passare alla `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` Directory:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

L'applicazione di esempio usa [Helm 3][helm-installed] per eseguire il servizio Windows nel cluster. Passare alla `charts` Directory e usare Helm eseguire il servizio Windows:

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

Il comando precedente USA Helm per eseguire il servizio Windows nello spazio dei nomi *dev* . Se non si dispone di uno spazio dei nomi denominato *dev*, verrà creato.

Usare il `kubectl get pods` comando per verificare che il servizio Windows sia in esecuzione nel cluster. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Abilita Azure Dev Spaces

Abilitare gli spazi di sviluppo nello stesso spazio dei nomi utilizzato per eseguire il servizio Windows. Il comando seguente abilita gli spazi di sviluppo nello spazio dei nomi *dev* :

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>Aggiornare il servizio Windows per gli spazi di sviluppo

Quando si abilitano gli spazi di sviluppo in uno spazio dei nomi esistente con contenitori già in esecuzione, per impostazione predefinita gli spazi di sviluppo proveranno a instrumentare tutti i nuovi contenitori in esecuzione in tale spazio dei nomi. Gli spazi di sviluppo proveranno anche a instrumentare tutti i nuovi contenitori creati per il servizio già in esecuzione nello spazio dei nomi. Per impedire agli spazi di sviluppo di instrumentare un contenitore in esecuzione nello spazio dei nomi, aggiungere l'intestazione *No-proxy* a `deployment.yaml` .

Aggiungere `azds.io/no-proxy: "true"` al `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` file:

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

Usare `helm list` per elencare la distribuzione per il servizio Windows:

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

Nell'esempio precedente, il nome della distribuzione è *Windows-Service*. Aggiornare il servizio Windows con la nuova configurazione usando `helm upgrade` :

```cmd
helm upgrade windows-service . --namespace dev
```

Poiché è stato aggiornato `deployment.yaml` , gli spazi di sviluppo non tenterà di instrumentare il servizio.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Eseguire l'applicazione Linux con Azure Dev Spaces

Passare alla `webfrontend` Directory e usare i `azds prep` comandi e `azds up` per eseguire l'applicazione Linux nel cluster.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

Il `azds prep --enable-ingress` comando genera il grafico Helm e dockerfile per l'applicazione.

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

È possibile visualizzare il servizio in esecuzione aprendo l'URL pubblico, che viene visualizzato nell'output del comando azds up. In questo esempio l'URL pubblico è `http://dev.webfrontend.abcdef0123.eus.azds.io/`. Passare al servizio in un browser e fare clic su *About (informazioni* ) nella parte superiore. Verificare che venga visualizzato un messaggio dal servizio *mywebapi* contenente la versione di Windows usata dal contenitore.

![App di esempio che mostra la versione di Windows da mywebapi](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul funzionamento di Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Funzionamento di Azure Dev Spaces](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
