---
title: Usare Azure Dev Spaces per interagire con i contenitori di Windows
services: azure-dev-spaces
ms.date: 07/25/2019
ms.topic: conceptual
description: Informazioni su come eseguire Azure Dev Spaces in un cluster esistente con i contenitori di Windows
keywords: Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori, contenitori di Windows
ms.openlocfilehash: ad91d8e48a9242795d4f5d5cd165e658339ebe08
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280039"
---
# <a name="use-azure-dev-spaces-to-interact-with-windows-containers"></a>Usare Azure Dev Spaces per interagire con i contenitori di Windows

È possibile abilitare Azure Dev Spaces per gli spazi dei nomi Kubernetes nuovi ed esistenti. Azure Dev Spaces eseguirà e instrumenterà i servizi eseguiti in contenitori Linux. Tali servizi possono anche interagire con le applicazioni eseguite in contenitori Windows nello stesso spazio dei nomi. Questo articolo illustra come usare gli spazi di sviluppo per eseguire i servizi in uno spazio dei nomi con i contenitori di Windows esistenti.

## <a name="set-up-your-cluster"></a>Configurare il cluster

Questo articolo presuppone che sia già presente un cluster con pool di nodi Linux e Windows. Se è necessario creare un cluster con pool di nodi Linux e Windows, è possibile seguire le istruzioni riportate [qui][windows-container-cli].

Connettersi al cluster usando [kubectl][kubectl], il client da riga di comando Kubernetes. Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][az-aks-get-credentials]. Questo comando scarica le credenziali e configura l'interfaccia della riga di comando di Kubernetes per usarli.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Per verificare la connessione al cluster, usare il comando [kubectl get][kubectl-get] per restituire un elenco dei nodi del cluster.

```azurecli-interactive
kubectl get nodes
```

L'output di esempio seguente mostra un cluster con un nodo Windows e Linux. Prima di procedere, verificare che lo stato sia *pronto* per ogni nodo.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
aksnpwin987654                      Ready    agent   108s   v1.14.1
```

Applicare una [macchia][using-taints] ai nodi di Windows. Il guasto nei nodi di Windows impedisce agli spazi di sviluppo di pianificare i contenitori Linux da eseguire nei nodi Windows. Il comando di esempio seguente consente di applicare una macchia al nodo *aksnpwin987654* di Windows dell'esempio precedente.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> Quando si applica una macchia a un nodo, è necessario configurare una tolleranza corrispondente nel modello di distribuzione del servizio per eseguire il servizio in tale nodo. L'applicazione di esempio è già configurata con una [tolleranza corrispondente][sample-application-toleration-example] al Tainio configurato nel comando precedente.

## <a name="run-your-windows-service"></a>Eseguire il servizio Windows

Eseguire il servizio Windows nel cluster AKS e verificare che sia in uno stato di *esecuzione* . Questo articolo usa un' [applicazione di esempio][sample-application] per illustrare un servizio Windows e Linux in esecuzione nel cluster.

Clonare l'applicazione di esempio da GitHub e passare alla directory `dev-spaces/samples/existingWindowsBackend/mywebapi-windows`:

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

L'applicazione di esempio usa [Helm][helm-installed] per eseguire il servizio Windows nel cluster. Installare Helm nel cluster e concedere a tale utente le autorizzazioni corrette:

```console
helm init --wait
kubectl create serviceaccount --namespace kube-system tiller
kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
``` 

Passare alla directory `charts` ed eseguire il servizio Windows:

```console
cd charts/
helm install . --namespace dev
```

Il comando precedente USA Helm per eseguire il servizio Windows nello spazio dei nomi *dev* . Se non si dispone di uno spazio dei nomi denominato *dev*, verrà creato.

Usare il comando `kubectl get pods` per verificare che il servizio Windows sia in esecuzione nel cluster. 

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

Quando si abilitano gli spazi di sviluppo in uno spazio dei nomi esistente con contenitori già in esecuzione, per impostazione predefinita gli spazi di sviluppo proveranno a instrumentare tutti i nuovi contenitori in esecuzione in tale spazio dei nomi. Gli spazi di sviluppo proveranno anche a instrumentare tutti i nuovi contenitori creati per il servizio già in esecuzione nello spazio dei nomi. Per impedire agli spazi di sviluppo di instrumentare un contenitore in esecuzione nello spazio dei nomi, aggiungere l'intestazione *No-proxy* al `deployment.yaml`.

Aggiungere `azds.io/no-proxy: "true"` al file `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml`:

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
$ helm list
NAME            REVISION    UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
gilded-jackal   1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

Nell'esempio precedente, il nome della distribuzione è *gilded-Jackal*. Aggiornare il servizio Windows con la nuova configurazione usando `helm upgrade`:

```cmd
$ helm upgrade gilded-jackal . --namespace dev
Release "gilded-jackal" has been upgraded.
```

Dal momento che è stato aggiornato il `deployment.yaml`, gli spazi di sviluppo non tenterà di instrumentare il servizio.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Eseguire l'applicazione Linux con Azure Dev Spaces

Passare alla directory `webfrontend` e usare i comandi `azds prep` e `azds up` per eseguire l'applicazione Linux nel cluster.

```console
cd ../../webfrontend-linux/
azds prep --public
azds up
```

Il `azds prep --public` comando genera il grafico Helm e Dockerfile per l'applicazione. Il `azds up` comando esegue il servizio nello spazio dei nomi.

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

Informazioni su come Azure Dev Spaces consente di sviluppare applicazioni più complesse in più contenitori e su come è possibile semplificare lo sviluppo collaborativo usando versioni o rami diversi del codice in spazi diversi.

> [!div class="nextstepaction"]
> [Sviluppo in team in Azure Dev Spaces][team-development-qs]

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[team-development-qs]: ../quickstart-team-development.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[team-development]: ../team-development-netcore.md
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
