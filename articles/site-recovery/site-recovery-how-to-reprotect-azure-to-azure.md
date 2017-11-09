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
ms.openlocfilehash: 32f5d2d142940bc515849dcd0edb1bb1f152aa6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="reprotect-from-failed-over-azure-region-back-to-primary-region"></a>Eseguire la riprotezione dall'area di Azure sottoposta a failover nell'area primaria



>[!NOTE]
>
> La replica di Site Recovery per le macchine virtuali di Azure è attualmente in anteprima.


## <a name="overview"></a>Panoramica
Quando si esegue il [failover](site-recovery-failover.md) delle macchine virtuali da un'area di Azure a un'altra, le macchine virtuali si trovano in uno stato non protetto. Se si desidera riportarle nell'area primaria, è innanzitutto necessario proteggere le macchine virtuali e sottoporle nuovamente a failover. Non vi è differenza tra l'esecuzione del failover in una direzione o nell'altra. Analogamente, dopo l'abilitazione della protezione delle macchine virtuali, non vi è differenza tra la riprotezione post failover o post failback.
Per spiegare i flussi di lavoro della riprotezione e per evitare confusione, si userà il sito primario delle macchine protette come Asia orientale e il sito di ripristino delle macchine come Asia sudorientale. Durante il failover, si eseguirà il failover delle macchine virtuali nell'area Asia sudorientale. Prima di eseguire il failback, è necessario riproteggere le macchine virtuali dall'area Asia sudorientale all'area Asia orientale. Questo articolo descrive i passaggi necessari per eseguire la riprotezione.

> [!WARNING]
> Se è stata [completata la migrazione](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), la macchina virtuale è stata spostata in un altro gruppo di risorse o la macchina virtuale di Azure è stata eliminata, non è possibile eseguire il failback.

Al termine della riprotezione e dopo l'avvio della replica delle macchine virtuali protette, è possibile avviare un failover sulle macchine virtuali per riportarle nell'area Asia orientale.

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Prerequisiti
1. È necessario aver eseguito il commit delle macchine virtuali.
2. Il sito di destinazione, in questo caso l'area di Azure Asia orientale, deve essere disponibile e in grado di accedere/creare nuove risorse in quell'area.

## <a name="steps-to-reprotect"></a>Passaggi della riprotezione

Di seguito sono riportati i passaggi necessari per riproteggere una macchina virtuale usando le impostazioni predefinite.

1. In **Insieme di credenziali** > **Elementi replicati** fare clic con il pulsante destro del mouse sulla macchina virtuale di cui è stato eseguito il failover e scegliere **Riproteggi**. È anche possibile fare clic sulla macchina virtuale e selezionare **Riproteggi** dai pulsanti di comando.

