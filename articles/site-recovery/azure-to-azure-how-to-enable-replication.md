---
title: Configurare la replica di macchine virtuali di Azure in Azure Site Recovery | Microsoft Docs
description: In questo articolo viene descritto come configurare la replica di macchine virtuali di Azure da un'area di Azure a un'altra mediante Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 0dec8179cc9b69eeea679f517693e4debb9fb56e
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340696"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Eseguire la replica di macchine virtuali di Azure in un'altra area di Azure


In questo articolo viene descritto come abilitare la replica di macchine virtuali di Azure da un'area di Azure a un'altra.

## <a name="before-you-start"></a>Prima di iniziare

Questo articolo si presuppone che è stata preparata per la distribuzione di Site Recovery, come descritto nel [esercitazione di ripristino di emergenza da Azure ad Azure](azure-to-azure-tutorial-enable-replication.md).

Prerequisiti devono essere presenti e siano stati creati un insieme di credenziali di servizi di ripristino.


## <a name="enable-replication"></a>Abilitare la replica

Abilitare la replica. Questa procedura presuppone che l'area di Azure primaria sia Asia orientale e l'area secondaria sia Asia sud-orientale.

1. Nell'insieme di credenziali fare clic su **+Replica**.
2. Annotare i campi seguenti:
   - **Origine**: punto di origine delle macchine virtuali, in questo caso **Azure**.
   - **Posizione di origine**: L'area di Azure da cui si desidera proteggere le macchine virtuali. Per questa illustrazione, il percorso di origine sarà "Asia orientale"
   - **Modello di distribuzione**: modello di distribuzione di Azure per le macchine virtuali di origine.
   - **Sottoscrizione di origine**: La sottoscrizione a cui appartengono le VM di origine. Può essere qualsiasi sottoscrizione che si trova nello stesso tenant di Azure Active Directory in cui è presente l'insieme di credenziali di Servizi di ripristino.
   - **Gruppo di risorse**: il gruppo di risorse a cui appartengono le macchine virtuali di origine. Tutte le macchine virtuali nel gruppo di risorse selezionato verranno elencate per la protezione nel passaggio successivo.

     ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. In **Macchine virtuali > Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.
    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. In **Impostazioni** è possibile configurare le impostazioni del sito di destinazione:

   - **Posizione di destinazione**: posizione in cui verrà eseguita la replica dei dati delle macchine virtuali di origine. In base al percorso selezionato per le macchine virtuali, Site Recovery fornirà l'elenco di aree di destinazione idonee. È consigliabile mantenere il percorso di destinazione identico al percorso dell'insieme di credenziali di Servizi di ripristino.
   - **Sottoscrizione di destinazione**: sottoscrizione di destinazione usata per il ripristino di emergenza. Per impostazione predefinita, la sottoscrizione di destinazione sarà uguale alla sottoscrizione di origine.
   - **Gruppo di risorse di destinazione**: gruppo di risorse a cui tutte le macchine virtuali replicate appartengono.
       - Per impostazione predefinita Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione con suffisso "asr" nel nome.
       - Se il gruppo di risorse creato da Site Recovery già esiste, verrà riusato.
       - È possibile personalizzare le impostazioni del gruppo di risorse.
       - Il percorso del gruppo di risorse di destinazione può essere qualsiasi area di Azure, tranne l'area in cui sono ospitate le macchine virtuali di origine.
   - **Rete virtuale di destinazione**: Per impostazione predefinita, Site Recovery crea una nuova rete virtuale nell'area di destinazione con suffisso "asr" nel nome. Questa rete è mappata alla rete di origine ed è usata per protezione futura. [Altre informazioni](site-recovery-network-mapping-azure-to-azure.md) sul mapping di rete.
   - **Account di archiviazione (macchina virtuale di origine non usa dischi gestiti) di destinazione**: per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione, che rispecchia la configurazione delle risorse di archiviazione della macchina virtuale di origine. Nel caso in cui l'account di archiviazione esista già, verrà riusato.
   - **I dischi gestiti di replica (macchina virtuale di origine Usa dischi gestiti)** : Site Recovery crea nuovi dischi gestiti di replica nell'area di destinazione per eseguire il mirroring dei dischi gestiti della VM di origine con lo stesso tipo di archiviazione (Standard o premium) disco gestito della macchina virtuale di origine.
   - **Account di archiviazione della cache**: Site Recovery necessita di un account di archiviazione aggiuntivo, definito account di archiviazione della cache, nell'area di origine. Tutte le modifiche apportate nelle VM di origine vengono registrate e inviate all'account di archiviazione della cache prima della replica nella posizione di destinazione.
   - **Set di disponibilità di destinazione**: Per impostazione predefinita, Site Recovery crea un nuovo set di disponibilità nell'area di destinazione con suffisso "asr" nel nome, per le macchine virtuali che fanno parte di un set di disponibilità nell'area di origine. Se il set di disponibilità creato da Site Recovery già esiste, verrà riusato.
   - **Zone di disponibilità di destinazione**: per impostazione predefinita, Site Recovery assegna nell'area di origine lo stesso numero di zona dell'area di origine se l'area di destinazione supporta le zone di disponibilità.

     Se l'area di destinazione non supporta le zone di disponibilità, per impostazione predefinita le macchine virtuali di destinazione vengono configurate come istanze singole. Se necessario, è possibile configurare tali macchine virtuali in modo che siano incluse nel set di disponibilità dell'area di destinazione facendo clic su 'Personalizza'.

     >[!NOTE]
     >Dopo avere abilitato la replica non è possibile modificare il tipo di disponibilità, ovvero l'istanza singola, il set di disponibilità o la zona di disponibilità. Per modificare il tipo di disponibilità, è necessario disabilitare e abilitare la replica.
     >
    
   - **Criteri di replica**: Site Recovery definisce le impostazioni per la cronologia della conservazione del punto di recupero e per una frequenza snapshot coerente con l'app. Per impostazione predefinita, Azure Site Recovery crea nuovi criteri di replica con impostazioni predefinite di "24 ore per la conservazione del punto di ripristino e '4 ore' coerente con l'app per la frequenza di snapshot.

     ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Abilitare la replica per i dischi aggiunti

