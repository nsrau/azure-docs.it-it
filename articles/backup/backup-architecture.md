---
title: Architettura di Backup di Azure
description: Panoramica dell'architettura, dei componenti e dei processi usati dal servizio Backup di Azure.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: raynew
ms.openlocfilehash: d3e6a17ba9d0712d921d8e0a1d0bcbcd68ce5cfb
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360480"
---
# <a name="azure-backup-architecture"></a>Architettura di Backup di Azure

È possibile usare il [servizio Backup di Azure](backup-overview.md) per eseguire il backup dei dati nel cloud di Microsoft Azure. Questo articolo riepiloga l'architettura, i componenti e i processi di Backup di Azure. 


## <a name="what-does-azure-backup-do"></a>Che cosa fa Backup di Azure?

Backup di Azure esegue il backup dei dati, dello stato della macchina e dei carichi di lavoro in esecuzione su computer locali e macchine virtuali di Azure. Esistono diversi scenari di Backup di Azure.

- **Backup di computer locali**:
    - È possibile eseguire il backup di computer locali direttamente in Azure con Backup di Azure.
    - È possibile proteggere i computer locali con System Center Data Protection Manager (DPM) o un server di Backup di Microsoft Azure (MABS) e quindi eseguire il backup dei dati protetti in DPM/MABS in Azure usando Backup di Azure.
- **Backup delle macchine virtuali di Azure**:
    - È possibile eseguire il backup di macchine virtuali di Azure direttamente con Backup di Azure.
    - È possibile proteggere le macchine virtuali di Azure con DPM e MABS in esecuzione in Azure e quindi eseguire il backup dei dati protetti in DPM/MABS con Backup di Azure.

Altre informazioni su [cosa è possibile includere nel backup](backup-overview.md) e sugli [scenari di backup supportati](backup-support-matrix.md).


## <a name="where-is-data-backed-up"></a>Dove viene eseguito il backup dei dati?

Backup di Azure archivia i dati di backup in un insieme di credenziali di Servizi di ripristino. Un insieme di credenziali è un'entità di archiviazione online in Azure usata per contenere dati, ad esempio copie di backup, punti di ripristino e criteri di backup.

- Gli insiemi di credenziali di Servizi di ripristino semplificano l'organizzazione dei dati di backup, riducendo al minimo l'overhead di gestione.
- In ogni sottoscrizione di Azure è possibile creare fino a 500 insiemi di credenziali di Servizi di ripristino. 
- È possibile monitorare gli elementi sottoposti a backup in un insieme di credenziali, tra cui computer locali e macchine virtuali di Azure.
- È possibile gestire l'accesso dell'insieme di credenziali tramite il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) di Azure.
- È necessario specificare come vengono replicati i dati nell'insieme di credenziali per la ridondanza:
    - **Archiviazione con ridondanza locale**: è possibile usare l'archiviazione con ridondanza locale per la protezione da errori in un data center. L'archiviazione con ridondanza locale replica i dati in un'unità di scala di archiviazione. [Altre informazioni](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)
    - **Archiviazione con ridondanza geografica**: è possibile usare l'archiviazione con ridondanza geografica per la protezione da interruzioni a livello di area e per replicare i dati in un'area secondaria. [Altre informazioni](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs) 
    - Per impostazione predefinita, gli insiemi di credenziali di Servizi di ripristino per il backup usano l'archiviazione con ridondanza geografica. 



## <a name="how-does-azure-backup-work"></a>Come funziona Backup di Azure?

Backup di Azure esegue i processi di backup in base a criteri di backup predefiniti o personalizzati. Il modo in cui Backup di Azure esegue un backup dipende dallo scenario.

