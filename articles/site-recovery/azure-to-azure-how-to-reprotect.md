---
title: Riproteggere macchine virtuali di Azure sottoposte a failover nell'area di Azure primaria con Azure Site Recovery | Microsoft Docs
description: Descrive come riproteggere macchine virtuali di Azure in un'area secondaria, dopo il failover da un'area primaria, tramite Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: rajanaki
ms.openlocfilehash: 47056c85c6cb66a7fa28d623a4472b827d970dab
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Riproteggere macchine virtuali di Azure sottoposte a failover nell'area primaria


>[!NOTE]
>
> La funzionalità di replica di Site Recovery per le macchine virtuali di Azure è attualmente disponibile in anteprima.



Quando si [effettua il failover](site-recovery-failover.md) di macchine virtuali di Azure da un'area a un'altra tramite [Azure Site Recovery](site-recovery-overview.md), le macchine virtuali si avviano nell'area secondaria in uno stato non protetto. Se si vuole effettuare il failback delle macchine virtuali nell'area primaria, è necessario eseguire le operazioni seguenti:

- Riproteggere le macchine virtuali nell'area secondaria, in modo da avviare la replica nell'area primaria. 
- Dopo la riprotezione e durante la replica delle macchine virtuali, è possibile effettuare il failover dall'area secondaria all'area primaria.

> [!WARNING]
> Per le macchine virtuali di cui è stata [eseguita la migrazione](migrate-overview.md#what-do-we-mean-by-migration) dall'area primaria all'area secondaria o lo spostamento in un altro gruppo di risorse o se è stata eliminata la macchina virtuale di Azure non è possibile effettuare la riprotezione o il failback.


## <a name="prerequisites"></a>Prerequisiti
1. È necessario eseguire il commit del failover delle macchine virtuali dall'area primaria all'area secondaria.
2. Il sito di destinazione primario deve essere disponibile e deve essere possibile accedere a quell'area o crearvi risorse.

## <a name="reprotect-a-vm"></a>Riproteggere una macchina virtuale

1. In **Insieme di credenziali** > **Elementi replicati** fare clic con il pulsante destro del mouse sulla VM sottoposta a failover e selezionare **Riproteggi**. La direzione della riprotezione deve essere da area secondaria ad area primaria. 

  ![Riproteggere](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Rivedere il gruppo di risorse, la rete, la risorsa di archiviazione e i set di disponibilità. Fare quindi clic su **OK**. Le eventuali risorse contrassegnate come nuove vengono create nell'ambito del processo di riprotezione.
3. Con questo processo il sito di destinazione viene aggiornato con i dati più recenti. Al termine, viene eseguita la replica differenziale. Sarà quindi possibile effettuare il failover al sito primario. È possibile selezionare l'account di archiviazione o la rete da usare durante la riprotezione tramite l'opzione Personalizza.

  ![Opzione di personalizzazione](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personalizzare le impostazioni di riprotezione

Durante la riprotezione è possibile personalizzare le proprietà della macchina virtuale di destinazione seguenti.

![Personalizza](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Proprietà |Note  |
|---------|---------|
|Gruppo di risorse di destinazione     | Consente di modificare il gruppo di risorse di destinazione in cui viene creata la macchina virtuale. Durante la riprotezione, la macchina virtuale di destinazione viene eliminata. È possibile scegliere un nuovo gruppo di risorse in cui creare la macchina virtuale dopo il failover.        |
|Rete virtuale di destinazione     | Durante il processo di riprotezione, la rete di destinazione non può essere modificata. Per modificare la rete, ripetere il mapping di rete.         |
|Archiviazione di destinazione     | È possibile modificare l'account di archiviazione usato dalla macchina virtuale dopo il failover.         |
|Archiviazione cache     | È possibile specificare un account di archiviazione della cache da usare durante la replica. Per impostazione predefinita, se non esiste un account di archiviazione della cache ne viene creato uno nuovo.         |
|Set di disponibilità     |Se la macchina virtuale nell'area secondaria fa parte di un set di disponibilità, è possibile scegliere un set di disponibilità per la macchina virtuale di destinazione nell'area primaria. Per impostazione predefinita, Site Recovery tenta di trovare e usare il set di disponibilità esistente nell'area primaria. Durante la personalizzazione, è possibile specificare un nuovo set di disponibilità.         |


### <a name="what-happens-during-reprotection"></a>Cosa accade durante la riprotezione?

Per impostazione predefinita, si verifica quanto segue:

1. Nell'area primaria viene creato un nuovo account di archiviazione della cache.
2. Se l'account di archiviazione di destinazione (l'account di archiviazione originario nell'area primaria) non esiste, ne viene creato uno nuovo. Il nome assegnato all'account di archiviazione corrisponde al nome dell'account di archiviazione usato dalla macchina virtuale secondaria, con il suffisso "asr".
3. Se il set di disponibilità di destinazione non esiste, ne viene creato uno nuovo durante il processo di riprotezione, se necessario. Se le impostazioni di riprotezione sono state personalizzate, viene usato il set selezionato.

Quando si attiva un processo di riprotezione e la macchina virtuale di destinazione esiste già, si verifica quanto segue:

1. I componenti necessari vengono creati nell'ambito della riprotezione. Se esistono già, vengono riusati.
2. Se la macchina virtuale sul lato di destinazione è in esecuzione, viene arrestata.
3. Site Recovery copia il disco della macchina virtuale di destinazione in un contenitore come BLOB di inizializzazione.
4. La macchina virtuale sul lato di destinazione viene quindi eliminata.
5. Il BLOB di inizializzazione viene usato dalla macchina virtuale sul lato di origine (secondaria) per la replica. In questo modo vengono replicati solo i valori delta.
6. Le modifiche più importanti tra il disco di origine e il BLOB di inizializzazione vengono sincronizzate. Questa operazione può richiedere del tempo.
7. Al termine del processo di riprotezione, inizia la replica differenziale, che crea un punto di ripristino in base ai criteri di replica.
8. Al termine del processo di riprotezione, la macchina virtuale acquisisce uno stato protetto.

## <a name="next-steps"></a>Passaggi successivi

Dopo che la macchina virtuale è stata protetta, è possibile avviare un failover. Il failover arresta la macchina virtuale nell'area secondaria e crea e avvia una macchina virtuale nell'area primaria, con un breve tempo di inattività. Per questo motivo è consigliabile scegliere un orario appropriato ed eseguire un failover di test prima di avviare un failover completo nel sito primario. [Altre informazioni](site-recovery-failover.md) sul failover.

