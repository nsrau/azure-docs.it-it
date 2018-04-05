---
title: Panoramica di Istanze di contenitore di Azure
description: Informazioni su Istanze di contenitore di Azure
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 03/23/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: d6e0637974d8076fc610d7154ad507f4e7af0cfa
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="azure-container-instances"></a>Istanze di contenitore di Azure

I contenitori si stanno affermando come soluzione preferita per la creazione di pacchetti, la distribuzione e la gestione di applicazioni cloud. Istanze di contenitore di Azure rappresenta il modo più semplice e rapido per eseguire un contenitore in Azure, senza dover gestire macchine virtuali né dover adottare un servizio di livello superiore.

Istanze di contenitore di Azure è un'ottima soluzione per qualsiasi scenario e funziona anche in contenitori isolati, inclusi i processi di compilazione, l'automazione di attività e le applicazioni semplici. Per gli scenari in cui si rende necessaria l'orchestrazione completa dei contenitori, quali il rilevamento dei servizi tra più contenitori, la scalabilità automatica e gli aggiornamenti coordinati delle applicazioni, è consigliabile usare il [servizio contenitore di Azure](../aks/index.yml).

## <a name="fast-startup-times"></a>Tempi di avvio rapidi

I contenitori offrono notevoli vantaggi in termini di avvio rispetto alle macchine virtuali. Istanze di contenitore di Azure consente di avviare contenitori in Azure in pochi secondi, senza dover gestire VM né doverne effettuare il provisioning.

## <a name="hypervisor-level-security"></a>Sicurezza a livello di hypervisor

In passato i contenitori offrivano l'isolamento di dipendenze delle applicazioni e la governance delle risorse, ma non erano considerati sufficientemente protetti per l'uso di multi-tenant ostili. Istanze di contenitore di Azure garantisce all'applicazione in un contenitore lo stesso livello di isolamento di cui usufruirebbe in una VM.

## <a name="custom-sizes"></a>Dimensioni personalizzate

I contenitori sono in genere ottimizzati per l'esecuzione di un'applicazione singola, ma le esigenze specifiche di tale applicazione possono variare notevolmente. Istanze di contenitore di Azure supporta un utilizzo ottimale consentendo di specificare esattamente memoria e core CPU. Si paga in base alle risorse necessarie con fatturazione al secondo ed è così possibile ottimizzare la spesa in base alle esigenze effettive.

## <a name="public-ip-connectivity"></a>Connettività tramite indirizzo IP pubblico

Istanze di contenitore di Azure consente di esporre i contenitori direttamente in Internet con un indirizzo IP pubblico e un'etichetta del nome DNS. In futuro, le funzionalità di rete verranno estese all'integrazione con reti virtuali, servizi di bilanciamento del carico e altre componenti essenziali dell'infrastruttura di rete di Azure.

## <a name="persistent-storage"></a>Archiviazione permanente

Per recuperare e rendere persistente lo stato con Istanze di contenitore di Azure, è disponibile il [montaggio diretto di condivisioni file di Azure](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Contenitori Linux e Windows

Istanze di contenitore di Azure consente di pianificare contenitori sia Windows che Linux con la stessa API. È sufficiente specificare il tipo di sistema operativo quando si creano i [gruppi di contenitori](container-instances-container-groups.md).

Alcune funzionalità attualmente sono limitate ai contenitori Linux. Microsoft si sta impegnando per garantire parità di funzionalità rispetto ai contenitori Windows. Nel frattempo, per informazioni sulle attuali differenze tra le piattaforme vedere [Quote e aree disponibili per Istanze di contenitore di Azure](container-instances-quotas.md).

## <a name="co-scheduled-groups"></a>Gruppi con pianificazione condivisa

Istanze di contenitore di Azure supporta la pianificazione di [gruppi multi-contenitore](container-instances-container-groups.md) che condividono un computer host, una rete locale, un archivio e un ciclo di vita. Questo consente di combinare il contenitore dell'applicazione principale con altri contenitori di ruoli di supporto, come quelli collaterali di registrazione.

## <a name="next-steps"></a>Passaggi successivi

Provare a distribuire un contenitore in Azure con un comando singolo, seguendo le indicazioni della [guida introduttiva](container-instances-quickstart.md).
