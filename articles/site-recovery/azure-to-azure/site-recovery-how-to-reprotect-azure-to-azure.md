---
title: Come riproteggere le macchine virtuali di Azure sottoposte a failover nell'area di Azure primaria | Microsoft Docs
description: "Dopo il failover delle macchine virtuali da un'area di Azure a un'altra, è possibile utilizzare Azure Site Recovery per proteggere le macchine nella direzione inversa. Imparare i passaggi per eseguire la riprotezione prima di un failover."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: ruturajd
ms.openlocfilehash: 0004323fee44c1433cdd3c39fc1fa7dad965dbf7
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2017
---
# <a name="reprotect-azure-vms-back-to-the-primary-region"></a>Riproteggere le macchine virtuali di Azure nell'area primaria



>[!NOTE]
>
> La replica di Site Recovery per le macchine virtuali (VM, Virtual Machine) di Azure è attualmente in anteprima.


Quando si [effettua il failover](../site-recovery-failover.md) di macchine virtuali da un'area di Azure a un'altra, le macchine virtuali con failover sono in uno stato non protetto. Se si vuole riportarle nell'area primaria, è prima necessario avviare la replica delle macchine virtuali e quindi effettuare di nuovo il failover. Non c'è differenza tra il failover in una direzione o nell'altra. Analogamente, dopo aver abilitato la replica di VM, non c'è alcuna differenza tra la riprotezione dopo il failover o quella dopo il failback.

Per spiegare il processo di riprotezione, si supponga a titolo di esempio che il sito primario delle VM protette sia l'area Asia orientale e che il sito di ripristino sia l'Asia sud-orientale. Durante il failover, le VM passano all'area Asia sud-orientale. Prima di effettuare il failback, è necessario effettuare la replica delle VM dall'area Asia sudorientale all'area Asia orientale. Questo articolo descrive i passaggi necessari per la riprotezione.

> [!WARNING]
> Se la migrazione è stata completata e la macchina virtuale di Azure è stata spostata in un altro gruppo di risorse o è stata eliminata, non è possibile effettuare il failback.

Al termine della riprotezione e durante la replica delle VM è possibile avviare il failover delle VM stesse per riportarle nell'area Asia orientale.

## <a name="prerequisites"></a>Prerequisiti
1. Deve essere stato eseguito il commit della VM.
2. Il sito di destinazione (Asia orientale) deve essere disponibile e l'utente deve essere in grado di accedere a quell'area e crearvi nuove risorse.

## <a name="reprotect"></a>Riproteggere

Per riproteggere una VM usando le impostazioni predefinite, seguire questa procedura.

