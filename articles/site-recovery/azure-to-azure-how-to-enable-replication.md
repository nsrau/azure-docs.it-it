---
title: Configurare la replica per le macchine virtuali di Azure in Azure Site RecoveryConfigure replication for Azure VMs in Azure Site Recovery
description: Informazioni su come configurare la replica in un'altra area per le macchine virtuali di Azure usando Site Recovery.Learn how to configure replication to another region for Azure VMs, using Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 1c6b7cfbf193f02598052b6922efec17fb16ec83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973699"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replicare le macchine virtuali di Azure in un'altra area di AzureReplicate Azure VMs to another Azure region


In questo articolo viene descritto come abilitare la replica di macchine virtuali di Azure da un'area di Azure a un'altra.

## <a name="before-you-start"></a>Prima di iniziare

Questo articolo presuppone che sia stato preparato per la distribuzione di Site Recovery, come descritto nell'esercitazione sul ripristino di emergenza da Azure ad Azure.This article assumes that you've prepared for Site Recovery deployment, as described in the [Azure to Azure disaster recovery tutorial.](azure-to-azure-tutorial-enable-replication.md)

I prerequisiti devono essere soddisfatti ed è necessario aver creato un insieme di credenziali dei servizi di ripristino.


## <a name="enable-replication"></a>Abilitare la replica

Abilitare la replica. Questa procedura presuppone che l'area di Azure primaria sia Asia orientale e l'area secondaria sia Asia sud-orientale.

1. Nell'insieme di credenziali fare clic su **+Replica**.
2. Annotare i campi seguenti:
   - **Origine:** punto di origine delle macchine virtuali, in questo caso **Azure**.
   - **Percorso di origine:** l'area di Azure da cui si vogliono proteggere le macchine virtuali. Per questa illustrazione, il percorso di origine sarà "Asia orientale"
   - **Modello di distribuzione:** modello di distribuzione di Azure per le macchine virtuali di origine.
   - **Sottoscrizione di origine:** sottoscrizione a cui appartengono le macchine virtuali di origine. Può essere qualsiasi sottoscrizione che si trova nello stesso tenant di Azure Active Directory in cui è presente l'insieme di credenziali di Servizi di ripristino.
   - **Gruppo di risorse:** gruppo di risorse a cui appartengono le macchine virtuali di origine. Tutte le macchine virtuali nel gruppo di risorse selezionato verranno elencate per la protezione nel passaggio successivo.

     ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. In **Macchine virtuali > selezionare le macchine virtuali**fare clic e selezionare ogni macchina virtuale da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.
    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. In **Impostazioni** è possibile configurare le impostazioni del sito di destinazione:

   - **Percorso di destinazione:** posizione in cui verrà eseguita la replica dei dati delle macchine virtuali di origine. In base al percorso selezionato per le macchine virtuali, Site Recovery fornirà l'elenco di aree di destinazione idonee. È consigliabile mantenere il percorso di destinazione identico al percorso dell'insieme di credenziali di Servizi di ripristino.
   - **Sottoscrizione di destinazione**: sottoscrizione di destinazione usata per il ripristino di emergenza. Per impostazione predefinita, la sottoscrizione di destinazione sarà uguale alla sottoscrizione di origine.
   - **Gruppo di risorse di destinazione:** gruppo di risorse a cui tutte le macchine virtuali replicate appartengono.
       - Per impostazione predefinita, Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione con un suffisso "asr" nel nome.
       - Se il gruppo di risorse creato da Site Recovery esiste già, viene riutilizzato.
       - È possibile personalizzare le impostazioni del gruppo di risorse.
       - La posizione del gruppo di risorse di destinazione può essere qualsiasi area di Azure, ad eccezione dell'area in cui sono ospitate le macchine virtuali di origine.
   - Rete virtuale di **destinazione:** per impostazione predefinita, Site Recovery crea una nuova rete virtuale nell'area di destinazione con un suffisso "asr" nel nome. Questa rete è mappata alla rete di origine ed è usata per protezione futura. [Altre informazioni](site-recovery-network-mapping-azure-to-azure.md) sul mapping di rete.
   - Account di archiviazione di destinazione (la macchina virtuale di **origine non usa dischi gestiti):** per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione che archivia la configurazione di archiviazione della macchina virtuale di origine. Nel caso in cui l'account di archiviazione esista già, verrà riusato.
   - **Dischi gestiti dalla replica (la macchina virtuale di origine utilizza dischi gestiti):** Site Recovery crea nuovi dischi gestiti dalla replica nell'area di destinazione per eseguire il mirroring dei dischi gestiti della macchina virtuale di origine con lo stesso tipo di archiviazione (Standard o Premium) del disco gestito della macchina virtuale di origine.
   - **Account di archiviazione della cache:** Site Recovery necessita di un account di archiviazione aggiuntivo, definito account di archiviazione della cache, nell'area di origine. Tutte le modifiche apportate nelle VM di origine vengono registrate e inviate all'account di archiviazione della cache prima della replica nella posizione di destinazione. Questo account di archiviazione deve essere Standard.This storage account should be Standard.
   - Set di **disponibilità di destinazione:** per impostazione predefinita, Site Recovery crea un nuovo set di disponibilità nell'area di destinazione con il suffisso "asr" nel nome, per le macchine virtuali che fanno parte di un set di disponibilità nell'area di origine. Se il set di disponibilità creato da Site Recovery esiste già, viene riutilizzato.
   - **Zone di disponibilità di destinazione**: per impostazione predefinita, Site Recovery assegna nell'area di origine lo stesso numero di zona dell'area di origine se l'area di destinazione supporta le zone di disponibilità.

     Se l'area di destinazione non supporta le zone di disponibilità, per impostazione predefinita le macchine virtuali di destinazione vengono configurate come istanze singole. Se necessario, è possibile configurare tali macchine virtuali in modo che siano incluse nel set di disponibilità dell'area di destinazione facendo clic su 'Personalizza'.

     >[!NOTE]
     >Dopo avere abilitato la replica non è possibile modificare il tipo di disponibilità, ovvero l'istanza singola, il set di disponibilità o la zona di disponibilità. Per modificare il tipo di disponibilità, è necessario disabilitare e abilitare la replica.
     >

   - **Criteri di replica:** definisce le impostazioni per la cronologia di conservazione dei punti di ripristino e la frequenza coerente degli snapshot dell'app. Per impostazione predefinita, Azure Site Recovery crea un nuovo criterio di replica con le impostazioni predefinite di '24 ore' per la conservazione dei punti di ripristino e '4 ore' per la frequenza coerente dello snapshot dell'app.

     ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Abilitare la replica per i dischi aggiuntiEnable replication for added disks

