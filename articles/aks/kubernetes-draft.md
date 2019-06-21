---
title: Sviluppare in Azure Kubernetes Service (AKS) con Draft
description: Usare Draft con il servizio Azure Container e con Registro Azure Container
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303535"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>Avvio rapido: Sviluppare in Azure Kubernetes Service (AKS) con Draft

Draft è uno strumento open source che consente di creare pacchetti ed eseguire i contenitori di applicazioni in un cluster Kubernetes. Con Draft, è possibile ridistribuire rapidamente un'applicazione in Kubernetes quando si verificano modifiche al codice senza la necessità di eseguire il commit delle modifiche al controllo della versione. Per altre informazioni su Draft, vedere la [documentazione di Draft su GitHub][draft-documentation].

Questo articolo illustra come usare il pacchetto di bozza ed eseguire un'applicazione nel servizio contenitore di AZURE.


## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata](/cli/azure/install-azure-cli?view=azure-cli-latest).
* Docker installato e configurato. Docker offre pacchetti che consentono di configurare Docker in un [Mac][docker-for-mac], [Windows][docker-for-windows], o [Linux][docker-for-linux] sistema.
* [Installate Helm](https://github.com/helm/helm/blob/master/docs/install.md).
* [Bozza installato][draft-documentation].

## <a name="create-an-azure-kubernetes-service-cluster"></a>Creare un cluster del servizio Azure Kubernetes

Creare un cluster AKS. I comandi seguenti, creare un gruppo di risorse denominato MyResourceGroup e un cluster AKS chiamato MyAKS.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Creare un'istanza di Registro Azure Container
Per usare Draft per eseguire l'applicazione nel cluster AKS, è necessario un registro contenitori di Azure per archiviare le immagini del contenitore. L'esempio seguente viene utilizzata [az acr create][az-acr-create] per creare un registro contenitori di AZURE denominato *MyDraftACR* nel *MyResourceGroup* gruppo di risorse con il *base* SKU. È necessario fornire il proprio nome univoco del Registro di sistema. Il nome del registro deve essere univoco in Azure e contenere da 5 a 50 caratteri alfanumerici. Lo SKU *Basic* è un punto di ingresso con costi ottimali a fini di sviluppo, che assicura l'equilibrio tra spazio di archiviazione e velocità effettiva.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

L'output è simile all'esempio seguente: Annotare il *loginServer* valore per il registro contenitori di AZURE, dal momento che verrà utilizzato in un passaggio successivo. Nell'esempio seguente *mydraftacr.azurecr.io* è la *loginServer* per *MyDraftACR*.

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


Per la bozza usare l'istanza di registro contenitori di AZURE, è prima necessario accedere. Usare la [account di accesso di az acr][az-acr-login] comando per l'accesso. L'esempio seguente eseguiranno l'accesso a un registro contenitori di AZURE denominato *MyDraftACR*.

```azurecli
az acr login --name MyDraftACR
```

Al termine, il comando restituisce un messaggio di *Accesso riuscito*.

## <a name="create-trust-between-aks-cluster-and-acr"></a>Creare una relazione di trust tra il cluster del servizio Azure Container e Registro Azure Container

Il cluster AKS deve inoltre acceda accesso per il registro contenitori di AZURE per eseguire il pull di immagini del contenitore e la relativa esecuzione. È consentire l'accesso per il registro contenitori di AZURE dal servizio contenitore di AZURE mediante la definizione di una relazione di trust. Per stabilire l'attendibilità tra un cluster del servizio Azure Container e un'istanza di Registro Azure Container, concedere le autorizzazioni necessarie perché l'entità servizio di Azure Active Directory usata dal cluster del servizio Azure Container possa accedere all'istanza di Registro Azure Container. I comandi seguenti concedono le autorizzazioni all'entità servizio del *MyAKS* del cluster nel *MyResourceGroup* per il *MyDraftACR* registro contenitori di AZURE nel  *MyResourceGroup*.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>Connettersi al cluster servizio contenitore di AZURE

Per connettersi al cluster Kubernetes dal computer locale, si utilizza [kubectl][kubectl], il client della riga di comando di Kubernetes.

Se si usa Azure Cloud Shell, `kubectl` è già installato. È anche possibile installarlo in locale usando il comando [az servizio Azure Kubernetes install-cli][]:

```azurecli
az aks install-cli
```

Per configurare `kubectl` per la connessione al cluster Kubernetes, usare il comando [az servizio Azure Kubernetes get-credentials][]. L'esempio seguente ottiene le credenziali per il cluster AKS denominato *MyAKS* nel *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Creare un account del servizio per Helm

Prima di poter distribuire Helm in un cluster servizio Azure Kubernetes abilitato per il controllo degli accessi in base al ruolo, sono necessari un account del servizio e un'associazione di ruolo per il servizio Tiller. Per altre informazioni sulla protezione Helm / cluster abilitato Tiller in un RBAC, vedere [Tiller, gli spazi dei nomi e RBAC][tiller-rbac]. Se il cluster AKS non è abilitato RBAC, ignorare questo passaggio.

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
Per distribuire un Tiller base in un cluster del servizio contenitore di AZURE, usare il [helm init][helm-init] comando. Se il cluster non è abilitato RBAC, rimuovere il `--service-account` argomento e il valore.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Configurare Draft

Se nel computer locale non è stato configurato bozza, eseguire `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

È inoltre necessario configurare Draft per usare la *loginServer* il registro contenitori di Azure. Il comando seguente usa `draft config set` da usare `mydraftacr.azurecr.io` come Registro di sistema.

```console
draft config set registry mydraftacr.azurecr.io
```

Si è configurato Draft per usare il registro contenitori di AZURE, e bozza può eseguire il push delle immagini del contenitore per il registro contenitori di AZURE. Quando Draft viene eseguita l'applicazione nel cluster AKS, alcuna password o segreti non sono necessari per eseguire il push o pull dal registro contenitori di AZURE. Poiché è stato creato un trust tra il cluster AKS e il registro contenitori di AZURE, l'autenticazione avviene a livello di Azure Resource Manager, tramite Azure Active Directory.

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

Questa Guida introduttiva Usa [un'applicazione java di esempio dal repository GitHub di Draft][example-java]. Clonare l'applicazione da GitHub e passare al `draft/examples/example-java/` directory.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Eseguire l'applicazione di esempio con Draft

Usare il `draft create` comando per preparare l'applicazione.

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

Questo comando compila il Dockerfile per creare un'immagine del contenitore, effettua il push dell'immagine per il registro contenitori di AZURE e installa il grafico Helm per avviare l'applicazione nel servizio contenitore di AZURE. La prima volta che si esegue questo comando, eseguire il push e il pull dell'immagine del contenitore potrebbe richiedere alcuni minuti. Una volta che vengono memorizzati nella cache i livelli di base, viene ridotto drasticamente il tempo impiegato per distribuire l'applicazione.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>Connettersi all'esecuzione dell'applicazione dal computer locale di esempio

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

Passare all'applicazione in un browser utilizzando il `localhost` url per visualizzare l'applicazione di esempio. Nell'esempio precedente, l'url è `http://localhost:49804`. Arrestare la connessione usando `Ctrl+c`.

## <a name="access-the-application-on-the-internet"></a>Accedere all'applicazione in Internet

Il passaggio precedente ha creato una connessione proxy al pod dell'applicazione nel cluster del servizio Azure Container. Durante lo sviluppo e il test di un'applicazione, è possibile rendere disponibile l'applicazione in Internet. Per esporre un'applicazione in internet, è possibile creare un servizio Kubernetes di tipo [LoadBalancer][kubernetes-service-loadbalancer].

Update `charts/example-java/values.yaml` per creare un *LoadBalancer* servizio. Modificare il valore della *service.type* dalla *IP del cluster* al *LoadBalancer*.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

Salvare le modifiche, chiudere il file ed eseguire `draft up` a eseguire nuovamente l'applicazione.

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

Passare al servizio di bilanciamento del carico dell'applicazione in un browser utilizzando il *EXTERNAL-IP* per visualizzare l'applicazione di esempio. Nell'esempio precedente, l'indirizzo IP è `52.175.224.118`.

## <a name="iterate-on-the-application"></a>Eseguire l'iterazione dell'applicazione

È possibile eseguire l'iterazione dell'applicazione per apportare modifiche in locale, quindi eseguire nuovamente `draft up`.

Aggiornare il messaggio restituito in [riga 7 di src/main/java/helloworld/Hello.java][example-java-hello-l7]

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

Per visualizzare l'applicazione aggiornata, passare di nuovo all'indirizzo IP del servizio di bilanciamento del carico e verificare che le modifiche verranno visualizzate.

## <a name="delete-the-cluster"></a>Eliminare il cluster

Quando il cluster non è non è più necessario, usare il [eliminazione gruppo az][az-group-delete] comando per rimuovere il gruppo di risorse, il cluster AKS, il registro contenitori, le immagini del contenitore archiviate non esiste, e tutte le risorse correlate.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Quando si elimina il cluster, l'entità servizio di Azure Active Directory utilizzata dal cluster servizio Azure Kubernetes non viene rimossa. Per istruzioni su come rimuovere l'entità servizio, vedere [considerazioni sull'entità e l'eliminazione del servizio AKS][sp-delete].

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Draft, vedere la documentazione relativa su GitHub.

> [!div class="nextstepaction"]
> [Documentazione Draft][draft-documentation]


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
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
