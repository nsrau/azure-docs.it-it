---
title: Calcolo pianificazione delle capacità per Azure Stack | Microsoft Docs
description: Informazioni sulle capacità di calcolo pianificazione delle distribuzioni di Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeffgilb
ms.reviewer: prchint
ms.custom: mvc
ms.openlocfilehash: b98879483d35a91810c9e9ab5e0ac81151bde52f
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2018
ms.locfileid: "46369071"
---
# <a name="azure-stack-compute-capacity-planning"></a>Pianificazione della capacità di calcolo di Azure Stack
Il [le dimensioni di macchina virtuale supportate in Azure Stack](.\user\azure-stack-vm-sizes.md) sono un subset di quelli supportati in Azure. Azure impone limiti delle risorse lungo molti vettori per evitare l'uso eccessivo delle risorse (server locali e a livello di servizio). Senza imporre alcune limitazioni all'uso di tenant, l'esperienza utente tenant ne risentiranno quando altri tenant overconsume le risorse. Rete in uscita dalla macchina virtuale, esistono limiti di larghezza di banda posto in Azure Stack che corrispondono a limitazioni di Azure. Per le risorse di archiviazione, i limiti di IOPs di archiviazione sono stati implementati in Azure Stack per evitare di base uso eccessivo delle risorse dai tenant per l'accesso di archiviazione.  

## <a name="vm-placement-and-virtual-to-physical-core-overprovisioning"></a>Selezione host VM e ricorrere all'overprovisioning core virtuale a fisico
In Azure Stack, non è possibile per un tenant specificare un server specifico da utilizzare per la selezione host di macchina virtuale. La considerazione sola quando si creano le macchine virtuali è se è disponibile memoria sufficiente nell'host per il tipo di macchina virtuale. Azure Stack non overcommit la memoria. Tuttavia, è consentito un overcommit del numero di core. Poiché non è Esaminiamo esistente al rapporto tra core fisici ricorrere all'overprovisioning come fattore virtuale gli algoritmi di selezione host, ogni host può avere un rapporto diverso. 

In Azure, per ottenere disponibilità elevata di un sistema di produzione di più macchine Virtuali, macchine virtuali vengono inserite in un set di disponibilità per essere distribuito tra più domini di errore. Ciò significa che le macchine virtuali inserite in un set di disponibilità sono fisicamente isolate tra loro in un rack per consentire la resilienza di errore come illustrato nel diagramma seguente:

![Domini di errore e aggiornamento](media\azure-stack-capacity-planning\domains.png)


Mentre l'infrastruttura di Azure Stack è resiliente agli errori, la tecnologia sottostante (clustering di failover) ancora comporta tempi di inattività per le macchine virtuali in un server fisico interessati in caso di errore hardware. Attualmente, Azure Stack supporta un set di disponibilità con un massimo di tre domini di errore siano coerenti con Azure. Le macchine virtuali inserite in un set di disponibilità è fisicamente isolate una da altra per la distribuzione di in modo più uniforme possibile in più domini di errore (nodi di Azure Stack). Se si verifica un errore hardware, le macchine virtuali dal dominio di errore vengono riavviate in altri nodi, ma, se possibile, mantenute in domini di errore da altre macchine virtuali nello stesso set di disponibilità. Quando l'hardware ritorna in linea, le macchine virtuali saranno ribilanciate per mantenere la disponibilità elevata.

Un altro concetto che viene usato da Azure per garantire un'elevata disponibilità è sotto forma di domini di aggiornamento nei set di disponibilità. Un dominio di aggiornamento è un gruppo logico di hardware sottostante che può essere sottoposto a manutenzione oppure riavviato nello stesso momento. In Azure Stack le macchine virtuali sono in tempo reale la migrazione tra gli altri host nel cluster online prima dell'aggiornamento dell'host sottostante. Perché durante un aggiornamento host è presente alcun tempo di inattività di tenant, la funzionalità del dominio di aggiornamento in Azure Stack è presente solo per la compatibilità dei modelli di Azure.

## <a name="azure-stack-resiliency-resources"></a>Risorse di resilienza di Azure Stack
Per consentire le patch e aggiornamento di Azure Stack integrato system e per essere resilienti agli errori hardware fisico, una parte della memoria totale del server non è riservato e non è disponibile per selezione host di macchina virtuale (VM) di tenant.

Se un server non riesce, le macchine virtuali ospitate nel server non riuscito verranno riavviate sui server rimanenti, disponibile a garantiscono la disponibilità della macchina virtuale. Analogamente, durante il processo di patch e aggiornamenti, tutte le macchine virtuali in esecuzione in un server di essere risiederanno migrati per gli altri server disponibili. La gestione delle macchine Virtuali o dallo spostamento può essere ottenuto solo se è presente la capacità riservata per consentire il riavvio o la migrazione si verifichi.

Il calcolo seguente comporta la memoria totale e disponibile che può essere utilizzata per la selezione host VM tenant. Questa capacità di memoria è per l'intero di unità di scala di Azure Stack.

  Memoria disponibile per selezione host VM = sovraccarico di memoria del Server totale – riserva resilienza: Azure Stack dell'infrastruttura <sup>1</sup>

  Riserva di resilienza = H + R * (n-1) + V * (n-2)

> Dove:
> - H = dimensioni della memoria del server singolo
> - N = dimensione dell'unità di scala (numero di server)
> - R = riserva di sistema operativo per l'overhead del sistema operativo<sup>2</sup>
> - V = più grande della macchina virtuale nell'unità di scala

  <sup>1</sup> dell'infrastruttura di azure Stack Overhead = 208 GB

  <sup>2</sup> riserva di sistema operativo per l'overhead = 15% di memoria del nodo. Il valore della riserva del sistema operativo è una stima e possono variare in base a capacità di memoria fisica del server e l'overhead del sistema operativo generale.

Il valore V, le VM di dimensioni maggiori in unità di scala, in modo dinamico è basato sul tenant più grande delle dimensioni di memoria della macchina virtuale. Ad esempio, il valore più grande della macchina virtuale potrebbe essere 7 GB o 112 GB o qualsiasi altra memoria dimensione di VM supportata nella soluzione di Azure Stack.

Il calcolo precedente è una stima e soggetto a modifiche in base alla versione corrente di Azure Stack. Possibilità di distribuire le macchine virtuali tenant e i servizi si basa sulle specifiche della soluzione distribuita. Questo esempio di calcolo è semplicemente una Guida che non la risposta assoluta derivante dalla possibilità di distribuire le macchine virtuali.



## <a name="next-steps"></a>Passaggi successivi
[Pianificazione della capacità di archiviazione](capacity-planning-storage.md)
