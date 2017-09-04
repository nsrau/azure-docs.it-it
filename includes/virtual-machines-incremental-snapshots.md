# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>Eseguire il backup dei dischi di VM non gestiti con snapshot incrementali
## <a name="overview"></a>Panoramica
Archiviazione di Azure offre la possibilità di creare snapshot di BLOB. Gli snapshot acquisiscono lo stato del BLOB in un dato momento. Questo articolo illustra uno scenario che permette di mantenere backup dei dischi delle macchine virtuali tramite snapshot. Quando si sceglie di non utilizzare Backup di Azure e il servizio di ripristino e si desidera creare una strategia di backup personalizzata per i dischi delle macchine virtuali, è possibile utilizzare questa metodologia.

I dischi delle macchine virtuali di Azure vengono archiviati come BLOB di pagine in Archiviazione di Azure. L'articolo descrive la strategia di backup per i dischi delle macchine virtuali e si fa riferimento agli snapshot nel contesto dei BLOB di pagine. Per ulteriori informazioni sugli snapshot, fare riferimento a [Creazione di uno snapshot di un BLOB](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>Che cos'è uno snapshot?
Uno snapshot di BLOB è una versione di sola lettura di un BLOB acquisito in un determinato momento. Una volta creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo. Gli snapshot consentono di eseguire il backup di un BLOB così com'è in un determinato momento. Fino alla versione 2015-04-05 di REST era possibile copiare snapshot completi. Con la versione 2015-07-08 di REST e le versioni successive è anche possibile copiare snapshot incrementali.

## <a name="full-snapshot-copy"></a>Copia di snapshot completi
Gli snapshot possono essere copiati in un altro account di archiviazione come BLOB, per mantenere i backup del BLOB di base. È inoltre possibile copiare uno snapshot sul relativo BLOB di base, che è come ripristinare una versione precedente del BLOB. Quando uno snapshot viene copiato da un account di archiviazione a un altro, occupa lo stesso spazio del BLOB di pagine di base. La copia di interi snapshot da un account di archiviazione a un altro è quindi un'operazione lenta e utilizza molto spazio nell'account di archiviazione di destinazione.

> [!NOTE]
> Se si copia il BLOB di base in un'altra destinazione, gli snapshot del BLOB non vengono copiati assieme ad esso. Analogamente, se si sovrascrive un BLOB di base con una copia, gli snapshot associati al BLOB di base non sono interessati e rimangono invariati con il nome del BLOB di base.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Backup di dischi mediante snapshot
Come strategia di backup per i dischi delle macchine virtuali, è possibile creare periodicamente snapshot del BLOB di pagine o del disco e copiarli in un altro account di archiviazione usando strumenti come l'operazione [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) o [AzCopy](../articles/storage/common/storage-use-azcopy.md). È possibile copiare uno snapshot in un BLOB di pagine di destinazione con un nome diverso. Il BLOB di pagine di destinazione risultante è un BLOB di pagine scrivibile, non uno snapshot. Più avanti in questo articolo viene descritta la procedura per eseguire backup dei dischi delle macchine virtuali tramite snapshot.

### <a name="restore-disks-using-snapshots"></a>Ripristino di dischi mediante snapshot
Al momento di ripristinare una versione stabile del disco precedentemente acquisita in uno degli snapshot di backup, è possibile copiare uno snapshot sul BLOB di pagine di base. Dopo che lo snapshot viene promosso a BLOB di pagine di base,  lo snapshot rimane, ma la relativa origine viene sovrascritta con una copia che è possibile leggere e scrivere. Più avanti in questo articolo viene descritta la procedura per ripristinare una versione precedente del disco dal relativo snapshot.

### <a name="implementing-full-snapshot-copy"></a>Implementazione di una copia di snapshot completo
È possibile implementare una copia di snapshot completo effettuando le seguenti operazioni:

* Innanzitutto, creare uno snapshot del BLOB di base utilizzando l’operazione [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) .
* Quindi, copiare lo snapshot in un account di archiviazione di destinazione mediante [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Ripetere questo processo per mantenere copie del backup del BLOB di base.

## <a name="incremental-snapshot-copy"></a>Copia di snapshot incrementale
La nuova funzionalità nell'API [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) offre un modo migliore per eseguire il backup degli snapshot dei BLOB di pagine o dei dischi. L'API restituisce l'elenco delle modifiche tra il BLOB di base e gli snapshot, riducendo la quantità di spazio di archiviazione usato nell'account di backup. L'API supporta i BLOB di pagine in Archiviazione Premium e in Archiviazione Standard. L'API permette di compilare soluzioni di backup più veloci ed efficienti per le macchine virtuali di Azure. L'API sarà disponibile con la versione 2015-07-08 di REST e le versioni successive.

La copia di snapshot incrementali consente di eseguire la copia da un account di archiviazione a un'altro della differenza tra,

* BLOB di base e relativo snapshot O
* due snapshot del BLOB di base

A condizione che siano soddisfatte le condizioni seguenti:

* Il BLOB è stato creato il 1° gennaio 2016 o in seguito.
* Il BLOB non è stato sovrascritto con [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) o [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) tra due snapshot.

**Nota**: questa funzionalità è disponibile per i BLOB di pagine di Azure Standard e Premium.

Quando si ha una strategia di backup personalizzata che usa gli snapshot, copiare gli snapshot da un account di archiviazione a un altro può richiedere molto tempo e utilizzare molto spazio di archiviazione. Anziché copiare l'intero snapshot in un account di archiviazione di backup, è possibile scrivere la differenza tra snapshot consecutivi in un BLOB di pagine di backup. In questo modo, il tempo di esecuzione della copia e lo spazio di archiviazione dei backup risultano notevolmente ridotti.

### <a name="implementing-incremental-snapshot-copy"></a>Implementazione di una copia di snapshot incrementale
È possibile implementare una copia di snapshot incrementale effettuando le seguenti operazioni,

* Creare uno snapshot del BLOB di base usando [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Copiare lo snapshot in un account di archiviazione di backup di destinazione mediante [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Questo è il BLOB di pagine di backup. Creare uno snapshot del BLOB di pagine di backup e archiviarlo nell'account di backup.
* Creare un altro snapshot del BLOB di base usando Snapshot Blob.
* Ottenere la differenza tra il primo e il secondo snapshot del BLOB di base usando [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Usare il nuovo parametro **prevsnapshot** per specificare il valore DateTime dello snapshot di cui ottenere la differenza. Quando questo parametro è presente, la risposta REST include solo le pagine modificate tra lo snapshot di destinazione e lo snapshot precedente, incluse quelle non crittografate.
* Usare [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) per applicare queste modifiche al BLOB di pagine di backup.
* Infine, creare uno snapshot del BLOB di pagine di backup e archiviarlo nell'account di archiviazione di backup.

Nella sezione successiva, viene illustrato in dettaglio come è possibile gestire i backup dei dischi utilizzando la copia di snapshot incrementali

## <a name="scenario"></a>Scenario
Questa sezione illustra uno scenario che comporta una strategia di backup personalizzata per i dischi delle macchine virtuali tramite gli snapshot.

Si consideri una VM Azure serie DS a cui è collegato un disco P30 di Archiviazione Premium. Il disco P30 chiamato *mypremiumdisk* viene archiviato in un account di Archiviazione Premium denominato *mypremiumaccount*. Un account di Archiviazione Standard denominato *mybackupstdaccount* viene usato per l'archiviazione del backup di *mypremiumdisk*. Si desidera mantenere uno snapshot di *mypremiumdisk* ogni 12 ore.

Per informazioni sulla creazione di account di archiviazione e dischi, fare riferimento a [Informazioni sugli account di archiviazione di Azure](../articles/storage/storage-create-storage-account.md).

Per informazioni sul backup di VM di Azure, fare riferimento a [Pianificare backup di VM di Azure](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Passaggi per gestire i backup di un disco con snapshot incrementali
La procedura seguente illustra come creare snapshot di *mypremiumdisk* e gestire i backup in *mybackupstdaccount*. Il backup è un BLOB di pagine standard denominato *mybackupstdpageblob*. Il BLOB di pagine di backup ha sempre lo stesso stato dell'ultimo snapshot di *mypremiumdisk*.

1. Creare il BLOB di pagine di backup per il disco di Archiviazione Premium. A tale scopo, creare uno snapshot di *mypremiumdisk* denominato *mypremiumdisk_ss1*.
2. Copiare questo snapshot in mybackupstdaccount come BLOB di pagine denominato *mybackupstdpageblob*.
3. Creare uno snapshot di *mybackupstdpageblob* denominato *mybackupstdpageblob_ss1* usando [Snapshot Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) e archiviarlo in *mybackupstdaccount*.
4. Durante la finestra di backup, creare un altro snapshot di *mypremiumdisk*, ad esempio *mypremiumdisk_ss2* e archiviarlo in *mypremiumaccount*.
5. Ottenere le modifiche incrementali tra i due snapshot, *mypremiumdisk_ss2* e *mypremiumdisk_ss1*, usando [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) in *mypremiumdisk_ss2* con il parametro **prevsnapshot** impostato sul timestamp di *mypremiumdisk_ss1*. Scrivere queste modifiche incrementali nel BLOB di pagine di backup *mybackupstdpageblob* in *mybackupstdaccount*. Se sono presenti intervalli eliminati nelle modifiche incrementali, devono essere cancellati dal BLOB di pagine di backup. Usare [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) per scrivere le modifiche incrementali nel BLOB di pagine di backup.
6. Creare uno snapshot del BLOB di pagine di backup *mybackupstdpageblob* denominato *mybackupstdpageblob_ss2*. Eliminare lo snapshot precedente *mypremiumdisk_ss1* dall'account di Archiviazione Premium.
7. Ripetere i passaggi da 4 a 6 per ogni finestra di backup. In questo modo, è possibile gestire i backup di *mypremiumdisk* in un account di Archiviazione Standard.

![Eseguire il backup dei dischi con snapshot incrementali](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Passaggi per ripristinare un disco da snapshot
La procedura seguente illustra come ripristinare il disco Premium *mypremiumdisk* in uno snapshot precedente dall'account di archiviazione di backup *mybackupstdaccount*.

1. Identificare il momento a cui si desidera ripristinare il disco premium. Si supponga che si tratti dello snapshot *mybackupstdpageblob_ss2* nell'account di archiviazione di backup *mybackupstdaccount*.
2. In mybackupstdaccount promuovere lo snapshot *mybackupstdpageblob_ss2* a nuovo BLOB di pagine di base di backup *mybackupstdpageblobrestored*.
3. Creare uno snapshot di questo BLOB di pagine di backup ripristinato, denominato *mybackupstdpageblobrestored_ss1*.
4. Copiare il BLOB di pagine ripristinato *mybackupstdpageblobrestored* da *mybackupstdaccount* a *mypremiumaccount* come nuovo disco premium *mypremiumdiskrestored*.
5. Creare uno snapshot di *mypremiumdiskrestored* denominato *mypremiumdiskrestored_ss1* per le future operazioni di backup incrementale.
6. Puntare la macchina virtuale serie DS al disco ripristinato *mypremiumdiskrestored* e scollegare il vecchio *mypremiumdisk* dalla macchina.
7. Avviare la procedura di backup descritta nella sezione precedente per il disco ripristinato *mypremiumdiskrestored*, usando *mybackupstdpageblobrestored* come BLOB di pagine di backup.

![Ripristino del disco da snapshot](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla creazione di snapshot di un BLOB e la pianificazione dell'infrastruttura di backup delle macchine virtuali, usare i collegamenti riportati di seguito.

* [Creazione di uno snapshot di un BLOB](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Pianificare l'infrastruttura di backup delle VM](../articles/backup/backup-azure-vms-introduction.md)

