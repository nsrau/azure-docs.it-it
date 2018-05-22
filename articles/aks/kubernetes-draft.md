---
title: Usare Draft con il servizio contenitore di Azure e con Registro contenitori di Azure
description: Usare Draft con il servizio contenitore di Azure e con Registro contenitori di Azure
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: bb80d68d7ac37c8930626b5163597dc0e492a0d2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>Usare Draft con Azure Kubernetes Service (AKS)

Draft è uno strumento open source che consente di includere e distribuire questi contenitori in un cluster Kubernetes, permettendo all'utente di concentrarsi sulla fase centrale del ciclo di sviluppo. Draft viene eseguito mentre è in corso lo sviluppo del codice, ma prima del commit nel controllo della versione. Con Draft è possibile ridistribuire rapidamente un'applicazione in Kubernetes quando il codice viene modificato. Per altre informazioni su Draft, vedere la [documentazione di Draft su GitHub][draft-documentation].

Il documento presente illustra l'uso di Draft con un cluster Kubernetes nel servizio contenitore di Azure.

## <a name="prerequisites"></a>prerequisiti

I passaggi dettagliati contenuti in questo documento presuppongono che sia stato creato un cluster del servizio contenitore di Azure e che sia stata stabilita una connessione kubectl al cluster. Se sono necessari questi elementi, vedere la [guida introduttiva al servizio contenitore di Azure][aks-quickstart].

È necessario anche un registro Docker privato in Registro contenitori di Azure. Per istruzioni sulla distribuzione di un'istanza di Registro contenitori di Azure, vedere la [guida introduttiva al Registro contenitori di Azure][acr-quickstart].

Anche Helm deve essere installato nel cluster AKS. Per altre informazioni sull'installazione di Helm, vedere [Usare Helm con Azure Kubernetes Service (AKS)][aks-helm].

È infine necessario installare [Docker](https://www.docker.com).

## <a name="install-draft"></a>Installare Draft

L'interfaccia della riga di comando di Draft è un client che viene eseguito nel sistema di sviluppo e che consente di distribuire rapidamente codice in un cluster Kubernetes.

> [!NOTE]
> Se è stata installata una versione di Draft precedente la 0.12, è necessario prima di tutto eliminare Draft dal cluster usando `helm delete --purge draft` e quindi rimuovere la configurazione locale eseguendo `rm -rf ~/.draft`. Se si usa MacOS, eseguire `brew upgrade draft`.

Per installare l'interfaccia della riga di comando di Draft in un computer Mac, usare `brew`. Per altre opzioni di installazione, vedere la [guida all'installazione di Draft][install-draft].

```console
brew tap azure/draft
brew install draft
```

A questo punto, inizializzare Draft con il comando `draft init`.

```console
draft init
```

## <a name="configure-draft"></a>Configurare Draft

Draft compila le immagini dei contenitori in locale e quindi le distribuisce dal registro locale (nel caso di Minikube). In alternativa, è necessario specificare il registro delle immagini da usare. Questo esempio usa Registro contenitori di Azure. È pertanto necessario stabilire una relazione di trust tra il cluster del servizio contenitore di Azure e l'istanza di Registro contenitori di Azure e configurare Draft in modo da eseguire il push del contenitore nel registro.

### <a name="create-trust-between-aks-cluster-and-acr"></a>Creare una relazione di trust tra il cluster del servizio contenitore di Azure e Registro contenitori di Azure

Per stabilire questa relazione di trust, è necessario modificare l'entità servizio di Azure Active Directory usata con il servizio contenitore di Azure aggiungendo a tale entità il ruolo di collaboratore con l'ambito del repository di Registro contenitori di Azure. A tale scopo, eseguire i comandi seguenti, sostituendo _&lt;aks-rg-name&gt;_ e _&lt;aks-cluster-name&gt;_ con il nome del gruppo di risorse e il nome del cluster del servizio contenitore di Azure e _&lt;acr-rg-nam&gt;_ e _&lt;acr-repo-name&gt;_ con il nome del gruppo di risorse e il nome del repository di Registro contenitori di Azure con il quale si vuole stabilire la relazione di trust.

