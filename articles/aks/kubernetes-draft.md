---
title: Sviluppare il servizio Azure Kubernetes (AKS) con DraftDevelop on Azure Kubernetes Service (AKS) with Draft
description: Usare Draft con il servizio Azure Container e con Registro Azure Container
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: b03256ee65a3c40d8a64d70b877c49e44e68f822
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595222"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Guida introduttiva: Sviluppare il servizio Azure Kubernetes (AKS) con Draft

Draft è uno strumento open source che consente di creare pacchetti ed eseguire contenitori di applicazioni in un cluster Kubernetes.Draft is an open-source tool that helps package and run application containers in a Kubernetes cluster. Con Draft, è possibile ridistribuire rapidamente un'applicazione in Kubernetes quando si verificano modifiche al codice senza dover eseguire il commit delle modifiche al controllo della versione. Per ulteriori informazioni su Draft, vedere la [documentazione di Draft su GitHub][draft-documentation].

In questo articolo viene illustrato come utilizzare Draft per creare un pacchetto ed eseguire un'applicazione in AKS.


## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker installato e configurato. Docker offre pacchetti che consentono di configurare Docker in un sistema [Mac][docker-for-mac], [Windows][docker-for-windows] o [Linux][docker-for-linux].
* [Helm v2 installato][helm-install].
* [Bozza installata][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Creare un cluster del servizio Azure Kubernetes

Creare un cluster del servizio Azure Kubernetes. I comandi seguenti creano un gruppo di risorse denominato MyResourceGroup e un cluster del servizio Azure Kubernetes denominato MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Creare un'istanza di Registro Azure Container
Per usare Bozza per eseguire l'applicazione nel cluster AKS, è necessario un registro contenitori di Azure per archiviare le immagini del contenitore. Nell'esempio seguente viene utilizzato [az acr create][az-acr-create] per creare un aCR denominato *MyDraftACR* nel gruppo di risorse *MyResourceGroup* con lo SKU *Basic.* È necessario fornire il proprio nome univoco del Registro di sistema. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Lo SKU *Basic* è un punto di ingresso con costi ottimali a fini di sviluppo, che assicura l'equilibrio tra spazio di archiviazione e velocità effettiva.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

L'output è simile all'esempio seguente: Prendere nota del valore *loginServer* per l'aCR poiché verrà utilizzato in un passaggio successivo. Nell'esempio seguente, *mydraftacr.azurecr.io* è il *loginServer* per *MyDraftACR*.

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```


Affinché Draft utilizzi l'istanza ACR, devi prima effettuare l'accesso. Utilizzare il comando [az acr login][az-acr-login] per accedere. Nell'esempio riportato di seguito verrà effettuato l'accesso a un ACR denominato *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Il comando restituisce un messaggio *Login riuscito una* volta completato.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Creare una relazione di trust tra il cluster del servizio Azure Container e Registro Azure Container

Il cluster AKS deve inoltre accedere all'aCR per estrarre le immagini del contenitore ed eseguirle. Consentire l'accesso all'ACR da AKS stabilendo una relazione di trust. Per stabilire l'attendibilità tra un cluster del servizio Azure Container e un'istanza di Registro Azure Container, concedere le autorizzazioni necessarie perché l'entità servizio di Azure Active Directory usata dal cluster del servizio Azure Container possa accedere all'istanza di Registro Azure Container. I comandi seguenti concedono autorizzazioni all'entità servizio del cluster *MyAKS* nel *MyResourceGroup* all'ACR *MyDraftACR* in *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Connettersi al cluster AKSConnect to your AKS cluster

Per connettersi al cluster Kubernetes dal computer locale si usa [kubectl][kubectl], il client da riga di comando di Kubernetes.

Se si usa Azure Cloud Shell, `kubectl` è già installato. È anche possibile installarlo in locale usando il comando [az servizio Azure Kubernetes install-cli][]:

```azurecli
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az aks get-credentials][]. L'esempio seguente ottiene le credenziali per il cluster AKS denominato *MyAKS* nel *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Creare un account di servizio per HelmCreate a service account for Helm

Prima di poter distribuire Helm in un cluster servizio Azure Kubernetes abilitato per il controllo degli accessi in base al ruolo, sono necessari un account del servizio e un'associazione di ruolo per il servizio Tiller. Per altre informazioni sulla sicurezza di Helm/Tiller in un cluster abilitato per il controllo degli accessi in base al ruolo, vedere [Tiller, Namespaces, and RBAC][tiller-rbac] (Tiller, spazi dei nomi ed RBAC). Se il cluster AKS non è abilitato al controllo degli accessi in base al ruolo, ignorare questo passaggio.

Creare un file denominato `helm-rbac.yaml` e copiarlo nel codice YAML seguente:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Creare l'account del servizio e l'associazione di ruolo con il comando `kubectl apply`:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Configurare Helm
Per distribuire un'applicazione Tiller di base in un cluster del servizio Azure Kubernetes, usare il comando [helm init][helm-init]. Se il cluster non è abilitato `--service-account` al controllo degli accessi in base al ruolo, rimuovere l'argomento e il valore.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Configurare Draft

