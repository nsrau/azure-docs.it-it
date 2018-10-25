---
title: Istanze di contenitore di Azure e orchestrazione dei contenitori
description: Informazioni sull'interazione tra Istanze di contenitore di Azure e agenti di orchestrazione dei contenitori.
services: container-instances
author: seanmck
ms.service: container-instances
ms.topic: article
ms.date: 10/05/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: c17bdb5a81640a7162ae735a4633a31cdfffbb1d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803512"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Istanze di contenitore di Azure e agenti di orchestrazione dei contenitori

Grazie alle dimensioni ridotte e all'orientamento alle applicazioni, i contenitori sono particolarmente adatti per ambienti di recapito flessibili e architetture basate su microservizi. L'attività di automazione e gestione di un numero elevato di contenitori e della loro interazione è nota come *orchestrazione*. I contenitori di agenti di orchestrazione più diffusi includono Kubernetes, DC/OS e Docker Swarm.

Istanze di contenitore di Azure fornisce alcune funzionalità di base di pianificazione delle piattaforme di orchestrazione. E anche se non copre servizi con un valore superiore forniti da tali piattaforme, Istanze di contenitore di Azure può esserne complementare. Questo articolo descrive l'ambito di gestione di Istanze di contenitore di Azure e l'interazione con gli agenti di orchestrazione completi dei contenitori.

## <a name="traditional-orchestration"></a>Orchestrazione tradizionale

La definizione standard dell'orchestrazione include le attività seguenti:

- **Pianificazione**: data un'immagine del contenitore e una richiesta di risorse, trovare una macchina adatta in cui eseguire il contenitore.
- **Affinità/Antiaffinità**: specificare che i contenitori di un set devono essere eseguiti vicini tra loro (per le prestazioni) o sufficientemente distanti tra loro (disponibilità).
- **Monitoraggio dell'integrità**: monitorare gli errori dei contenitori e modificare automaticamente la pianificazione.
- **Failover**: tenere traccia degli elementi in esecuzione in ogni macchina e ripianificare i contenitori dalle macchine con errori ai nodi integri.
- **Scalabilità**: aggiungere o rimuovere istanze di contenitori per soddisfare la domanda, in modo automatico o manuale.
- **Rete**: fornire una rete di overlay per coordinare i contenitori nella comunicazione tra più computer host.
- **Individuazione del servizio**: consentire l'individuazione reciproca dei contenitori anche quando si spostano tra computer host e cambiano indirizzo IP.
- **Aggiornamenti coordinati delle applicazioni**: gestire gli aggiornamenti dei contenitori per evitare tempi di inattività delle applicazioni e consentire il rollback in caso di errore.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestrazione con Istanze di contenitore di Azure: un approccio a più livelli

Istanze di contenitore di Azure consente un approccio a più livelli all'orchestrazione, fornendo tutte le funzionalità di pianificazione e gestione necessarie per eseguire un singolo contenitore, consentendo alle piattaforme degli agenti di orchestrazione di gestire attività di multi-contenitore sul contenitore stesso.

Poiché l’infrastruttura sottostante per Istanze di contenitore è gestita da Azure, una piattaforma dell'agente di orchestrazione non ha bisogno di cercare un computer host appropriato in cui eseguire un singolo contenitore. L'elasticità del cloud garantisce che ci sia sempre un computer host a disposizione. L'agente di orchestrazione può invece concentrarsi sulle attività che semplificano lo sviluppo di architetture multi-contenitore, tra cui il ridimensionamento e gli aggiornamenti coordinati.

## <a name="potential-scenarios"></a>Potenziali scenari

Anche se l'integrazione degli agenti di orchestrazione con Istanze di contenitore di Azure è ancora agli inizi, si prevede che possano emergere alcuni ambienti diversi:

### <a name="orchestration-of-container-instances-exclusively"></a>Orchestrazione di Istanze di contenitore in modo esclusivo

Dato l'avvio rapido e la fatturazione al secondo, un ambiente basato esclusivamente su Istanze di contenitore di Azure rappresenta il modo più veloce per iniziare e gestire carichi di lavoro estremamente variabili.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Combinazione di Istanze di contenitore e contenitori in Macchine virtuali

Per i carichi di lavoro stabili e a esecuzione prolungata, l'orchestrazione di contenitori in un cluster di macchine virtuali dedicate è in genere più economica rispetto all'esecuzione degli stessi contenitori con Istanze di contenitore di Azure. Istanze di contenitore offre tuttavia un'ottima soluzione per la rapida espansione e contrazione della capacità complessiva, per gestire i picchi di utilizzo di breve durata o imprevisti.

Invece di aumentare il numero di macchine virtuali nel cluster e quindi distribuire altri contenitori in tali macchine, l'agente di orchestrazione può semplicemente pianificare i contenitori aggiuntivi in Istanze di contenitore di Azure ed eliminarli quando non sono più necessari.

## <a name="sample-implementation-virtual-kubelet-for-kubernetes"></a>Implementazione di esempio: Virtual Kubelet per Kubernetes

Il progetto [Virtual Kubelet][aci-connector-k8s] illustra come le piattaforme di orchestrazione dei contenitori si integrano con le istanze di contenitore di Azure.

Virtual Kubelet simula il [kubelet][kubelet-doc] di Kubernetes registrandosi come nodo con capacità illimitata e realizzando la creazione di [POD][pod-doc] come gruppi di contenitori in Istanze di contenitore di Azure.

È possibile creare connettori per altri agenti di orchestrazione che si integrano in modo simile con le primitive di piattaforma per combinare le funzionalità avanzate dell'API dell'agente di orchestrazione con la velocità e la semplicità di gestione dei contenitori in Istanze di contenitore di Azure.

## <a name="next-steps"></a>Passaggi successivi

Creare il primo contenitore usando la [guida introduttiva](container-instances-quickstart.md) di Istanze di contenitore di Azure.

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/