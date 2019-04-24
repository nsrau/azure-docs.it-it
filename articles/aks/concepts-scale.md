---
title: Concetti - Ridimensionare le applicazioni nel servizio Azure Kubernetes
description: Informazioni sul ridimensionamento nel servizio Azure Kubernetes, tra cui il ridimensionamento automatico orizzontale dei pod, il ridimensionamento automatico del cluster e il connettore di Istanze di Azure Container.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: d7df4d2c7e824f143201e2c6af220730bcd38fb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466959"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Opzioni di ridimensionamento per le applicazioni nel servizio Azure Kubernetes

Quando si eseguono applicazioni nel servizio Azure Kubernetes può risultare necessario aumentare o ridurre la quantità di risorse di calcolo. Se cambia il numero di istanze dell'applicazione necessarie, potrebbe risultare necessario cambiare anche il numero di nodi Kubernetes sottostanti. Potrebbe anche nascere l'esigenza di effettuare rapidamente il provisioning di un numero elevato di istanze aggiuntive dell'applicazione.

Questo articolo introduce i principali concetti utili per gestire il ridimensionamento delle applicazioni nel servizio Azure Kubernetes:

- [Ridimensionamento manuale](#manually-scale-pods-or-nodes)
- [Ridimensionamento automatico orizzontale dei pod](#horizontal-pod-autoscaler)
- [Ridimensionamento automatico del cluster](#cluster-autoscaler)
- [Integrazione di Istanza di contenitore di Azure con il servizio Azure Kubernetes](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Ridimensionare manualmente i pod o i nodi

È possibile ridimensionare manualmente le repliche (pod) e i nodi per verificare come risponde l'applicazione a una modifica delle risorse disponibili e dello stato. Il ridimensionamento manuale delle risorse consente anche di definire una quantità specifica di risorse da usare per mantenere un costo fisso, ad esempio il numero di nodi. Per il ridimensionamento manuale occorre definire il numero di repliche o di nodi e l'API di Kubernetes pianifica la creazione di pod aggiuntivi o lo svuotamento dei nodi.

Per informazioni introduttive sul ridimensionamento manuale di pod e nodi, vedere [Ridimensionare le applicazioni nel servizio Azure Kubernetes][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Ridimensionamento automatico orizzontale dei pod

Kubernetes usa il ridimensionamento automatico orizzontale dei pod per monitorare la richiesta di risorse e adattare automaticamente il numero di repliche. Per impostazione predefinita, il ridimensionamento automatico orizzontale dei pod controlla l'API Metriche ogni 30 secondi per rilevare eventuali modifiche richieste nel numero di repliche. Quando sono necessarie modifiche, il numero di repliche viene aumentato o ridotto di conseguenza. Il ridimensionamento automatico orizzontale dei pod può essere applicato ai cluster servizio Azure Kubernetes in cui è stato distribuito Metrics Server per Kubernetes 1.8+.

![Ridimensionamento automatico orizzontale dei pod Kubernetes](media/concepts-scale/horizontal-pod-autoscaling.png)

Quando si configura il ridimensionamento automatico orizzontale dei pod per una distribuzione specifica, si definisce il numero minimo e massimo di repliche che può essere eseguito. È anche possibile definire le metriche da monitorare e su cui basare qualsiasi decisione per il ridimensionamento, ad esempio l'utilizzo della CPU.

Per informazioni introduttive sul ridimensionamento automatico orizzontale dei pod in servizio Azure Kubernetes, vedere [Scalare automaticamente i pod][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Raffreddamento degli eventi di ridimensionamento

Dato che il ridimensionamento automatico orizzontale dei pod controlla l'API Metriche ogni 30 secondi, è possibile che eventi di ridimensionamento precedenti non siano stati completati prima dell'esecuzione del controllo successivo. A causa di questo comportamento, il ridimensionamento automatico orizzontale dei pod potrebbe modificare il numero di repliche prima che l'evento di ridimensionamento precedente sia riuscito a ricevere il carico di lavoro dell'applicazione e le richieste di risorse da modificare di conseguenza.

Per ridurre al minimo questi eventi di race condition, è possibile impostare valori di raffreddamento o ritardo. Questi valori indicano il tempo di attesa di un evento di ridimensionamento da parte del ridimensionamento automatico orizzontale dei pod prima che possa essere attivato un altro evento di ridimensionamento. Questo comportamento consente al nuovo numero di repliche di diventare effettivo e all'API Metriche di rispecchiare il carico di lavoro distribuito. Per impostazione predefinita, il ritardo per gli eventi di aumento delle risorse è di 3 minuti e il ritardo per gli eventi di riduzione delle risorse è di 5 minuti.

Potrebbe essere necessario regolare questi valori di raffreddamento. I valori di raffreddamento predefiniti potrebbero dare l'impressione che il ridimensionamento automatico orizzontale dei pod non intervenga in modo sufficientemente rapido sul numero delle repliche. Ad esempio, per aumentare più rapidamente il numero di repliche in uso, ridurre `--horizontal-pod-autoscaler-upscale-delay` quando si creano le definizioni per il ridimensionamento automatico orizzontale dei pod con `kubectl`.

## <a name="cluster-autoscaler"></a>Ridimensionamento automatico del cluster

Per rispondere alle variazioni della domanda di pod, Kubernetes ha una scalabilità automatica di cluster (attualmente in anteprima nel servizio contenitore di AZURE) che consente di regolare il numero di nodi basati sulle risorse di calcolo richiesto nel pool di nodi. Per impostazione predefinita, il ridimensionamento automatico del cluster controlla il server API ogni 10 secondi per rilevare eventuali modifiche necessarie al numero di nodi. Se il ridimensionamento automatico del cluster determina che è necessaria una modifica, il numero di nodi nel cluster servizio Azure Kubernetes viene aumentato o ridotto di conseguenza. Il ridimensionamento automatico del cluster funziona con i cluster servizio Azure Kubernetes abilitati per RBAC che eseguono Kubernetes 1.10.x o versione successiva.

![Ridimensionamento automatico del cluster Kubernetes](media/concepts-scale/cluster-autoscaler.png)

Il ridimensionamento automatico del cluster viene generalmente usato insieme al ridimensionamento automatico orizzontale dei pod. In combinazione, il l ridimensionamento automatico orizzontale dei pod aumenta o riduce il numero di pod in base alle richieste dell'applicazione e il ridimensionamento automatico del cluster regola di conseguenza il numero di nodi in base alle necessità per l'esecuzione di tali pod aggiuntivi.

Per informazioni introduttive sul ridimensionamento automatico del cluster nel servizio Azure Kubernetes, vedere [Ridimensionamento automatico del cluster sul servizio Azure Kubernetes][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Eventi di aumento delle risorse

Se un nodo non ha risorse di calcolo sufficienti per eseguire un pod richiesto, quel pod non può procedere nel processo di pianificazione. Il pod non può essere avviato a meno che non siano disponibili risorse di calcolo aggiuntive all'interno del pool di nodi.

Quando il ridimensionamento automatico del cluster rileva pod non pianificabili a causa dei vincoli di risorse del pool di nodi, il numero di nodi all'interno del pool viene aumentato per fornire risorse di calcolo aggiuntive. Dopo la distribuzione corretta di tali nodi aggiuntivi e quando sono disponibili per l'uso all'interno del pool di nodi, i pod vengono quindi pianificati per l'esecuzione su tali nodi.

Se l'applicazione deve essere ridimensionata rapidamente, alcuni pod potrebbero rimanere in attesa di pianificazione fino a quando i nodi aggiuntivi distribuiti dal ridimensionamento automatico del cluster non possono accettare i pod pianificati. Per le applicazioni con richieste burst elevate, è possibile gestire il ridimensionamento con nodi virtuali e Istanze di Azure Container.

### <a name="scale-down-events"></a>Eventi di riduzione delle risorse

Il ridimensionamento automatico del cluster esegue il monitoraggio anche dello stato di pianificazione dei pod per i nodi che non hanno ricevuto di recente nuove richieste di pianificazione. Questo scenario indica che il pool di nodi ha più risorse di calcolo del necessario e che il numero di nodi può essere ridotto.

Un nodo che supera una soglia perché non è più richiesto per 10 minuti viene pianificato per l'eliminazione per impostazione predefinita. Quando si verifica questa situazione, i pod vengono pianificati per l'esecuzione su altri nodi all'interno del pool di nodi e il ridimensionamento automatico del cluster riduce il numero di nodi.

Le applicazioni potrebbero riscontrare alcuni problemi mentre i pod vengono pianificati per nodi diversi quando il ridimensionamento automatico del cluster riduce il numero di nodi. Per ridurre al minimo i disagi, evitare le applicazioni che usano una singola istanza di pod.

## <a name="burst-to-azure-container-instances"></a>Ridimensionamento burst in Istanze di Azure Container

Per ridimensionare rapidamente il cluster del servizio Azure Kubernetes, è possibile ricorrere all'integrazione con Istanze di Azure Container. Kubernetes include componenti predefiniti per modificare il numero di repliche e di nodi. Tuttavia, se l'applicazione deve essere ridimensionata rapidamente, il ridimensionamento automatico orizzontale dei pod può pianificare più pod di quelli che possono essere forniti dalle risorse di calcolo esistenti nel pool di nodi. Se configurato, questo scenario attiverebbe il ridimensionamento automatico del cluster per distribuire nodi aggiuntivi nel pool di nodi, ma potrebbero essere necessari alcuni minuti prima che venga completato il provisioning di tali nodi e prima che tali nodi consentano all'utilità di pianificazione di Kubernetes di eseguire pod su di essi.

![Ridimensionamento burst di Kubernetes in Istanze di contenitore di Azure](media/concepts-scale/burst-scaling.png)

Istanze di Azure Container consente di distribuire rapidamente istanze di contenitore senza sovraccarico aggiuntivo per l'infrastruttura. Quando ci si connette con il servizio Azure Kubernetes, Istanze di Azure Container diventa un'estensione logica protetta del cluster servizio Azure Kubernetes. Il componente kubelet virtuale viene installato nel cluster servizio Azure Kubernetes che presenta Istanze di Azure Container come nodo Kubernetes virtuale. Kubernetes può quindi pianificare i pod che vengono eseguiti come istanze di Istanze di Azure Container tramite i nodi virtuali e non come pod sui nodi macchina virtuale direttamente nel cluster del servizio Azure Kubernetes. I nodi virtuali sono attualmente in anteprima nel servizio contenitore di AZURE.

L'applicazione non richiede alcuna modifica per usare i nodi virtuali. Le distribuzioni possono essere ridimensionate su Istanze di Azure Container e il servizio Azure Kubernetes senza ritardo mentre il ridimensionamento automatico del cluster distribuisce nuovi nodi nel cluster del servizio Azure Kubernetes.

I nodi virtuali vengono distribuiti in una subnet aggiuntiva nella stessa rete virtuale del cluster del servizio Azure Kubernetes. Questa configurazione di rete virtuale consente di proteggere il traffico tra Istanze di Azure Container e il servizio Azure Kubernetes. Come un cluster AKS, un'istanza di Istanze di contenitore di Azure è una risorsa di calcolo logica e sicura isolata dagli altri utenti.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare le funzionalità di ridimensionamento delle applicazioni, seguire innanzitutto la [guida introduttiva per creare un cluster AKS con l'interfaccia della riga di comando di Azure][aks-quickstart]. È quindi possibile iniziare a ridimensionare manualmente o automaticamente le applicazioni nel cluster servizio Azure Kubernetes:

- Ridimensionare manualmente i [pod][aks-manually-scale-pods] o i [nodi][aks-manually-scale-nodes]
- Usare il [ridimensionamento automatico orizzontale dei pod][aks-hpa]
- Usare il [ridimensionamento automatico del cluster][aks-cluster-autoscaler]

Per altre informazioni sui concetti fondamentali di Kubernetes e del servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Kubernetes / Cluster servizio Azure Kubernetes e carichi di lavoro][aks-concepts-clusters-workloads]
- [Kubernetes / Accesso e identità per AKS][aks-concepts-identity]
- [Kubernetes / Sicurezza di servizio Azure Kubernetes][aks-concepts-security]
- [Kubernetes / Reti virtuali in servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes / Archiviazione in servizio Azure Kubernetes][aks-concepts-storage]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