Se Draft non è stato configurato nel `draft init`computer locale, eseguire:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

È inoltre necessario configurare Draft per utilizzare il *loginServer* dell'ACR. Il comando `draft config set` seguente `mydraftacr.azurecr.io` utilizza per utilizzare come Registro di sistema.

```console
draft config set registry mydraftacr.azurecr.io
```

Draft è stato configurato per l'utilizzo dell'ACR e Draft può eseguire il push delle immagini contenitore nell'aCR. Quando Draft esegue l'applicazione nel cluster AKS, non sono necessarie password o segreti per eseguire il push o il pull dal Registro di sistema ACR. Poiché è stata creata una relazione di trust tra il cluster AKS e l'ACR, l'autenticazione viene eseguita a livello di Azure Resource Manager usando Azure Active Directory.Since a trust was created between your AKS cluster and your ACR, authentication happens at the Azure Resource Manager level, using Azure Active Directory.

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

Questa guida introduttiva utilizza [un'applicazione Java di esempio dal repository Draft GitHub][example-java]. Clonare l'applicazione da GitHub e passare alla `draft/examples/example-java/` directory.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Eseguire l'applicazione di esempio con DraftRun the sample application with Draft

Utilizzare `draft create` il comando per preparare l'applicazione.

```console
draft create
```

Questo comando crea gli artefatti che vengono usati per eseguire l'applicazione in un cluster Kubernetes. Tali elementi includono un Dockerfile, un grafico Helm e un file *draft.toml*, ovvero il file di configurazione di Draft.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Per eseguire l'applicazione di esempio nel cluster di servizio Azure Container, usare il comando `draft up`.

```console
draft up
```

Questo comando crea il Dockerfile per creare un'immagine contenitore, invia l'immagine al controllo dell'account utente e installa il grafico Helm per avviare l'applicazione in AKS. La prima volta che si esegue questo comando, il push e il pull dell'immagine contenitore potrebbe richiedere del tempo. Una volta che vengono memorizzati nella cache i livelli di base, viene ridotto drasticamente il tempo impiegato per distribuire l'applicazione.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Connettersi all'applicazione di esempio in esecuzione dal computer localeConnect to the running sample application from your local machine

Per testare l'applicazione, usare il comando `draft connect`.

```console
draft connect
```

Questo comando esegue un proxy sulla connessione del pod Kubernetes consentendo una connessione locale sicura. Al termine dell'operazione, è possibile accedere all'applicazione dall'URL specificato.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Passare all'applicazione in un `localhost` browser usando l'URL per visualizzare l'applicazione di esempio. Nell'esempio precedente, l'URL è `http://localhost:49804`. Interrompere la `Ctrl+c`connessione utilizzando .

## <a name="access-the-application-on-the-internet"></a>Accedere all'applicazione in Internet

Il passaggio precedente ha creato una connessione proxy al pod dell'applicazione nel cluster del servizio Azure Container. Durante lo sviluppo e il test di un'applicazione, è possibile rendere disponibile l'applicazione in Internet. Per esporre un'applicazione su Internet, è possibile creare un servizio Kubernetes con un tipo di [LoadBalancer][kubernetes-service-loadbalancer].

Eseguire `charts/example-java/values.yaml` l'aggiornamento per creare un servizio *LoadBalancer.Update* to create a LoadBalancer service. Modificare il valore di *service.type* da *ClusterIP* a *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Salvare le modifiche, chiudere il `draft up` file ed eseguire per eseguire nuovamente l'applicazione.

```console
draft up
```

Ci vogliono alcuni minuti perché il servizio restituisca un indirizzo IP pubblico. Per monitorare lo stato di avanzamento, usare il comando `kubectl get service` con il parametro *espressione di controllo*:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Passare al servizio di bilanciamento del carico dell'applicazione in un browser usando *EXTERNAL-IP* per visualizzare l'applicazione di esempio. Nell'esempio precedente, l'IP è `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Eseguire l'iterazione dell'applicazione

È possibile scorrere l'applicazione apportando `draft up`modifiche in locale ed eseguendo nuovamente .

Aggiornare il messaggio restituito alla [riga 7 di src/main/java/helloworld/Hello.java][example-java-hello-l7]

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

Eseguire il comando `draft up` per distribuire nuovamente l'applicazione:

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

Per visualizzare l'applicazione aggiornata, passare nuovamente all'indirizzo IP del servizio di bilanciamento del carico e verificare che le modifiche vengano visualizzate.

## <a name="delete-the-cluster"></a>Eliminare il cluster

Quando il cluster non è più necessario, usare il comando [az group delete][az-group-delete] per rimuovere il gruppo di risorse, il cluster AKS, il registro contenitori, le immagini del contenitore archiviate in tale gruppo e tutte le risorse correlate.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere le [considerazioni sull'entità servizio servizio Azure Kubernetes e la sua eliminazione][sp-delete].

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Draft, vedere la documentazione relativa su GitHub.

> [!div class="nextstepaction"]
> [Documentazione di Draft][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az servizio Azure Kubernetes get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az servizio Azure Kubernetes install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
