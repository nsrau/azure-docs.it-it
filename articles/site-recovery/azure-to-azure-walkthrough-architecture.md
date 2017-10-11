---
title: Esaminare l'architettura per la replica di VM di Azure tra aree di Azure | Microsoft Docs
description: Questo articolo offre una panoramica dei componenti e dell'architettura usati per la replica di VM di Azure tra aree di Azure con il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/12/2017
ms.author: raynew
ms.openlocfilehash: f471add4f4dee26482e2820fb06d010d6c0c0e88
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="step-1-review-the-architecture-for-azure-vm-replication-between-azure-regions"></a>Passaggio 1: Esaminare l'architettura per la replica di VM di Azure tra aree di Azure


Dopo aver esaminato i [passaggi elencati nella panoramica](azure-to-azure-walkthrough-overview.md) per questa distribuzione, questo articolo consente di conoscere i componenti e i processi usati per la replica e il ripristino di macchine virtuali (VM) di Azure da un'area di Azure a un'altra con [Azure Site Recovery](site-recovery-overview.md).

- Al termine dell'articolo, si avrà una conoscenza precisa del funzionamento della replica di VM in un'altra area.
- È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
>La replica di VM di Azure con il servizio Site Recovery è attualmente disponibile in anteprima.



## <a name="architectural-components"></a>Componenti dell'architettura

Il diagramma seguente fornisce una visualizzazione generale di un ambiente di VM di Azure in un'area specifica. In questo esempio si tratta dell'area Stati Uniti orientali. In un ambiente di VM di Azure:
- Le app possono essere in esecuzione in macchine virtuali con dischi distribuiti tra gli account di archiviazione.
- Le VM possono essere incluse in una o più subnet in una rete virtuale.

![customer-environment](./media/azure-to-azure-walkthrough-architecture/source-environment.png)

## <a name="replication-process"></a>Processo di replica

### <a name="step-1"></a>Passaggio 1

Quando si abilita la replica di VM di Azure nel portale di Azure, le risorse visualizzate nel diagramma e nella tabella seguenti vengono create automaticamente nell'area di destinazione. Per impostazione predefinita, le risorse vengono create in base alle impostazioni dell'area di origine. È possibile personalizzare le impostazioni di destinazione in base alle esigenze specifiche. [Altre informazioni](site-recovery-replicate-azure-to-azure.md).

![Abilitare il processo di replica - Passaggio 1](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-1.png)

**Risorsa** | **Dettagli**
--- | ---
**Gruppo di risorse di destinazione** | Gruppo di risorse a cui appartengono le VM replicate dopo il failover.
**Rete virtuale di destinazione** | Rete virtuale in cui si trovano le VM replicate dopo il failover. Un mapping di rete viene creato tra le reti virtuali di origine e di destinazione e viceversa.
**Account di archiviazione della cache** | Prima che le modifiche apportate nelle VM di origine vengano replicate nell'account di archiviazione di destinazione, vengono registrate e inviate all'account di archiviazione della cache nel percorso di destinazione. In questo modo si assicura un impatto minimo sulle app di produzione in esecuzione nella VM.
**Account di archiviazione di destinazione**  | Account di archiviazione nel percorso di destinazione in cui vengono replicati i dati.
**Set di disponibilità di destinazione**  | Set di disponibilità in cui si trovano le VM replicate dopo il failover.

### <a name="step-2"></a>Passaggio 2

Poiché la replica è abilitata, il servizio Mobility dell'estensione di Site Recovery viene installato automaticamente nella VM. Vengono eseguite le operazioni seguenti:

1. La VM viene registrata in Site Recovery.

2. La replica continua viene configurata per la VM. Le operazioni di scrittura di dati nei dischi delle VM vengono trasferite continuamente nell'account di archiviazione della cache nel percorso di origine.

   ![Abilitare il processo di replica - Passaggio 2](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-2.png)

  
  Si noti che Site Recovery non necessita mai della connettività in ingresso per la VM. È necessaria solo la connettività in uscita agli URL/indirizzi IP del servizio Site Recovery, agli URL/indirizzi IP di autenticazione di Office 365 e agli indirizzi IP dell'account di archiviazione della cache. 

## <a name="continuous-replication-process"></a>Processo di replica continua

Quando la replica continua è attivata correttamente, le operazioni di scrittura nei dischi vengono trasferite immediatamente nell'account di archiviazione della cache. Site Recovery elabora i dati e li invia all'account di archiviazione di destinazione. Dopo l'elaborazione dei dati, vengono generati punti di ripristino nell'account di archiviazione di destinazione a intervalli di pochi minuti.

## <a name="failover-process"></a>Processo di failover

Quando si avvia un failover, le VM vengono create nel gruppo di risorse di destinazione, nella rete virtuale di destinazione, nella subnet di destinazione e nel set di disponibilità di destinazione. Durante un failover è possibile usare qualsiasi punto di ripristino.

![Processo di failover](./media/azure-to-azure-walkthrough-architecture/failover.png)

## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 2: Esaminare i prerequisiti e le limitazioni](azure-to-azure-walkthrough-prerequisites.md)
