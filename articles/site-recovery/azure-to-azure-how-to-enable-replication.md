---
title: Configurare la replica di macchine virtuali di Azure in Azure Site Recovery | Microsoft Docs
description: In questo articolo viene descritto come configurare la replica di macchine virtuali di Azure da un'area di Azure a un'altra mediante Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: c8b5cf840b8cb5eec2a993cfe35c8a8a7ac904fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60791384"
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Eseguire la replica di macchine virtuali di Azure in un'altra area di Azure



In questo articolo viene descritto come abilitare la replica di macchine virtuali di Azure da un'area di Azure a un'altra.

## <a name="prerequisites"></a>Prerequisiti

In questo articolo si presuppone di avere già configurato Site Recovery per questo scenario, come descritto nell'[Esercitazione da Azure in Azure](azure-to-azure-tutorial-enable-replication.md). Assicurarsi di aver preparato i prerequisiti e creato l'insieme di credenziali di Servizi di ripristino.



## <a name="enable-replication"></a>Abilitare la replica

Abilitare la replica. Questa procedura presuppone che l'area di Azure primaria sia Asia orientale e l'area secondaria sia Asia sud-orientale.

1. Nell'insieme di credenziali fare clic su **+Replica**.
2. Annotare i campi seguenti:
   - **Origine**: punto di origine delle macchine virtuali, in questo caso **Azure**.
   - **Posizione di origine**: area di Azure da cui si vogliono proteggere le macchine virtuali. Per questa illustrazione, il percorso di origine sarà "Asia orientale"
   - **Modello di distribuzione**: modello di distribuzione di Azure per le macchine virtuali di origine.
   - **Sottoscrizione di origine**: la sottoscrizione a cui appartengono le macchine virtuali di origine. Può essere qualsiasi sottoscrizione che si trova nello stesso tenant di Azure Active Directory in cui è presente l'insieme di credenziali di Servizi di ripristino.
   - **Gruppo di risorse**: il gruppo di risorse a cui appartengono le macchine virtuali di origine. Tutte le macchine virtuali nel gruppo di risorse selezionato verranno elencate per la protezione nel passaggio successivo.

     ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. In **Macchine virtuali > Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.
    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. In **Impostazioni** è possibile configurare le impostazioni del sito di destinazione:

   - **Posizione di destinazione**: posizione in cui verrà eseguita la replica dei dati delle macchine virtuali di origine. In base al percorso selezionato per le macchine virtuali, Site Recovery fornirà l'elenco di aree di destinazione idonee. È consigliabile mantenere il percorso di destinazione identico al percorso dell'insieme di credenziali di Servizi di ripristino.
   - **Sottoscrizione di destinazione**: sottoscrizione di destinazione usata per il ripristino di emergenza. Per impostazione predefinita, la sottoscrizione di destinazione sarà uguale alla sottoscrizione di origine.
   - **Gruppo di risorse di destinazione**: gruppo di risorse a cui tutte le macchine virtuali replicate appartengono. Per impostazione predefinita, Azure Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione il cui nome presenta il suffisso "asr". Nel caso in cui il gruppo di risorse creato da Azure Site Recovery esista già, verrà riusato. È anche possibile scegliere di personalizzarlo, come illustrato nella sezione seguente. Il percorso del gruppo di risorse di destinazione può essere in qualsiasi area di Azure, ad eccezione di quella in cui sono ospitate le macchine virtuali di origine.
   - **Rete virtuale di destinazione**: per impostazione predefinita, Site Recovery crea una nuova rete virtuale con un nome con il suffisso "asr" nell'area di destinazione. Questa rete è mappata alla rete di origine ed è usata per protezione futura. [Altre informazioni](site-recovery-network-mapping-azure-to-azure.md) sul mapping di rete.
   - **Account di archiviazione di destinazione (se la VM di origine non usa dischi gestiti)**: per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione, che rispecchia la configurazione delle risorse di archiviazione della macchina virtuale di origine. Nel caso in cui l'account di archiviazione esista già, verrà riusato.
   - **Dischi gestiti di replica (se la VM di origine sfrutta dischi gestiti)**: Site Recovery crea dischi gestiti di replica nell'area di destinazione per eseguire il mirroring dei dischi gestiti della VM di origine con lo stesso tipo di archiviazione (Standard o Premium) del disco gestito della VM di origine.
   - **Account di archiviazione della cache**: Site Recovery necessita di un account di archiviazione aggiuntivo, definito account di archiviazione della cache, nell'area di origine. Tutte le modifiche apportate nelle VM di origine vengono registrate e inviate all'account di archiviazione della cache prima della replica nella posizione di destinazione.
   - **Set di disponibilità di destinazione**: per impostazione predefinita, Azure Site Recovery crea nell'area di destinazione un nuovo set di disponibilità il cui nome include il suffisso "asr" per la parte attinente alle macchine virtuali di un set di disponibilità nell'area di origine. Nel caso in cui il set di disponibilità creato da Azure Site Recovery esista già, verrà riusato.
   - **Zone di disponibilità di destinazione**: per impostazione predefinita, Site Recovery assegna nell'area di origine lo stesso numero di zona dell'area di origine se l'area di destinazione supporta le zone di disponibilità.

     Se l'area di destinazione non supporta le zone di disponibilità, per impostazione predefinita le macchine virtuali di destinazione vengono configurate come istanze singole. Se necessario, è possibile configurare tali macchine virtuali in modo che siano incluse nel set di disponibilità dell'area di destinazione facendo clic su 'Personalizza'.

     >[!NOTE]
     >Dopo avere abilitato la replica non è possibile modificare il tipo di disponibilità, ovvero l'istanza singola, il set di disponibilità o la zona di disponibilità. Per modificare il tipo di disponibilità, è necessario disabilitare e abilitare la replica.
     >
    
   - **Criteri di replica**: Site Recovery definisce le impostazioni per la cronologia della conservazione del punto di recupero e per una frequenza snapshot coerente con l'app. Per impostazione predefinita, Azure Site Recovery crea nuovi criteri di replica con impostazioni predefinite di 24 ore per la conservazione del punto di recupero e di 60 minuti per la frequenza snapshot coerente con l'app.

     ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
  
## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

È possibile modificare le impostazioni di destinazione predefinite usate da Site Recovery.

1. Fare clic su **Personalizza:** accanto a "Sottoscrizione di destinazione" per modificare la sottoscrizione di destinazione predefinita. Selezionare la sottoscrizione dall'elenco di tutte le sottoscrizioni disponibili nello stesso tenant di Azure Active Directory (AAD).

2. Fare clic su **Personalizza:** per modificare le impostazioni predefinite:
    - In **Gruppo di risorse di destinazione** è possibile selezionare il gruppo di risorse dall'elenco di tutti i gruppi di risorse nel percorso di destinazione all'interno della sottoscrizione.
    - In **Rete virtuale di destinazione:**, selezionare la rete dall'elenco di tutte le reti virtuali nel percorso di destinazione.
    - In **Set di disponibilità** è possibile aggiungere le impostazioni del set di disponibilità per la macchina virtuale, se sono parte di un set di disponibilità nell'area di origine.
    - In **Account di archiviazione di destinazione**, selezionare l'account da usare.

        ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
1. Fare clic su **Personalizza** per modificare le impostazioni di replica.
   - In **Coerenza tra più macchine virtuali** selezionare le macchine virtuali da replicare insieme. 
   - Tutte le macchine virtuali in un gruppo di replica avranno punti di ripristino coerenti con l'arresto anomalo del sistema e coerenti con l'app quando si esegue il failover. L'abilitazione della coerenza tra più macchine virtuali può influire sulle prestazioni del carico di lavoro, in quanto implica un uso intensivo della CPU, e deve essere usata solo se le macchine eseguono lo stesso carico di lavoro ed è necessaria la coerenza tra più macchine virtuali. Ad esempio, se un'applicazione ha due macchine virtuali SQL e due server Web, solo le macchine virtuali SQL devono essere aggiunte in un gruppo di replica.
   - È possibile scegliere di inserire al massimo 16 macchine virtuali in un gruppo di replica.
   - Se si abilita la coerenza tra più macchine virtuali, i computer inclusi nel gruppo di replica comunicano tra loro sulla porta 20004. Verificare che nessuna appliance firewall blocchi la comunicazione interna tra VM sulla porta 20004. Se le VM Linux devono far parte di un gruppo di replica, verificare che il traffico in uscita sulla porta 20004 venga aperto manualmente in base alle indicazioni della versione Linux specifica.
![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)
    
2. Fare clic su **Crea risorsa di destinazione** > **Abilitazione della replica**.
3. Una volta abilitate le macchine virtuali per la replica è possibile controllare lo stato di integrità della macchina virtuale in **Elementi replicati**

>[!NOTE]
>Durante la replica iniziale, l'aggiornamento dello stato può richiedere tempo, senza mostrare alcun progresso. Per ottenere l'ultimo stato del processo, fare clic sul pulsante **Aggiorna**.
>

# <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
