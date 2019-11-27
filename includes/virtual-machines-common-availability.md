---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 06b54c3038e8b4f5879a93b696920534c2199008
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414608"
---
Questo articolo fornisce una panoramica delle funzionalità di disponibilità delle macchine virtuali (VM) di Azure.

## <a name="high-availability"></a>disponibilità elevata

I carichi di lavoro vengono in genere distribuiti tra macchine virtuali diverse per ottenere velocità effettiva elevata, prestazioni e creazione di ridondanza in caso di conseguenze di una macchina virtuale a causa di un aggiornamento o di un altro evento. 

Sono disponibili alcune opzioni offerte da Azure per ottenere la disponibilità elevata. Si esamineranno prima di tutto i costrutti di base. 

### <a name="availability-zones"></a>Zone di disponibilità

Le [zone di disponibilità](../articles/availability-zones/az-overview.md) ampliano il livello di controllo necessario per mantenere la disponibilità delle applicazioni e dei dati nelle macchine virtuali. Una zona di disponibilità è una zona fisicamente separata all'interno di un'area di Azure. Esistono tre zone di disponibilità per ogni area di Azure supportata. 

Ogni zona di disponibilità può contare su risorse di alimentazione, rete e raffreddamento a sé. Progettando le soluzioni in modo che usino VM replicate nelle zone, è possibile proteggere app e dati dalla perdita di un data center. Se una zona è compromessa, le app e i dati replicati diventano immediatamente disponibili in un'altra zona. 

