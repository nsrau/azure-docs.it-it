---
title: Funzioni di Azure in Kubernetes con KEDA
description: Informazioni su come eseguire Funzioni di Azure in Kubernetes nel cloud o in locale usando KEDA, la scalabilità automatica basata su eventi di Kubernetes.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 525635ef40437fe308c52e2d5aba2c97ed8f20e7
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927533"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Funzioni di Azure in Kubernetes con KEDA

Il runtime di Funzioni di Azure offre la flessibilità necessaria per eseguire l'hosting dove e come si vuole.  [KEDA](https://keda.sh) (Kubernetes-based Event Driven Autoscaling) si integra perfettamente con il runtime e i tool di Funzioni di Azure per offrire la scalatura gestita dagli eventi in Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Come operano le funzioni basate su Kubernetes

Il servizio Funzioni di Azure è costituito da due componenti principali: un runtime e un controller di scalabilità.  Il runtime di Funzioni di Azure esegue il codice.  Il runtime include logica per attivare, registrare e gestire le esecuzioni di funzioni.  Il runtime di Funzioni di Azure può essere eseguito *ovunque* .  L'altro componente è un controller di scalabilità.  Il controller di scalabilità monitora la frequenza degli eventi destinati alla funzione e ridimensiona in modo proattivo il numero di istanze che eseguono l'app.  Per altre informazioni, vedere [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md).

Le funzioni basate su Kubernetes offrono il runtime di Funzioni in un [contenitore Docker](functions-create-function-linux-custom-image.md) con scalabilità guidata dagli eventi tramite KEDA.  KEDA può ridurre in scala fino a 0 istanze (quando non si verificano eventi) e aumentare fino a *n* istanze. Questa operazione viene eseguita esponendo metriche personalizzate per la scalabilità automatica Kubernetes (Horizontal Pod AutoScaler).  L'uso di contenitori di Funzioni con KEDA consente di replicare le capacità della funzione serverless in qualsiasi cluster Kubernetes.  Queste funzioni possono essere distribuite anche usando la funzionalità [nodi virtuali dei servizi Azure Kubernetes (AKS)](../aks/virtual-nodes-cli.md) per l'infrastruttura serverless.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gestione di KEDA e funzioni in Kubernetes

Per eseguire Funzioni nel cluster Kubernetes, è necessario installare il componente KEDA. È possibile installare questo componente usando [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-helm"></a>Installare con Helm

Esistono diversi modi per installare KEDA in qualsiasi cluster Kubernetes, tra cui Helm.  Le opzioni di distribuzione sono documentate nel [sito KEDA](https://keda.sh/docs/1.4/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Distribuzione di un'app per le funzioni in Kubernetes

È possibile distribuire qualsiasi app per le funzioni in un cluster Kubernetes che esegue KEDA.  Poiché le funzioni vengono eseguite in un contenitore Docker, il progetto richiede un `Dockerfile`.  Se non ne ha già uno, è possibile aggiungere un Dockerfile eseguendo il comando seguente nella radice del progetto di Funzioni:

> [!NOTE]
> Gli strumenti di base creano automaticamente il Dockerfile per funzioni di Azure scritte in .NET, node, Python o PowerShell. Per le app per le funzioni scritte in Java, il Dockerfile deve essere creato manualmente. Usare l'elenco di [Immagini](https://github.com/Azure/azure-functions-docker) di funzioni di Azure per trovare l'immagine corretta per basare la funzione di Azure.

```cli
func init --docker-only
```

Per compilare un'immagine e distribuire le funzioni in Kubernetes, eseguire il comando seguente:

> [!NOTE]
> Gli strumenti di base useranno l'interfaccia della riga di comando di Docker per compilare e pubblicare l'immagine. Assicurarsi che Docker sia già installato e connesso all'account con `docker login`.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Sostituire `<name-of-function-deployment>` con il nome dell'app per le funzioni.

Il comando Distribuisci esegue una serie di azioni:
1. Il Dockerfile creato in precedenza viene usato per compilare un'immagine locale per l'app per le funzioni.
2. L'immagine locale viene contrassegnata e inserita nel registro contenitori in cui l'utente è connesso.
3. Un manifesto viene creato e applicato al cluster che definisce una risorsa Kubernetes `Deployment` , una `ScaledObject` risorsa e `Secrets` , che include le variabili di ambiente importate dal `local.settings.json` file.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Distribuzione di un'app per le funzioni da un registro privato

Il flusso precedente funziona anche per i registri privati.  Se si esegue il pull dell'immagine del contenitore da un registro privato, includere il flag `--pull-secret` che fa riferimento al segreto Kubernetes contenente le credenziali del registro di sistema privato durante l'esecuzione di `func kubernetes deploy`.

## <a name="removing-a-function-app-from-kubernetes"></a>Rimozione di un'app per le funzioni da Kubernetes

Dopo la distribuzione è possibile rimuovere una funzione rimuovendo gli elementi `Deployment`, `ScaledObject` e `Secrets` creati.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Disinstallazione di KEDA da Kubernetes

I passaggi per la disinstallazione di KEDA sono documentati nel [sito KEDA](https://keda.sh/docs/1.4/deploy/).

## <a name="supported-triggers-in-keda"></a>Trigger supportati in KEDA

KEDA supporta i trigger di Funzioni di Azure seguenti:

* [Code di archiviazione di Azure](functions-bindings-storage-queue.md)
* [Code del bus di servizio di Azure](functions-bindings-service-bus.md)
* [Hub eventi/IoT di Azure](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Coda RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Supporto del trigger HTTP

È possibile usare funzioni di Azure che espongono trigger HTTP, ma KEDA non li gestisce direttamente.  È possibile utilizzare il trigger prometheus di KEDA per [scalare le funzioni HTTP di Azure da 1 a *n* istanze](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Creare una funzione tramite un'immagine personalizzata](functions-create-function-linux-custom-image.md)
* [Scrivere codici per Funzioni di Azure e testarle in locale](functions-develop-local.md)
* [Funzionamento del piano a consumo di Funzioni di Azure](functions-scale.md)