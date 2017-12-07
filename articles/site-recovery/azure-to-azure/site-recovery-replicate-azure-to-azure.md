---
title: Eseguire la replica di macchine virtuali di Azure in un'area secondaria con Azure Site Recovery | Microsoft Docs
description: Questo articolo illustra come eseguire la replica di macchine virtuali di Azure in esecuzione all'interno di un'area di Azure in un'altra area di Azure tramite il servizio Azure Site Recovery.
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Eseguire la replica di macchine virtuali di Azure in un'altra area di Azure


Questo articolo illustra come eseguire la replica di macchine virtuali (VM, Virtual Machine) di Azure all'interno di un'area di Azure in un'area secondaria di Azure tramite il servizio Azure Site Recovery.

>[!NOTE]
>
> La replica di macchine virtuali di Azure con il servizio Site Recovery è attualmente disponibile in anteprima.

## <a name="prerequisites"></a>Prerequisiti

* È necessario un insieme di credenziali di Servizi di ripristino. È consigliabile creare l'insieme di credenziali nell'area di destinazione in cui si vuole eseguire la replica delle VM.
* Se per controllare l'accesso alla connettività Internet in uscita per le VM di Azure si usano le regole dei gruppi di sicurezza di rete o un proxy firewall, assicurarsi di consentire gli URL o gli IP necessari. [Altre informazioni](./site-recovery-azure-to-azure-networking-guidance.md)
* Se tra il percorso locale e quello di origine in Azure esiste una connessione VPN o ExpressRoute, vedere le [informazioni per la loro configurazione](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration).
* Per consentire la replica di una VM di Azure, l'account utente di Azure deve avere [autorizzazioni specifiche](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).
È necessario che la sottoscrizione di Azure sia abilitata per la creazione di VM nel percorso di destinazione da usare come area per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria.

## <a name="enable-replication"></a>Abilitare la replica

In questa procedura, l'Asia orientale viene usata come percorso di origine e l'Asia sud-orientale come destinazione.

1. Fare clic su **+Replica** nell'insieme di credenziali per abilitare la replica per le macchine virtuali.
2. Verificare che **Origine:** sia impostata su **Azure**.
3. Impostare **Percorso di origine** su Asia orientale.
4. In **Modello di distribuzione** selezionare **classica** o **Resource Manager**.
5. In **Gruppo di risorse** selezionare il gruppo a cui appartengono le VM di origine. Sono elencate tutte le VM del gruppo di risorse selezionato.

    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. In **Macchine virtuali > Seleziona macchine virtuali** fare clic per selezionare tutte le VM da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.

    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. In **Impostazioni** > **Percorso di destinazione** specificare dove eseguire la replica delle VM di origine. Site Recovery presenta un elenco di aree di destinazione appropriate, a seconda dell'area delle VM selezionate.
8. Site Recovery configura le impostazioni predefinite di destinazione, che possono essere modificate in base alle necessità.

    - **Gruppo di risorse di destinazione**. Per impostazione predefinita, Site Recovery crea un nuovo gruppo di risorse con suffisso "asr" nell'area di destinazione. Se il gruppo di risorse che si vuole creare esiste già, viene riusato.
    - **Rete virtuale di destinazione**. Per impostazione predefinita, Site Recovery crea una nuova rete virtuale con suffisso "asr" nell'area di destinazione. Questa rete è mappata alla rete di origine. [Altre informazioni](site-recovery-network-mapping-azure-to-azure.md) sul mapping di rete.
    - **Account di archiviazione di destinazione**. Per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione che corrisponde alla configurazione di archiviazione della VM di origine. Se l'account che si vuole creare esiste già, viene riusato.
    - **Account di archiviazione della cache**. Azure Site Recovery crea un account di archiviazione della cache aggiuntivo, l'area di origine. Tutte le modifiche apportate nelle VM di origine vengono registrate e inviate all'account di archiviazione della cache prima della replica nel percorso di destinazione.
    - **Set di disponibilità**. Per impostazione predefinita, Site Recovery crea un nuovo set di disponibilità con suffisso "asr" nell'area di destinazione. Se il set che si vuole creare esiste già, viene riusato.
    - **Criteri di replica**. Site Recovery definisce le impostazioni per la cronologia della conservazione del punto di recupero e per una frequenza snapshot coerente con l'app. Per impostazione predefinita, Site Recovery crea nuovi criteri di replica con impostazioni predefinite di 24 ore per la conservazione del punto di recupero e di 60 minuti per la frequenza snapshot coerente con l'app.

    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. Fare clic su **Abilitazione della replica** per iniziare a proteggere le VM.

## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

1. Modificare qualsiasi impostazione predefinita di destinazione:

    - **Gruppo di risorse di destinazione**. Selezionare qualsiasi gruppo di risorse dall'elenco di tutti i gruppi di risorse nel percorso di destinazione all'interno della sottoscrizione.
    - **Rete virtuale di destinazione**. Effettuare una selezione dall'elenco di tutte le reti virtuali nel percorso di destinazione.
    - **Set di disponibilità** È possibile aggiungere impostazioni di set di disponibilità a VM situate in un set nell'area di origine.
    - **Account di archiviazione di destinazione**: aggiungere un qualsiasi account disponibile.

        ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Fare clic su **Crea risorsa di destinazione** > **Abilitazione della replica**. Durante la replica iniziale l'aggiornamento dello stato della VM può richiedere tempo. Fare clic **Aggiorna** per visualizzare lo stato più recente.

    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. Dopo che le VM sono state protette, verificarne l'integrità in **Elementi replicati**.



## <a name="next-steps"></a>Passaggi successivi
[Come eseguire](../azure-to-azure-tutorial-dr-drill.md) un failover di test.

