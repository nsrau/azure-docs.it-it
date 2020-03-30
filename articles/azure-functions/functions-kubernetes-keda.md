---
title: Azure Functions on Kubernetes with KEDA
description: Comprendere come eseguire Funzioni di Azure in Kubernetes nel cloud o in locale usando KEDA, basato su Kubernetes, basato su eventi Kubernetes con scalabilità automatica.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301676"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions on Kubernetes with KEDA

Il runtime di Funzioni di Azure offre flessibilità nell'hosting dove e come si vuole.  [KEDA](https://keda.sh) (Kubernetes-based Event Driven Autoscaling) si associa senza problemi con il runtime di Funzioni di Azure e gli strumenti per fornire la scala guidata dagli eventi in Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Come funzionano le funzioni basate su Kubernetes

Il servizio Funzioni di Azure è costituito da due componenti chiave: un runtime e un controller di scalabilità.  Il runtime di Funzioni esegue ed esegue il codice.  Il runtime include la logica su come attivare, registrare e gestire le esecuzioni di funzioni.  Il runtime di Funzioni di Azure può essere eseguito *ovunque.*  L'altro componente è un controller di scala.  Il controller di scalabilità monitora la frequenza di eventi destinati alla funzione e ridimensiona in modo proattivo il numero di istanze che eseguono l'app.  Per altre informazioni, vedere [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md).

Kubernetes-based Functions fornisce il runtime di funzioni in un [contenitore Docker](functions-create-function-linux-custom-image.md) con il ridimensionamento basato su eventi tramite KEDA.  KEDA può scalare in a 0 istanze (quando non si verificano eventi) e orizzontalmente a *n* istanze. A tale scopo, espone metriche personalizzate per il scaler automatico Kubernetes (Horizontal Pod Autoscaler).  L'utilizzo di contenitori Di funzioni con KEDA consente di replicare le funzionalità senza server in qualsiasi cluster Kubernetes.  Queste funzioni possono essere distribuite anche usando la funzionalità dei nodi virtuali di [Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) per l'infrastruttura senza server.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gestione di KEDA e funzioni in Kubernetes

Per eseguire Funzioni nel cluster Kubernetes, è necessario installare il componente KEDA. È possibile installare questo componente usando gli strumenti di base di Funzioni di [Azure.](functions-run-local.md)

### <a name="installing-with-helm"></a>Installazione con Helm

Ci sono vari modi per installare KEDA in qualsiasi cluster Kubernetes tra cui Helm.  Le opzioni di distribuzione sono documentate sul [sito KEDA.](https://keda.sh/deploy/)

## <a name="deploying-a-function-app-to-kubernetes"></a>Distribuzione di un'app per le funzioni in KubernetesDeploying a function app to Kubernetes

È possibile distribuire qualsiasi app per le funzioni in un cluster Kubernetes che esegue KEDA.  Poiché le funzioni vengono eseguite in un `Dockerfile`contenitore Docker, il progetto richiede un file .  Se non ne ha già uno, è possibile aggiungere un Dockerfile eseguendo il comando seguente nella radice del progetto Functions:

```cli
func init --docker-only
```

Per compilare un'immagine e distribuire le funzioni in Kubernetes, eseguire il comando seguente:

> [!NOTE]
> Gli strumenti di base sfrutteranno l'interfaccia della riga di comando per compilare e pubblicare l'immagine. Assicurati di avere docker già installato e `docker login`collegato al tuo account con .

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Sostituire `<name-of-function-deployment>` con il nome dell'app per le funzioni.

In questo modo vengono `Deployment` create una `ScaledObject` risorsa `Secrets`Kubernetes, una `local.settings.json` risorsa e , che include le variabili di ambiente importate dal file.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Distribuzione di un'app per le funzioni da un registro privatoDeploying a function app from a private registry

Il flusso di cui sopra funziona anche per i registri privati.  Se si estrae l'immagine del `--pull-secret` contenitore da un registro privato, includere il `func kubernetes deploy`flag che fa riferimento al segreto Kubernetes che contiene le credenziali del Registro di sistema private durante l'esecuzione di .

## <a name="removing-a-function-app-from-kubernetes"></a>Rimozione di un'app per le funzioni da Kubernetes

Dopo la distribuzione è possibile rimuovere una `Deployment` `ScaledObject`funzione `Secrets` rimuovendo l'oggetto associato , ovvero un oggetto created .

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Disinstallazione di KEDA da Kubernetes

I passaggi per disinstallare KEDA sono documentati [sul sito KEDA](https://keda.sh/deploy/).

## <a name="supported-triggers-in-keda"></a>Trigger supportati in KEDASupported triggers in KEDA

KEDA include il supporto per i trigger di funzione di Azure seguenti:KEDA has support for the following Azure Function triggers:

* [Code di Archiviazione di Azure](functions-bindings-storage-queue.md)
* [Code del bus di servizio di AzureAzure Service Bus Queues](functions-bindings-service-bus.md)
* [Azure Event / IoT Hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Coda RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Supporto trigger HTTP

È possibile usare Funzioni di Azure che espongono trigger HTTP, ma KEDA non li gestisce direttamente.  È possibile sfruttare il trigger di prometeo KEDA per ridimensionare le [funzioni di Azure HTTP da 1 a *n* istanze](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Creare una funzione usando un'immagine personalizzataCreate a function using a custom image](functions-create-function-linux-custom-image.md)
* [Scrivere codici per Funzioni di Azure e testarle in locale](functions-develop-local.md)
* [Funzionamento del piano di consumo delle funzioni di AzureHow the Azure Function Consumption plan works](functions-scale.md)