---
title: Abilitare la replica per le VM di Azure in un'altra area di Azure con Azure Site Recovery | Microsoft Docs
description: Riepiloga i passaggi necessari per abilitare la replica delle macchine virtuali di Azure in un'altra area di Azure con il servizio Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a309644f-d36b-4188-bba7-ad45a2d9bede
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 8/01/2017
ms.author: raynew
ms.openlocfilehash: 8691ff9dbdb79ef445482b355b00859072d539e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-enable-replication-for-azure-vms"></a>Passaggio 5: Abilitare la replica per le macchine virtuali di Azure


Dopo aver configurato un [insieme di credenziali di Servizi di ripristino](azure-to-azure-walkthrough-vault.md), usare questo articolo per abilitare la replica delle macchine virtuali in un'altra area di Azure con [Azure Site Recovery](site-recovery-overview.md). Per abilitare la replica, configurare le impostazioni di origine e destinazione, verificare i criteri di replica e selezionare le macchine virtuali da replicare.

- Al termine dell'articolo, le macchine virtuali di Azure verranno replicate nell'area secondaria di Azure.
- È possibile inserire commenti alla fine di questo articolo oppure porre domande nel [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

>[!NOTE]
>
> La replica di macchine virtuali di Azure è attualmente in anteprima.


## <a name="select-the-source"></a>Selezionare l'origine

1. In Insiemi di credenziali dei servizi di ripristino fare clic sul nome dell'insieme di credenziali e quindi selezionare **+Replica**.
2. In **Origine** selezionare **Azure - ANTEPRIMA**.
2. In **Percorso di origine** selezionare l'area di Azure di origine in cui le VM sono attualmente in esecuzione.
3. Selezionare il **modello di distribuzione delle macchine virtuali di Azure**: **Resource Manager** o **Classica**.
4. Selezionare il **Gruppo di risorse di origine** per le VM di Resource Manager o **Servizio cloud** per le VM classiche.
5. Fare clic su **OK** per salvare le impostazioni.

    ![Configurare l'origine](./media/azure-to-azure-walkthrough-enable-replication/source.png)

## <a name="select-the-vms"></a>Selezionare le macchine virtuali

Site Recovery recupera un elenco delle macchine virtuali associate alla sottoscrizione e al gruppo di risorse/servizio cloud.

1. In **Macchine virtuali** selezionare le macchine virtuali da replicare.
2. Fare clic su **OK**.

    ![Selezionare le VM](./media/azure-to-azure-walkthrough-enable-replication/vms.png)


## <a name="configure-settings"></a>Configurare le impostazioni

Site Recovery esegue il provisioning delle impostazioni predefinite per l'area di destinazione (in base alle impostazioni dell'area di origine) e dei criteri di replica:

   - **Percorso di destinazione**: area di destinazione da usare per il ripristino di emergenza. È consigliabile che il percorso di destinazione corrisponda al percorso dell'insieme di credenziali di Site Recovery.
   - **Gruppo di risorse di destinazione**: gruppo di risorse al quale apparterranno le macchine virtuali di Azure nell'area di destinazione dopo il failover. Per impostazione predefinita, Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione con suffisso "asr".
   - **Rete virtuale di destinazione**: rete in cui verranno collocate le VM di Azure nell'area di destinazione dopo il failover. Per impostazione predefinita, Site Recovery crea una nuova rete virtuale e subnet nell'area di destinazione con suffisso "asr". Questa rete è mappata alla rete di origine. Si noti che è possibile assegnare un indirizzo IP specifico dopo il failover di una macchina virtuale, se è necessario mantenere lo stesso indirizzo IP nei percorsi di origine e destinazione.
   - **Account di archiviazione della cache**: Site Recovery usa un account di archiviazione nell'area di origine. Le modifiche apportate nelle macchine virtuali di origine vengono inviate a questo account prima della replica nel percorso di destinazione.
   - **Account di archiviazione di destinazione**: per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione nell'area di destinazione, per eseguire il mirroring dell'account di archiviazione macchina virtuale di origine.
   -  **Set di disponibilità di destinazione**: per impostazione predefinita, Site Recovery crea un nuovo set di disponibilità nell'area di destinazione con suffisso "asr".
   - **Nome del criterio di replica**: nome del criterio.
   - **Conservazione del punto di ripristino**: per impostazione predefinita, Site Recovery conserva i punti di ripristino per 24 ore. È possibile configurare un valore compreso tra 1 e 72 ore.
   - **Frequenza snapshot coerenti con l'app**: per impostazione predefinita, Site Recovery accetta uno snapshot coerente con l'app ogni 4 ore. È possibile configurare un valore compreso tra 1 e 12 ore. I dati vengono replicati in modo continuo:
    - I punti di ripristino coerenti con l'arresto anomalo conservano un ordine di scrittura dati coerente, se creati. Questo tipo di punto di ripristino è in genere sufficiente se l'app è progettata per il ripristino da un arresto anomalo senza incoerenze dei dati
    - I punti di ripristino coerenti con l'arresto anomalo vengono generati ogni pochi minuti. L'uso di questi punti di ripristino per il failover e il ripristino delle macchine virtuali fornisce un obiettivo del punto di ripristino (RPO) nell'ordine di minuti.
    - I punti di ripristino coerenti con l'app, oltre alla coerenza dell'ordine di scrittura, assicurano che le app in esecuzione completino tutte le operazioni e scarichino i buffer nel disco (disattivazione dell'applicazione). È consigliabile usare questi punti di ripristino per le app di database come Exchange, Oracle e SQL Server.

    ![Configurare le impostazioni](./media/azure-to-azure-walkthrough-enable-replication/settings.png)


### <a name="modify-settings"></a>Modificare le impostazioni

Se si vogliono modificare le impostazioni dei criteri di replica e della destinazione, seguire questa procedura:

1. Per visualizzare o modificare le impostazioni di destinazione, fare clic su **Impostazioni**.
2. Per eseguire l'override delle impostazioni di destinazione predefinite, fare clic su **Personalizza**. È possibile specificare un gruppo di risorse di destinazione, una rete virtuale, un set di disponibilità e account di archiviazione di destinazione. I set di disponibilità possono essere aggiunti solo se le VM fanno parte di un set nell'area di origine.

    ![Configurare le impostazioni](./media/azure-to-azure-walkthrough-enable-replication/customize-target.png)

3. Per eseguire l'override delle impostazioni di replica per i punti di ripristino e gli snapshot coerenti con l'app, fare clic su **Personalizza** accanto a **Criteri di replica**.

    ![Configurare le impostazioni](./media/azure-to-azure-walkthrough-enable-replication/customize-policy.png)

4. Per avviare il provisioning delle risorse di destinazione, fare clic su **Create target resources** (Crea risorse di destinazione). Il provisioning richiede circa un minuto. Non chiudere il pannello durante il provisioning, altrimenti sarà necessario ripetere l'operazione.




## <a name="enable-replication"></a>Abilitare la replica

1. In **Impostazioni** fare clic su **Abilita replica**. Verrà così eseguita la replica iniziale delle VM selezionate. L'aggiornamento dello stato della replica iniziale può richiedere del tempo. Fare clic **Aggiorna** per visualizzare lo stato più recente.

2. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**.

3. In **Impostazioni** > **Elementi replicati** è possibile visualizzare lo stato delle VM e lo stato della replica iniziale. Fare clic sulla VM per visualizzare nel dettaglio le rispettive impostazioni.



## <a name="next-steps"></a>Passaggi successivi

Andare a [Passaggio 6: Eseguire un failover di test](azure-to-azure-walkthrough-test-failover.md)
