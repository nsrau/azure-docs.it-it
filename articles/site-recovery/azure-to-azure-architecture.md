---
title: Architettura della replica da Azure ad Azure in Azure Site Recovery | Microsoft Docs
description: Questo articolo offre una panoramica dei componenti e dell'architettura usati per la replica di VM di Azure tra aree di Azure con il servizio Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/07/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 409dd26cc1dfcb1c562d175a43e842b213501d03
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="azure-to-azure-replication-architecture"></a>Architettura della replica da Azure ad Azure


Questo articolo descrive l'architettura usata per la replica, il failover e il ripristino di macchine virtuali (VM) di Azure tra aree di Azure tramite il servizio [Azure Site Recovery](site-recovery-overview.md).

>[!NOTE]
>La replica di VM di Azure con il servizio Site Recovery è attualmente disponibile in anteprima.



## <a name="architectural-components"></a>Componenti dell'architettura

Il grafico seguente offre una visualizzazione generale di un ambiente di macchine virtuali di Azure in un'area specifica, in questo esempio l'area Stati Uniti orientali. In un ambiente di VM di Azure:
- Le app possono essere in esecuzione in macchine virtuali con dischi distribuiti tra gli account di archiviazione.
- Le VM possono essere incluse in una o più subnet in una rete virtuale.


**Replica da Azure ad Azure**

![customer-environment](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Processo di replica

### <a name="step-1"></a>Passaggio 1

Quando si abilita la replica delle macchine virtuali di Azure, nell'area di destinazione vengono automaticamente create le risorse seguenti, in base alle impostazioni dell'area di origine. È possibile personalizzare le impostazioni delle risorse di destinazione nel modo necessario.

![Abilitare il processo di replica - Passaggio 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Risorsa** | **Dettagli**
--- | ---
**Gruppo di risorse di destinazione** | Gruppo di risorse a cui appartengono le VM replicate dopo il failover.
**Rete virtuale di destinazione** | Rete virtuale in cui si trovano le VM replicate dopo il failover. Un mapping di rete viene creato tra le reti virtuali di origine e di destinazione e viceversa.
**Account di archiviazione della cache** | Prima che le modifiche apportate alle macchine virtuali di origine vengano replicate in un account di archiviazione di destinazione, vengono registrate e inviate all'account di archiviazione della cache nella posizione di origine. Questo passaggio assicura un impatto minimo sulle applicazioni di produzione in esecuzione nella macchina virtuale.
**Account di archiviazione di destinazione**  | Account di archiviazione nel percorso di destinazione in cui vengono replicati i dati.
**Set di disponibilità di destinazione**  | Set di disponibilità in cui si trovano le VM replicate dopo il failover.

### <a name="step-2"></a>Passaggio 2

Poiché la replica è abilitata, il servizio Mobility dell'estensione di Site Recovery viene installato automaticamente nella macchina virtuale:

1. La VM viene registrata in Site Recovery.

2. La replica continua viene configurata per la VM. Le operazioni di scrittura dei dati nei dischi delle macchine virtuali vengono trasferite continuamente nell'account di archiviazione della cache nella posizione di origine.

   ![Abilitare il processo di replica - Passaggio 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Site Recovery non necessita mai della connettività in ingresso per la VM. Per le operazioni seguenti è necessaria soltanto la connettività in uscita.

 - Indirizzi IP/URL del servizio di Site Recovery
 - Indirizzi IP/URL di autenticazione di Office 365
 - Indirizzi IP dell'account di archiviazione della cache

Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004. Verificare che nessuna appliance firewall blocchi la comunicazione interna tra VM sulla porta 20004.

> [!IMPORTANT]
Se le VM Linux devono far parte di un gruppo di replica, verificare che il traffico in uscita sulla porta 20004 venga aperto manualmente in base alle indicazioni della versione Linux specifica.

### <a name="step-3"></a>Passaggio 3

Una volta attivata la replica continua, le operazioni di scrittura nei dischi vengono immediatamente trasferite nell'account di archiviazione della cache. Site Recovery elabora i dati e li invia all'account di archiviazione di destinazione. Dopo l'elaborazione dei dati, vengono generati punti di ripristino nell'account di archiviazione di destinazione a intervalli di pochi minuti.

## <a name="failover-process"></a>Processo di failover

Quando si avvia un failover, le macchine virtuali vengono create nel gruppo di risorse di destinazione, nella rete virtuale di destinazione, nella subnet di destinazione e nel set di disponibilità di destinazione. Durante un failover è possibile usare qualsiasi punto di ripristino.

![Processo di failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Passaggi successivi

[Replicare rapidamente](azure-to-azure-quickstart.md) una macchina virtuale di Azure in un'area secondaria.