1. In **Insieme di credenziali** > **Elementi replicati** fare clic con il pulsante destro del mouse sulla VM di cui è stato effettuato il failover e quindi selezionare **Riproteggi**. È anche possibile fare clic sulla macchina virtuale e selezionare **Riproteggi** dai pulsanti di comando.

  ![Riprotezione](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Verificare che la direzione della replica selezionata sia **Southeast Asia to East Asia** (Da Asia sud-orientale ad Asia orientale).

  ![Riproteggere](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Rivedere le informazioni in **Gruppo di risorse, rete, archiviazione e set di disponibilità** e fare clic su **OK**. Eventuali risorse contrassegnate con (novità) vengono create durante la riprotezione.

In questo modo viene attivato un processo di riprotezione che inizializza il sito di destinazione con i dati più recenti e che, dopo che l'operazione è stata completata, replica i delta prima del failback nell'area Asia sud-orientale.

### <a name="reprotect-customization"></a>Personalizzazione della riprotezione
Se si vuole scegliere l'account di archiviazione o la rete di estrazione durante la riprotezione, è possibile usare l'opzione di personalizzazione disponibile nella pagina di riprotezione.

![Opzione di personalizzazione](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Durante la riprotezione è possibile personalizzare le proprietà seguenti della macchina virtuale di destinazione.

![Personalizza](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Proprietà |Note  |
|---------|---------|
|Gruppo di risorse di destinazione     | È possibile scegliere di modificare il gruppo di risorse di destinazione in cui verrà creata la macchina virtuale. Nell'ambito della riprotezione, la macchina virtuale di destinazione verrà eliminata, pertanto è possibile scegliere un nuovo gruppo di risorse in cui creare la macchina virtuale post failover         |
|Rete virtuale di destinazione     | Durante la riprotezione non è possibile modificare la rete. Per modificare la rete, ripetere il mapping di rete.         |
|Archiviazione di destinazione     | È possibile modificare l'account di archiviazione in cui verrà creata la macchina virtuale post failover.         |
|Archiviazione cache     | È possibile specificare un account di archiviazione cache che verrà usato durante la replica. Se si procede con i valori predefiniti, se non esiste verrà creato un nuovo account di archiviazione cache.         |
|Set di disponibilità     |Se la macchina virtuale in Asia orientale fa parte di un set di disponibilità, è possibile scegliere un set di disponibilità per la macchina virtuale di destinazione in Asia sudorientale. I valori predefiniti troveranno il set di disponibilità SEA esistente e proveranno a usarlo. Durante la personalizzazione, è possibile specificare un set di disponibilità completamente nuovo.         |


### <a name="what-happens-during-reprotect"></a>Cosa accade durante la riprotezione?

Come dopo la prima azione di abilitazione della protezione, di seguito si trovano gli elementi che vengono creati se si usano le impostazioni predefinite.
1. Un account di archiviazione cache viene creato nell'area Asia orientale.
2. Se l'account di archiviazione di destinazione (l'account di archiviazione originale della macchina virtuale in Asia sudorientale) non esiste, ne viene creato uno nuovo. Il nome è composto dall'account di archiviazione della macchina virtuale in Asia orientale più il suffisso "asr".
3. Se il set di disponibilità di destinazione non esiste e le impostazioni predefinite rilevano che è necessario crearne uno nuovo, questo viene creato durante il processo di riprotezione. Se la riprotezione è stata personalizzata, viene usato il set di disponibilità selezionato.


Di seguito è riportato l'elenco dei passaggi che vengono eseguiti quando si attiva un processo di riprotezione. Questo avviene nel caso in cui la macchina virtuale di destinazione esista.

1. Gli elementi necessari vengono creati nell'ambito della riprotezione. Se esistono già, vengono riusati.
2. La macchina virtuale di destinazione (Asia sudorientale) viene prima di tutto disattivata, se è in esecuzione.
3. Il disco della macchina virtuale di destinazione viene copiato da Azure Site Recovery in un contenitore come BLOB di inizializzazione.
4. La macchina virtuale di destinazione viene quindi eliminata.
5. Il BLOB di inizializzazione viene usato dalla macchina virtuale di origine (Asia orientale) per la replica. In questo modo vengono replicati solo i valori delta.
6. Le principali modifiche tra il disco di origine e il BLOB di inizializzazione vengono sincronizzate. Questa operazione può richiedere del tempo.
7. Dopo aver completato il processo di riprotezione, ha inizio la replica dei valori delta che crea un punto di recupero in base ai criteri.

> [!NOTE]
> Non è possibile eseguire la riprotezione a un livello di piano di ripristino. È solo possibile eseguire la riprotezione solo a un livello di macchina virtuale.

Al termine della riprotezione, la macchina virtuale entrerà in uno stato protetto.

## <a name="next-steps"></a>Passaggi successivi

Quando la macchina virtuale si trova in uno stato protetto, è possibile avviare un failover. Il failover arresterà la macchina virtuale nell'area di Azure Asia orientale, quindi creerà e avvierà la macchina virtuale dell'area Asia sudorientale. Ci sarà pertanto un breve tempo di inattività per l'applicazione. Scegliere un orario per il failover in cui l'inattività dell'applicazione non causerà conseguenze. Prima di avviare un failover è consigliabile sottoporre la macchina virtuale a un failover di test, per assicurarsi che il processo venga eseguito correttamente.

-   [Passaggi per avviare il failover di test della macchina virtuale](../site-recovery-test-failover-to-azure.md)

-   [Passaggi per avviare il failover della macchina virtuale](../site-recovery-failover.md)
