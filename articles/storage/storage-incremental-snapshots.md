<properties
	pageTitle="Utilizzare snapshot incrementali per il backup e il ripristino di macchine virtuali di Azure | Microsoft Azure"
	description="Creare una soluzione personalizzata per il backup e il ripristino dei dischi di macchine virtuali di Azure tramite snapshot incrementali."
	services="storage"
	documentationCenter="na"
	authors="aungoo-msft"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="tamram;aungoo"/>


# Backup dei dischi delle macchine virtuali di Azure con snapshot incrementali

## Overview

Archiviazione di Azure offre la possibilità di creare snapshot di BLOB. Gli snapshot acquisiscono lo stato del BLOB in un dato momento. In questo articolo verrà descritto uno scenario che spiega come mantenere backup dei dischi delle macchine virtuali utilizzando gli snapshot. Quando si sceglie di non utilizzare Backup di Azure e il servizio di ripristino e si desidera creare una strategia di backup personalizzata per i dischi delle macchine virtuali, è possibile utilizzare questa metodologia.

I dischi delle macchine virtuali di Azure vengono archiviati come BLOB di pagine in Archiviazione di Azure. Poiché in questo articolo viene trattata la strategia di backup per i dischi delle macchine virtuali, si farà riferimento agli snapshot nel contesto dei BLOB di pagine. Per ulteriori informazioni sugli snapshot, fare riferimento a [Creazione di uno snapshot di un BLOB](https://msdn.microsoft.com/library/azure/hh488361.aspx).

## Che cos'è uno snapshot?

Uno snapshot di BLOB è una versione di sola lettura di un BLOB acquisito in un determinato momento. Una volta creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo. Gli snapshot consentono di eseguire il backup di un BLOB così com'è in un determinato momento. Fino alla versione 2015-04-05 di REST era possibile copiare snapshot completi. Con la versione 2015-07-08 di REST e le versioni successive è anche possibile copiare snapshot incrementali.

## Copia di snapshot completi

Gli snapshot possono essere copiati in un altro account di archiviazione come BLOB, per mantenere i backup del BLOB di base. È inoltre possibile copiare uno snapshot sul relativo BLOB di base, che è come ripristinare una versione precedente del BLOB. Quando uno snapshot viene copiato da un account di archiviazione a un altro, occupa lo stesso spazio del BLOB di pagine di base. Pertanto, la copia di snapshot interi da un account di archiviazione a un altro sarà un’operazione lenta e richiederà molto spazio nell'account di archiviazione di destinazione.

>[AZURE.NOTE] Se si copia il BLOB di base in un'altra destinazione, gli snapshot del BLOB non vengono copiati assieme ad esso. Analogamente, se si sovrascrive un BLOB di base con una copia, gli snapshot associati al BLOB di base non sono interessati e rimangono invariati con il nome del BLOB di base.

### Backup di dischi mediante snapshot

Come strategia di backup per i dischi delle macchine virtuali, è possibile creare periodicamente snapshot del BLOB di pagine o del disco e copiarli in un altro account di archiviazione tramite strumenti come l’operazione [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) o [AzCopy](storage-use-azcopy.md). È possibile copiare uno snapshot in un BLOB di pagine di destinazione con un nome diverso. Il BLOB di pagine di destinazione risultante è un BLOB di pagine scrivibile, non uno snapshot. In questo articolo verranno descritti i passaggi da eseguire per creare backup dei dischi delle macchine virtuali utilizzando gli snapshot.

### Ripristino di dischi mediante snapshot

Quando è il momento di ripristinare un disco a una versione stabile precedente acquisita in uno degli snapshot di backup, è possibile copiare uno snapshot sul BLOB di pagine di base. Dopo che lo snapshot viene promosso a BLOB di pagine di base, lo snapshot rimane, ma la relativa origine viene sovrascritta con una copia che è possibile leggere e scrivere. Più avanti in questo articolo vengono descritti i passaggi per ripristinare una versione precedente del disco dal relativo snapshot.

### Implementazione di una copia di snapshot completo

È possibile implementare una copia di snapshot completo effettuando le seguenti operazioni:

-   Innanzitutto, creare uno snapshot del BLOB di base utilizzando l’operazione [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Quindi, copiare lo snapshot in un account di archiviazione di destinazione mediante [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx).
-   Ripetere questo processo per mantenere copie del backup del BLOB di base.

## Copia di snapshot incrementale

La nuova funzionalità nell'API [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) rappresenta un modo migliore per eseguire il backup degli snapshot dei BLOB di pagine o dischi. L’API restituisce l’elenco delle modifiche tra il BLOB di base e gli snapshot. In questo modo si riduce la quantità di spazio di archiviazione utilizzato nell'account di backup. L'API supporta i BLOB di pagine in Archiviazione Premium e in Archiviazione Standard. Utilizzando questa API, è possibile compilare soluzioni di backup più veloci ed efficienti per le VM di Azure. Questo sarà disponibile con la versione 2015-07-08 di REST e le versioni successive.

La copia di snapshot incrementali consente di eseguire la copia da un account di archiviazione a un'altro della differenza tra,

-   BLOB di base e relativo snapshot O
-   due snapshot del BLOB di base

A condizione che siano soddisfatte le condizioni seguenti:

- Il BLOB è stato creato il 1ª gennaio 2016 o in seguito.
- Il BLOB non è stato sovrascritto con [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) o [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) tra due snapshots.


**Nota**: questa funzionalità è disponibile per i BLOB di pagine di Azure Standard e Premium.

Quando si dispone di una strategia di backup personalizzata che utilizza gli snapshot, copiare gli snapshot da un account di archiviazione a un altro può essere un’operazione molto lenta e che richiede molto spazio di archiviazione. Anziché copiare l'intero snapshot in un account di archiviazione di backup, è possibile scrivere la differenza tra snapshot consecutivi in un BLOB di pagine di backup. In questo modo, il tempo di esecuzione della copia e lo spazio di archiviazione dei backup risultano notevolmente ridotti.

### Implementazione di una copia di snapshot incrementale

È possibile implementare una copia di snapshot incrementale effettuando le seguenti operazioni,

-   Creare uno snapshot del BLOB di base usando [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx).
-   Copiare lo snapshot in un account di archiviazione di backup di destinazione mediante [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx). Questo sarà il BLOB di pagine di backup. Creare uno snapshot di questo BLOB di pagine di backup e archiviarlo in un account di backup.
-   Creare un altro snapshot del BLOB di base usando Snapshot Blob.
-   Ottenere la differenza tra il primo e il secondo snapshot del BLOB di base tramite [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx). Usare il nuovo parametro **prevsnapshot** per specificare il valore DateTime dello snapshot di cui ottenere la differenza. Quando questo parametro è presente, la risposta REST includerà solo le pagine che sono state modificate tra lo snapshot di destinazione e lo snapshot precedente, incluse le pagine non crittografate.
-   Usare [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) per applicare queste modifiche al BLOB di pagine di backup.
-   Infine, creare uno snapshot del BLOB di pagine di backup e archiviarlo nell'account di archiviazione di backup.

Nella sezione successiva, viene illustrato in dettaglio come è possibile gestire i backup dei dischi utilizzando la copia di snapshot incrementali

## Scenario

In questa sezione verrà descritto uno scenario che comporta una strategia di backup personalizzata per i dischi di macchine virtuali utilizzando gli snapshot.

Si consideri una VM Azure serie DS a cui è collegato un disco P30 di Archiviazione Premium. Il disco P30 chiamato *mypremiumdisk* viene archiviato in un account di Archiviazione Premium denominato *mypremiumaccount*. Un account di Archiviazione Standard denominato *mybackupstdaccount* verrà utilizzato per l'archiviazione del backup di *mypremiumdisk*. Si desidera mantenere uno snapshot di *mypremiumdisk* ogni 12 ore.

Per informazioni sulla creazione di account di archiviazione e dischi, fare riferimento a [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md).

Per informazioni sul backup di VM di Azure, fare riferimento a [Pianificare backup di VM di Azure](../backup/backup-azure-vms-introduction.md).

## Passaggi per gestire i backup di un disco con snapshot incrementali

I passaggi descritti di seguito consentono di creare snapshot di *mypremiumdisk* e di gestire i backup in *mybackupstdaccount*. Il backup sarà un BLOB di pagine standard denominato *mybackupstdpageblob*. Il BLOB di pagine di backup rifletterà sempre lo stesso stato dell'ultimo snapshot di *mypremiumdisk*.

1.  Innanzitutto, creare il BLOB di pagine di backup per il disco di Archiviazione Premium. A tale scopo, creare uno snapshot di *mypremiumdisk* denominato *mypremiumdisk\_ss1*.
2.  Copiare questo snapshot in mybackupstdaccount come BLOB di pagine denominato *mybackupstdpageblob*.
3.  Creare uno snapshot di *mybackupstdpageblob* denominato *mybackupstdpageblob\_ss1*, usando [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) e archiviarlo in *mybackupstdaccount*.
4.  Durante la finestra di backup, creare un altro snapshot di *mypremiumdisk*, ad esempio *mypremiumdisk\_ss2* e archiviarlo in *mypremiumaccount*.
5.  Ottenere le modifiche incrementali tra i due snapshot, *mypremiumdisk\_ss2* e *mypremiumdisk\_ss1*, usando [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) su *mypremiumdisk\_ss2* con il parametro **prevsnapshot** impostato sul timestamp di *mypremiumdisk\_ss1*. Scrivere queste modifiche incrementali nel BLOB di pagine di backup *mybackupstdpageblob* in *mybackupstdaccount*. Se sono presenti intervalli eliminati nelle modifiche incrementali, devono essere cancellati dal BLOB di pagine di backup. Usare [PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) per scrivere le modifiche incrementali nel BLOB di pagine di backup.
6.  Creare uno snapshot del BLOB di pagine di backup *mybackupstdpageblob* denominato *mybackupstdpageblob\_ss2*. Eliminare lo snapshot precedente *mypremiumdisk\_ss1* dall'account di Archiviazione Premium.
7.  Ripetere i passaggi da 4 a 6 per ogni finestra di backup. In questo modo, è possibile gestire i backup di *mypremiumdisk* in un account di Archiviazione Standard.

![Eseguire il backup dei dischi con snapshot incrementali](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

## Passaggi per ripristinare un disco da snapshot

I passaggi descritti di seguito consentono di ripristinare il disco premium *mypremiumdisk* in uno snapshot precedente dall'account di archiviazione di backup *mybackupstdaccount*.

1.  Identificare il momento a cui si desidera ripristinare il disco premium. Si supponga di archiviare lo snapshot *mybackupstdpageblob\_ss2* nell'account di archiviazione di backup *mybackupstdaccount*.
2.  In mybackupstdaccount, promuovere lo snapshot *mybackupstdpageblob\_ss2* a nuovo BLOB di pagine di base di backup *mybackupstdpageblobrestored*.
3.  Creare uno snapshot di questo BLOB di pagine di backup ripristinato, denominato *mybackupstdpageblobrestored\_ss1*.
4.  Copiare il BLOB di pagine ripristinato *mybackupstdpageblobrestored* da *mybackupstdaccount* a *mypremiumaccount* come nuovo disco premium *mypremiumdiskrestored*.
5.  Creare uno snapshot di *mypremiumdiskrestored* denominato *mypremiumdiskrestored\_ss1* per le future operazioni di backup incrementale.
6.  Puntare la VM serie DS al disco ripristinato *mypremiumdiskrestored* e scollegare il vecchio *mypremiumdisk* dalla VM.
7.  Avviare la procedura di backup descritta nella sezione precedente per il disco ripristinato *mypremiumdiskrestored*, usando *mybackupstdpageblobrestored* come BLOB di pagine di backup.

![Ripristino del disco da snapshot](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

## Passaggi successivi

Altre informazioni sulla creazione di snapshot di un BLOB e la pianificazione dell'infrastruttura di backup delle VM sono disponibili tramite i collegamenti riportati di seguito.

- [Creazione di uno snapshot di un BLOB](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [Pianificare l'infrastruttura di backup delle VM](../backup/backup-azure-vms-introduction.md)

<!---HONumber=AcomDC_0921_2016-->