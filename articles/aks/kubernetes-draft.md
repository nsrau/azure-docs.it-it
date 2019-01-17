---
title: Usare Draft con il servizio contenitore di Azure e con Registro contenitori di Azure
description: Usare Draft con il servizio contenitore di Azure e con Registro contenitori di Azure
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: d94f70771cf3ee364dbb3e4c4256cd2248ce3828
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164773"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Usare Draft con il servizio Azure Kubernetes

Draft è uno strumento open source che consente di includere e distribuire questi contenitori in un cluster Kubernetes, permettendo all'utente di concentrarsi sulla fase centrale del ciclo di sviluppo. Draft viene eseguito mentre è in corso lo sviluppo del codice, ma prima del commit nel controllo della versione. Con Draft è possibile ridistribuire rapidamente un'applicazione in Kubernetes quando il codice viene modificato. Per altre informazioni su Draft, vedere la [documentazione di Draft su GitHub][draft-documentation].

Questo articolo illustra come usare Draft in un cluster Kubernetes nel servizio Kubernetes di Azure.

## <a name="prerequisites"></a>Prerequisiti

I passaggi dettagliati contenuti in questo articolo presuppongono che sia stato creato un cluster del servizio Kubernetes di Azure e che sia stata stabilita una connessione `kubectl` al cluster. Se sono necessari questi elementi, vedere la [guida introduttiva al servizio contenitore di Azure][aks-quickstart].

È necessario un registro Docker privato in Registro contenitori di Azure. Per la procedura di creazione di un'istanza di Registro contenitori di Azure, vedere la [guida introduttiva al Registro contenitori di Azure][acr-quickstart].

Anche Helm deve essere installato nel cluster AKS. Per altre informazioni sull'installazione e la configurazione di Helm, vedere [Usare Helm con il servizio Azure Kubernetes][aks-helm].

