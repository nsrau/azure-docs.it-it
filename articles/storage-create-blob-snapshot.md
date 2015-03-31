<properties 
	pageTitle="Creazione di uno snapshot di un BLOB" 
	description="Guida introduttiva alla creazione di snapshot dei BLOB di archiviazione di Azure" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

#Creazione di uno snapshot di un BLOB

È possibile creare uno snapshot di un BLOB. Uno snapshot è una versione di sola lettura di un BLOB eseguito in un determinato momento. Una volta creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo. Gli snapshot consentono di eseguire il backup di un BLOB così com'è in un determinato momento.

Uno snapshot di un BLOB ha lo stesso nome del BLOB di base da cui è stato eseguito, con un valore DateTime aggiunto per indicare l'ora in cui lo snapshot è stato creato. Ad esempio, se l'URI del BLOB di pagine è http://storagesample.core.blob.windows.net/mydrives/myvhd, l'URI dello snapshot sarà simile a http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z. Questo valore può essere usato per fare riferimento allo snapshot per altre operazioni. Gli snapshot di un BLOB ne condividono l'URI e si distinguono solo per il valore DateTime. Nel codice della libreria client, la proprietà Snapshot del BLOB restituisce un valore DateTime che identifica in modo univoco lo snapshot in relazione al rispettivo BLOB di base. È possibile usare questo valore per eseguire altre operazioni sullo snapshot.

Un BLOB può avere un numero qualsiasi di snapshot. Gli snapshot rimangono fino a quando non vengono eliminati in modo esplicito. Uno snapshot non può sopravvivere al relativo BLOB di origine. È possibile enumerare gli snapshot associati al BLOB per tenere traccia degli snapshot correnti.

##Ereditarietà delle proprietà

Quando si crea uno snapshot di un BLOB, le proprietà di sistema vengono copiate nello snapshot con gli stessi valori. In questo elenco sono illustrate le proprietà di sistema copiate per la libreria client di archiviazione .NET:

- ContentType 

- ContentEncoding 

- ContentLanguage

- Length

- CacheControl

- ContentMd5 


Un lease associato al BLOB di base non viene copiato nello snapshot. Non è possibile associare un lease agli snapshot.

##Copia di snapshot 

Le operazioni di copia che interessano BLOB e snapshot si attengono alle seguenti regole:

- È possibile copiare uno snapshot sul relativo BLOB di base. Promuovendo uno snapshot alla posizione del BLOB di base, è possibile ripristinare la versione precedente di un BLOB. Lo snapshot rimane, ma la relativa origine viene sovrascritta con una copia che è possibile leggere e scrivere.

- È possibile copiare uno snapshot in un BLOB di destinazione con un nome diverso. Il BLOB di destinazione risultante è un BLOB scrivibile, non uno snapshot.

- Quando si copia un BLOB di origine, gli snapshot del BLOB di origine non vengono copiati nella destinazione. Quando un BLOB di destinazione viene sovrascritto con una copia, gli snapshot associati al BLOB di destinazione rimangono invariati con il relativo nome. 

- Quando si crea uno snapshot di un BLOB in blocchi, anche l'elenco di blocchi di cui è stato eseguito il commit del BLOB viene copiato nello snapshot. Eventuali blocchi di cui non è stato eseguito il commit non vengono copiati.

##Specifica di una condizione di accesso 

È possibile specificare una condizione di accesso in modo da creare lo snapshot solo se viene soddisfatta tale condizione. Per specificare una condizione di accesso, utilizzare la proprietà AccessCondition. Se la condizione specificata non viene soddisfatta, lo snapshot non viene creato e il servizio BLOB restituisce il codice di stato HTTPStatusCode.PreconditionFailed.

##Eliminazione di snapshot 

Non è possibile eliminare un BLOB contenente snapshot a meno che non vengano eliminati anche gli snapshot. È possibile eliminare uno snapshot singolarmente o indicare al servizio di archiviazione di eliminare tutti gli snapshot quando si elimina il BLOB di origine. Se si tenta di eliminare un BLOB che presenta ancora degli snapshot, la chiamata restituirà un errore.

##Snapshot con Archiviazione Premium
Per utilizzare snapshot con Archiviazione Premium è necessario attenersi alle regole seguenti:

- Il numero di snapshot per BLOB di pagine in un account di Archiviazione Premium è limitato a 100. Se tale limite viene superato, l'operazione Snapshot BLOB restituisce il codice errore 409 (SnapshotCountExceeded).

- Uno snapshot di un BLOB di pagine in un account di Archiviazione Premium può essere adottato una volta ogni dieci minuti. Se tale frequenza viene superata, l'operazione Snapshot BLOB restituisce il codice errore 409 (SnaphotOperationRateExceeded).

- La lettura di uno snapshot di un BLOB di pagine in un account di Archiviazione Premium tramite l'operazione Get BLOB non è supportata. La chiamata Get BLOB per uno snapshot in un account di Archiviazione Premium restituisce il codice errore 400 (Operazione non valida). Tuttavia, le chiamate Get Blob Properties e Get Blob Metadata su uno snapshot sono supportate.

- Per leggere uno snapshot, è possibile utilizzare l'operazione Copy BLOB per copiare uno snapshot in un altro BLOB di pagine nell'account. Il BLOB di destinazione per l'operazione di copia non deve contenere snapshot. Se il BLOB di destinazione contiene degli snapshot, l'operazione Copy BLOB restituisce il codice errore 409 (SnapshotsPresent).

##Costruzione dell'URI assoluto di uno snapshot 

In questo esempio di codice viene costruito l'URI assoluto di uno snapshot dal relativo oggetto BLOB di base.

C#

	var snapshot = blob.CreateSnapshot();
	var uri = Microsoft.WindowsAzure.StorageClient.Protocol.BlobRequest.Get
 (snapshot.Uri, 
 0, 
 snapshot.SnapshotTime.Value, 
 null).Address.AbsoluteUri;

<!--HONumber=47-->