```console
export AKS_SP_ID=$(az aks show -g <aks-rg-name> -n <aks-cluster-name> --query "servicePrincipalProfile.clientId" -o tsv)
export ACR_RESOURCE_ID=$(az acr show -g <acr-rg-name> -n <acr-repo-name> --query "id" -o tsv)
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

Questi passaggi e altri meccanismi di autenticazione per accedere a Registro contenitori di Azure sono descritti in [Eseguire l'autenticazione con Registro contenitori di Azure](../container-registry/container-registry-auth-aks.md).

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>Configurare Draft per il push in Registro contenitori di Azure e la distribuzione da tale registro

Dopo aver stabilito una relazione di trust tra il servizio contenitore di Azure e Registro contenitori di Azure, è possibile eseguire i passaggi seguenti per usare Registro contenitori di Azure dal cluster del servizio contenitore di Azure.
1. Impostare il valore di configurazione `registry` di Draft eseguendo `draft config set registry <registry name>.azurecr.io`, dove _&lt;registry name&lt;_ corrisponde al nome dell'istanza di Registro contenitori di Azure.
2. Accedere all'istanza di Registro contenitori di Azure eseguendo `az acr login -n <registry name>`.

Poiché si è stabilita una connessione locale a Registro contenitori di Azure e si è creata una relazione di trust tra il servizio contenitore di Azure e tale registro, non è necessario specificare password o segreti per eseguire il push in Registro contenitori di Azure o il pull da tale registro nel servizio contenitore di Azure. L'autenticazione viene eseguita a livello di Azure Resource Manager, tramite Azure Active Directory.

## <a name="run-an-application"></a>Eseguire un'applicazione

Il repository di Draft include diverse applicazioni di esempio che possono essere usate per eseguire una dimostrazione. Creare una copia clonata del repository.

```console
git clone https://github.com/Azure/draft
```

Passare alla directory di esempi di Java.

```console
cd draft/examples/example-java/
```

Per avviare il processo, usare il comando `draft create`. Questo comando crea gli artefatti che vengono usati per eseguire l'applicazione in un cluster Kubernetes. Tali elementi includono un Dockerfile, un grafico Helm e un file `draft.toml`, ovvero il file di configurazione di Draft.

```console
draft create
```

Output:

```console
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Per eseguire l'applicazione in un cluster Kubernetes, usare il comando `draft up`. Questo comando compila il Dockerfile per creare un'immagine del contenitore, esegue il push dell'immagine in Registro contenitori di Azure e infine installa il grafico Helm per avviare l'applicazione nel servizio contenitore di Azure.

La prima volta che si esegue questo comando, il push e il pull dell'immagine del contenitore possono richiedere del tempo, ma una volta memorizzati nella cache i livelli di base, il tempo impiegato si riduce notevolmente.

```console
draft up
```

Output:

```console
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (3.0007s)
example-java: Releasing Application: SUCCESS ⚓  (0.9322s)
example-java: Build ID: 01C9NPDYQQH2CZENDMZW7ESJAM
Inspect the logs with `draft logs 01C9NPDYQQH2CZENDMZW7ESJAM`
```

## <a name="test-the-application"></a>Test dell'applicazione

Per testare l'applicazione, usare il comando `draft connect`. Questo comando esegue un proxy sulla connessione del pod Kubernetes consentendo una connessione locale sicura. Al termine dell'operazione, è possibile accedere all'applicazione dall'URL specificato.

In alcuni casi, il download dell'immagine del contenitore e l'avvio dell'applicazione possono richiedere alcuni minuti. Se viene visualizzato un errore durante l'accesso all'applicazione, rieseguire la connessione.

```console
draft connect
```

