---
title: Gestire i punti di ripristino
description: Informazioni su come il servizio backup di Azure gestisce i punti di ripristino per le macchine virtuali
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428706"
---
# <a name="manage-recovery-points"></a>Gestire i punti di ripristino

Questo articolo descrive il funzionamento della conservazione per le macchine virtuali. Ogni volta che si verificano i backup, vengono creati punti di ripristino da cui è possibile eseguire le operazioni di ripristino.

Per le macchine virtuali, il backup iniziale è un backup completo e i backup successivi sono backup incrementali.

## <a name="recovery-points-and-retention"></a>Punti di ripristino e conservazione

### <a name="scheduled-initial-and-incremental-backup"></a>Backup iniziale e incrementale pianificato

Di seguito viene riportato un esempio semplificato della macchina virtuale *V1* con un disco dati costituito da quattro blocchi: Block 1, Block 2, Block 3 e Block 4. Ogni blocco ha una dimensione di 16 KB.

![Macchina virtuale con quattro blocchi](./media/manage-recovery-points/four-blocks.png)

**Passaggio 1-backup iniziale:** Il backup iniziale è un backup completo. Funge da baseline in base a cui vengono applicati i successivi backup incrementali. Si supponga che siano presenti dati scritti nel blocco 1 e nel blocco 2 nella macchina virtuale di origine. Gli stessi dati verranno replicati come D1 e D2 nell'archivio dell'insieme di credenziali di servizi di ripristino.

![Backup iniziale replicato](./media/manage-recovery-points/initial-backup.png)

**Passaggio 2-backup incrementale 1:** Si consiglia di aggiungere nuovi dati al blocco 3 della macchina virtuale. Gli stessi dati verranno replicati al successivo backup incrementale e solo il blocco modificato viene archiviato come D3.  Durante ogni passaggio, anche se si modifica 1 KB del blocco, nel punto di ripristino viene caricato l'intero blocco di 16 KB.

![Primo backup incrementale](./media/manage-recovery-points/first-incremental-backup.png)

**Passaggio 3-backup incrementale 2:**  Si considerino ora le modifiche ai dati nel blocco 3 e nel blocco 2 della VM di origine. Queste modifiche verranno replicate al successivo backup incrementale come D3 "e D2".

