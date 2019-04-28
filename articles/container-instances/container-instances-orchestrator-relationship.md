---
title: Istanze di Azure Container e orchestrazione dei contenitori
description: Informazioni sull'interazione tra Istanze di Azure Container e agenti di orchestrazione dei contenitori.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 11/30/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 0a1e3c2facc10b68fe4b33d4cd0531f181b1e813
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60581122"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Istanze di Azure Container e agenti di orchestrazione dei contenitori

Grazie alle dimensioni ridotte e all'orientamento alle applicazioni, i contenitori sono particolarmente adatti per ambienti di recapito flessibili e architetture basate su microservizi. L'attività di automazione e gestione di un numero elevato di contenitori e della loro interazione è nota come *orchestrazione*. I contenitori di agenti di orchestrazione più diffusi includono Kubernetes, DC/OS e Docker Swarm.

Istanze di Azure Container fornisce alcune funzionalità di base di pianificazione delle piattaforme di orchestrazione. E anche se non copre servizi con un valore superiore forniti da tali piattaforme, Istanze di Azure Container può esserne complementare. Questo articolo descrive l'ambito di gestione di Istanze di Azure Container e l'interazione con gli agenti di orchestrazione completi dei contenitori.

## <a name="traditional-orchestration"></a>Orchestrazione tradizionale

La definizione standard dell'orchestrazione include le attività seguenti:

- **Pianificazione**: Data un'immagine del contenitore e una richiesta di risorse, trovare una macchina adatta in cui eseguire il contenitore.
- **Affinità/Antiaffinità**: Specificare che i contenitori di un set devono essere eseguiti vicini tra loro (per le prestazioni) o sufficientemente distanti tra loro (per la disponibilità).
- **Monitoraggio dell'integrità**: Monitorare gli errori dei contenitori e modificare automaticamente la pianificazione.
- **Failover**: Tenere traccia degli elementi in esecuzione in ogni computer e ripianificare i contenitori dai computer con errori ai nodi integri.
- **Ridimensionamento**: Aggiungere o rimuovere istanze di contenitori per soddisfare la domanda, in modo automatico o manuale.
- **Rete**: Fornire una rete di overlay per coordinare i contenitori nella comunicazione tra più computer host.
- **Individuazione del servizio**: Consentire l'individuazione reciproca dei contenitori anche quando si spostano tra computer host e cambiano indirizzo IP.
- **Aggiornamenti coordinati delle applicazioni**: Gestire gli aggiornamenti dei contenitori per evitare tempi di inattività delle applicazioni e consentire il rollback in caso di errore.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestrazione con Istanze di Azure Container: un approccio a più livelli

Istanze di Azure Container consente un approccio a più livelli all'orchestrazione, fornendo tutte le funzionalità di pianificazione e gestione necessarie per eseguire un singolo contenitore, consentendo alle piattaforme degli agenti di orchestrazione di gestire attività di multi-contenitore sul contenitore stesso.

Poiché l'infrastruttura sottostante delle istanze di contenitore è gestita da Azure, una piattaforma dell'agente di orchestrazione non ha bisogno di cercare un computer host appropriato in cui eseguire un singolo contenitore. L'elasticità del cloud garantisce che ci sia sempre un computer host a disposizione. L'agente di orchestrazione può invece concentrarsi sulle attività che semplificano lo sviluppo di architetture multi-contenitore, tra cui il ridimensionamento e gli aggiornamenti coordinati.

## <a name="scenarios"></a>Scenari

Anche se l'integrazione degli agenti di orchestrazione con Istanze di Azure Container è ancora agli inizi, si prevede che emergeranno alcuni ambienti diversi:

### <a name="orchestration-of-container-instances-exclusively"></a>Orchestrazione delle istanze di contenitore in modo esclusivo

Dato l'avvio rapido e la fatturazione al secondo, un ambiente basato esclusivamente su Istanze di Azure Container rappresenta il modo più veloce per iniziare e gestire carichi di lavoro estremamente variabili.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Combinazione di istanze di contenitore e contenitori in Macchine virtuali

Per i carichi di lavoro stabili e a esecuzione prolungata, l'orchestrazione di contenitori in un cluster di macchine virtuali dedicate è in genere più economica rispetto all'esecuzione degli stessi contenitori con Istanze di Azure Container. Le istanze di contenitore offrono tuttavia un'ottima soluzione per la rapida espansione e contrazione della capacità complessiva, per gestire i picchi di utilizzo di breve durata o imprevisti.

Invece di aumentare il numero di macchine virtuali nel cluster e quindi distribuire altri contenitori in tali macchine, l'agente di orchestrazione può semplicemente pianificare i contenitori aggiuntivi in Istanze di Azure Container ed eliminarli quando non sono più necessari.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Implementazione di esempio: nodi virtuali per il servizio Azure Kubernetes (AKS)

Per ridimensionare rapidamente i carichi di lavoro dell'applicazione in un cluster del [servizio Azure Kubernetes](../aks/intro-kubernetes.md) è possibile usare *nodi virtuali* creati dinamicamente in Istanze di Azure Container. Attualmente in anteprima, i nodi virtuali abilitano la comunicazione di rete tra i pod eseguiti in ACI e nel cluster del servizio Azure Kubernetes. 

I nodi virtuali supportano attualmente le istanze di contenitore di Linux. Per imparare a usare i nodi virtuali usare l'[interfaccia della riga di comando di Azure](https://go.microsoft.com/fwlink/?linkid=2047538) o il [portale di Azure](https://go.microsoft.com/fwlink/?linkid=2047545).

I nodi virtuali usano il [kubelet virtuale][aci-connector-k8s] open source per simulare il [kubelet][kubelet-doc] di Kubernetes eseguendo la registrazione come nodo con capacità illimitata. Il kubelet virtuale recapita la creazione di [pod][pod-doc] come gruppi di contenitori in Istanze di Azure Container.

Vedere il progetto [Virtual Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) (Kubelet virtuale) per altri esempi di estensione dell'API Kubernetes nelle piattaforme di contenitori senza server.

## <a name="next-steps"></a>Passaggi successivi

Creare il primo contenitore usando la [guida introduttiva](container-instances-quickstart.md) di Istanze di Azure Container.

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/