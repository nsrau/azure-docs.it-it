---
title: Risoluzione dei problemi di eliminazione di account di Archiviazione di Azure, contenitori o VHD | Microsoft Docs
description: 'Risoluzione dei problemi di eliminazione di account di Archiviazione di Azure, contenitori o VHD '
services: storage
documentationcenter: ''
author: genlin
manager: felixwu
editor: tysonn
tags: storage

ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: minet;genli;robinsh

---
# Risoluzione dei problemi di eliminazione di account di Archiviazione di Azure, contenitori o VHD
## Riepilogo
Durante il tentativo di eliminazione di account di Archiviazione di Azure, contenitori o VHD nel [Portale di Azure](https://portal.azure.com/) o nel [Portale di Azure classico](https://manage.windowsazure.com/) è possibile ricevere errori. I problemi possono essere causati dalle circostanze seguenti:

* Quando si elimina una VM, il disco e il VHD non vengono automaticamente eliminati. Questo potrebbe essere il motivo dell'errore di eliminazione dell'account di archiviazione. Il disco non viene eliminato perché sia possibile utilizzarlo per montare un’altra VM.
* Sul disco o sul BLOB associato al disco è ancora attivo un lease.

Se l'articolo non tratta il problema riguardante Azure, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). In questi forum o in @AzureSupport su Twitter, è possibile pubblicare il problema. È anche possibile inviare una richiesta di supporto Azure selezionando **Ottenere supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

## Risoluzione
Per risolvere i problemi più comuni, provare una delle procedure seguenti:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Selezionare **MACCHINA VIRTUALE** > **DISCHI**.
   
    ![Immagine di dischi all'interno di macchine virtuali nel Portale di Azure classico.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)
3. Individuare i dischi associati all'account di archiviazione, al contenitore o al VHD che si vuole eliminare. Controllando la posizione del disco, è possibile trovare l'account di archiviazione, il contenitore e il VHD associati.
   
    ![Immagine che mostra le informazioni sulla posizione per i dischi nel Portale di Azure classico](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)
4. Verificare che nel campo **Collegato a** non siano specificate VM e quindi eliminare i dischi.
   
   > [!NOTE]
   > Se un disco è collegato a una VM, non sarà possibile eliminarlo. I dischi vengono scollegati da una VM eliminata in modo asincrono. Dopo l'eliminazione della VM la cancellazione di questo campo potrebbe richiedere alcuni minuti.
   > 
   > 
5. Selezionare **MACCHINA VIRTUALE** > **IMMAGINI** e quindi eliminare le immagini associate all'account di archiviazione, al contenitore o al VHD.
   
    Provare quindi di nuovo a eliminare l'account di archiviazione, il contenitore o il VHD.

> [!WARNING]
> Assicurarsi di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account. Non è possibile ripristinare un account di archiviazione eliminato, né recuperare gli elementi che conteneva prima dell'eliminazione. Lo stesso vale per tutte le risorse nell'account: dopo aver eliminato un VHD, un BLOB, una tabella, una coda o un file, non è più possibile recuperarlo. Assicurarsi che la risorsa non sia in uso.
> 
> 

## Problemi comuni
La sezione seguente elenca alcuni errori comuni che potrebbero essere visualizzati quando si tenta di eliminare gli account di Archiviazione di Azure, contenitori o VHD.

### Scenario 1: Impossibile eliminare un account di archiviazione
Quando si passa all'account di archiviazione nel [Portale di Azure](https://portal.azure.com/) o nel [Portale di Azure classico](https://manage.windowsazure.com/) e si seleziona **Elimina**, potrebbe essere visualizzato il messaggio di errore seguente:

**Nel Portale di Azure**:

*Non è stato possibile eliminare l'account di archiviazione <nome-account-archiviazione-vm>. Impossibile eliminare l'account di archiviazione <nome-account-archiviazione-vm>: "<nome-account-archiviazione-vm> presenta alcune immagini e/o dischi attivi. Assicurarsi di rimuovere tali immagini e/o dischi prima di eliminare l'account di archiviazione.*

**Nel Portale di Azure classico**:

*L'account di archiviazione<nome-account-archiviazione-vm> presenta alcune immagini e/o dischi attivi, ad esempio xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Assicurarsi di rimuovere tali immagini e/o dischi prima di eliminare l'account di archiviazione.*

Potrebbe inoltre essere visualizzato questo errore:

**Nel Portale di Azure**:

*L'account di archiviazione <nome-account-archiviazione-vm> presenta 1 contenitore con un'immagine attiva e/o elementi di disco. Assicurarsi che gli elementi locali siano rimossi dall'archivio immagini prima di eliminare l'account di archiviazione*.

**Nel Portale di Azure classico**:

*Invio non riuscito L'account di archiviazione <nome-account-archiviazione-vm> presenta 1 contenitore con un'immagine attiva e/o elementi di disco. Assicurarsi che gli elementi locali siano rimossi dall'archivio immagini prima di eliminare l'account di archiviazione. Quando si tenta di eliminare un account di archiviazione con dischi associati ancora attivi, viene visualizzato un messaggio in cui si chiede di eliminare i dischi attivi*.

### Scenario 2. Impossibile eliminare un contenitore
Quando si tenta di eliminare il contenitore di archiviazione, potrebbe essere visualizzato l'errore seguente:

*Non è stato possibile eliminare il contenitore di archiviazione <nome contenitore>. Errore: sul contenitore è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta*.

### Scenario 3. Impossibile eliminare un VHD
Dopo l'eliminazione di una VM, se si tenta di eliminare i BLOB relativi ai VHD associati, potrebbe essere visualizzato il messaggio seguente:

*Non è stato possibile eliminare il BLOB ''percorso/XXXXXX-XXXXXX-os-1447379084699.vhd''. Errore: sul BLOB è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta.*

## Informazioni sullo stato Arrestato (deallocato)
Le VM create nel modello di distribuzione classico che sono state mantenute avranno lo stato **Arrestato (deallocato)** nel [Portale di Azure](https://portal.azure.com/) o nel [Portale di Azure classico](https://manage.windowsazure.com/).

**Portale di Azure classico**:

![Stato Arrestato (deallocato) per VM nel Portale di Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

**Portale di Azure**:

![Stato Arrestato (deallocato) per VM nel Portale di Azure classico.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Lo stato "Arrestato (deallocato)" rilascia le risorse del computer, ad esempio CPU, memoria e rete. I dischi, tuttavia, vengono mantenuti, in modo che l'utente possa rapidamente ricreare la VM se necessario. Questi dischi vengono creati all'interno di VHD, che sono supportati da Archiviazione di Azure. L'account di archiviazione possiede questi VHD e i dischi hanno un lease su di essi.

## Passaggi successivi
* [Eliminare un account di archiviazione](storage-create-storage-account.md#delete-a-storage-account)
* [Procedura: Interrompere il lease bloccato di archiviazione BLOB in Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

<!---HONumber=AcomDC_0921_2016-->