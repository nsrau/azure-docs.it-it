---
title: Procedure consigliate per l'operatore - Isolamento cluster nel servizio Azure Kubernetes (AKS)
description: Procedure consigliate per l'operatore del cluster per l'isolamento nel servizio Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 3a4b62fb16745a3b226bda6c0574812278a34456
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52430226"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Procedure consigliate per l'isolamento cluster nel servizio Azure Kubernetes (AKS)

Quando si gestiscono i cluster nel servizio Azure Kubernetes (AKS), spesso è necessario isolare i team e i carichi di lavoro. AKS offre flessibilità nella modalità di esecuzione dei cluster multi-tenant e di isolamento delle risorse. Per ottimizzare l'investimento in Kubernetes, queste funzionalità di isolamento e multi-tenancy devono essere comprese e implementate.

Questo articolo sulle procedure consigliate è incentrato sull'isolamento per gli operatori del cluster. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Pianificare cluster multi-tenant e la separazione delle risorse
> * Usare l'isolamento logico o fisico nei cluster AKS

## <a name="design-clusters-for-multi-tenancy"></a>Progettare cluster per il multi-tenancy

Kubernetes offre funzionalità che consentono di isolare in modo logico team e carichi di lavoro nello stesso cluster. L'obiettivo consiste nel fornire il minor numero di privilegi, limitati alle risorse di cui ogni team ha bisogno. Uno [spazio dei nomi][k8s-namespaces] in Kubernetes crea un limite di isolamento logico. Ulteriori funzionalità di Kubernetes e considerazioni per l'isolamento e il multi-tenancy includono le aree seguenti:

* La **pianificazione** include l'uso di funzionalità di base come le quote di risorse e i budget di interruzione dei pod. Per altre informazioni su queste funzionalità, vedere [Procedure consigliate per le funzionalità di base dell'utilità di pianificazione in AKS][aks-best-practices-scheduler].
  * Le funzionalità più avanzate dell'utilità di pianificazione includono taint e tolleranze, selettori di nodo e affinità tra nodi e tra pod o anti-affinità. Per altre informazioni su queste funzionalità, vedere [Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione in AKS][aks-best-practices-advanced-scheduler].
* La **rete** include l'uso di criteri di rete per controllare il flusso del traffico in ingresso e in uscita dai pod.
* L'**autenticazione e l'autorizzazione** includono l'utente del controllo degli accessi in base al ruolo e l'integrazione di Azure Active Directory (AD), le identità del pod e i segreti in Azure Key Vault. Per altre informazioni su queste funzionalità, vedere [Procedure consigliate per l'autenticazione e l'autorizzazione in AKS][aks-best-practices-identity].
* I **contenitori** includono i criteri di sicurezza del pod, i contesti di sicurezza del pod e l'analisi di immagini e runtime per le vulnerabilità. Prevedono inoltre l'uso di App Armor o Seccomp (Secure Computing) per limitare l'accesso del contenitore al nodo sottostante.

## <a name="logically-isolate-clusters"></a>Isolare i cluster in modo logico

**Indicazioni sulle procedure consigliate**. Usare l'isolamento logico per separare team e progetti. Provare a ridurre al minimo il numero di cluster AKS fisici distribuiti per isolare i team o le applicazioni.

Con l'isolamento logico, un singolo cluster AKS può essere usato per più carichi di lavoro, team o ambienti. Gli [spazi dei nomi][k8s-namespaces] di Kubernetes costituiscono il limite di isolamento logico per i carichi di lavoro e le risorse.

![Isolamento logico di un cluster Kubernetes in AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

La separazione logica dei cluster fornisce in genere una maggiore densità del pod rispetto ai cluster isolati fisicamente. Il risultato è una minore capacità di calcolo in eccesso che resta inattiva nel cluster. Se combinato con il ridimensionamento automatico del cluster Kubernetes, è possibile aumentare o ridurre il numero dei nodi in base alle esigenze. Questa procedura consigliata per il ridimensionamento automatico consente di eseguire solo il numero di nodi richiesti e riduce al minimo i costi.

## <a name="physically-isolate-clusters"></a>Isolare i cluster in modo fisico

**Indicazioni sulle procedure consigliate**. Ridurre al minimo l'uso dell'isolamento fisico per ogni distribuzione di applicazioni o team distinto. Usare invece l'isolamento *logico*, come descritto nella sezione precedente.

Un approccio comune all'isolamento cluster consiste nell'usare cluster AKS fisicamente separati. In questo modello di isolamento ai team o ai carichi di lavoro viene assegnato il proprio cluster AKS. Spesso questo approccio costituisce il modo più semplice per isolare i carichi di lavoro o i team, ma aggiunge un sovraccarico a livello economico e di gestione. È infatti necessario gestire più cluster, oltre che fornire l'accesso e assegnare le autorizzazioni singolarmente. Vengono inoltre addebitati i costi per tutti i singoli nodi.

![Isolamento fisico di singoli cluster Kubernetes in AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

I cluster separati fisicamente hanno in genere una bassa densità del pod. Dal momento che ogni team o carico di lavoro ha il proprio cluster AKS, spesso il cluster è sottoposto a over-provisioning per le risorse di calcolo. Spesso, su questi nodi viene pianificato un numero ridotto di pod. La capacità inutilizzata sui nodi non può essere usata per le applicazioni o i servizi in fase di sviluppo da parte di altri team. Queste risorse in eccesso contribuiscono ai costi aggiuntivi nei cluster separati fisicamente.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato l'isolamento cluster. Per altre informazioni sulle operazioni cluster in AKS, vedere le procedure consigliate seguenti:

* [Funzionalità di base dell'utilità di pianificazione di Kubernetes][aks-best-practices-scheduler]
* [Funzionalità avanzate dell'utilità di pianificazione di Kubernetes][aks-best-practices-advanced-scheduler]
* [Autenticazione e autorizzazione][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
