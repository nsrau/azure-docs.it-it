---
title: Architettura della replica di Azure in Azure in Azure Site Recovery | Documenti Microsoft
description: Questo articolo offre una panoramica dei componenti e dell'architettura usati per la replica di VM di Azure tra aree di Azure con il servizio Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/19/2017
ms.author: raynew
ms.openlocfilehash: b37af3462a58f4418653d0e1b2300b5805e0a864
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="azure-to-azure-replication-architecture"></a>Architettura della replica da Azure ad Azure


Questo articolo vengono descritti l'architettura utilizzata quando la replica, il failover e ripristinare macchine virtuali di Azure (VM) tra le aree di Azure, utilizzando il [Azure Site Recovery](site-recovery-overview.md) servizio.

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

Quando si abilita la replica di macchina virtuale di Azure, le risorse seguenti vengono create automaticamente nell'area di destinazione, in base alle impostazioni dell'area di origine. È possibile personalizzare le impostazioni delle risorse di destinazione nel modo necessario.

![Abilitare il processo di replica - Passaggio 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Risorsa** | **Dettagli**
--- | ---
**Gruppo di risorse di destinazione** | Gruppo di risorse a cui appartengono le VM replicate dopo il failover.
**Rete virtuale di destinazione** | Rete virtuale in cui si trovano le VM replicate dopo il failover. Un mapping di rete viene creato tra le reti virtuali di origine e di destinazione e viceversa.
**Account di archiviazione della cache** | Prima di modifiche alla macchina virtuale di origine vengono replicate a un account di archiviazione di destinazione, vengono rilevati e inviati all'account di archiviazione della cache nel percorso di origine. In questo modo un impatto minimo sulle applicazioni di produzione in esecuzione nella macchina virtuale.
**Account di archiviazione di destinazione**  | Account di archiviazione nel percorso di destinazione in cui vengono replicati i dati.
**Set di disponibilità di destinazione**  | Set di disponibilità in cui si trovano le VM replicate dopo il failover.

### <a name="step-2"></a>Passaggio 2

Poiché la replica è abilitata, il servizio Mobility dell'estensione di Site Recovery viene installato automaticamente nella macchina virtuale:

1. La VM viene registrata in Site Recovery.

2. La replica continua viene configurata per la VM. Le operazioni di scrittura dei dati nei dischi delle macchine virtuali vengono trasferite continuamente nell'account di archiviazione della cache nella posizione di origine.

   ![Abilitare il processo di replica - Passaggio 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Site Recovery non necessita mai della connettività in ingresso per la VM. Solo la connettività in uscita è necessaria per le operazioni seguenti.

 - Indirizzi URL/IP del servizio di ripristino sito
 - Risolve gli URL/IP di autenticazione di Office 365
 - Indirizzi IP account di archiviazione della cache

Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004. Verificare che non sia presente alcun dispositivo firewall blocca la comunicazione tra le macchine virtuali tramite la porta 20004 interna.

> [!IMPORTANT]
Se si desidera far parte di un gruppo di replica delle macchine virtuali Linux, verificare che il traffico in uscita sulla porta 20004 viene aperta manualmente in base alle indicazioni della versione di Linux specifica.

### <a name="step-3"></a>Passaggio 3

Una volta attivata la replica continua, le operazioni di scrittura nei dischi vengono immediatamente trasferite nell'account di archiviazione della cache. Site Recovery elabora i dati e li invia all'account di archiviazione di destinazione. Dopo l'elaborazione dei dati, vengono generati punti di ripristino nell'account di archiviazione di destinazione a intervalli di pochi minuti.

## <a name="failover-process"></a>Processo di failover

Quando si avvia un failover, le macchine virtuali vengono create nel gruppo di risorse di destinazione, nella rete virtuale di destinazione, nella subnet di destinazione e nel set di disponibilità di destinazione. Durante un failover è possibile usare qualsiasi punto di ripristino.

![Processo di failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Passaggi successivi

[Replicare rapidamente](azure-to-azure-quickstart.md) una macchina virtuale di Azure in un'area secondaria.
