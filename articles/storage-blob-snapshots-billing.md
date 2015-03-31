<properties 
	pageTitle="Informazioni sull'aumento delle spese causato dagli snapshot" 
	description="Guida alla conoscenza della fatturazione degli snapshot BLOB di Azure" 
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

#Informazioni sull'aumento delle spese causato dagli snapshot

La creazione di uno snapshot, una copia di sola lettura di un BLOB, può dar luogo a costi di archiviazione dei dati aggiuntivi sul conto. Nel progettare l'applicazione, è importante essere a conoscenza di come i costi possono aumentare, in modo da poter ridurre al minimo i costi inutili.

##Considerazioni importanti sulla fatturazione

Nell'elenco seguente sono inclusi i punti principali da considerare quando si crea uno snapshot.

- Sono previsti dei costi per blocchi univoci o pagine, che si trovino nel BLOB o nello snapshot. Non sono previsti costi aggiuntivi per gli snapshot associati a un BLOB finché il BLOB su cui si basano non viene aggiornato. Una volta aggiornato il BLOB di base, questo differisce dai relativi snapshot e all'utente vengono addebitati i blocchi univoci o le pagine di ogni BLOB o snapshot.

- Quando si sostituisce un blocco all'interno di un BLOB di blocchi, tale blocco viene successivamente addebitato come blocco univoco. Ciò è vero persino se il blocco ha lo stesso ID blocco e la stessa data che ha nello snapshot. Una volta che il blocco viene inviato nuovamente, differisce dalla controparte in ogni snapshot e all'utente verrà addebitato un costo per i relativi dati. Lo stesso rimane vero per una pagina in un BLOB di pagine che viene aggiornata con dati identici.

- Se si sostituisce un BLOB di blocchi richiamando il metodo UploadFile, UploadText, UploadStream o UploadByteArray vengono sostituiti tutti i blocchi di quel BLOB. Se si dispone di uno snapshot associato al BLOB, tutti i blocchi del BLOB di base e lo snapshot ora differiranno e all'utente verranno addebitati i costi di tutti i blocchi in entrambi i BLOB. Questo vale persino se i dati nel BLOB di base e nello snapshot restano identici.

- Il servizio BLOB di Azure non dispone dei mezzi per determinare se due blocchi contengono dati identici. Ogni blocco che viene caricato e inviato viene trattato come univoco, persino se contiene gli stessi dati e ha lo stesso ID blocco. Poiché i costi aumentano per i blocchi univoci, è importante considerare che se si aggiorna un BLOB che contiene uno snapshot si genereranno altri blocchi univoci e e costi aggiuntivi.

> [AZURE.NOTE] Le procedure consigliate indicano di gestire gli snapshot con attenzione per evitare costi supplementari. È consigliabile gestire gli snapshot nel modo descritto di seguito:

> - Eliminare e ricreare gli snapshot associati a un BLOB ogni volta che si aggiorna il BLOB, persino se l'aggiornamento viene eseguito con dati identici, a meno che la progettazione dell'applicazione non richieda di mantenerli. Eliminando e ricreando gli snapshot del BLOB è possibile essere sicuri che il BLOB e gli snapshot non differiscano.

> - Se si stanno gestendo gli snapshot di un BLOB, evitare di chiamare UploadFile, UploadText, UploadStream, o UploadByteArray per aggiornare il BLOB, in quanto questi metodi sostituiscono tutti i blocchi del BLOB. Aggiornare, invece, il minor numero possibile di blocchi utilizzando i metodi PutBlock e PutBlockList.


##Scenari di fatturazione degli snapshot


Gli scenari seguenti dimostrano in che modo aumentano i costi per un BLOB di blocchi e i relativi snapshot. 

Nello Scenario 1, il BLOB di base non è stato aggiornato da quanto è stato scattato lo snapshot, pertanto i costi sono relativi sono ai blocchi univoci 1, 2 e 3.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-1.png)

Nello Scenario 2, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato aggiornato, e sebbene contenga gli stessi dati e lo stesso ID, non è lo stesso del blocco 3 nello snapshot. Di conseguenza, all'account vengono addebitati quattro blocchi.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-2.png)

Nello Scenario 3, il BLOB di base è stato aggiornato, ma lo snapshot no. Il blocco 3 è stato sostituito con il blocco 4 nel BLOB di base, ma lo snapshot continua a riflettere il blocco 3. Di conseguenza, all'account vengono addebitati quattro blocchi.
 
![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-3.png)

Nello Scenario 4, il BLOB di base è stato completamente aggiornato e non contiene nessuno dei blocchi originali. Di conseguenza, all'account vengono addebitati tutti gli otto blocchi univoci. Questo scenario può verificarsi se si sta utilizzando un metodo di aggiornamento come UploadFile, UploadText, UploadFromStream o UploadByteArray, in quanto questi metodi sostituiscono tutto il contenuto di un BLOB.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-4.png)
<!--HONumber=47-->