Output:

```console
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Ora testare l'applicazione usando l'indirizzo http://localhost:46143. Per l'esempio precedente è possibile che la porta sia diversa. Al termine del test dell'applicazione, usare `Control+C` per interrompere la connessione proxy.

> [!NOTE]
> Per rendere ancora più rapido il ciclo di iterazione, è anche possibile usare il comando `draft up --auto-connect` per compilare e distribuire l'applicazione e connettersi immediatamente al primo contenitore in esecuzione.

## <a name="expose-application"></a>Esporre l'applicazione

Durante il test di un'applicazione in Kubernetes, è possibile rendere disponibile l'applicazione in Internet. Questa operazione può essere eseguita tramite un servizio Kubernetes con tipo [LoadBalancer][kubernetes-service-loadbalancer] oppure tramite un [controller di ingresso][kubernetes-ingress]. Il documento presente illustra l'uso di un servizio Kubernetes.


In primo luogo, il pacchetto Draft deve essere aggiornato per specificare che è necessario creare un servizio con tipo `LoadBalancer`. A tale scopo, aggiornare il tipo di servizio nel file `values.yaml`.

```console
vi charts/java/values.yaml
```

Individuare la proprietà `service.type` e aggiornare il valore da `ClusterIP` a `LoadBalancer`.

```yaml
replicaCount: 2
image:
  repository: openjdk
  tag: 8-jdk-alpine
  pullPolicy: IfNotPresent
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
  ```

Eseguire `draft up` per rieseguire l'applicazione.

```console
draft up
```

Possono volerci alcuni minuti perché il servizio restituisca un indirizzo IP pubblico. Per monitorare l'avanzamento, usare il comando `kubectl get service` con un'espressione di controllo.

```console
kubectl get service -w
```

*EXTERNAL-IP* per il servizio inizialmente viene visualizzato come `pending`.

```
example-java-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Dopo che l'indirizzo EXTERNAL-IP passa da `pending` a `IP address`, usare `Control+C` per arrestare il processo kubectl watch.

```
example-java-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
```

Per vedere l'applicazione, passare all'indirizzo IP esterno.

```console
curl 52.175.224.118
```

Output:

```
Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>Eseguire l'iterazione dell'applicazione

Ora che Draft è stato configurato e che l'applicazione è in esecuzione in Kubernetes, è possibile eseguire l'iterazione del codice. Ogni volta in cui si vuole testare il codice aggiornato, eseguire il comando `draft up` per aggiornare l'applicazione in esecuzione.

Per questo esempio, aggiornare l'applicazione hello world di Java.

```console
vi src/main/java/helloworld/Hello.java
```

Aggiornare il testo Hello World.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
}
```

Eseguire il comando `draft up --auto-connect` per distribuire nuovamente l'applicazione non appena un pod è pronto a rispondere.

```console
draft up --auto-connect
```

Output

```
Draft Up Started: 'example-java'
example-java: Building Docker Image: SUCCESS ⚓  (1.0003s)
example-java: Pushing Docker Image: SUCCESS ⚓  (4.0010s)
example-java: Releasing Application: SUCCESS ⚓  (1.1336s)
example-java: Build ID: 01C9NPMJP6YM985GHKDR2J64KC
Inspect the logs with `draft logs 01C9NPMJP6YM985GHKDR2J64KC`
Connect to java:4567 on localhost:39249
Your connection is still active.
Connect to java:4567 on localhost:39249
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567

```

Infine, visualizzare l'applicazione per vedere gli aggiornamenti.

```console
curl 52.175.224.118
```

Output:

```
Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Draft, vedere la documentazione relativa su GitHub.

> [!div class="nextstepaction"]
> [Documentazione di Draft][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[install-draft]: https://github.com/Azure/draft/blob/master/docs/install.md
[kubernetes-ingress]: ./ingress.md
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[aks-quickstart]: ./kubernetes-walkthrough.md
