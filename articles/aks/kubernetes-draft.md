---
title: Usare Draft con il servizio contenitore di Azure e con Registro contenitori di Azure
description: Usare Draft con il servizio contenitore di Azure e con Registro contenitori di Azure
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a77e214c1138ce936b2ec6c521950704e5beb3ff
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="use-draft-with-azure-container-service-aks"></a>Usare Draft con il servizio contenitore di Azure

Draft è uno strumento open source che consente di creare pacchetti di codice ed eseguirli in un cluster Kubernetes. Draft ha come destinazione il ciclo di iterazione dello sviluppo. Viene usato quando il codice viene sviluppato ma prima di eseguire il commit del controllo della versione. Con Draft è possibile ridistribuire rapidamente un'applicazione in Kubernetes quando il codice viene modificato. Per ulteriori informazioni sul progetto, vedere il [bozza documentazione su Github][draft-documentation].

Il documento presente illustra l'uso di Draft con un cluster Kubernetes nel servizio contenitore di Azure.

## <a name="prerequisites"></a>Prerequisiti

I passaggi dettagliati contenuti in questo documento presuppongono che sia stato creato un cluster del servizio contenitore di Azure e che sia stata stabilita una connessione kubectl al cluster. Se è necessario di questi elementi, vedere il [delle Guide rapide AKS][aks-quickstart].

È necessario anche un registro Docker privato in Registro contenitori di Azure. Per istruzioni sulla distribuzione di un'istanza di record, vedere il [Guida introduttiva di Azure contenitore del Registro di sistema][acr-quickstart].

## <a name="install-helm"></a>Installare Helm

L'interfaccia della riga di comando di Helm è un client eseguito nel sistema di sviluppo che consente di avviare, arrestare e gestire applicazioni con grafici Helm.

Per installare l'interfaccia della riga di comando di Helm in un computer Mac, usare `brew`. Per le opzioni di installazione aggiuntive, vedere [installazione Helm][install-helm].

```console
brew install kubernetes-helm
```

Output:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="install-draft"></a>Installare Draft

L'interfaccia della riga di comando di Draft è un client che viene eseguito nel sistema di sviluppo e che consente di distribuire rapidamente codice in un cluster Kubernetes.

Per installare l'interfaccia della riga di comando di Draft in un computer Mac, usare `brew`. Per le opzioni di installazione aggiuntive vedere, il [Guida all'installazione bozza][install-draft].

```console
brew install draft
```

Output:

```
==> Installing draft from azure/draft
==> Downloading https://azuredraft.blob.core.windows.net/draft/draft-v0.7.0-darwin-amd64.tar.gz
Already downloaded: /Users/neilpeterson/Library/Caches/Homebrew/draft-0.7.0.tar.gz
==> /usr/local/Cellar/draft/0.7.0/bin/draft init --client-only
🍺  /usr/local/Cellar/draft/0.7.0: 6 files, 61.2MB, built in 1 second
```

## <a name="configure-draft"></a>Configurare Draft

Quando si configura Draft, è necessario specificare un registro contenitori. In questo esempio viene usato Registro contenitori di Azure.

Eseguire il comando seguente per ottenere il nome dell'istanza di Registro contenitori di Azure e del relativo server di accesso. Aggiornare il comando con il nome del gruppo di risorse contenente l'istanza di Registro contenitori di Azure.

```console
az acr list --resource-group <resource group> --query "[].{Name:name,LoginServer:loginServer}" --output table
```

È necessaria anche la password di Registro contenitori di Azure.

Per restituire la password di Registro contenitori di Azure, eseguire il comando seguente. Aggiornare il comando con il nome dell'istanza di Registro contenitori di Azure.

```console
az acr credential show --name <acr name> --query "passwords[0].value" --output table
```

Inizializzare Draft con il comando `draft init`.

```console
draft init
```

Durante il processo, vengono richieste le credenziali del registro contenitori. Quando si usa Registro contenitori di Azure, l'URL del registro è il nome del server di accesso di Registro contenitori di Azure, il nome utente è il nome dell'istanza di Registro contenitori di Azure e la password è la password di Registro contenitori di Azure.

```console
1. Enter your Docker registry URL (e.g. docker.io/myuser, quay.io/myuser, myregistry.azurecr.io): <ACR Login Server>
2. Enter your username: <ACR Name>
3. Enter your password: <ACR Password>
```

Al termine dell'operazione, Draft è stato configurato nel cluster Kubernetes ed è pronto per l'uso.

```
Draft has been installed into your Kubernetes Cluster.
Happy Sailing!
```

