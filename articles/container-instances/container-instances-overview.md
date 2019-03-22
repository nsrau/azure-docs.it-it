---
title: Informazioni su Istanze di Azure Container
description: Il servizio Istanze di Azure Container rappresenta il modo più semplice e rapido per eseguire contenitori isolati in Azure, senza dover gestire macchine virtuali né adottare un agente di orchestrazione di livello superiore.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: overview
ms.date: 11/30/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 8362ae5e9647c023ff950a363f9ba7bfde37fdb6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863322"
---
# <a name="what-is-azure-container-instances"></a>Informazioni su Istanze di Azure Container

I contenitori si stanno affermando come soluzione preferita per la creazione di pacchetti, la distribuzione e la gestione di applicazioni cloud. Istanze di Azure Container rappresenta il modo più semplice e rapido per eseguire un contenitore in Azure, senza dover gestire macchine virtuali né dover adottare un servizio di livello superiore.

Istanze di Azure Container è un'ottima soluzione per qualsiasi scenario e funziona anche in contenitori isolati, inclusi i processi di compilazione, l'automazione di attività e le applicazioni semplici. Per gli scenari in cui si rende necessaria l'orchestrazione completa dei contenitori, quali il rilevamento dei servizi tra più contenitori, la scalabilità automatica e gli aggiornamenti coordinati delle applicazioni, è consigliabile usare il [servizio Azure Kubernetes](../aks/index.yml).

## <a name="fast-startup-times"></a>Tempi di avvio rapidi

I contenitori offrono notevoli vantaggi in termini di avvio rispetto alle macchine virtuali. Istanze di Azure Container consente di avviare contenitori in Azure in pochi secondi, senza dover gestire VM né doverne effettuare il provisioning.

## <a name="public-ip-connectivity-and-dns-name"></a>Connettività tramite indirizzo IP pubblico e nome DNS

Istanze di Azure Container consente di esporre i contenitori direttamente in Internet con un indirizzo IP e un nome di dominio completo. Quando si crea un'istanza di contenitore, è possibile specificare un'etichetta personalizzata per il nome DNS, in modo che l'applicazione sia raggiungibile tramite *etichettapersonalizzata*.*areaazure*.contenitoreazure.io.

## <a name="hypervisor-level-security"></a>Sicurezza a livello di hypervisor

In passato i contenitori offrivano l'isolamento di dipendenze delle applicazioni e la governance delle risorse, ma non erano considerati sufficientemente protetti per l'uso di multi-tenant ostili. Istanze di Azure Container garantisce all'applicazione in un contenitore lo stesso livello di isolamento di cui usufruirebbe in una VM.

## <a name="custom-sizes"></a>Dimensioni personalizzate

I contenitori sono in genere ottimizzati per l'esecuzione di un'applicazione singola, ma le esigenze specifiche di tale applicazione possono variare notevolmente. Istanze di Azure Container supporta un utilizzo ottimale consentendo di specificare esattamente memoria e core CPU. Si paga in base alle risorse necessarie con fatturazione al secondo ed è così possibile ottimizzare la spesa in base alle esigenze effettive.

Per i processi a elevato utilizzo di calcolo, ad esempio l'apprendimento automatico, Istanze di Azure Container può pianificare contenitori Linux per l'uso di [risorse GPU](container-instances-gpu.md) NVIDIA Tesla (anteprima).

## <a name="persistent-storage"></a>Archiviazione permanente

Per recuperare e rendere persistente lo stato con Istanze di Azure Container, è disponibile il [montaggio diretto di condivisioni file di Azure](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Contenitori Linux e Windows

Istanze di Azure Container consente di pianificare contenitori sia Windows che Linux con la stessa API. È sufficiente specificare il tipo di sistema operativo quando si creano i [gruppi di contenitori](container-instances-container-groups.md).

Alcune funzionalità attualmente sono limitate ai contenitori Linux:

* Più contenitori per gruppo di contenitori
* Montaggio di volumi ([File di Azure](container-instances-volume-azure-files.md), [emptyDir](container-instances-volume-emptydir.md), [GitRepo](container-instances-volume-gitrepo.md), [segreto](container-instances-volume-secret.md))
* [Distribuzione in una rete virtuale](container-instances-vnet.md) (anteprima)
* [Risorse GPU](container-instances-gpu.md) (anteprima)

Istanze di Azure Container supporta le immagini di Windows Server 2016 che si basano sulle versioni Long-Term Servicing Channel (LTSC). Le versioni Canale semestrale di Windows, come ad esempio 1709 e 1803, non sono supportate.

## <a name="co-scheduled-groups"></a>Gruppi con pianificazione condivisa

Istanze di Azure Container supporta la pianificazione di [gruppi multi-contenitore](container-instances-container-groups.md) che condividono un computer host, una rete locale, un archivio e un ciclo di vita. Questo consente di combinare il contenitore dell'applicazione principale con altri contenitori di ruoli di supporto, come quelli collaterali di registrazione.

## <a name="virtual-network-deployment-preview"></a>Distribuzione in una rete virtuale (anteprima)

Questa funzionalità di Istanze di Azure Container, attualmente in anteprima, consente [la distribuzione di istanze di contenitore in una rete virtuale di Azure](container-instances-vnet.md). Quando sono distribuite in una subnet all'interno della rete virtuale, le istanze di contenitore possono comunicare in modo sicuro con altre risorse nella rete virtuale, incluse le risorse in locale (tramite [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ ExpressRoute](../expressroute/expressroute-introduction.md)).

> [!IMPORTANT]
> Alcune funzionalità di Istanze di Azure Container sono in anteprima e [si applicano alcune limitazioni](container-instances-vnet.md#preview-limitations). Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo][terms-of-use]. Alcuni aspetti di queste funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="next-steps"></a>Passaggi successivi

Provare a distribuire un contenitore in Azure con un comando singolo, seguendo le indicazioni della guida introduttiva:

> [!div class="nextstepaction"]
> [Guida introduttiva di Istanze di Azure Container](container-instances-quickstart.md)

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
