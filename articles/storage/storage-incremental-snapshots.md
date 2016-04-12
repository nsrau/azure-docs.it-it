<properties
	pageTitle="Utilizzare snapshot incrementali per il backup e il ripristino di macchine virtuali di Azure | Microsoft Azure"
	description="Creare una soluzione personalizzata per il backup e il ripristino dei dischi di macchine virtuali di Azure tramite snapshot incrementali."
	services="storage"
	documentationCenter="na"
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="tamram;prkhad"/>


# Backup dei dischi delle macchine virtuali di Azure con snapshot incrementali

## Panoramica 

Archiviazione di Azure offre la possibilità di creare snapshot di BLOB. Gli snapshot acquisiscono lo stato del BLOB in un dato momento. In questo articolo verrà descritto uno scenario che spiega come mantenere backup dei dischi delle macchine virtuali utilizzando gli snapshot. Quando si sceglie di non utilizzare Azure Backup and Recovery Service e si desidera creare una strategia di backup personalizzata per i dischi delle macchine virtuali, è possibile utilizzare questa metodologia.

I dischi delle macchine virtuali di Azure vengono archiviati come BLOB di pagine in Archiviazione di Azure. Poiché in questo articolo viene trattata la strategia di backup per i dischi delle macchine virtuali, si farà riferimento agli snapshot nel contesto dei BLOB di pagine. Per ulteriori informazioni sugli snapshot, fare riferimento a [Creazione di uno snapshot di un Blob](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## Che cos'è uno snapshot?

Uno snapshot di BLOB è una versione di sola lettura di un BLOB creato in un determinato momento. Una volta creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo. Gli snapshot consentono di eseguire il backup di un BLOB così com'è in un determinato momento. Fino alla versione XX di REST era possibile copiare snapshot completi. Con la versione REST YY e versioni successive, è possibile copiare snapshot incrementali.

## Copia di snapshot completi

Gli snapshot possono essere copiati in un altro account di archiviazione come BLOB, per mantenere i backup del BLOB di base. È inoltre possibile copiare uno snapshot sul relativo BLOB di base, che è come ripristinare una versione precedente del BLOB. Quando uno snapshot viene copiato da un account di archiviazione a un altro, occupa lo stesso spazio del BLOB di pagine di base nell'account di archiviazione di destinazione. Pertanto, la copia di snapshot interi da un account di archiviazione a un altro sarà un’operazione lenta e richiederà molto spazio nell'account di archiviazione di destinazione.

>[AZURE.NOTE] Se si copia il BLOB di base in un'altra destinazione, gli snapshot del BLOB non vengono copiati assieme ad esso. Analogamente, se si sovrascrive un BLOB di base con una copia, gli snapshot associati al BLOB di base non sono interessati e rimangono invariati con il nome del BLOB di base.

### Backup di dischi mediante snapshot

Come strategia di backup per i dischi delle macchine virtuali, è possibile creare periodicamente snapshot del BLOB di pagine o del disco e copiarli in un altro account di archiviazione tramite strumenti come l’operazione [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) o [AzCopy](storage-use-azcopy.md). È possibile copiare uno snapshot in un BLOB di pagine di destinazione con un nome diverso. Il BLOB di pagine di destinazione risultante è un BLOB di pagine scrivibile, non uno snapshot. In questo articolo verranno descritti i passaggi da eseguire per creare backup dei dischi delle macchine virtuali utilizzando gli snapshot.

### Ripristino di dischi mediante snapshot

Quando è il momento di ripristinare un disco a una versione stabile precedente acquisita in uno degli snapshot di backup, è possibile copiare uno snapshot sul BLOB di pagine di base. Dopo che lo snapshot viene promosso a BLOB di pagine di base, lo snapshot rimane, ma la relativa origine viene sovrascritta con una copia che è possibile leggere e scrivere. Più avanti in questo articolo vengono descritti i passaggi per ripristinare una versione precedente del disco dal relativo snapshot.

### Implementazione di una copia di snapshot completo

È possibile implementare una copia di snapshot completo effettuando le seguenti operazioni,

-   Innanzitutto, creare uno snapshot del BLOB di base utilizzando l’operazione [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).

-   Quindi, copiare lo snapshot in un account di archiviazione di destinazione mediante [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).

&lt; Frammenti di codice che mostrano create snapshot e copy blob &gt;

## Copia di snapshot incrementale

La nuova API Diff Page Ranges fornisce un modo migliore per eseguire il backup degli snapshot dei BLOB di pagine o dei dischi. L’API restituisce l’elenco delle modifiche tra il BLOB di base e gli snapshot. In questo modo si riduce la quantità di spazio di archiviazione utilizzato nell'account di backup. L'API supporta i BLOB di pagine in Archiviazione Premium e in Archiviazione Standard. Utilizzando questa API, è possibile compilare soluzioni di backup più veloci ed efficienti per le VM di Azure. Questo sarà disponibile con la versione xx di REST e le versioni successive.

La copia di snapshot incrementali consente di eseguire la copia da un account di archiviazione a un'altro della differenza tra,

-   BLOB di base e relativo snapshot O

-   due snapshot del BLOB di base

**Nota**: questa funzionalità è disponibile per i BLOB di pagine di Azure Standard e Premium.

Quando si dispone di una strategia di backup personalizzata che utilizza gli snapshot, copiare gli snapshot da un account di archiviazione a un altro può essere un’operazione molto lenta e che richiede molto spazio di archiviazione. Anziché copiare l'intero snapshot in un account di archiviazione di backup, è possibile scrivere la differenza in un BLOB di pagine di backup. In questo modo, il tempo di esecuzione della copia e lo spazio di archiviazione dei backup risultano notevolmente ridotti.

### Implementazione di una copia di snapshot incrementale

È possibile implementare una copia di snapshot incrementale effettuando le seguenti operazioni,

-   Creare uno snapshot del BLOB di base utilizzando [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).

-   Copiare il BLOB di base nell’account di archiviazione di destinazione mediante [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).

-   Ottenere la differenza tra il BLOB di base e lo snapshot utilizzando [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) per il BLOB di base. Utilizzare il nuovo parametro **prevsnapshot** per specificare il valore DateTime dello snapshot di cui ottenere la differenza. Quando questo parametro è presente, la risposta REST includerà solo le pagine che sono state modificate tra il BLOB di destinazione e lo snapshot precedente, incluse le pagine non crittografate.

-   Infine, utilizzare [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) per applicare queste modifiche al BLOB di pagine di backup.

&lt; Frammenti di codice che mostrano get page ranges e put page &gt;

Nella sezione successiva, viene illustrato in dettaglio come è possibile gestire i backup dei dischi utilizzando la copia di snapshot incrementali

## Scenario

In questa sezione verrà descritto uno scenario che comporta una strategia di backup personalizzata per i dischi di macchine virtuali utilizzando gli snapshot.

Si consideri una VM Azure serie DS a cui è collegato un disco P30 di Archiviazione Premium. Il disco P30 chiamato mypremiumdisk viene archiviato in un account di Archiviazione Premium denominato mypremiumaccount. Un account di archiviazione standard denominato mybackupstdaccount verrà utilizzato per l'archiviazione del backup di mypremiumdisk. Desideriamo mantenere uno snapshot di mypremiumdisk ogni 12 ore.

Per ulteriori informazioni sulla creazione di account di archiviazione e dischi, fare riferimento a [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md).

Per ulteriori informazioni sul backup di VM di Azure, fare riferimento a [Pianificare backup di VM di Azure](../backup/backup-azure-vms-introduction.md).

## Passaggi per gestire i backup di un disco con snapshot incrementali

I passaggi descritti di seguito consentono di creare snapshot di mypremiumdisk e di gestire i backup in mybackupstdaccount. Il backup sarà un BLOB di pagine standard denominato mybackupstdpageblob, che in qualsiasi momento rifletterà lo stesso stato dell'ultimo snapshot di mypremiumdisk.

1.  Creare una copia del disco di archiviazione premium nell'account di archiviazione standard di backup. Copiare mypremiumdisk da mypremiumaccount a mybackupstdaccount come nuovo BLOB di pagine chiamato mybackupstdpageblob.

2.  Creare uno snapshot di mybackupstdpageblob chiamato mybackupstdpageblob\_ss1, utilizzando Snapshot Blob e archiviarlo in mybackupstdaccount.

3.  Durante la finestra di backup, creare uno snapshot di mypremiumdisk, ad esempio mypremiumdisk\_ss1 e archiviarlo in mypremiumaccount.

4.  Ottenere le modifiche incrementali tra mypremiumdisk e il relativo primo snapshot mypremiumdisk\_ss1 utilizzando GetPageRanges. Scrivere queste modifiche incrementali nel BLOB di pagine di backup mybackupstdpageblob in mybackupstdaccount. Se sono presenti intervalli eliminati nelle modifiche incrementali, devono essere cancellati dal BLOB di pagine di backup. Utilizzare Put Page per scrivere le modifiche incrementali nel BLOB di pagine di backup.

5.  Creare uno snapshot del BLOB di pagine di backup mybackupstdpageblob chiamato mybackupstdpageblob\_ss2 archiviarlo in mybackupstdaccount. Eliminare lo snapshot dell’account di Archiviazione Premium mypremiumdisk\_ss1 .

6.  Ripetere i passaggi da 3 a 5 per ogni finestra di backup. In questo modo, è possibile gestire i backup di mypremiumdisk in un account di Archiviazione Standard.

![Disco di backup mediante snapshot incrementali](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

&lt; Frammento di codice che mostra Copy Blob, Get Page Ranges, Put Page &gt;

## Passaggi per ripristinare un disco da snapshot

I passaggi descritti di seguito consentono di ripristinare il disco premium mypremiumdisk a uno snapshot precedente dall’account di archiviazione di backup mybackupstdaccount.

1.  Identificare il momento a cui si desidera ripristinare il disco premium. Si supponga che sia lo snapshot mybackupstdpageblob\_ss1, che viene archiviato nell’account di archiviazione di backup mybackupstdaccount.

2.  In mybackupstdaccount, promuovere lo snapshot mybackupstdpageblob\_ss1 come il nuovo BLOB di pagine di base di backup mybackupstdpageblobrestored.

3.  Copiare il BLOB di pagine di base di backup mybackupstdpageblobrestored da mybackupstdaccount a mypremiumaccount come il nuovo disco premium mypremiumdiskrestored.

4.  Puntare la VM serie DS al disco ripristinato mypremiumdiskrestored e scollegare il vecchio mypremiumdisk dalla VM.

5.  Avviare la procedura di backup descritta nella sezione precedente per il disco ripristinato mypremiumdiskrestored, utilizzando mybackupstdpageblobrestored come BLOB di pagine di backup.

![Ripristino del disco da snapshot](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

&lt; Frammento di codice che mostra Copy Blob, Attach disk e detach disk &gt;

## Vedere anche

- [Creazione di uno snapshot di un BLOB](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Pianificare l'infrastruttura di backup delle VM](../backup/backup-azure-vms-introduction.md)

<!---HONumber=AcomDC_0330_2016-->