![Secondo backup incrementale](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>Backup su richiesta

È possibile scegliere di eseguire un backup su richiesta di una macchina virtuale in qualsiasi momento dopo aver configurato la protezione.

- Il backup su richiesta sarà un backup completo se viene attivato prima del primo backup iniziale pianificato.
- Se il backup iniziale è completo e viene attivato un backup su richiesta, si tratta di un backup incrementale.
- Il periodo di conservazione dei punti di ripristino creati per un backup su richiesta è il valore di conservazione specificato quando si attiva il backup.

### <a name="storage-cost"></a>Costo di archiviazione

Il **punto di ripristino** creato per il backup iniziale contiene tutti i blocchi che contengono i dati. I punti di ripristino incrementali successivi sono costituiti solo da blocchi con dati modificati. I costi di archiviazione corrispondono alla somma di tutti i blocchi che si estendono su tutti i punti di ripristino.

Usare l'esempio precedente per comprendere il costo di archiviazione dopo ogni passaggio:

|Passaggio  |Tipo di backup  |Blocchi modificati  |Tipo di archiviazione |
|------|---------|---------|---------|
|1     |     Backup iniziale    | Blocco 1, blocco 2        |    Corrispondente al punto di ripristino 1 (D1 + D2)     |
|2     |  Backup incrementale 1       |  Blocco 3       |   Corrispondente al punto di ripristino 1 (D1 + D2) + punto di ripristino 2 (D3)      |
|3     |    Backup incrementale 2     |    Blocco 2, blocco 3     |   Corrispondente al punto di ripristino 1 (D1 + D2) + punto di ripristino 2 (D3) + punto di ripristino 3 (D2' + D3')      |

### <a name="recovery-point-expiration"></a>Scadenza punto di ripristino

Ogni punto di ripristino ha una durata di conservazione, come specificato nei criteri di backup. La pulizia viene eseguita a intervalli regolari e tutti i punti di ripristino scaduti vengono eliminati.

Quando il punto di ripristino scade, viene eliminato o Unito.

### <a name="case-1-initial-recovery-point-expires"></a>Caso 1: il punto di ripristino iniziale scade

Quando il punto di ripristino iniziale scade, viene eseguito il merge con il successivo punto di ripristino incrementale. Tutti i blocchi di dati sovrascritti nel punto di ripristino incrementale vengono eliminati e il resto viene Unito. Il backup incrementale diventa quindi il backup completo iniziale. Esaminiamo un esempio:

- Il *punto di ripristino 1* creato durante il backup iniziale include il backup completo della macchina virtuale.
- Quando il *punto di ripristino 1* scade, il *punto di ripristino 2* è il backup completo successivo.
- Il blocco D1 viene unito al *punto di ripristino 2* e D2 viene eliminato poiché i dati nel blocco 2 vengono sovrascritti nel *punto di ripristino 2*. Questa modifica viene acquisita come Block D2 ".
- Il blocco D1 viene mantenuto nei punti di ripristino consecutivi così come è, fino a quando non vengono apportate modifiche prima del backup successivo.

![Primo caso](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>Caso 2: scadenza del punto di ripristino incrementale tra

- Se *il punto di ripristino 2* scade prima del *punto di ripristino 1* , i dati del *punto di ripristino 2* vengono uniti al successivo punto di ripristino disponibile: punto di ripristino *3*. Quindi, il blocco D3 viene unito al *punto di ripristino 3*.
- Il *punto di ripristino 1* è ancora il backup completo con blocco D1 e D2.

![Secondo caso](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>Caso 3: il punto di ripristino su richiesta scade

In questo esempio, è pianificato l'esecuzione di un criterio di pianificazione (backup giornaliero) con periodo di conservazione di *n* giorni.  Se un backup su richiesta viene attivato il quarto giorno prima del successivo backup pianificato e il relativo periodo di conservazione viene specificato come 10 giorni, sarà ancora un backup incrementale. Un punto di ripristino ( *RP1 su richiesta* ) verrà creato dopo il *punto di ripristino 3* e prima del *punto di ripristino 4*.  Alla fine del giorno 14, il punto di ripristino su richiesta ( *RP1 su richiesta* ) viene scaduto e verrà unito al successivo punto di ripristino disponibile. I blocchi di dati ancora presenti nel server vengono Uniti, mentre i blocchi di dati modificati (sovrascritti o eliminati) vengono eliminati dal punto di ripristino scaduto.

![Terzo caso](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>Effetti della modifica dei criteri nei punti di ripristino

Quando i criteri vengono modificati, vengono applicati ai punti di ripristino nuovi ed esistenti. Per ulteriori informazioni, vedere [conseguenze della modifica dei criteri nei punti di ripristino](backup-architecture.md#impact-of-policy-change-on-recovery-points).

### <a name="impact-of-stop-protection-on-recovery-points"></a>Conseguenze dell'arresto della protezione nei punti di ripristino

Esistono due modi per arrestare la protezione di una macchina virtuale:

- **Arrestare la protezione ed eliminare i dati di backup.** Questa opzione consente di arrestare tutti i processi di backup futuri dalla protezione della macchina virtuale ed Elimina tutti i punti di ripristino. Se l' [eliminazione](backup-azure-security-feature-cloud.md) temporanea è abilitata, i dati eliminati verranno conservati per 14 giorni. Gli addebiti non vengono addebitati per gli elementi in stato di eliminazione temporanea. È possibile annullare l'eliminazione dei dati entro il periodo di 14 giorni. Se l'eliminazione temporanea non è abilitata, i dati verranno puliti immediatamente e non sarà possibile ripristinare la macchina virtuale o usare l'opzione **Riprendi backup** .
- **Arrestare la protezione e conservare i dati di backup.** Questa opzione consente di arrestare tutti i processi di backup futuri dalla protezione della macchina virtuale. Tuttavia, il servizio backup di Azure manterrà per sempre i punti di ripristino di cui è stato eseguito il backup. È necessario pagare per i punti di ripristino nell'insieme di credenziali. per informazioni dettagliate, vedere [prezzi di backup di Azure](https://azure.microsoft.com/pricing/details/backup/) . Se necessario, sarà possibile ripristinare la macchina virtuale. Se si decide di riprendere la protezione della macchina virtuale, è possibile usare l'opzione **Riprendi backup** . Dopo la ripresa del backup, le regole di conservazione verranno applicate ai punti di scadenza. È anche possibile eliminare i dati di cui è stato eseguito il backup usando l'opzione  **Elimina dati di backup** .

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>Conseguenze dell'eliminazione di una macchina virtuale senza arrestare la protezione

L'eliminazione di una macchina virtuale senza arresto della protezione ha un effetto sui punti di ripristino ed è uno scenario indesiderato. Prima di eliminare la macchina virtuale, è consigliabile arrestare i backup idealmente. Poiché la risorsa non esiste, i backup pianificati avranno esito negativo con l' [errore VMNotFoundV2](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). I punti di ripristino verranno puliti periodicamente in base ai criteri di conservazione, ma l'ultima copia della macchina virtuale rimarrà sempre e verrà addebitata di conseguenza. A seconda dello scenario, sono disponibili le due opzioni seguenti:

- **Opzione 1:** Ripristinare la macchina virtuale usando uno dei punti di ripristino. Se si vuole ripristinare la macchina virtuale eliminata, eseguire il ripristino usando lo stesso nome e nello stesso gruppo di risorse. Se si protegge la macchina virtuale ripristinata nello stesso insieme di credenziali, i punti di ripristino esistenti verranno collegati automaticamente.
- **Opzione 2:** Passare all'insieme di credenziali dei servizi di ripristino e arrestare la protezione con l'eliminazione dei dati.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>Effetti dei punti di ripristino scaduti per gli elementi in stato eliminato temporaneamente

Se l' [eliminazione](backup-azure-security-feature-cloud.md) temporanea è abilitata per l'insieme di credenziali di servizi di ripristino, il punto di ripristino scaduto rimane in stato di eliminazione temporanea e non viene eliminato. Non viene addebitato alcun addebito quando un punto di ripristino è in stato di eliminazione temporanea.

### <a name="impact-of-churn-on-backup-performance"></a>Effetti della varianza sulle prestazioni del backup

Si supponga che lo spazio di archiviazione totale di una macchina virtuale sia 8 TB e che la varianza sia pari al 5%. Quindi, l'archivio di backup incrementale corrispondente sarà pari al 5% di 8 TB 0,4 TB. Una varianza più elevata corrisponde all'archiviazione back-end più elevata per i successivi backup incrementali. La varianza influisca sulle prestazioni del backup. Maggiore è la varianza, più lento è il processo di backup e maggiore è il consumo di spazio di archiviazione back-end.

Per comprendere in che modo la varianza influisca sulle prestazioni del backup, vedere questo scenario:

|Macchine virtuali  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|Numero di dischi dati    | 4 (A1, A2, A3, A4)        | 4 (B1, B2, B3, B4)        |  4 (C1, C2, C3, C4)       |
|Dimensioni di ogni disco   |      4 TB   | 4 TB        |  4 TB       |
|Varianza dei dati di backup    |   A1-4 TB      | B1-1 TB; B2-1 TB <br> B3-1 TB; B4-1 TB  |   C1-2 TB; C4-2 TB      |

Le prestazioni di backup saranno nell'ordine VM2>VM3>VM1. Il motivo è che i dati varianza sono distribuiti tra i vari dischi. Poiché il backup dei dischi avviene in parallelo, VM2 visualizzerà le prestazioni migliori.

## <a name="next-steps"></a>Passaggi successivi

- [Architettura e componenti di backup di Azure](backup-architecture.md)
