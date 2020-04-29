---
title: Funzioni di Azure in Kubernetes con KEDA
description: Informazioni su come eseguire funzioni di Azure in Kubernetes nel cloud o in locale usando KEDA, la scalabilità automatica basata su eventi basata su Kubernetes.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78301676"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Funzioni di Azure in Kubernetes con KEDA

Il runtime di funzioni di Azure offre la flessibilità necessaria per ospitare il percorso e il modo desiderato.  Le coppie di scalabilità automatica basata su eventi di [Keda](https://keda.sh) (Kubernetes) con il runtime di funzioni di Azure e gli strumenti per fornire la scalabilità basata sugli eventi in Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Funzionamento delle funzioni basate su Kubernetes

Il servizio funzioni di Azure è costituito da due componenti principali: un runtime e un controller di scalabilità.  Il runtime di funzioni viene eseguito ed esegue il codice.  Il runtime include la logica su come attivare, registrare e gestire le esecuzioni di funzioni.  Il runtime di funzioni di Azure può essere eseguito *ovunque*.  L'altro componente è un controller di scalabilità.  Il controller di scalabilità monitora la frequenza degli eventi destinati alla funzione e ridimensiona in modo proattivo il numero di istanze che eseguono l'app.  Per altre informazioni, vedere [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md).

Le funzioni basate su Kubernetes forniscono il runtime di funzioni in un [contenitore Docker](functions-create-function-linux-custom-image.md) con scalabilità guidata dagli eventi tramite Keda.  KEDA può essere ridimensionato a 0 istanze (quando non si verificano eventi) e a *n* istanze. Questa operazione viene eseguita esponendo metriche personalizzate per il ridimensionamento automatico Kubernetes (Horizontal Pod AutoScaler).  L'uso di contenitori di funzioni con KEDA consente di replicare le funzionalità della funzione senza server in qualsiasi cluster Kubernetes.  Queste funzioni possono anche essere distribuite usando la funzionalità [nodi virtuali di Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) per l'infrastruttura senza server.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gestione di KEDA e funzioni in Kubernetes

Per eseguire funzioni nel cluster Kubernetes, è necessario installare il componente KEDA. È possibile installare questo componente usando [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-helm"></a>Installazione con Helm

Esistono diversi modi per installare KEDA in qualsiasi cluster Kubernetes, incluso Helm.  Le opzioni di distribuzione sono documentate nel [sito di Keda](https://keda.sh/deploy/).

## <a name="deploying-a-function-app-to-kubernetes"></a>Distribuzione di un'app per le funzioni in Kubernetes

È possibile distribuire qualsiasi app per le funzioni in un cluster Kubernetes che esegue KEDA.  Poiché le funzioni vengono eseguite in un contenitore Docker, il progetto necessita `Dockerfile`di un.  Se non ne è già presente uno, è possibile aggiungere un Dockerfile eseguendo il comando seguente nella radice del progetto di funzioni:

```cli
func init --docker-only
```

Per compilare un'immagine e distribuire le funzioni in Kubernetes, eseguire il comando seguente:

> [!NOTE]
> Gli strumenti di base utilizzeranno l'interfaccia della riga di comando di Docker per compilare e pubblicare l'immagine. Assicurarsi che Docker sia già installato e connesso all'account con `docker login`.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Sostituire `<name-of-function-deployment>` con il nome dell'app per le funzioni.

Viene creata una risorsa `Deployment` Kubernetes, una `ScaledObject` risorsa e `Secrets`, che include le `local.settings.json` variabili di ambiente importate dal file.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Distribuzione di un'app per le funzioni da un registro privato

Il flusso precedente funziona anche per i registri privati.  Se si esegue il pull dell'immagine del contenitore da un registro privato, `--pull-secret` includere il flag che fa riferimento al segreto Kubernetes che contiene le credenziali `func kubernetes deploy`del registro di sistema privato durante l'esecuzione.

## <a name="removing-a-function-app-from-kubernetes"></a>Rimozione di un'app per le funzioni da Kubernetes

Dopo la distribuzione, è possibile rimuovere una funzione rimuovendo `Deployment`l' `ScaledObject`oggetto associato `Secrets` ,, creato.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Disinstallazione di KEDA da Kubernetes

I passaggi per la disinstallazione di KEDA sono documentati [nel sito di Keda](https://keda.sh/deploy/).

## <a name="supported-triggers-in-keda"></a>Trigger supportati in KEDA

KEDA dispone del supporto per i trigger di funzione di Azure seguenti:

* [Code di Archiviazione di Azure](functions-bindings-storage-queue.md)
* [Code del bus di servizio di Azure](functions-bindings-service-bus.md)
* [Hub eventi di Azure](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [Coda RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Supporto del trigger HTTP

È possibile usare funzioni di Azure che espongono trigger HTTP, ma KEDA non li gestisce direttamente.  È possibile usare il trigger KEDA Prometheus per [scalare le funzioni http di Azure da 1 a *n* istanze](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Creare una funzione usando un'immagine personalizzata](functions-create-function-linux-custom-image.md)
* [Scrivere codici per Funzioni di Azure e testarle in locale](functions-develop-local.md)
* [Funzionamento del piano a consumo di funzioni di Azure](functions-scale.md)