Se si aggiungono dischi a una VM di Azure per cui è abilitata la replica, si verifica quanto segue:
-   Integrità della replica per la macchina virtuale viene visualizzato un avviso e una nota indica che una o più dischi sono disponibili per la protezione.
-   Se si abilita la protezione per i dischi aggiunti, l'avviso scompare dopo la replica iniziale del disco.
-   Se si sceglie di non abilitare la replica per il disco, è possibile selezionare per ignorare l'avviso.

    
    ![Nuovo disco aggiunto](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Per abilitare la replica per un disco aggiunto, eseguire le operazioni seguenti:

1.  Nell'insieme di credenziali > **elementi replicati**, scegliere la macchina virtuale a cui è stato aggiunto il disco.
2.  Fare clic su **dischi**e quindi selezionare il disco dati per il quale si desidera abilitare la replica (questi dischi hanno un **non protette** stato).
3.  Nelle **dettagli disco**, fare clic su **abilita la replica**.

    ![Abilitare la replica disco aggiunto](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Dopo l'esecuzione dei processi di enable replica e il completamento della replica iniziale, l'avviso di integrità della replica per il problema del disco viene rimosso.


  
## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

È possibile modificare le impostazioni di destinazione predefinite usate da Site Recovery.

1. Fare clic su **Personalizza:** accanto a "Sottoscrizione di destinazione" per modificare la sottoscrizione di destinazione predefinita. Selezionare la sottoscrizione dall'elenco di tutte le sottoscrizioni disponibili nello stesso tenant di Azure Active Directory (AAD).

2. Fare clic su **Personalizza:** per modificare le impostazioni predefinite:
    - In **Gruppo di risorse di destinazione** è possibile selezionare il gruppo di risorse dall'elenco di tutti i gruppi di risorse nel percorso di destinazione all'interno della sottoscrizione.
    - In **Rete virtuale di destinazione:** , selezionare la rete dall'elenco di tutte le reti virtuali nel percorso di destinazione.
    - In **Set di disponibilità** è possibile aggiungere le impostazioni del set di disponibilità per la macchina virtuale, se sono parte di un set di disponibilità nell'area di origine.
    - In **Account di archiviazione di destinazione**, selezionare l'account da usare.

        ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Fare clic su **Personalizza** per modificare le impostazioni di replica.
4. Nelle **coerenza tra più VM**, selezionare le macchine virtuali da replicare insieme.
    - Tutte le macchine virtuali in un gruppo di replica avranno punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover.
    - Abilitazione della coerenza di più macchine Virtuali può influire sulle prestazioni del carico di lavoro (come avviene con utilizzo intensivo della CPU). Deve essere abilitata solo se i computer eseguono stesso carico di lavoro, ed è necessaria la coerenza tra più computer.
    - Ad esempio, se un'applicazione ha 2 macchine virtuali di SQL Server e due server web, è consigliabile aggiungere solo le VM di SQL Server a un gruppo di replica.
    - È possibile scegliere di avere un massimo di 16 macchine virtuali in un gruppo di replica.
    - Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004.
    - Non verificare che nessuna appliance firewall blocchi la comunicazione interna tra VM sulla porta 20004.
    - Se si desidera che le VM Linux devono far parte di un gruppo di replica, verificare che il traffico in uscita sulla porta 20004 venga aperto manualmente in base alle linee guida per la versione Linux specifica.
![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
5. Fare clic su **Crea risorsa di destinazione** > **Abilitazione della replica**.
6. Una volta abilitate le macchine virtuali per la replica è possibile controllare lo stato di integrità della macchina virtuale in **Elementi replicati**

>[!NOTE]
>Durante la replica iniziale, l'aggiornamento dello stato può richiedere tempo, senza mostrare alcun progresso. Per ottenere l'ultimo stato del processo, fare clic sul pulsante **Aggiorna**.
>

# <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
