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
ms.openlocfilehash: 818c053c22cfa47cac0f4f6a19349cf239d3cdec
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368598"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Riproteggere macchine virtuali di Azure sottoposte a failover nell'area primaria

Quando si esegue il failover di macchine virtuali di Azure da un'area a un'altra usando [Azure Site Recovery](site-recovery-overview.md), le macchine virtuali si avviano nell'area secondaria, in uno stato [non](site-recovery-failover.md) **protetto** . Se si vuole eseguire il failback delle macchine virtuali nell'area primaria, eseguire le attività seguenti:

1. Riproteggere le macchine virtuali nell'area secondaria, in modo da avviare la replica nell'area primaria.
1. Al termine della riprotezione e durante la replica delle macchine virtuali, è possibile eseguire il failover dall'area secondaria a quella primaria.

## <a name="prerequisites"></a>Prerequisiti

- È necessario eseguire il commit del failover delle macchine virtuali dall'area primaria all'area secondaria.
- Il sito di destinazione primario deve essere disponibile e deve essere possibile accedere a quell'area o crearvi risorse.

## <a name="reprotect-a-vm"></a>Riproteggere una macchina virtuale

1. In **Insieme di credenziali** > **Elementi replicati** fare clic con il pulsante destro del mouse sulla VM sottoposta a failover e selezionare **Riproteggi**. La direzione della riprotezione deve essere da area secondaria ad area primaria.

   ![Riproteggere](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Rivedere il gruppo di risorse, la rete, la risorsa di archiviazione e i set di disponibilità. Fare quindi clic su **OK**, Se sono presenti risorse contrassegnate come nuove, vengono create come parte del processo di riprotezione.
1. Con questo processo il sito di destinazione viene aggiornato con i dati più recenti. Al termine del processo, si verifica delta replication. Sarà quindi possibile effettuare il failover al sito primario. È possibile selezionare l'account di archiviazione o la rete da usare durante la riprotezione tramite l'opzione Personalizza.

   ![Opzione di personalizzazione](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Personalizzare le impostazioni di riprotezione

Durante la riprotezione è possibile personalizzare le proprietà seguenti della macchina virtuale di destinazione.

![Personalizza](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Proprietà |Note  |
|---------|---------|
|Gruppo di risorse di destinazione | Consente di modificare il gruppo di risorse di destinazione in cui viene creata la macchina virtuale. Durante la riprotezione, la macchina virtuale di destinazione viene eliminata. È possibile scegliere un nuovo gruppo di risorse in cui creare la macchina virtuale dopo il failover. |
|Rete virtuale di destinazione | Durante il processo di riprotezione, la rete di destinazione non può essere modificata. Per modificare la rete, ripetere il mapping di rete. |
|Archiviazione di destinazione (la VM secondaria non usa Managed Disks) | È possibile modificare l'account di archiviazione usato dalla macchina virtuale dopo il failover. |
|Dischi gestiti di replica (la VM secondaria usa dischi gestiti) | Site Recovery crea dischi gestiti di replica nell'area primaria per eseguire il mirroring dei dischi gestiti della VM secondaria. |
|Archiviazione cache | È possibile specificare un account di archiviazione della cache da usare durante la replica. Per impostazione predefinita, viene creato un nuovo account di archiviazione della cache, se non esiste. |
|Set di disponibilità | Se la macchina virtuale nell'area secondaria fa parte di un set di disponibilità, è possibile scegliere un set di disponibilità per la macchina virtuale di destinazione nell'area primaria. Per impostazione predefinita, Site Recovery tenta di trovare e usare il set di disponibilità esistente nell'area primaria. Durante la personalizzazione, è possibile specificare un nuovo set di disponibilità. |

### <a name="what-happens-during-reprotection"></a>Cosa accade durante la riprotezione?

Per impostazione predefinita, si verifica quanto segue:

1. Viene creato un nuovo account di archiviazione della cache nell'area in cui è in esecuzione la macchina virtuale del failover.
1. Se l'account di archiviazione di destinazione (l'account di archiviazione originario nell'area primaria) non esiste, ne viene creato uno nuovo. Il nome dell'account di archiviazione assegnato è il nome dell'account di archiviazione usato dalla macchina virtuale secondaria, con suffisso `asr`.
1. Se la VM usa dischi gestiti, nell'area primaria vengono creati dischi gestiti di replica per archiviare i dati replicati dai dischi della VM secondaria.
1. Se il set di disponibilità di destinazione non esiste, ne viene creato uno nuovo come parte del processo di riprotezione, se necessario. Se le impostazioni di riprotezione sono state personalizzate, viene usato il set selezionato.

Quando si attiva un processo di riprotezione e la macchina virtuale di destinazione esiste già, si verifica quanto segue:

1. Se la macchina virtuale sul lato di destinazione è in esecuzione, viene arrestata.
1. Se la macchina virtuale usa dischi gestiti, viene creata una copia del disco originale con un suffisso `-ASRReplica`. I dischi originali vengono eliminati. Le copie `-ASRReplica` vengono usate per la replica.
1. Se la macchina virtuale usa dischi non gestiti, i dischi dati della macchina virtuale di destinazione vengono scollegati e usati per la replica. Una copia del disco del sistema operativo viene creata e collegata alla macchina virtuale. Il disco del sistema operativo originale viene scollegato e usato per la replica.
1. Vengono sincronizzate solo le modifiche tra il disco di origine e il disco di destinazione. I backup differenziali vengono calcolati confrontando entrambi i dischi e quindi trasferiti. Controllare di seguito per trovare il tempo stimato per il completamento della riprotezione.
1. Al termine della sincronizzazione, inizia il delta replication e viene creato un punto di ripristino in linea con i criteri di replica.

Quando si attiva un processo di riprotezione e la macchina virtuale e i dischi di destinazione non esistono, si verifica quanto segue:

1. Se la macchina virtuale usa dischi gestiti, i dischi di replica vengono creati con `-ASRReplica` suffisso. Le copie `-ASRReplica` vengono usate per la replica.
1. Se la macchina virtuale usa dischi non gestiti, i dischi della replica vengono creati nell'account di archiviazione di destinazione.
1. I dischi interi vengono copiati dall'area sottoposta a failover nella nuova area di destinazione.
1. Al termine della sincronizzazione, inizia il delta replication e viene creato un punto di ripristino in linea con i criteri di replica.

#### <a name="estimated-time-to-do-the-reprotection"></a>Tempo stimato per la riprotezione

Nella maggior parte dei casi, Azure Site Recovery non replica i dati completi nell'area di origine.
Le condizioni seguenti determinano la quantità di dati replicati:

1. Se per qualche motivo i dati della VM di origine vengono eliminati, danneggiati o inaccessibili, ad esempio una modifica/eliminazione di un gruppo di risorse, durante la riprotezione viene eseguita una replica iniziale completa perché non sono disponibili dati nell'area di origine da usare.
1. Se i dati della VM di origine sono accessibili, solo i dati differenziali vengono calcolati confrontando entrambi i dischi e quindi trasferiti. Controllare la tabella seguente per ottenere il tempo stimato.

|Situazione di esempio | Tempo impiegato per la riprotezione |
|---|---|
|L'area di origine ha 1 VM con disco standard da 1 TB.<br/>Vengono usati solo i dati di 127 GB e il resto del disco è vuoto.<br/>Il tipo di disco è standard con velocità effettiva di 60 MiB/S.<br/>Non vengono apportate modifiche ai dati dopo il failover.| Tempo approssimativo: 45 minuti – 1,5 ore.<br/>Durante la riprotezione, Site Recovery compilerà il checksum di tutti i dati che richiederanno 127 GB/45 MB circa 45 minuti.<br/>È necessario un tempo di overhead per la scalabilità automatica di Site Recovery, circa 20-30 minuti.<br/>Nessun addebito in uscita. |
|L'area di origine ha 1 VM con disco standard da 1 TB.<br/>Vengono usati solo i dati di 127 GB e il resto del disco è vuoto.<br/>Il tipo di disco è standard con velocità effettiva di 60 MiB/S.<br/>45 GB di modifiche ai dati dopo il failover.| Tempo approssimativo: 1 ora-2 ore.<br/>Durante la riprotezione, Site Recovery compilerà il checksum di tutti i dati che richiederanno 127 GB/45 MB circa 45 minuti.<br/>Tempo di trasferimento per applicare le modifiche di 45 GB 45 GB/45 MBps, circa 17 minuti.<br/>Gli addebiti in uscita sono per le modifiche ai dati di 45 GB, non per il checksum. |

## <a name="next-steps"></a>Passaggi successivi

Dopo che la macchina virtuale è stata protetta, è possibile avviare un failover. Il failover arresta la macchina virtuale nell'area secondaria e crea e avvia la macchina virtuale nell'area primaria, con un breve tempo di inattività durante questo processo. Si consiglia di scegliere un orario appropriato per questo processo e di eseguire un failover di test prima di avviare un failover completo nel sito primario. [Altre](site-recovery-failover.md) informazioni su Azure Site Recovery failover.