**Scenario** | **Dettagli** 
--- | ---
**Backup diretto di computer locali** | Per eseguire direttamente il backup di computer locali, Backup di Azure usa l'agente di Servizi di ripristino di Microsoft Azure. L'agente viene installato in ogni computer di cui si vuole eseguire il backup. <br/><br/> Questo tipo di backup non è disponibile per macchine virtuali Linux locali. 
**Backup diretto delle macchine virtuali di Azure** | Per eseguire direttamente il backup di macchine virtuali di Azure, viene installata un'estensione di macchina virtuale di Azure nella macchina virtuale alla prima esecuzione del backup per la macchina virtuale. 
**Backup di computer e app protetti da DPM o MABS** | In questo scenario, prima di tutto viene eseguito il backup del computer o dell'app nello spazio di archiviazione locale di DPM o MABS. Backup di Azure esegue poi il backup dei dati in DPM/MABS nell'insieme di credenziali. I computer locali possono essere protetti da DPM/MABS in esecuzione in locale. Le macchine virtuali di Azure possono essere protette da DPM/MABS in esecuzione in Azure.

Vedere una [panoramica](backup-overview.md) e [informazioni sulle funzionalità supportate](backup-support-matrix.md) per ogni scenario.


### <a name="backup-agents"></a>Agenti di backup

Backup di Azure offre diversi agenti, a seconda del tipo di backup.

**Agente** | **Dettagli** 
--- | --- 
**Agente di Servizi di ripristino di Microsoft Azure (MARS)** | Questo agente viene eseguito nel singoli server Windows locali per eseguire il backup di file, cartelle e stato del sistema<br/><br/> Questo agente viene eseguito nei server DPM/MABS per eseguire il backup del disco di archiviazione locale di DPM/MABS. Il backup delle macchine e delle app viene eseguito in locale in questo disco di DPM/MABS.
**Estensione per le macchine virtuali di Azure** | Per eseguire il backup di macchine virtuali di Azure, viene aggiunta un'estensione di backup all'agente di macchine virtuali di Azure in esecuzione nelle macchine virtuali. 


## <a name="backup-types"></a>Tipi di backup

**Tipo di backup** | **Dettagli** | **Utilizzo**
--- | --- | ---
**Completo** | Un backup contiene l'intera origine dati.<br/><br/> Il backup completo richiede maggiore larghezza di banda di rete. | Usato per il backup iniziale.
**Differenziale** |  Archivia i blocchi modificati dopo il backup completo iniziale. Usa una quantità inferiore di risorse di rete e archiviazione e non conserva copie ridondanti dei dati non modificati.<br/><br/> Non è efficiente perché vengono trasferiti e archiviati i blocchi di dati invariati tra backup successivi. | Non è usato da Backup di Azure.
**Incrementale** | Efficienza elevata per rete e archiviazione. Archivia solo i blocchi di dati modificati dal backup precedente. <br/><br/> Con i backup incrementali non è necessario integrare con backup completi. | Usato da DPM/MABS per i backup su disco e usato in tutti i backup in Azure.

### <a name="comparison"></a>Confronto

L'utilizzo dell'archiviazione, l'obiettivo del tempo di ripristino (RTO) e l'utilizzo della rete variano per ogni tipo di backup. L'immagine seguente mostra un confronto dei tipi di backup.
- L'origine dati A è composta da 10 blocchi di archiviazione A1-A10, di cui viene eseguito il backup mensilmente.
- I blocchi A2, A3, A4 e A9 cambiano nel primo mese, mentre il blocco A5 cambia il mese successivo.
- Per i backup differenziali, nel secondo mese viene eseguito il backup dei blocchi modificati A2, A3, A4 e A9. Nel terzo mese, viene eseguito nuovamente il backup di questi stessi blocchi, insieme al blocco A5 modificato. Il backup continua a essere eseguito per i blocchi modificati fino al backup completo successivo.
- Per i backup incrementali, dopo aver eseguito il backup completo per il primo mese, i blocchi A2, A3, A4 e A9 vengono contrassegnati come modificati e trasferiti al secondo mese. Nel terzo mese, viene contrassegnato e trasferito solo il blocco A5 modificato. 

