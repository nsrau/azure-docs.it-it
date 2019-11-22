---
title: Spostamento delle VM di Azure in un'altra area con Azure Site Recovery
description: Uso di Azure Site Recovery per spostare le VM di Azure da un'area di Azure a un'altra.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 05d0fcb9e689b8954bffaff402475762344c023d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091951"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Spostamento delle VM di Azure in un'altra area di Azure

Questo articolo fornisce una panoramica dei motivi e dei passaggi necessari per lo spostamento di VM di Azure in un'altra area di Azure tramite [Azure Site Recovery](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Motivi per spostare le macchine virtuali di Azure

Si possono spostare le macchine virtuali per i motivi seguenti:

- È stata già eseguita la distribuzione in un'area geografica ed è stato aggiunto il supporto per una nuova area più vicina a quella degli utenti finali dell'applicazione o del servizio. In questo scenario, è consigliabile spostare le macchine virtuali così come sono nella nuova area per ridurre la latenza. Usare lo stesso approccio per consolidare le sottoscrizioni o se esistono regole di governance/organizzazione che richiedono lo spostamento.
- La macchina virtuale è stata distribuita come VM a istanza singola o come parte di un set di disponibilità. Se si vuole aumentare la disponibilità di contratti di servizio, è possibile spostare le macchine virtuali in una zona di disponibilità.

## <a name="steps-to-move-azure-vms"></a>Operazioni per spostare le macchine virtuali di Azure

Per spostare le macchine virtuali, seguire questa procedura:

1. Verificare i prerequisiti.
2. Preparare le VM di origine.
3. Preparare l'area di destinazione.
4. Copiare i dati nell’area di destinazione. Usare la tecnologia di replica di Azure Site Recovery per copiare i dati dalla macchina virtuale di origine all'area di destinazione.
5. Testare la configurazione. Dopo aver completato la replica, testare la configurazione eseguendo un failover di test in una rete non di produzione.
6. Eseguire lo spostamento.
7. Rimuovere le risorse dall'area di origine.

> [!NOTE]
> Nelle sezioni seguenti vengono fornite informazioni dettagliate su queste operazioni.
> [!IMPORTANT]
> Attualmente Azure Site Recovery supporta lo spostamento delle macchine virtuali da un'area a un'altra, ma non all'interno della stessa area.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Architetture tipiche per una distribuzione multilivello

Questa sezione illustra le architetture di distribuzione più comuni per un'applicazione multilivello di Azure. L'esempio mostrato è relativo a un'applicazione a tre livelli con un IP pubblico. Ogni livello, ossia Web, applicazione e database, include due VM ciascuno ed è connesso agli altri livelli tramite un servizio di bilanciamento del carico di Azure. Per il livello del database è impostata la replica Always On di SQL Server tra le VM per la disponibilità elevata.

* **VM a istanza singola distribuite tra vari livelli**: Ogni VM di un livello è configurata come VM a istanza singola, connessa tramite servizi di bilanciamento del carico agli altri livelli. Questa configurazione è la più semplice da adottare.

     ![Distribuzione di macchine virtuali a istanza singola tra i livelli](media/move-vm-overview/regular-deployment.png)

* **VM di ogni livello distribuite in set di disponibilità**: Ogni VM di un livello è configurata in un set di disponibilità. I [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) assicurano che le in Azure le macchine virtuali vengano distribuite tra più nodi hardware isolati in un cluster. Questa operazione assicura che, se si verifica un errore hardware o software all'interno di Azure, solo un subset delle macchine virtuali viene interessato e che nel complesso la soluzione rimane disponibile e operativa.

     ![Distribuzione di macchine virtuali nei set di disponibilità](media/move-vm-overview/avset.png)

* **VM di ogni livello distribuite in zone di disponibilità**: Ogni VM di un livello è configurata in [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di errore e un dominio di aggiornamento. Ad esempio, se si creano tre o più macchine virtuali in tre aree in un'area di Azure, le macchine virtuali vengono distribuite in modo efficace in tre domini di errore e tre domini di aggiornamento. La piattaforma di Azure riconosce questa distribuzione in domini di aggiornamento per assicurarsi che le macchine virtuali in diverse aree non vengano aggiornate contemporaneamente.

     ![Distribuzione zone di disponibilità](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Spostare le macchine virtuali così come sono in un'area di destinazione

In base alle [architetture](#typical-architectures-for-a-multi-tier-deployment) citate sopra, ecco come saranno le distribuzioni dopo aver eseguito lo spostamento delle VM così come sono nell'area di destinazione.

* **VM a istanza singola distribuite tra vari livelli**

     ![Distribuzione di macchine virtuali a istanza singola tra i livelli](media/move-vm-overview/single-zone.png)

* **VM di ogni livello distribuite in set di disponibilità**

     ![Set di disponibilità tra aree](media/move-vm-overview/crossregionaset.png)

* **VM di ogni livello distribuite tra zone di disponibilità**

     ![Distribuzione di macchine virtuali tra zone di disponibilità](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Spostare le macchine virtuali per aumentare la disponibilità

* **VM a istanza singola distribuite tra vari livelli**

     ![Distribuzione di macchine virtuali a istanza singola tra i livelli](media/move-vm-overview/single-zone.png)

* **VM di ogni livello distribuite in set di disponibilità**: È possibile configurare le macchine virtuali di un set di disponibilità in zone di disponibilità separate quando si abilita la replica della macchina virtuale usando Azure Site Recovery. Una volta completato lo spostamento, il contratto di servizio per la disponibilità sarà del 99,9%.

     ![Distribuzione di macchine virtuali tra set di disponibilità e zone di disponibilità](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> 
> * [Spostare macchine virtuali di Azure in un'altra area](azure-to-azure-tutorial-migrate.md)
> 
> * [Spostare macchine virtuali di Azure nelle zone di disponibilità](move-azure-vms-avset-azone.md)

