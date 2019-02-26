---
title: Spostare macchine virtuali IaaS di Azure in un'altra area di Azure usando il servizio Azure Site Recovery | Microsoft Docs
description: Usare Site Recovery per lo spostamento di macchine virtuali IaaS di Azure da un'area di Azure a un'altra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 1560dad31e00b00a2d27cf1675e05901e6611825
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309863"
---
# <a name="move-azure-vms-to-another-region"></a>Spostare macchine virtuali di Azure in un'altra area

Azure continua a crescere di pari passo con la base dei clienti e, in risposta a una crescente domanda, è stato aggiunto il supporto per nuove aree geografiche. Ogni mese vengono anche aggiunte nuove funzionalità nei vari servizi. Per questo motivo, in alcuni casi si può scegliere di spostare le macchine virtuali in un'area geografica diversa o in zone di disponibilità per aumentare la disponibilità.

Questo documento illustra i vari scenari in cui è consigliabile spostare le macchine virtuali, oltre a fornire informazioni su come configurare l'architettura nella destinazione per aumentare la disponibilità. 
> [!div class="checklist"]
> * [Perché spostare le macchine virtuali di Azure](#why-would-you-move-azure-vms)
> * [Come spostare le macchine virtuali di Azure](#how-to-move-azure-vms)
> * [Architetture tipiche](#typical-architectures-for-a-multi-tier-deployment)
> * [Spostare le macchine virtuali così come sono in un'area di destinazione](#move-azure-vms-to-another-region)
> * [Spostare le macchine virtuali per aumentare la disponibilità](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>Perché spostare le macchine virtuali di Azure

I clienti spostano le macchine virtuali per i motivi seguenti:

- Se è stata già eseguita la distribuzione in un'area geografica ed è stato aggiunto il supporto per una nuova area più vicina a quella degli utenti finali dell'applicazione o del servizio, è consigliabile **spostare le macchine virtuali così come sono nella nuova area** per ridurre la latenza. Lo stesso approccio è valido se si vogliono consolidare le sottoscrizioni o esistono regole di governance/organizzazione che richiedono lo spostamento. 
- Se la macchina virtuale è stata distribuita come VM a istanza singola o come parte di un set di disponibilità e si vogliono aumentare i contratti di servizio per la disponibilità, è possibile **spostare le macchine virtuali in una zona di disponibilità**. 

## <a name="how-to-move-azure-vms"></a>Come spostare le macchine virtuali di Azure
Per spostare le macchine virtuali, seguire questa procedura:

1. Verificare i prerequisiti 
2. Preparare le VM di origine 
3. Preparare l'area di destinazione 
4. Copiare i dati nell'area di destinazione. Usare la tecnologia di replica di Azure Site Recovery per copiare i dati dalla macchina virtuale di origine all'area di destinazione
5. Testare la configurazione: dopo aver completato la replica, testare la configurazione eseguendo un failover di test in una rete non di produzione.
6. Eseguire lo spostamento 
7. Rimuovere le risorse dall'area di origine 


> [!IMPORTANT]
> Attualmente Azure Site Recovery supporta lo spostamento delle VM da un'area a un'altra, ma non all'interno della stessa area. 

> [!NOTE]
> Le istruzioni dettagliate per questi passaggi sono disponibili nella documentazione relativa a ogni scenario come indicato [qui](#next-steps)

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Architetture tipiche per una distribuzione multilivello
La sezione seguente illustra le architetture di distribuzione più comuni adottate dai clienti per un'applicazione multilivello di Azure. L'esempio mostrato è relativo a un'applicazione a tre livelli con un IP pubblico. Ogni livello, ossia Web, applicazione e database, include 2 VM ciascuno ed è connesso agli altri livelli tramite un servizio di bilanciamento del carico. Per il livello del database è impostata la replica Always On di SQL tra le VM per la disponibilità elevata.

1.  **VM a istanza singola distribuite tra i vari livelli**. Ogni VM di un livello è configurata come VM a istanza singola, connessa tramite servizi di bilanciamento del carico agli altri livelli. Questa è la configurazione più semplice adottata dai clienti.

       ![single-VMs](media/move-vm-overview/regular-deployment.PNG)

2. **VM di ogni livello distribuite in set di disponibilità**. Ogni VM di un livello è configurata in un set di disponibilità. I [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) assicurano che le in Azure le macchine virtuali vengano distribuite tra più nodi hardware isolati in un cluster. Questa operazione assicura che, se si verifica un errore hardware o software all'interno di Azure, solo un subset delle macchine virtuali viene interessato e che nel complesso la soluzione rimane disponibile e operativa. 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **VM di ogni livello distribuite in zone di disponibilità**. Ogni VM di un livello è configurata in [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di errore e un dominio di aggiornamento. Ad esempio, se si creano tre o più macchine virtuali in tre aree in un'area di Azure, le macchine virtuali vengono distribuite in modo efficace in tre domini di errore e tre domini di aggiornamento. La piattaforma di Azure riconosce questa distribuzione in domini di aggiornamento per assicurarsi che le macchine virtuali in diverse aree non vengano aggiornate contemporaneamente.

      ![zone-deploymnt](media/move-vm-overview/zone.PNG)



 ## <a name="move-vms-as-is-to-a-target-region"></a>Spostare le macchine virtuali così come sono in un'area di destinazione

In base alle [architetture](#typical-architectures-for-a-multi-tier-deployment) citate sopra, ecco come saranno le distribuzioni dopo aver eseguito lo spostamento delle VM così come sono nell'area di destinazione.


1. **VM a istanza singola distribuite tra vari livelli** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **VM di ogni livello distribuite in set di disponibilità**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **VM di ogni livello distribuite in zone di disponibilità**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>Spostare le macchine virtuali per aumentare la disponibilità

1. **VM a istanza singola distribuite tra vari livelli** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **VM di ogni livello distribuite in set di disponibilità**. È possibile scegliere di configurare l'inserimento delle VM di un set di disponibilità in zone di disponibilità distinte, se si sceglie di abilitare la replica per le VM con Azure Site Recovery. Il contratto di servizio per la disponibilità sarà del 99,9% dopo aver completato l'operazione di spostamento.

     ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>Passaggi successivi

In questo documento sono riportate le istruzioni generali per lo spostamento delle macchine virtuali. Per informazioni dettagliate sull'esecuzione, vedere:


> [!div class="nextstepaction"]
> * [Spostare macchine virtuali di Azure in un'altra area](azure-to-azure-tutorial-migrate.md)

> * [Spostare macchine virtuali di Azure nelle zone di disponibilità](move-azure-VMs-AVset-Azone.md)

