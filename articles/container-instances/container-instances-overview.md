---
title: Informazioni su Istanze di Azure Container
description: Il servizio Istanze di Azure Container rappresenta il modo più semplice e rapido per eseguire contenitori isolati in Azure, senza dover gestire macchine virtuali né adottare un agente di orchestrazione di livello superiore.
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 11/30/2018
ms.author: seanmck
ms.custom: seodec18, mvc
ms.openlocfilehash: ba454965ff2bb78ebe526e71d9280200b1f4b08b
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187195"
---
# <a name="what-is-azure-container-instances"></a>Informazioni su Istanze di Azure Container

I contenitori si stanno affermando come soluzione preferita per la creazione di pacchetti, la distribuzione e la gestione di applicazioni cloud. Istanze di contenitore di Azure rappresenta il modo più semplice e rapido per eseguire un contenitore in Azure, senza dover gestire macchine virtuali né dover adottare un servizio di livello superiore.

Istanze di contenitore di Azure è un'ottima soluzione per qualsiasi scenario e funziona anche in contenitori isolati, inclusi i processi di compilazione, l'automazione di attività e le applicazioni semplici. Per gli scenari in cui si rende necessaria l'orchestrazione completa dei contenitori, quali il rilevamento dei servizi tra più contenitori, la scalabilità automatica e gli aggiornamenti coordinati delle applicazioni, è consigliabile usare [Azure Kubernetes Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Tempi di avvio rapidi

I contenitori offrono notevoli vantaggi in termini di avvio rispetto alle macchine virtuali. Istanze di contenitore di Azure consente di avviare contenitori in Azure in pochi secondi, senza dover gestire VM né doverne effettuare il provisioning.

## <a name="public-ip-connectivity-and-dns-name"></a>Connettività tramite indirizzo IP pubblico e nome DNS

Istanze di contenitore di Azure consente di esporre i contenitori direttamente in Internet con un indirizzo IP e un nome di dominio completo. Quando si crea un'istanza di contenitore, è possibile specificare un'etichetta personalizzata per il nome DNS, in modo che l'applicazione sia raggiungibile tramite *etichettapersonalizzata*.*areaazure*.contenitoreazure.io.

## <a name="hypervisor-level-security"></a>Sicurezza a livello di hypervisor

In passato i contenitori offrivano l'isolamento di dipendenze delle applicazioni e la governance delle risorse, ma non erano considerati sufficientemente protetti per l'uso di multi-tenant ostili. Istanze di contenitore di Azure garantisce all'applicazione in un contenitore lo stesso livello di isolamento di cui usufruirebbe in una VM.

## <a name="custom-sizes"></a>Dimensioni personalizzate

I contenitori sono in genere ottimizzati per l'esecuzione di un'applicazione singola, ma le esigenze specifiche di tale applicazione possono variare notevolmente. Istanze di contenitore di Azure supporta un utilizzo ottimale consentendo di specificare esattamente memoria e core CPU. Si paga in base alle risorse necessarie con fatturazione al secondo ed è così possibile ottimizzare la spesa in base alle esigenze effettive.

Per i processi a elevato utilizzo di calcolo, ad esempio l'apprendimento automatico, Istanze di contenitore di Azure può pianificare contenitori Linux per l'uso di [risorse GPU](container-instances-gpu.md) NVIDIA Tesla (anteprima).

## <a name="persistent-storage"></a>Archiviazione permanente

Per recuperare e rendere persistente lo stato con Istanze di contenitore di Azure, è disponibile il [montaggio diretto di condivisioni file di Azure](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Contenitori Linux e Windows

Istanze di contenitore di Azure consente di pianificare contenitori sia Windows che Linux con la stessa API. È sufficiente specificare il tipo di sistema operativo quando si creano i [gruppi di contenitori](container-instances-container-groups.md).

Alcune funzionalità attualmente sono limitate ai contenitori Linux. Microsoft si sta impegnando per garantire parità di funzionalità rispetto ai contenitori Windows. Nel frattempo, per informazioni sulle attuali differenze tra le piattaforme vedere [Quote e aree disponibili per Istanze di contenitore di Azure](container-instances-quotas.md).

Istanze di contenitore di Azure supporta le immagini di Windows che si basano sulle versioni Long-Term Servicing Channel (LTSC). Le versioni Canale semestrale di Windows, come ad esempio 1709 e 1803, non sono supportate.

## <a name="co-scheduled-groups"></a>Gruppi con pianificazione condivisa

Istanze di contenitore di Azure supporta la pianificazione di [gruppi multi-contenitore](container-instances-container-groups.md) che condividono un computer host, una rete locale, un archivio e un ciclo di vita. Questo consente di combinare il contenitore dell'applicazione principale con altri contenitori di ruoli di supporto, come quelli collaterali di registrazione.

## <a name="virtual-network-deployment-preview"></a>Distribuzione in una rete virtuale (anteprima)

Questa funzionalità di Istanze di contenitore di Azure, attualmente in anteprima, consente [la distribuzione di istanze di contenitore in una rete virtuale di Azure](container-instances-vnet.md). Quando sono distribuite in una subnet all'interno della rete virtuale, le istanze di contenitore possono comunicare in modo sicuro con altre risorse nella rete virtuale, incluse le risorse in locale (tramite [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ ExpressRoute](../expressroute/expressroute-introduction.md)).

> [!IMPORTANT]
> Alcune funzionalità di Istanze di contenitore di Azure sono in anteprima e [si applicano alcune limitazioni](container-instances-vnet.md#preview-limitations). Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di queste funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="next-steps"></a>Passaggi successivi

Provare a distribuire un contenitore in Azure con un comando singolo, seguendo le indicazioni della guida introduttiva:

> [!div class="nextstepaction"]
> [Guida introduttiva di Istanze di contenitore di Azure](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
