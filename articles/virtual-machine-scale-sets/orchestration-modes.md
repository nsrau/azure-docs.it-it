---
title: Altre informazioni sulle modalità di orchestrazione per i set di scalabilità di macchine virtuali in AzureLearn more about orchestration modes for virtual machine scale sets in Azure
description: Altre informazioni sulle modalità di orchestrazione per i set di scalabilità di macchine virtuali in Azure.Learn more about orchestration modes for virtual machine scale sets in Azure.
author: shandilvarun
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: vashan
ms.openlocfilehash: 4a0be30f181921461ad0bacea6f18ce439d22353
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279054"
---
# <a name="orchestration-mode-preview"></a>Modalità orchestrazione (anteprima)Orchestration mode (preview)

I set di scalabilità delle macchine virtuali forniscono un raggruppamento logico di macchine virtuali gestite dalla piattaforma. Con i set di scalabilità, si crea un modello di configurazione della macchina virtuale, si aggiungono o rimuovono automaticamente istanze aggiuntive in base al carico della CPU o della memoria e si esegue automaticamente l'aggiornamento alla versione più recente del sistema operativo. Tradizionalmente, i set di scalabilità consentono di creare macchine virtuali usando un modello di configurazione della macchina virtuale fornito al momento della creazione del set di scalabilità e il set di scalabilità può gestire solo le macchine virtuali create in modo implicito in base al modello di configurazione.

Con la modalità di orchestrazione del set di scalabilità (anteprima), è ora possibile scegliere se il set di scalabilità deve orchestrare le macchine virtuali create in modo esplicito all'esterno di un modello di configurazione del set di scalabilità o le istanze di macchine virtuali create in modo implicito modello di configurazione. La modalità di orchestrazione dei set di scalabilità consente inoltre di progettare l'infrastruttura della macchina virtuale per la disponibilità elevata distribuendo queste macchine virtuali nei domini di errore e nelle zone di disponibilità.


I set di scalabilità delle macchine virtuali supporteranno due modalità di orchestrazione distinte:Virtual machine scale sets will support 2 distinct orchestration modes:

- ScaleSetVM: le istanze di macchine virtuali aggiunte al set di scalabilità si basano sul modello di configurazione del set di scalabilità. Il ciclo di vita dell'istanza della macchina virtuale, ovvero creazione, aggiornamento, eliminazione, è gestito dal set di scalabilità.
- VM (macchine virtuali): le macchine virtuali create all'esterno del set di scalabilità possono essere aggiunte in modo esplicito al set di scalabilità. 
 

> [!IMPORTANT]
> La modalità di orchestrazione viene definita quando si crea il set di scalabilità e non può essere modificata o aggiornata in un secondo momento. 
> 
> Questa funzionalità dei set di scalabilità delle macchine virtuali è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. 
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="orchestration-modes"></a>Modalità di orchestrazione

|                             | "orchestrationMode": "VM" (VirtualMachine) | "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|-----------------------------|--------------------------------------------|--------------------------------------------------------------|
| Modello di configurazione della macchina virtualeVM configuration model      | nessuno                                       | Obbligatoria |
| Aggiunta di nuova macchina virtuale al set di scalabilitàAdding new VM to Scale Set  | Le macchine virtuali vengono aggiunte in modo esplicito al set di scalabilità quando viene creata la macchina virtuale. | Le macchine virtuali vengono create in modo implicito e aggiunte al set di scalabilità in base al modello di configurazione della macchina virtuale, al numero di istanze e alle regole di scalabilità automatica | |
| Eliminazione di una macchina virtuale                   | Le macchine virtuali devono essere eliminate singolarmente, il set di scalabilità non verrà eliminato se contiene macchine virtuali. | Le macchine virtuali possono essere eliminate singolarmente, eliminando il set di scalabilità verranno eliminate tutte le istanze della macchina virtuale.  |
| Collegare/scollegare macchine virtuali           | Non supportate                              | Non supportate |
| Ciclo di vita dell'istanza (creazione tramite eliminazione)Instance Lifecycle (Creation through Deletion) | Le macchine virtuali e i relativi elementi (ad esempio dischi e schede di interfaccia di rete) possono essere gestiti in modo indipendente. | Le istanze e i relativi elementi (ad esempio dischi e schede di interfaccia di rete) sono impliciti nelle istanze del set di scalabilità che le creano. Non possono essere scollegati o gestiti separatamente al di fuori del set di scalabilità |
| Domini di errore               | Può definire domini di errore. 2 o 3 in base al supporto regionale e 5 per la zona di disponibilità. | Può definire i domini di errore che vanno da 1 a 5 |
| Domini di aggiornamento              | I domini di aggiornamento vengono mappati automaticamente ai domini di errore | I domini di aggiornamento vengono mappati automaticamente ai domini di errore |
| Zone di disponibilità          | Supporta la distribuzione regionale o le macchine virtuali in un'unica zona di disponibilitàSupports regional deployment or VMs in one Availability zone | Supporta la distribuzione regionale o più zone di disponibilità; Può definire la strategia di bilanciamento delle zone |
| Scalabilità automatica                   | Non supportate                              | Supportato |
| Aggiornamento del sistema operativo                  | Non supportate                              | Supportato |
| Aggiornamenti del modello               | Non supportate                              | Supportato |
| Controllo dell'istanza            | Controllo completo della macchina virtuale. Le macchine virtuali hanno URI completo che supportano l'intera gamma di funzionalità di gestione delle macchine virtuali di Azure (ad esempio Criteri di Azure, Backup di Azure e Azure Site Recovery) | Le macchine virtuali sono risorse dipendenti del set di scalabilità. È possibile accedere alle istanze per la gestione solo tramite il set di scalabilità. |
| Modello di istanzaInstance Model              | Definizione del modello Microsoft.Compute/VirtualMachines.Microsoft.Compute/VirtualMachines model definition. | Definizione del modello Microsoft.Compute/VirtualMachineScaleSets/VirtualMachines. |
| Capacity                    | È possibile creare un set di scalabilità vuoto; fino a 200 macchine virtuali possono essere aggiunte al set di scalabilità | I set di scalabilità possono essere definiti con un numero di istanze 0 - 1000Scale sets can be defined with an instance count 0 - 1000 |
| Spostamento                        | Supportato                                  | Supportato |
| Gruppo di posizionamento singolo: false | Non supportate                          | Supportato |


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [Panoramica delle opzioni di disponibilità](availability.md).
