---
title: Altre informazioni sulle modalità di orchestrazione per i set di scalabilità di macchine virtuali in Azure
description: Altre informazioni sulle modalità di orchestrazione per i set di scalabilità di macchine virtuali in Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 10/23/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: eb7d4d8a6f1c1ee55601cdd839e330147e60bcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011074"
---
# <a name="orchestration-modes-preview"></a>Modalità di orchestrazione (anteprima)

> [!CAUTION]
> Grazie a tutti gli utenti che hanno partecipato a questa versione di anteprima pubblica. Siamo riusciti a raccogliere i preziosi commenti della nostra community. Questa versione di anteprima è stata **chiusa** per tutti i nuovi partecipanti, al fine di integrare il feedback. Questo spazio verrà aggiornato con nuove informazioni.

I set di scalabilità di macchine virtuali offrono un raggruppamento logico di macchine virtuali gestite dalla piattaforma. Con i set di scalabilità è possibile creare un modello di configurazione della macchina virtuale, aggiungere o rimuovere automaticamente istanze aggiuntive in base al carico della CPU o della memoria e aggiornarsi automaticamente alla versione più recente del sistema operativo. Tradizionalmente, i set di scalabilità consentono di creare macchine virtuali usando un modello di configurazione della macchina virtuale disponibile al momento della creazione del set di scalabilità e il set di scalabilità può gestire solo le macchine virtuali create in modo implicito in base al modello di configurazione.

Con la modalità di orchestrazione del set di scalabilità (anteprima), è ora possibile scegliere se il set di scalabilità deve orchestrare macchine virtuali create in modo esplicito all'esterno di un modello di configurazione del set di scalabilità o istanze di macchine virtuali create in modo implicito in base al modello di configurazione. La modalità di orchestrazione del set di scalabilità consente inoltre di progettare l'infrastruttura della macchina virtuale per la disponibilità elevata distribuendo queste macchine virtuali in domini di errore e zone di disponibilità.


I set di scalabilità di macchine virtuali supporteranno due modalità di orchestrazione distinte:

- ScaleSetVM: le istanze di macchine virtuali aggiunte al set di scalabilità sono basate sul modello di configurazione del set di scalabilità. Il ciclo di vita dell'istanza di macchina virtuale, ovvero la creazione, l'aggiornamento e l'eliminazione, viene gestito dal set di scalabilità.
- VM (macchine virtuali): le macchine virtuali create all'esterno del set di scalabilità possono essere aggiunte in modo esplicito al set di scalabilità. 
 

> [!IMPORTANT]
> La modalità di orchestrazione viene definita quando si crea il set di scalabilità e non può essere modificata o aggiornata in un secondo momento. 
> 
> Questa funzionalità dei set di scalabilità di macchine virtuali è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Modalità di orchestrazione

| Funzionalità                     | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Modello di configurazione della macchina virtuale      | Nessuno                                       | Necessario |
| Aggiunta di una nuova macchina virtuale al set di scalabilità  | Al momento della creazione della macchina virtuale, le macchine virtuali vengono aggiunte esplicitamente al set di scalabilità. | Le VM vengono create in modo implicito e aggiunte al set di scalabilità in base al modello di configurazione della macchina virtuale, al numero di istanze e alle regole di scalabilità automatica | |
| Eliminazione di una macchina virtuale                   | Le macchine virtuali devono essere eliminate singolarmente, il set di scalabilità non verrà eliminato se contiene macchine virtuali. | Le macchine virtuali possono essere eliminate singolarmente, eliminando il set di scalabilità verranno eliminate tutte le istanze di VM.  |
| Connetti/scollega macchine virtuali           | Non supportato                              | Non supportato |
| Ciclo di vita dell'istanza (creazione tramite eliminazione) | Le macchine virtuali e i relativi elementi, come dischi e schede di rete, possono essere gestiti in modo indipendente. | Le istanze e i relativi elementi, come dischi e schede di rete, sono impliciti nelle istanze del set di scalabilità che li creano. Non possono essere scollegati o gestiti separatamente al di fuori del set di scalabilità |
| Domini di errore               | Consente di definire domini di errore. 2 o 3 Basato sul supporto regionale e 5 per la zona di disponibilità. | Consente di definire domini di errore da 1 a 5 |
| Domini di aggiornamento              | I domini di aggiornamento vengono mappati automaticamente ai domini di errore | I domini di aggiornamento vengono mappati automaticamente ai domini di errore |
| Zone di disponibilità          | Supporta la distribuzione a livello di area o le macchine virtuali in una zona di disponibilità | Supporta la distribuzione a livello di area o più zone di disponibilità; Consente di definire la strategia di bilanciamento della zona |
| Scalabilità automatica                   | Non supportato                              | Supportato |
| Aggiornamento del sistema operativo                  | Non supportato                              | Supportato |
| Aggiornamenti del modello               | Non supportato                              | Supportato |
| Controllo istanza            | Controllo completo della macchina virtuale. Le macchine virtuali hanno un URI completo che supporta la gamma completa di funzionalità di gestione delle macchine virtuali di Azure, ad esempio criteri di Azure, backup di Azure e Azure Site Recovery | Le macchine virtuali sono risorse dipendenti del set di scalabilità. È possibile accedere alle istanze per la gestione solo tramite il set di scalabilità. |
| Modello di istanza              | Definizione del modello Microsoft. Compute/VirtualMachines. | Definizione del modello Microsoft. Compute/VirtualMachineScaleSets/VirtualMachines. |
| Capacità                    | È possibile creare un set di scalabilità vuoto; è possibile aggiungere fino a 200 VM al set di scalabilità | I set di scalabilità possono essere definiti con un numero di istanze 0-1000 |
| Spostamento                        | Supportato                                  | Supportato |
| Gruppo di posizionamento singolo = = false | Non supportato                          | Supportato |


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere la [Panoramica delle opzioni di disponibilità](../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machine-scale-sets%2ftoc.json).