Se si aggiungono dischi a una macchina virtuale di Azure per cui è abilitata la replica, si verifica quanto segue:If you add disks to an Azure VM for which replication is enabled, the following occurs:
-   L'integrità della replica per la macchina virtuale mostra un avviso e una nota informa che uno o più dischi sono disponibili per la protezione.
-   Se si abilita la protezione per i dischi aggiunti, l'avviso scomparirà dopo la replica iniziale del disco.
-   Se si sceglie di non abilitare la replica per il disco, è possibile scegliere di ignorare l'avviso.


    ![Nuovo disco aggiunto](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Per abilitare la replica per un disco aggiunto, eseguire le operazioni seguenti:To enable replication for an added disk, do the following:

1.  Nell'insieme di credenziali > **elementi replicati**fare clic sulla macchina virtuale a cui è stato aggiunto il disco.
2.  Fare clic su **Dischi**, quindi selezionare il disco dati per il quale si desidera abilitare la replica (questi dischi hanno lo stato **Non protetto).**
3.  In **Dettagli disco**fare clic su Abilita **replica**.

    ![Abilitare la replica per il disco aggiuntoEnable replication for added disk](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Dopo l'esecuzione del processo di replica di abilitazione e il completamento della replica iniziale, viene rimosso l'avviso di integrità della replica per il problema del disco.



## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

È possibile modificare le impostazioni di destinazione predefinite usate da Site Recovery.

1. Fare clic su **Personalizza:** accanto a "Sottoscrizione di destinazione" per modificare la sottoscrizione di destinazione predefinita. Selezionare la sottoscrizione dall'elenco di tutte le sottoscrizioni disponibili nello stesso tenant di Azure Active Directory (AAD).

2. Fare clic su **Personalizza:** per modificare le impostazioni predefinite:
    - In **Gruppo di risorse di destinazione** è possibile selezionare il gruppo di risorse dall'elenco di tutti i gruppi di risorse nel percorso di destinazione all'interno della sottoscrizione.
    - In **Rete virtuale di destinazione:**, selezionare la rete dall'elenco di tutte le reti virtuali nel percorso di destinazione.
    - In **Set di disponibilità** è possibile aggiungere le impostazioni del set di disponibilità per la macchina virtuale, se sono parte di un set di disponibilità nell'area di origine.
    - In **Account di archiviazione di destinazione**, selezionare l'account da usare.

        ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Fare clic su **Personalizza** per modificare le impostazioni di replica.
4. In **Coerenza multi-VM**selezionare le macchine virtuali che si desidera replicare insieme.
    - Tutte le macchine virtuali in un gruppo di replica avranno punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover.
    - L'abilitazione della coerenza di più macchine virtuali può influire sulle prestazioni del carico di lavoro (in quanto richiede un utilizzo intensivo della CPU). Deve essere abilitato solo se i computer eseguono lo stesso carico di lavoro ed è necessaria la coerenza tra più computer.
    - Ad esempio, se un'applicazione dispone di 2 macchine virtuali di SQL Server e due server Web, è necessario aggiungere solo le macchine virtuali di SQL Server a un gruppo di replica.
    - È possibile scegliere di avere un massimo di 16 macchine virtuali in un gruppo di replica.
    - Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004.
    - Assicurarsi che non vi sia alcuna appliance firewall che blocca la comunicazione interna tra le macchine virtuali sulla porta 20004.
    - Se si desidera che le macchine virtuali Linux facciano parte di un gruppo di replica, assicurarsi che il traffico in uscita sulla porta 20004 venga aperto manualmente in base alle indicazioni per la versione Linux specifica.
![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Fare clic su **Crea risorsa** > di destinazione**Abilita replica**.
6. Una volta abilitate le macchine virtuali per la replica è possibile controllare lo stato di integrità della macchina virtuale in **Elementi replicati**

>[!NOTE]
>Durante la replica iniziale, l'aggiornamento dello stato può richiedere tempo, senza mostrare alcun progresso. Per ottenere l'ultimo stato del processo, fare clic sul pulsante **Aggiorna**.
>

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