![Zone di disponibilità](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Altre informazioni sulla distribuzione di una macchina virtuale [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) o [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) in una zona di disponibilità.


### <a name="fault-domains"></a>Domini di errore

Un dominio di errore è un raggruppamento logico di tutto l'hardware sottostante che condivide una fonte di alimentazione e uno switch di rete comuni, come a un rack in un datacenter locale. 

### <a name="update-domains"></a>Domini di aggiornamento

Un dominio di aggiornamento è un gruppo logico di hardware sottostante che può essere sottoposto a manutenzione oppure riavviato nello stesso momento. 

Questo approccio garantisce che almeno un'istanza dell'applicazione rimanga in esecuzione durante gli interventi di manutenzione periodica della piattaforma Azure. L'ordine dei domini di aggiornamento da riavviare potrebbe non proseguire in sequenza durante la manutenzione, ma viene riavviato un solo dominio di aggiornamento alla volta.


## <a name="virtual-machines-scale-sets"></a>Set di scalabilità di macchine virtuali 

I set di scalabilità di macchine virtuali di Azure consentono di creare e gestire un gruppo di VM con carico bilanciato. Il numero di istanze di macchine virtuali può aumentare o diminuire automaticamente in risposta alla domanda o a una pianificazione definita. I set di scalabilità garantiscono una disponibilità elevata per le applicazioni e consentono di gestire, configurare e aggiornare in modo centralizzato molte macchine virtuali. È consigliabile creare due o più macchine virtuali all'interno di un set di scalabilità per fornire un'applicazione a disponibilità elevata e per soddisfare il [99,95% del contratto](https://azure.microsoft.com/support/legal/sla/virtual-machines/)di base di Azure. Non sono previsti costi per il set di scalabilità, ma si paga solo per ogni istanza di macchina virtuale creata. Quando una sola macchina virtuale usa le [unità SSD Premium di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd), per gli eventi di manutenzione non pianificati viene applicato il Contratto di servizio di Azure. Le macchine virtuali in un set di scalabilità possono essere distribuite in più domini di aggiornamento e domini di errore per massimizzare la disponibilità e la resilienza a causa di interruzioni del data center e di eventi di manutenzione pianificata o non pianificata. Le macchine virtuali in un set di scalabilità possono anche essere distribuite in una singola zona di disponibilità o in un'area geografica. Le opzioni di distribuzione della zona di disponibilità possono variare in base alla modalità di orchestrazione.

### <a name="preview-orchestration-mode-preview"></a>Anteprima: anteprima modalità di orchestrazione
I set di scalabilità di macchine virtuali consentono di specificare la modalità di orchestrazione.  Con la modalità di orchestrazione del set di scalabilità di macchine virtuali (anteprima) è ora possibile scegliere se il set di scalabilità deve orchestrare le macchine virtuali create in modo esplicito all'esterno di un modello di configurazione del set di scalabilità o istanze di macchine virtuali create in modo implicito in base al modello di configurazione. Scegliere la modalità di orchestrazione che il modello di orchestrazione VM consente di raggruppare in modo esplicito le macchine virtuali in un'area o in una zona di disponibilità. Le macchine virtuali distribuite in una zona di disponibilità forniscono isolamento di zona alle macchine virtuali sono associate al limite della zona di disponibilità e non sono soggette a errori che potrebbero verificarsi in un'altra zona di disponibilità nell'area. 

|   | "orchestrationMode": "VM" (VirtualMachine)| "orchestrationMode": "ScaleSetVM" (VirtualMachineScaleSetVM) |
|----|----|----|
| Modello di configurazione della macchina virtuale| Nessuno. VirtualMachineProfile non è definito nel modello del set di scalabilità. | Obbligatorio. VirtualMachineProfile viene popolato nel modello del set di scalabilità. |
| Aggiunta di una nuova macchina virtuale al set di scalabilità| Al momento della creazione della macchina virtuale, le macchine virtuali vengono aggiunte esplicitamente al set di scalabilità. | Le VM vengono create in modo implicito e aggiunte al set di scalabilità in base al modello di configurazione della macchina virtuale, al numero di istanze e alle regole di scalabilità automatica. |
| Zone di disponibilità| Supporta la distribuzione a livello di area o le macchine virtuali in una zona di disponibilità| Supporta la distribuzione a livello di area o più zone di disponibilità; Consente di definire la strategia di bilanciamento della zona |
| Domini di errore| Consente di definire il numero di domini di errore. 2 o 3 Basato sul supporto regionale e 5 per la zona di disponibilità. Il dominio di errore della VM assegnato viene mantenuto con il ciclo di vita della macchina virtuale, inclusi deallocazione e riavvio. | Consente di definire 1, 2 o 3 domini di errore per le distribuzioni non di zona e 5 per le distribuzioni di zone di disponibilità. Il dominio di errore della VM assegnato non è persistente con il ciclo di vita della macchina virtuale. alle macchine virtuali viene assegnato un dominio di errore al momento dell'allocazione. |
| Domini di aggiornamento| N/D. I domini di aggiornamento vengono mappati automaticamente ai domini di errore| N/D. I domini di aggiornamento vengono mappati automaticamente ai domini di errore |

**Domini di errore e domini di aggiornamento**

I set di scalabilità di macchine virtuali semplificano la progettazione per la disponibilità elevata allineando domini di errore e domini di aggiornamento. È necessario definire solo il numero di domini di errore per il set di scalabilità. Il numero di domini di errore disponibili per i set di scalabilità può variare in base all'area. Vedere [numero di domini di errore per area](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#number-of-fault-domains-per-region).


## <a name="availability-sets"></a>Set di disponibilità
Un set di disponibilità è un raggruppamento logico di macchine virtuali in un data center che consente ad Azure di comprendere come è compilata l'applicazione per garantirne la ridondanza e la disponibilità. È consigliabile creare due o più macchine virtuali in un set di disponibilità, per garantire un'elevata disponibilità dell'applicazione e raggiungere il [99,95% di disponibilità previsto dal contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Non è previsto alcun costo per il set di disponibilità, si paga solo per ogni istanza di macchina virtuale creata. Quando una sola macchina virtuale usa le [unità SSD Premium di Azure](../articles/virtual-machines/windows/disks-types.md#premium-ssd), per gli eventi di manutenzione non pianificati viene applicato il Contratto di servizio di Azure.

In un set di disponibilità, le macchine virtuali vengono distribuite automaticamente in questi domini di errore. Questo approccio consente di limitare l'impatto di eventuali guasti dell'hardware fisico, interruzioni di rete o interruzioni dell'alimentazione.

Le VM che usano [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) sono allineate con i domini di errore dei dischi gestiti quando si usa un set di disponibilità gestito. Questo allineamento garantisce che tutti i dischi gestiti collegati a una VM siano nello stesso dominio di errore dei dischi gestiti. 

In un set di disponibilità gestito possono essere create solo VM con dischi gestiti. Il numero di domini di errore dei dischi gestiti varia in base all'area: due o tre domini di errore di dischi gestiti per area. Sono disponibili altre informazioni su questi domini di errore dei dischi gestiti per le [VM Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) o le [VM Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Set di disponibilità gestito](./media/virtual-machines-common-manage-availability/md-fd-updated.png)


Anche le VM all'interno di un set di disponibilità vengono distribuite automaticamente tra i domini di aggiornamento. 

![Set di disponibilità](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="next-steps"></a>Passaggi successivi
Ora è possibile iniziare a usare le funzionalità di ridondanza e disponibilità per compilare l'ambiente Azure. Per altre informazioni, vedere le [procedure consigliate per la disponibilità di Azure](/azure/architecture/checklist/resiliency-per-service).

