---
title: Riproteggere le macchine virtuali di Azure nell'area primaria con Azure Site Recovery | Microsoft Docs
description: Viene descritto come riproteggere le macchine virtuali di Azure dopo il failover, dall'area secondaria a quella primaria, usando Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 955e1b84f897a5eb877033e0a58b8d661f143a14
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954196"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Riproteggere macchine virtuali di Azure sottoposte a failover nell'area primaria


Quando si [effettua il failover](site-recovery-failover.md) di macchine virtuali di Azure da un'area a un'altra tramite [Azure Site Recovery](site-recovery-overview.md), le macchine virtuali si avviano nell'area secondaria in uno stato non protetto. Se si vuole effettuare il failback delle macchine virtuali nell'area primaria, è necessario eseguire le operazioni seguenti:

- Riproteggere le macchine virtuali nell'area secondaria, in modo da avviare la replica nell'area primaria.
- Dopo la riprotezione e durante la replica delle macchine virtuali, è possibile effettuare il failover dall'area secondaria all'area primaria.

## <a name="prerequisites"></a>prerequisiti
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

|Proprietà |note  |
|---------|---------|
|Gruppo di risorse di destinazione     | Consente di modificare il gruppo di risorse di destinazione in cui viene creata la macchina virtuale. Durante la riprotezione, la macchina virtuale di destinazione viene eliminata. È possibile scegliere un nuovo gruppo di risorse in cui creare la macchina virtuale dopo il failover.        |
|Rete virtuale di destinazione     | Durante il processo di riprotezione, la rete di destinazione non può essere modificata. Per modificare la rete, ripetere il mapping di rete.         |
|Archiviazione di destinazione (la VM secondaria non usa dischi gestiti)     | È possibile modificare l'account di archiviazione usato dalla macchina virtuale dopo il failover.         |
|Dischi gestiti di replica (la VM secondaria usa dischi gestiti)    | Site Recovery crea dischi gestiti di replica nell'area primaria per eseguire il mirroring dei dischi gestiti della VM secondaria.         |
|Archiviazione cache     | È possibile specificare un account di archiviazione della cache da usare durante la replica. Per impostazione predefinita, se non esiste un account di archiviazione della cache ne viene creato uno nuovo.         |
|Set di disponibilità     |Se la macchina virtuale nell'area secondaria fa parte di un set di disponibilità, è possibile scegliere un set di disponibilità per la macchina virtuale di destinazione nell'area primaria. Per impostazione predefinita, Site Recovery tenta di trovare e usare il set di disponibilità esistente nell'area primaria. Durante la personalizzazione, è possibile specificare un nuovo set di disponibilità.         |


### <a name="what-happens-during-reprotection"></a>Cosa accade durante la riprotezione?

Per impostazione predefinita, si verifica quanto segue:

1. Viene creato un nuovo account di archiviazione della cache nell'area in cui è in esecuzione la macchina virtuale del failover.
2. Se l'account di archiviazione di destinazione (l'account di archiviazione originario nell'area primaria) non esiste, ne viene creato uno nuovo. Il nome assegnato all'account di archiviazione corrisponde al nome dell'account di archiviazione usato dalla macchina virtuale secondaria, con il suffisso "asr".
3. Se la VM usa dischi gestiti, nell'area primaria vengono creati dischi gestiti di replica per archiviare i dati replicati dai dischi della VM secondaria.
4. Se il set di disponibilità di destinazione non esiste, ne viene creato uno nuovo durante il processo di riprotezione, se necessario. Se le impostazioni di riprotezione sono state personalizzate, viene usato il set selezionato.

Quando si attiva un processo di riprotezione e la macchina virtuale di destinazione esiste già, si verifica quanto segue:

1. Se la macchina virtuale sul lato di destinazione è in esecuzione, viene arrestata.
2. Se la macchina virtuale usa dischi gestiti, viene creata una copia dei dischi originali con "-ASRReplica". I dischi originali vengono eliminati. Le copie "-ASRReplica" vengono usate per la replica.
3. Se la macchina virtuale usa dischi non gestiti, i dischi dati della macchina virtuale di destinazione vengono scollegati e usati per la replica. Una copia del disco del sistema operativo viene creata e collegata alla macchina virtuale. Il disco del sistema operativo originale viene scollegato e usato per la replica.
4. Vengono sincronizzate solo le modifiche tra il disco di origine e il disco di destinazione. I backup differenziali vengono calcolati confrontando entrambi i dischi e quindi trasferiti. Per trovare il controllo del tempo stimato riportato di seguito.
5. Al termine della sincronizzazione inizia la replica differenziale, che crea un punto di ripristino in base ai criteri di replica.

Quando si attiva un processo di riprotezione e non esistono i dischi e la macchina virtuale di destinazione, si verifica quanto segue:
1. Se la macchina virtuale usa dischi gestiti, i dischi della replica vengono creati usando il suffisso "-ASRReplica". Le copie "-ASRReplica" vengono usate per la replica.
2. Se la macchina virtuale usa dischi non gestiti, i dischi della replica vengono creati nell'account di archiviazione di destinazione.
3. I dischi interi vengono copiati dall'area sottoposta a failover nella nuova area di destinazione.
4. Al termine della sincronizzazione inizia la replica differenziale, che crea un punto di ripristino in base ai criteri di replica.

#### <a name="estimated-time--to-do-the-reprotection"></a>Tempo stimato per la riprotezione 

Nella maggior parte dei casi, Azure Site Recovery non replica i dati completi nell'area di origine. Di seguito sono riportate le condizioni che determinano la quantità di dati da replicare:

1.  Se i dati della VM di origine vengono eliminati, danneggiati o inaccessibili a causa di qualche motivo, ad esempio modifica/eliminazione del gruppo di risorse, durante la riprotezione, il runtime di integrazione viene completato perché non sono disponibili dati nell'area di origine da usare.
2.  Se i dati della VM di origine sono accessibili, vengono calcolati solo i differenziali confrontando entrambi i dischi e quindi trasferiti. Controllare la tabella seguente per ottenere il tempo stimato 

|\* * Situazione di esempio * * | \* * Tempo impiegato per la riprotezione * * |
|--- | --- |
|L'area di origine ha 1 VM con disco standard da 1 TB<br/>-Vengono usati solo i dati 127 GB e il resto del disco è vuoto<br/>-Il tipo di disco è standard con velocità effettiva di 60 MiB/S<br/>-Nessuna modifica dei dati dopo il failover| Tempo approssimativo 45 minuti-1,5 ore<br/> -Durante la riprotezione Site Recovery compilerà il checksum di dati interi che importerà 127 GB/45 MB ~ 45 minuti<br/>-È necessario un tempo di overhead per Site Recovery per eseguire la scalabilità automatica 20-30 minuti<br/>-Nessun costo in uscita |
|L'area di origine ha 1 VM con disco standard da 1 TB<br/>-Vengono usati solo i dati 127 GB e il resto del disco è vuoto<br/>-Il tipo di disco è standard con velocità effettiva di 60 MiB/S<br/>-45 GB di modifiche ai dati dopo il failover| Tempo approssimativo di 1 ora-2 ore<br/>-Durante la riprotezione Site Recovery compilerà il checksum di dati interi che importerà 127 GB/45 MB ~ 45 minuti<br/>-Tempo di trasferimento per applicare le modifiche di 45 GB 45 GB/45 MBps ~ 17 minuti<br/>-Le tariffe in uscita sono solo per i dati di 45 GB non per il checksum|
 



## <a name="next-steps"></a>Passaggi successivi

Dopo che la macchina virtuale è stata protetta, è possibile avviare un failover. Il failover arresta la macchina virtuale nell'area secondaria e crea e avvia una macchina virtuale nell'area primaria, con un breve tempo di inattività. Per questo motivo è consigliabile scegliere un orario appropriato ed eseguire un failover di test prima di avviare un failover completo nel sito primario. [Altre informazioni](site-recovery-failover.md) sul failover.
