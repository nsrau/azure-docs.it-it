---
title: Configurare la replica di macchine virtuali di Azure in Azure Site Recovery | Microsoft Docs
description: In questo articolo viene descritto come configurare la replica di macchine virtuali di Azure da un'area di Azure a un'altra mediante Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2017
ms.author: asgang
ms.openlocfilehash: 5a0e1f638d68131c3c9f1cac36d63795b87e8f41
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>Eseguire la replica di macchine virtuali di Azure in un'altra area di Azure


>[!NOTE]
>
> La replica di Site Recovery per le macchine virtuali di Azure è attualmente in anteprima.

In questo articolo viene descritto come abilitare la replica di macchine virtuali di Azure da un'area di Azure a un'altra.

## <a name="prerequisites"></a>prerequisiti

In questo articolo si presuppone di avere già configurato Site Recovery per questo scenario, come descritto nell'[Esercitazione da Azure in Azure](azure-to-azure-tutorial-enable-replication.md). Assicurarsi di aver preparato i prerequisiti e creato l'insieme di credenziali di Servizi di ripristino.



## <a name="enable-replication"></a>Abilitare la replica

Abilitare la replica. Questa procedura presuppone che l'area di Azure primaria sia Asia orientale e l'area secondaria sia Asia sud-orientale.

1. Nell'insieme di credenziali fare clic su **+Replica**.
2. Annotare i campi seguenti:
    - **Origine:** punto di origine delle macchine virtuali, in questo caso **Azure**.
    - **Percorso di origine:** area di Azure da cui si vogliono proteggere le macchine virtuali. Per questa illustrazione, il percorso di origine sarà "Asia orientale"
    - **Modello di distribuzione:** modello di distribuzione di Azure per le macchine virtuali di origine.
    - **Gruppo di risorse:** gruppo di risorse a cui appartengono le macchine virtuali di origine. Tutte le macchine virtuali nel gruppo di risorse selezionato verranno elencate per la protezione nel passaggio successivo.

    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. In **Macchine virtuali > Seleziona macchine virtuali** fare clic per selezionare tutte le macchine virtuali da replicare. È possibile selezionare solo i computer per cui è possibile abilitare la replica. Fare quindi clic su **OK**.
    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. In **Impostazioni** è possibile configurare le impostazioni del sito di destinazione:

    - **Percorso di destinazione:** posizione in cui verrà eseguita la replica dei dati delle macchine virtuali di origine. In base al percorso selezionato per le macchine virtuali, Site Recovery fornirà l'elenco di aree di destinazione idonee. È consigliabile mantenere il percorso di destinazione identico al percorso dell'insieme di credenziali di Servizi di ripristino.
    - **Gruppo di risorse di destinazione:** gruppo di risorse a cui tutte le macchine virtuali replicate appartengono. Per impostazione predefinita, Azure Site Recovery crea un nuovo gruppo di risorse nell'area di destinazione il cui nome presenta il suffisso "asr". Nel caso in cui il gruppo di risorse creato da Azure Site Recovery esista già, verrà riusato. È anche possibile scegliere di personalizzarlo, come illustrato nella sezione seguente.
    - **Rete virtuale di destinazione:** per impostazione predefinita, Site Recovery crea una nuova rete virtuale nell'area di destinazione con un nome con suffisso "asr". Questa rete è mappata alla rete di origine ed è usata per protezione futura. [Altre informazioni](site-recovery-network-mapping-azure-to-azure.md) sul mapping di rete.
    - **Account di archiviazione di destinazione:** per impostazione predefinita, Site Recovery crea un nuovo account di archiviazione di destinazione, che rispecchia la configurazione delle risorse di archiviazione della macchina virtuale di origine. Nel caso in cui l'account di archiviazione esista già, verrà riusato.
    - **Account di archiviazione della cache:** Site Recovery necessita di un account di archiviazione aggiuntivo, definito account di archiviazione della cache, nell'area di origine. Tutte le modifiche apportate nelle VM di origine vengono registrate e inviate all'account di archiviazione della cache prima della replica nella posizione di destinazione.
    - **Set di disponibilità:** per impostazione predefinita, Azure Site Recovery crea un nuovo set di disponibilità nell'area di destinazione, con un nome con suffisso "asr". Nel caso in cui il set di disponibilità creato da Azure Site Recovery esista già, verrà riusato.
    - **Criteri di replica:** definisce le impostazioni per la cronologia della conservazione del punto di ripristino e per la frequenza snapshot coerenti con l'app. Per impostazione predefinita, Azure Site Recovery crea nuovi criteri di replica con impostazioni predefinite di 24 ore per la conservazione del punto di recupero e di 60 minuti per la frequenza snapshot coerente con l'app.

    ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>Personalizzare le risorse di destinazione

È possibile modificare le impostazioni di destinazione predefinite usate da Site Recovery.

1. Fare clic su **Personalizza:** per modificare le impostazioni predefinite:
    - In **Gruppo di risorse di destinazione** è possibile selezionare il gruppo di risorse dall'elenco di tutti i gruppi di risorse nel percorso di destinazione all'interno della sottoscrizione.
    - In **Rete virtuale di destinazione:**, selezionare la rete dall'elenco di tutte le reti virtuali nel percorso di destinazione.
    - In **Set di disponibilità** è possibile aggiungere le impostazioni del set di disponibilità per la macchina virtuale, se sono parte di un set di disponibilità nell'area di origine.
    - In **Account di archiviazione di destinazione**, selezionare l'account da usare.

        ![Abilitare la replica](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. Fare clic su **Crea risorsa di destinazione** > **Abilitazione della replica**.
3. Una volta abilitate le macchine virtuali per la replica è possibile controllare lo stato di integrità della macchina virtuale in **Elementi replicati**

>[!NOTE]
>Durante la replica iniziale, l'aggiornamento dello stato può richiedere tempo, senza mostrare alcun progresso. Per ottenere l'ultimo stato del processo, fare clic sul pulsante **Aggiorna**.
>

# <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.