![Fare clic con il pulsante destro del mouse per riproteggere](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Nel pannello, si noti che la direzione della protezione, **da Asia sudorientale ad Asia orientale**, è già selezionata.

![Riproteggere il pannello](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotectblade.png)

3. Rivedere le informazioni su **gruppo di risorse, rete, set di disponibilità e archiviazione** e fare clic su OK. Le eventuali risorse contrassegnate (nuove) verranno create come parte della riprotezione.

Verrà avviato un processo di riprotezione che eseguirà il seeding del sito di destinazione (SEA in questo caso) con i dati più recenti, e al termine, replicherà i valori delta prima di rieseguire il failover nell'area Asia sudorientale.

### <a name="reprotect-customization"></a>Personalizzazione della riprotezione
Se si desidera scegliere l'account di archiviazione di estrazione o la rete durante la riprotezione, è possibile usare l'opzione di personalizzazione fornita nel pannello riprotezione.

![Opzione di personalizzazione](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

Durante la riprotezione è possibile personalizzare le proprietà seguenti della macchina virtuale di destinazione.

![Personalizzare il pannello](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Proprietà |Note  |
|---------|---------|
|Gruppo di risorse di destinazione     | È possibile scegliere di modificare il gruppo di risorse di destinazione in cui verrà creata la macchina virtuale. Nell'ambito della riprotezione, la macchina virtuale di destinazione verrà eliminata, pertanto è possibile scegliere un nuovo gruppo di risorse in cui creare la macchina virtuale post failover         |
|Rete virtuale di destinazione     | La rete non può essere modificata durante la riprotezione. Per modificare la rete, ripetere il mapping di rete.         |
|Archiviazione di destinazione     | È possibile modificare l'account di archiviazione in cui verrà creata la macchina virtuale post failover.         |
|Archiviazione cache     | È possibile specificare un account di archiviazione cache che verrà usato durante la replica. Se si procede con i valori predefiniti, se non esiste verrà creato un nuovo account di archiviazione cache.         |
|Set di disponibilità     |Se la macchina virtuale in Asia orientale fa parte di un set di disponibilità, è possibile scegliere un set di disponibilità per la macchina virtuale di destinazione in Asia sudorientale. I valori predefiniti troveranno il set di disponibilità SEA esistente e proveranno a usarlo. Durante la personalizzazione, è possibile specificare un set di disponibilità completamente nuovo.         |


### <a name="what-happens-during-reprotect"></a>Cosa accade durante la riprotezione?

Proprio come dopo la prima azione Abilita protezione, di seguito si trovano gli elementi che vengono creati se si utilizzano le impostazioni predefinite.
1. Un account di archiviazione cache viene creato nell'area Asia orientale.
2. Se l'account di archiviazione di destinazione (l'account di archiviazione originale della macchina virtuale in Asia sudorientale) non esiste, ne viene creato uno nuovo. Il nome è composto dall'account di archiviazione della macchina virtuale in Asia orientale più il suffisso "asr".
3. Se il set di disponibilità di destinazione non esiste e le impostazioni predefinite rilevano che è necessario crearne uno nuovo, verrà creato durante il processo di riprotezione. Se la riprotezione è stata personalizzata, verrà usato il set di disponibilità selezionato.
4.

Di seguito è riportato l'elenco delle azioni che si verificano quando si avvia un processo di riprotezione. Questo avviene nel caso in cui la macchina virtuale di destinazione esista.

1. Gli elementi necessari vengono creati durante la riprotezione. Se esistono già, vengono riusati.
2. La macchina virtuale di destinazione (Asia sudorientale) viene prima di tutto disattivata, se è in esecuzione.
3. Il disco della macchina virtuale di destinazione viene copiato da Azure Site Recovery in un contenitore come BLOB di inizializzazione.
4. La macchina virtuale di destinazione viene quindi eliminata.
5. Il BLOB di inizializzazione viene usato dalla macchina virtuale di origine (Asia orientale) per la replica. In questo modo vengono replicati solo i valori delta.
6. Le principali modifiche tra il disco di origine e il BLOB di inizializzazione vengono sincronizzate. Questa operazione può richiedere del tempo.
7. Dopo aver completato il processo di riprotezione, ha inizio la replica dei valori delta che crea un punto di ripristino in base ai criteri.

> [!NOTE]
> Non è possibile eseguire la riprotezione a un livello di piano di ripristino. È solo possibile eseguire la riprotezione solo a un livello di macchina virtuale.

Dopo la riprotezione, la macchina virtuale entrerà in uno stato protetto.

## <a name="next-steps"></a>Passaggi successivi

Quando la macchina virtuale si trova in uno stato protetto, è possibile avviare un failover. Il failover arresterà la macchina virtuale nell'area di Azure Asia orientale, quindi creerà e avvierà la macchina virtuale dell'area Asia sudorientale. Ci sarà pertanto un breve tempo di inattività per l'applicazione. Scegliere un orario per il failover in cui l'inattività dell'applicazione non causerà conseguenze. È consigliabile sottoporre a failover di test la macchina virtuale per assicurarsi che il processo venga eseguito correttamente, prima di avviare un failover.

-   [Passaggi per avviare il failover di test della macchina virtuale](site-recovery-test-failover-to-azure.md)

-   [Passaggi per avviare il failover della macchina virtuale](site-recovery-failover.md)
