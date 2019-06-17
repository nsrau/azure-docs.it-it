---
title: Funzioni di Azure in Kubernetes con KEDA
description: Informazioni su come eseguire le funzioni di Azure in Kubernetes nel cloud o locale usando KEDA, la scalabilità automatica basata su eventi basati su Kubernetes.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure funzioni, funzioni, elaborazione eventi, calcolo dinamico, architettura senza server, kubernetes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: c82ed7aa841f53f5c81f3281ed1b09926e565e75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077621"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Funzioni di Azure in Kubernetes con KEDA

Il runtime di funzioni di Azure offre flessibilità in hosting dove e come desiderato.  [KEDA](https://github.com/kedacore/kore) (basato su Kubernetes evento basato su ridimensionamento automatico) coppie senza problemi con il runtime di funzioni di Azure e degli strumenti per fornire la scalabilità basata sugli eventi in Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>Lavoro funzioni come basato su Kubernetes

Il servizio funzioni di Azure è costituito da due componenti principali: un runtime e un controller di scalabilità.  Il runtime di funzioni viene eseguita ed esegue il codice.  Il runtime include la logica su come attivare, registrare e gestire le esecuzioni di funzione.  L'altro componente è un controller di scalabilità.  Il controller di scalabilità monitora la frequenza degli eventi che sono destinate a funzione e ridimensiona in modo proattivo il numero di istanze che eseguono l'app.  Per altre informazioni, vedere [scalabilità di funzioni di Azure e l'hosting](functions-scale.md).

Basato su Kubernetes funzioni fornisce il runtime di funzioni in un [contenitore Docker](functions-create-function-linux-custom-image.md) guidato dagli eventi ridimensionamento tramite KEDA.  KEDA possibile ridurre le prestazioni per le istanze di 0 (quando non vengono generati eventi) e fino a *n* istanze. Ciò avviene esponendo le metriche personalizzate per il ridimensionamento automatico di Kubernetes (scalabilità orizzontale automatica Pod).  Uso di contenitori di funzioni con KEDA rende possibile replicare le funzioni senza server in qualsiasi cluster Kubernetes.  Queste funzioni possono essere distribuite tramite [i nodi virtuali servizi Kubernetes di Azure (AKS)](../aks/virtual-nodes-cli.md) funzionalità per l'infrastruttura senza server.

## <a name="managing-keda-and-functions-in-kubernetes"></a>Gestione KEDA e funzioni in Kubernetes

Per eseguire le funzioni nel cluster Kubernetes, è necessario installare il componente KEDA. È possibile installare questo componente usando [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Installazione di strumenti di base con le funzioni di Azure

Per impostazione predefinita, Core Tools viene installato sia componenti KEDA e Osiris, supportano basato sugli eventi e il ridimensionamento HTTP, rispettivamente.  L'installazione utilizza `kubectl` in esecuzione nel contesto corrente.

Installare KEDA nel cluster eseguendo il comando di installazione seguenti:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Distribuzione di un'app per le funzioni in Kubernetes

È possibile distribuire qualsiasi app per le funzioni in un cluster Kubernetes in esecuzione KEDA.  Poiché le funzioni vengono eseguite in un contenitore Docker, per il progetto è necessario un `Dockerfile`.  Se non già presente, è possibile aggiungere un file Docker eseguendo il comando seguente nella radice del progetto di funzioni:

```cli
func init --docker-only
```

Per compilare un'immagine e distribuire le funzioni in Kubernetes, eseguire il comando seguente:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Sostituire `<name-of-function-deployment>` con il nome dell'app per le funzioni.

Verrà creato un Kubernetes `Deployment` risorsa, una `ScaledObject` risorsa, e `Secrets`, che include le variabili di ambiente importate dal `local.settings.json` file.

## <a name="removing-a-function-app-from-kubernetes"></a>Rimozione di un'app per le funzioni da Kubernetes

Dopo la distribuzione è possibile rimuovere una funzione rimuovendo l'oggetto associato `Deployment`, `ScaledObject`, un `Secrets` creato.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>Disinstallazione KEDA da Kubernetes

È possibile eseguire il seguente comando degli strumenti core rimuovere KEDA da un cluster Kubernetes:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Trigger supportati in KEDA

KEDA è attualmente in versione beta con il supporto per i trigger di funzione di Azure seguenti:

* [Code di archiviazione di Azure](functions-bindings-storage-queue.md)
* [Code del Bus di servizio di Azure](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Fasi successive
Per altre informazioni, vedere le seguenti risorse:

* [Creare una funzione usando un'immagine personalizzata](functions-create-function-linux-custom-image.md)
* [Scrivere codici per Funzioni di Azure e testarle in locale](functions-develop-local.md)
* [Come funziona il piano a consumo funzioni di Azure](functions-scale.md)