È infine necessario installare [Docker](https://www.docker.com).

## <a name="install-draft"></a>Installare Draft

L'interfaccia della riga di comando di Draft è un client che viene eseguito nel sistema di sviluppo e che consente di distribuire il codice in un cluster Kubernetes. Per installare l'interfaccia della riga di comando di Draft in un computer Mac, usare `brew`. Per altre opzioni di installazione, vedere la [guida all'installazione di Draft][draft-documentation].

> [!NOTE]
> Se è stata installata una versione di Draft precedente la 0.12, è necessario prima di tutto eliminare Draft dal cluster usando `helm delete --purge draft` e quindi rimuovere la configurazione locale eseguendo `rm -rf ~/.draft`. Se si usa MacOS, eseguire `brew upgrade draft`.

```console
brew tap azure/draft
brew install draft
```

A questo punto, inizializzare Draft con il comando `draft init`:

```console
draft init
```

## <a name="configure-draft"></a>Configurare Draft

Draft compila le immagini dei contenitori in locale e quindi le distribuisce dal registro locale (come nel caso di Minikube). In alternativa, è necessario specificare il registro delle immagini da usare. Questo articolo usa Registro contenitori di Azure. È pertanto necessario stabilire una relazione di trust tra il cluster del servizio contenitore di Azure e l'istanza di Registro contenitori di Azure e configurare Draft in modo da eseguire il push delle immagini del contenitore in Registro contenitori di Azure.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Creare una relazione di trust tra il cluster del servizio contenitore di Azure e Registro contenitori di Azure

Per stabilire l'attendibilità tra un cluster del servizio contenitore di AZURE e un registro contenitori di AZURE, concedere le autorizzazioni necessarie perché l'entità servizio di Azure Active Directory usata dal cluster del servizio contenitore di AZURE possa accedere al registro contenitori di AZURE. Nei comandi seguenti, specificare il proprio `<resourceGroupName>`, sostituire `<aksName>` con il nome del cluster del servizio contenitore di Azure e sostituire `<acrName>` con il nome del registro contenitori di AZURE:

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Per altre informazioni sulla procedura di accesso al registro contenitori di AZURE, vedere [Autenticazione con registro contenitori di AZURE](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Configurare Draft per il push in Registro contenitori di Azure e la distribuzione da tale registro

Dopo aver stabilito una relazione di trust tra il servizio contenitore di Azure e Registro contenitori di Azure, abilitare l'utilizzo del Registro contenitori di Azure dal cluster del servizio contenitore di Azure.

1. Impostare il valore del *registro* di configurazione di Draft. Nei comandi seguenti, sostituire `<acrName>` con il nome del registro contenitori di AZURE:

    ```console
    draft config set registry <acrName>.azurecr.io
    ```

1. Accedere al registro contenitori di AZURE con l'[account di accesso di az acr][az-acr-login]:

    ```azurecli
    az acr login --name <acrName>
    ```

Dato che è stato creato un trust tra il servizio contenitore di Azure e il registro contenitori di AZURE, non sono necessari password o segreti per eseguire il push o il pull dal registro contenitori di Azure. L'autenticazione viene eseguita a livello di Azure Resource Manager, tramite Azure Active Directory.

## <a name="run-an-application"></a>Eseguire un'applicazione

Per visualizzare Draft in azione, si distribuirà un'applicazione di esempio dal [repository di Draft][draft-repo]. Innanzitutto, clonare il repository:

```console
git clone https://github.com/Azure/draft
```

Passare alla directory di esempi di Java:

```console
cd draft/examples/example-java/
```

Per avviare il processo, usare il comando `draft create`. Questo comando crea gli artefatti che vengono usati per eseguire l'applicazione in un cluster Kubernetes. Tali elementi includono un Dockerfile, un grafico Helm e un file *draft.toml*, ovvero il file di configurazione di Draft.

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

Per eseguire l'applicazione di esempio nel cluster di servizio contenitore di Azure, usare il comando `draft up`. Questo comando compila il Dockerfile per creare un'immagine del contenitore, esegue il push dell'immagine in Registro contenitori di Azure e infine installa il grafico Helm per avviare l'applicazione nel servizio contenitore di Azure.

La prima volta che viene eseguito questo comando, l'esecuzione del push e del pull dell'immagine del contenitore potrebbe richiedere alcuni minuti. Una volta che vengono memorizzati nella cache i livelli di base, viene ridotto drasticamente il tempo impiegato per distribuire l'applicazione.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Se si verificano problemi con il push dell'immagine Docker, assicurarsi di avere eseguito l'accesso al registro contenitori di AZURE con l'[account di accesso di az acr][az-acr-login], quindi ripetere di nuovo il comando `draft up`.

## <a name="test-the-application-locally"></a>Testare l'applicazione in locale

Per testare l'applicazione, usare il comando `draft connect`. Questo comando esegue un proxy sulla connessione del pod Kubernetes consentendo una connessione locale sicura. Al termine dell'operazione, è possibile accedere all'applicazione dall'URL specificato.

> [!NOTE]
> In alcuni casi, il download dell'immagine del contenitore e l'avvio dell'applicazione possono richiedere alcuni minuti. Se viene visualizzato un errore durante l'accesso all'applicazione, rieseguire la connessione.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

Per accedere all'applicazione, aprire un browser WEB all'indirizzo e sulla porta specificata nell'output `draft connect`, ad esempio *http://localhost:49804*. 

![App Java di esempio in esecuzione con Draft](media/kubernetes-draft/sample-app.png)

Usare `Control+C` per interrompere la connessione proxy.

> [!NOTE]
> È anche possibile usare il comando `draft up --auto-connect` per compilare e distribuire l'applicazione e connettersi immediatamente al primo contenitore in esecuzione.

## <a name="access-the-application-on-the-internet"></a>Accedere all'applicazione in Internet

Il passaggio precedente ha creato una connessione proxy al pod dell'applicazione nel cluster del servizio contenitore di Azure. Durante lo sviluppo e il test di un'applicazione, è possibile rendere disponibile l'applicazione in Internet. Per esporre un'applicazione in internet, creare un servizio Kubernetes di tipo [LoadBalancer][kubernetes-service-loadbalancer], o creare un [controller di ingresso] [ kubernetes-ingress]. Verrà qui creato un servizio *LoadBalancer*.

In primo luogo, aggiornare il pacchetto Draft *values.yaml* per specificare che un servizio con tipo *LoadBalancer* deve essere creato:

```console
vi charts/java/values.yaml
```

Individuare la proprietà *service.type* e aggiornare il valore da *ClusterIP* al *LoadBalancer*, come illustrato nell'esempio sintetico seguente:

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

Salvare e chiudere il file, quindi usare `draft up` per eseguire nuovamente l'applicazione:

```console
draft up
```

Ci vogliono alcuni minuti perché il servizio restituisca un indirizzo IP pubblico. Per monitorare lo stato di avanzamento, usare il comando `kubectl get service` con il parametro *espressione di controllo*:

```console
kubectl get service --watch
```

*EXTERNAL-IP* per il servizio inizialmente viene visualizzato come *in sospeso*:

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

Dopo il passaggio di EXTERNAL-IP da *in sospeso* a un indirizzo IP, usare `Control+C` per arrestare il processo di espressione di controllo `kubectl`:

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

Per vedere l'applicazione, passare all'indirizzo IP esterno del bilanciamento del carico con `curl`:

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Eseguire l'iterazione dell'applicazione

Ora che Draft è stato configurato e che l'applicazione è in esecuzione in Kubernetes, è possibile eseguire l'iterazione del codice. Ogni volta in cui si vuole testare il codice aggiornato, eseguire il comando `draft up` per aggiornare l'applicazione in esecuzione.

In questo esempio, aggiornare l'applicazione di esempio Java per modificare il testo visualizzato. Aprire il file *Hello.java*:

```console
vi src/main/java/helloworld/Hello.java
```

Aggiornare il testo di output da visualizzare con *Hello World, I'm Java in AKS!*:

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
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

Per visualizzare l'applicazione aggiornata, eseguire nuovamente il curl dell'indirizzo IP del servizio di bilanciamento del carico:

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Draft, vedere la documentazione relativa su GitHub.

> [!div class="nextstepaction"]
> [Documentazione di Draft][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: /cli/azure/acr#az-acr-login