![immagine che mostra i confronti dei metodi di backup](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>Funzionalità di backup

La tabella seguente riepiloga le funzionalità per i diversi tipi di backup.

**Funzionalità** | **Computer Windows locali (diretto)** | **Macchine virtuali di Azure** | **Computer/app con DPM/MABS**
--- | --- | --- | ---
Backup nell'insieme di credenziali | ![Yes][green] | ![Sì][green] | ![Yes][green] 
Backup su disco DPM/MABS e poi in Azure | | | ![Yes][green] 
Compressione dei dati inviati per il backup | ![Yes][green] | Durante il trasferimento dei dati non viene usata alcuna compressione. Leggero aumento dello spazio di archiviazione richiesto, ma ripristino più veloce.  | ![Yes][green] 
Backup incrementale |![Yes][green] |![Sì][green] |![Yes][green] 
Backup di dischi deduplicati | | | ![Parzialmente][yellow]<br/><br/> Solo per i server DPM/MABS distribuiti in locale. 

![chiave tabella](./media/backup-architecture/table-key.png)


## <a name="architecture-direct-backup-of-on-premises-windows-machines"></a>Architettura: backup diretto di computer Windows locali

1. Per configurare lo scenario, scaricare e installare l'agente di Servizi di ripristino di Microsoft Azure nel computer, selezionare gli elementi da includere nel backup, quando verranno eseguiti i backup e per quanto tempo verranno conservati in Azure.
2. Il backup iniziale viene eseguito in base alle impostazioni di backup specificate.
3. L'agente di Servizi di ripristino di Microsoft Azure usa il servizio Copia Shadow del volume di Windows (VSS) per creare uno snapshot temporizzato dei volumi selezionati per il backup.
    - L'agente di Servizi di ripristino di Microsoft Azure usa solo il writer del sistema di Windows per acquisire lo snapshot.
    - L'agente non usa alcun VSS writer dell'applicazione e pertanto non acquisisce snapshot coerenti con l'app.
3. Dopo la creazione dello snapshot con VSS, l'agente di Servizi di ripristino di Microsoft Azure crea un disco rigido virtuale nella cartella della cache specificata al momento della configurazione del backup e archivia i checksum per ogni blocco di dati. 
4. I backup incrementali vengono eseguiti in base alla pianificazione specificata, a meno che non si esegua un backup ad hoc.
5. Nei backup incrementali, i file modificati vengono identificati e viene creato un nuovo disco rigido virtuale, che viene compresso, crittografato e poi inviato all'insieme di credenziali.
6. Al termine dell'esecuzione del backup incrementale, il nuovo disco rigido virtuale viene unito con il disco rigido virtuale creato dopo la replica iniziale, rendendo così disponibile lo stato più recente da usare per il confronto per il backup in corso. 

![Backup del server Windows locale con l'agente di Servizi di ripristino di Microsoft Azure](./media/backup-architecture/architecture-on-premises-mars.png)


## <a name="architecture-direct-backup-of-azure-vms"></a>Architettura: backup diretto di macchine virtuali di Azure

1. Quando si abilita il backup per una macchina virtuale di Azure, viene eseguito un backup in base alla pianificazione specificata.
2. Durante il primo backup, viene installata un'estensione di backup nella macchina virtuale se è in esecuzione.
    - Per le macchine virtuali Windows viene installata l'estensione VMSnapshot.
    - Per le macchine virtuali Linux viene installata l'estensione VMSnapshot per Linux.
3. L'estensione acquisisce uno snapshot a livello di archiviazione. 
    - Per le macchine virtuali Windows in esecuzione, il servizio Backup si coordina con il servizio VSS per creare uno snapshot della macchina virtuale coerente con l'app. Per impostazione predefinita, Backup di Azure esegue backup VSS completi. Se Backup di Azure non riesce a creare uno snapshot coerente con l'app, acquisisce uno snapshot coerente con i file.
    - Per le macchine virtuali Linux, Backup di Azure crea un backup coerente con i file. Per gli snapshot coerenti con l'app è necessario personalizzare manualmente gli script pre/post.
    - Il backup viene ottimizzato eseguendo in parallelo il backup di ogni disco di macchina virtuale. Per ogni disco di cui si esegue il backup, Backup di Azure legge i blocchi nel disco e archivia solo i dati modificati. 
4. Dopo la creazione dello snapshot, i dati vengono trasferiti nell'insieme di credenziali. 
    - Vengono copiati solo i blocchi di dati modificati dopo l'ultimo backup.
    - I dati non vengono crittografati. Backup di Azure può eseguire il backup di macchine virtuali di Azure crittografate tramite Crittografia dischi di Azure.
    - I dati dello snapshot potrebbero non essere copiati immediatamente nell'insieme di credenziali. Questa operazione potrebbe richiedere alcune ore nei momenti di picco. Il tempo di backup totale per una macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.
5. Dopo aver inviato i dati nell'insieme di credenziali, lo snapshot viene rimosso e viene creato un punto di ripristino.


![Backup di macchine virtuali di Azure](./media/backup-architecture/architecture-azure-vm.png)


## <a name="architecture-back-up-to-dpmmabs"></a>Architettura: backup in DPM/MABS

1. È necessario installare l'agente di protezione DPM o MABS nei computer da proteggere e aggiungere i computer a un gruppo protezione dati di DPM.
    - Per proteggere i computer locali, il server DPM o MABS deve essere in locale.
    - Per proteggere le macchine virtuali di Azure, il server DPM o MABS deve trovarsi in Azure, in esecuzione come macchina virtuale di Azure.
    - Con DPM/MABS è possibile proteggere i volumi, le condivisioni, i file e le cartelle di backup. È possibile proteggere lo stato del sistema dei computer o computer bare metal e proteggere app specifiche con le impostazioni di backup per le app.
2. Quando si configura la protezione per un computer o un'app in DPM, si seleziona il backup nel disco locale di DPM per l'archiviazione a breve termine e in Azure (protezione online). È anche possibile specificare quando deve essere eseguito il backup nello spazio di archiviazione locale di DPM/MABS e quando eseguire il backup online in Azure.
3. Il backup del disco del carico di lavoro protetto viene eseguito nei dischi DPM locali e in Azure, in base alla pianificazione specificata.
4. Il backup online nell'insieme di credenziali viene gestito dall'agente di Servizi di ripristino di Microsoft Azure in esecuzione nel server DPM/MABS.

![Backup di computer/carichi di lavoro protetti da DPM o MABS](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Risorse di archiviazione delle macchine virtuali di Azure

- Le macchine virtuali di Azure usano dischi per archiviare il sistema operativo, le app e i dati.
- Le macchine virtuali di Azure dispongono di almeno due dischi, uno per il sistema operativo e un disco temporaneo. Possono anche avere dischi dati per i dati delle app. I dischi vengono archiviati come dischi rigidi virtuali.
- I dischi rigidi virtuali vengono archiviati come BLOB di pagine negli account di archiviazione Standard o Premium in Azure.
    - Archiviazione standard: supporto di dischi affidabili e a basso costo per le macchine virtuali che eseguono carichi di lavoro non sensibili alla latenza. L'archiviazione Standard può usare dischi SSD standard.
    - Archiviazione Premium: supporto per dischi ad alte prestazioni. Usa dischi SSD Premium.
- Sono disponibili tre diversi livelli di prestazioni per i dischi:
    - Disco HDD Standard: supportato da unità HDD e usato per un'archiviazione conveniente.
    - Disco SSD Standard: combina elementi dei dischi SSD Premium e dei dischi HDD Standard, offrendo maggiore coerenza di prestazioni e affidabilità rispetto ai dischi HDD, ma comunque in modo conveniente.
    - Disco SSD Premium: supportato da unità SSD, offre alte prestazioni e bassa latenza per le macchine virtuali che eseguono carichi di lavoro con I/O elevato.
- I dischi possono essere gestiti o non gestiti:
    - Dischi non gestiti: tipi di dischi tradizionali usati dalle macchine virtuali. Per questi dischi è necessario creare un account di archiviazione personale e specificarlo durante la creazione del disco. Occorre stabilire come ottimizzare le risorse di archiviazione per le macchine virtuali.
    - Dischi gestiti: Azure gestisce automaticamente la creazione e la gestione degli account di archiviazione. Occorre specificare le dimensioni del disco e il livello di prestazioni e Azure crea e gestisce i dischi automaticamente. Azure gestisce l'archiviazione quando si aggiungono dischi o si ridimensionano le macchine virtuali.

Altre informazioni:

- Altre informazioni sull'archiviazione su disco per le macchine virtuali [Windows](../virtual-machines/windows/about-disks-and-vhds.md) e [Linux](../virtual-machines/linux/about-disks-and-vhds.md).
- Informazioni sull'archiviazione [Standard](../virtual-machines/windows/standard-storage.md) e [Premium](../virtual-machines/windows/premium-storage.md).


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>Backup e ripristino di macchine virtuali di Azure con l'archiviazione Premium 

È possibile eseguire il backup di macchine virtuali di Azure usando l'archiviazione Premium con Backup di Azure:

- Durante il backup di macchine virtuali con l'archiviazione Premium, il servizio Backup crea un percorso di gestione temporanea, denominato "AzureBackup-", nell'account di archiviazione. Il percorso di staging ha le stesse dimensioni dello snapshot del punto di ripristino.
- Assicurarsi che sia presente spazio libero sufficiente nell'account di archiviazione Premium per il percorso di gestione temporanea. [Altre informazioni](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets) Non modificare il percorso di gestione temporanea.
- Al termine del processo di backup, il percorso di gestione temporanea viene eliminato.
- Il prezzo della risorsa di archiviazione usata per il percorso di gestione temporanea è in linea con i [prezzi dell'archiviazione Premium](../virtual-machines/windows/premium-storage.md#pricing-and-billing).

Quando si ripristinano macchine virtuali di Azure usando l'archiviazione Premium, è possibile ripristinarle nell'archiviazione Standard o Premium. In genere si esegue il ripristino nell'archiviazione Premium, ma può risultare conveniente eseguire il ripristino nell'archiviazione Standard se è necessario solo un subset di file dalla macchina virtuale.

### <a name="backing-up-and-restoring-managed-disks"></a>Backup e ripristino dei dischi gestiti

È possibile eseguire il backup di macchine virtuali di Azure con dischi gestiti.
- Il backup di macchine virtuali con dischi gestiti funziona come per qualsiasi altra macchina virtuale di Azure. È possibile eseguire il backup della macchina virtuale direttamente dalle impostazioni della macchina virtuale oppure è possibile abilitare il backup per le macchine virtuali nell'insieme di credenziali di Servizi di ripristino.
- È possibile eseguire il backup delle macchine virtuali nei dischi gestiti tramite raccolte RestorePoint basate su dischi gestiti.
- Backup di Azure supporta anche il backup delle macchine virtuali con dischi gestiti crittografate con Crittografia dischi di Azure.

Quando si ripristinano le macchine virtuali con dischi gestiti, è possibile eseguire il ripristino in una macchina virtuale completa con dischi gestiti o in un account di archiviazione.
- Azure gestisce i dischi gestiti durante il processo di ripristino e con l'opzione dell'account di archiviazione, è possibile gestire l'account di archiviazione creato durante il ripristino.
- Se si ripristina una macchina virtuale gestita crittografata, le chiavi e i segreti della macchina virtuale devono esistere nell'insieme di credenziali delle chiavi prima di avviare il processo di ripristino.




## <a name="next-steps"></a>Passaggi successivi

- [Vedere](backup-support-matrix.md) la matrice di supporto per informazioni sulle funzionalità supportate e sulle limitazioni per gli scenari di backup.
- Configurare il backup per uno degli scenari:
    - [Eseguire un backup delle VM di Azure](backup-azure-arm-vms-prepare.md)
    - [Eseguire il backup di computer Windows direttamente](tutorial-backup-windows-server-to-azure.md), senza un server di backup.
    - [Configurare MABS](backup-azure-microsoft-azure-backup.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in tale server.
    - [Configurare DPM](backup-azure-dpm-introduction.md) per il backup in Azure e quindi eseguire il backup dei carichi di lavoro in tale server.


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

