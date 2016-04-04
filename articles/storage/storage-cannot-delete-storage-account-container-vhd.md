<properties
	pageTitle="Risoluzione dei problemi di eliminazione di account di archiviazione di Azure, contenitori o VHD | Microsoft Azure"
	description="Risoluzione dei problemi di eliminazione di account di archiviazione di Azure, contenitori o VHD "
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor=""
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/20/2016"
	ms.author="genli"/>

# Risoluzione dei problemi di eliminazione di account di archiviazione di Azure, contenitori o VHD

## Riepilogo
È possibile ricevere errori durante il tentativo di eliminazione di account di archiviazione di Azure, contenitori o VHD nel [portale di Azure](https://portal.azure.com/) o nel [portale di Azure classico](https://manage.windowsazure.com/). Le cause possono essere le seguenti:

-	Quando si elimina una VM, il disco e il VHD non vengono eliminati automaticamente. Questo potrebbe essere il motivo dell'errore durante l’eliminazione dell'account di archiviazione. Il disco non viene eliminato perché sia possibile utilizzarlo per montare un’altra VM.

-	Sul disco o sul BLOB associato al disco è ancora attivo un lease.

Se il problema riguardante Azure non è trattato in questo articolo, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). In questi forum o in @AzureSupport su Twitter, è possibile pubblicare il problema. È anche possibile inviare una richiesta di supporto Azure selezionando **Ottenere supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

## Risoluzione
Per risolvere i problemi più comuni, provare una delle procedure seguenti:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Selezionare **MACCHINA VIRTUALE**>**DISCHI**.

	![disk.png](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Individuare i dischi associati all’account di archiviazione, al contenitore o al VHD che si intende eliminare. Controllare il percorso del disco per trovare l'account di archiviazione, il contenitore e il VHD associati.

	![location](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Verificare che nel campo **Associato a** non siano specificate VM e quindi eliminare i dischi.

 	> [AZURE.NOTE] Se un disco è associato a una VM, non sarà possibile eliminarlo. I dischi vengono disconnessi da una VM eliminata in modo asincrono. Dopo che la VM è stata eliminata, è possibile che passino alcuni minuti prima che il campo risulti vuoto.

5. Selezionare **MACCHINA VIRTUALE**>**IMMAGINI** e quindi eliminare le immagini che sono associate all'account di archiviazione, al contenitore o al VHD.

Provare quindi a eliminare l'account di archiviazione, il contenitore o il VHD.

> [AZURE.WARNING] Assicurarsi di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account. Non è possibile ripristinare un account di archiviazione eliminato, né recuperare gli elementi che conteneva prima dell'eliminazione. Lo stesso vale per tutte le risorse nell'account. Dopo aver eliminato un VHD, un BLOB, una tabella, una coda o un file, non è più possibile recuperarlo. Assicurarsi che la risorsa non sia in uso.

## Sintomo

La sezione seguente elenca gli errori comuni che potrebbero essere visualizzati durante l’eliminazione di account di archiviazione di Azure, contenitori o VHD.

### Scenario 1. Impossibile eliminare un account di archiviazione

Quando si tenta di eliminare un account di archiviazione non più in uso selezionando l'account di archiviazione nel [portale di Azure](https://portal.azure.com/) o nel [portale di Azure classico](https://manage.windowsazure.com/) e scegliendo Elimina, è possibile che venga visualizzato il messaggio di errore seguente:

**Nel portale di Azure**:

*Non è possibile eliminare l'account di archiviazione <vm-storage-account-name>. Non è stato possibile eliminare l'account di archiviazione <vm-storage-account-name>: L'account di archiviazione <vm-storage-account-name> presenta alcune immagini e/o dischi attivi. Assicurarsi di rimuovere tali immagini e/o dischi prima di eliminare l'account di archiviazione.*

**Nel portale di Azure classico**:

*L’account di archiviazione<vm-storage-account-name> presenta alcune immagini e/o dischi attivi, ad esempio xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Assicurarsi di rimuovere tali immagini e/o dischi prima di eliminare l'account di archiviazione.*

È inoltre possibile visualizzare questo errore:

**Nel portale di Azure**:

*L'account di archiviazione<vm-storage-account-name> presenta contenitori con un'immagine attiva e/o elementi di disco. Assicurarsi che gli elementi locali siano rimossi dall'archivio immagini prima di eliminare l'account di archiviazione*.

**Nel portale di Azure classico**:

*Invio non riuscito. L'account di archiviazione<vm-storage-account-name> presenta contenitori con un'immagine attiva e/o elementi di disco. Assicurarsi che gli elementi locali siano rimossi dall'archivio immagini prima di eliminare l'account di archiviazione. Quando si tenta di eliminare un account di archiviazione con dischi associati ancora attivi, viene visualizzato un messaggio in cui si chiede di eliminare i dischi attivi*.

### Scenario 2. Impossibile eliminare un contenitore

Quando si tenta di eliminare il contenitore di archiviazione, viene visualizzato l'errore seguente:

*Non è stato possibile eliminare il contenitore di archiviazione<container name>. Errore: sul contenitore è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta*.

### Scenario 3. Impossibile eliminare un VHD

Dopo aver eliminato una VM, si tenta di eliminare i BLOB dei VHD associate. Viene visualizzato il messaggio seguente:

*Impossibile eliminare il blob ''percorso/XXXXXX-XXXXXX-os-1447379084699.vhd''. Errore: sul BLOB è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta.*

## Altre informazioni

Le VM V1 che non state conservate mantengono lo stato Arrestato (deallocato) sia nel [portale di Azure](https://portal.azure.com/) che nel [portale di Azure classico](https://manage.windowsazure.com/).

**Portale di Azure classico**:

![screenshot1](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

**Portale di Azure**:

![screenshot2](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

Lo stato Arrestato (deallocato) determina il rilascio delle risorse di calcolo, ad esempio CPU, memoria e rete. I dischi vengono invece conservati perché l’utente possa ricreare velocemente la macchina virtuale qualora fosse necessario. Questi dischi vengono su VHD supportati da Archiviazione di Azure. L’account di archiviazione dispone di questi VHD e i dischi possiedono un lease su tali VHD.

## Riferimenti

- [Eliminare un account di archiviazione](storage-create-storage-account.md#delete-a-storage-account)
- [Procedura: Interrompere il lease bloccato di archiviazione BLOB in Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0323_2016-->