## <a name="run-an-application"></a>Eseguire un'applicazione

Il repository di Draft include diverse applicazioni di esempio che possono essere usate per eseguire una dimostrazione. Creare una copia clonata del repository.

```console
git clone https://github.com/Azure/draft
```

Passare alla directory di esempi di Java.

```console
cd draft/examples/java/
```

Per avviare il processo, usare il comando `draft create`. Questo comando crea gli artefatti che vengono usati per eseguire l'applicazione in un cluster Kubernetes. Tali elementi includono un Dockerfile, un grafico Helm e un file `draft.toml`, ovvero il file di configurazione di Draft.

```console
draft create
```

Output:

```
--> Draft detected the primary language as Java with 92.205567% certainty.
--> Ready to sail
```

Per eseguire l'applicazione in un cluster Kubernetes, usare il comando `draft up`. Questo comando consente di caricare il codice e i file di configurazione dell'applicazione nel cluster Kubernetes. In seguito esegue il Dockerfile per creare un'immagine del contenitore, esegue il push dell'immagine nel registro contenitori e infine esegue il grafico Helm per avviare l'applicazione.

```console
draft up
```

Output:

```
Draft Up Started: 'open-jaguar'
open-jaguar: Building Docker Image: SUCCESS ⚓  (28.0342s)
open-jaguar: Pushing Docker Image: SUCCESS ⚓  (7.0647s)
open-jaguar: Releasing Application: SUCCESS ⚓  (4.5056s)
open-jaguar: Build ID: 01BW3VVNZYQ5NQ8V1QSDGNVD0S
```

## <a name="test-the-application"></a>Test dell'applicazione

Per testare l'applicazione, usare il comando `draft connect`. Questo comando esegue un proxy sulla connessione del pod Kubernetes consentendo una connessione locale sicura. Al termine dell'operazione, è possibile accedere all'applicazione dall'URL specificato.

In alcuni casi, il download dell'immagine del contenitore e l'avvio dell'applicazione possono richiedere alcuni minuti. Se viene visualizzato un errore durante l'accesso all'applicazione, rieseguire la connessione.

```console
draft connect
```

Output:

```
Connecting to your app...SUCCESS...Connect to your app on localhost:46143
Starting log streaming...
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
== Spark has ignited ...
>> Listening on 0.0.0.0:4567
```

Al termine del test dell'applicazione, usare `Control+C` per interrompere la connessione proxy.

## <a name="expose-application"></a>Esporre l'applicazione

Durante il test di un'applicazione in Kubernetes, è possibile rendere disponibile l'applicazione in Internet. Questa operazione può essere eseguita tramite un servizio Kubernetes con un tipo di [LoadBalancer] [ kubernetes-service-loadbalancer] o [controller in ingresso][kubernetes-ingress]. Il documento presente illustra l'uso di un servizio Kubernetes.


In primo luogo, il pacchetto Draft deve essere aggiornato per specificare che è necessario creare un servizio con tipo `LoadBalancer`. A tale scopo, aggiornare il tipo di servizio nel file `values.yaml`.

```console
vi chart/java/values.yaml
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
deadly-squid-java   10.0.141.72   <pending>     80:32150/TCP   14m
```

Dopo che l'indirizzo EXTERNAL-IP passa da `pending` a `IP address`, usare `Control+C` per arrestare il processo kubectl watch.

```
deadly-squid-java   10.0.141.72   52.175.224.118   80:32150/TCP   17m
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
        get("/", (req, res) -> "Hello World, I'm Java - Draft Rocks!");
    }
}
```

Eseguire il comando `draft up` per distribuire nuovamente l'applicazione.

```console
draft up
```

Output

```
Draft Up Started: 'deadly-squid'
deadly-squid: Building Docker Image: SUCCESS ⚓  (18.0813s)
deadly-squid: Pushing Docker Image: SUCCESS ⚓  (7.9394s)
deadly-squid: Releasing Application: SUCCESS ⚓  (6.5005s)
deadly-squid: Build ID: 01BWK8C8X922F5C0HCQ8FT12RR
```

Infine, visualizzare l'applicazione per vedere gli aggiornamenti.

```console
curl 52.175.224.118
```

Output:

```
Hello World, I'm Java - Draft Rocks!
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Draft, vedere la documentazione relativa su GitHub.

> [!div class="nextstepaction"]
> [Documentazione di bozza][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[install-draft]: https://github.com/Azure/draft/blob/master/docs/install.md
[install-helm]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[kubernetes-ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md