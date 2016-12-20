---
title: Risolvere i problemi eliminando account di archiviazione di Azure, contenitori o dischi rigidi virtuali in una distribuzione classica | Microsoft Docs
description: Risolvere i problemi eliminando account di archiviazione di Azure, contenitori o dischi rigidi virtuali in una distribuzione classica
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: tysonn
tags: storage
ms.assetid: 0f7a8243-d8dc-432a-9d37-1272a0cb3a5c
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a50ac3b7fe76dd44887fb197f685c1311d9dc04c


---
# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Risolvere i problemi eliminando account di archiviazione di Azure, contenitori o dischi rigidi virtuali in una distribuzione classica
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Durante il tentativo di eliminazione di account di Archiviazione di Azure, contenitori o VHD nel [Portale di Azure](https://portal.azure.com/) o nel [portale di Azure classico](https://manage.windowsazure.com/) è possibile che vengano visualizzati errori. I problemi possono essere causati dalle circostanze seguenti:

* Quando si elimina una VM, il disco e il VHD non vengono automaticamente eliminati. Questo potrebbe essere il motivo dell'errore di eliminazione dell'account di archiviazione. Il disco non viene eliminato perché sia possibile utilizzarlo per montare un’altra VM.
* Sul disco o sul BLOB associato al disco è ancora attivo un lease.

Se il problema riguardante Azure non è trattato in questo articolo, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in @AzureSupport su Twitter. È anche possibile inviare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Sintomi
La sezione seguente elenca alcuni errori comuni che potrebbero essere visualizzati quando si tenta di eliminare gli account di Archiviazione di Azure, contenitori o VHD.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Scenario 1: Impossibile eliminare un account di archiviazione
Quando si passa all'account di archiviazione nel [Portale di Azure](https://portal.azure.com/) o nel [portale di Azure classico](https://manage.windowsazure.com/) e si seleziona **Elimina**, potrebbe essere visualizzato il messaggio di errore seguente:

*L'account di archiviazione StorageAccountName contiene immagini di VM. Assicurarsi di rimuovere queste immagini prima di eliminare questo account di archiviazione.*

Potrebbe inoltre essere visualizzato questo errore:

**Nel Portale di Azure**:

*Non è stato possibile eliminare l'account di archiviazione <nome-account-archiviazione-vm>. Impossibile eliminare l'account di archiviazione <nome-account-archiviazione-vm>: "<nome-account-archiviazione-vm> presenta alcune immagini e/o dischi attivi. Assicurarsi di rimuovere tali immagini e/o dischi prima di eliminare l'account di archiviazione.*

**Nel Portale di Azure classico**:

*L'account di archiviazione<nome-account-archiviazione-vm> presenta alcune immagini e/o dischi attivi, ad esempio xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Assicurarsi di rimuovere tali immagini e/o dischi prima di eliminare l'account di archiviazione.*

oppure

**Nel Portale di Azure**:

*L'account di archiviazione <nome-account-archiviazione-vm> presenta 1 contenitore con un'immagine attiva e/o elementi di disco. Assicurarsi che gli elementi locali siano rimossi dall'archivio immagini prima di eliminare l'account di archiviazione*.

**Nel Portale di Azure classico**:

*Invio non riuscito L'account di archiviazione <nome-account-archiviazione-vm> presenta 1 contenitore con un'immagine attiva e/o elementi di disco. Assicurarsi che gli elementi locali siano rimossi dall'archivio immagini prima di eliminare l'account di archiviazione. Quando si tenta di eliminare un account di archiviazione con dischi associati ancora attivi, viene visualizzato un messaggio in cui si chiede di eliminare i dischi attivi*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Scenario 2. Impossibile eliminare un contenitore
Quando si tenta di eliminare il contenitore di archiviazione, potrebbe essere visualizzato l'errore seguente:

*Non è stato possibile eliminare il contenitore di archiviazione<container name>. Errore: sul contenitore è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta*.

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Scenario 3. Impossibile eliminare un VHD
Dopo l'eliminazione di una VM, se si tenta di eliminare i BLOB relativi ai VHD associati, potrebbe essere visualizzato il messaggio seguente:

*Non è stato possibile eliminare il BLOB ''percorso/XXXXXX-XXXXXX-os-1447379084699.vhd''. Errore: sul BLOB è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta.*

## <a name="solution"></a>Soluzione
Per risolvere i problemi più comuni, provare una delle procedure seguenti:

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Passaggio 1: eliminare eventuali dischi del sistema operativo che impediscono l'eliminazione dell'account di archiviazione, del contenitore o del disco rigido virtuale
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

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Passaggio 2: Eliminare eventuali immagini di VM che impediscono l'eliminazione dell'account di archiviazione o del contenitore
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Selezionare **MACCHINA VIRTUALE** > **IMMAGINI** e quindi eliminare le immagini associate all'account di archiviazione, al contenitore o al disco rigido virtuale.
   
    Provare quindi di nuovo a eliminare l'account di archiviazione, il contenitore o il VHD.

> [!WARNING]
> Assicurarsi di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account. Non è possibile ripristinare un account di archiviazione eliminato, né recuperare gli elementi che conteneva prima dell'eliminazione. Lo stesso vale per tutte le risorse nell'account: dopo aver eliminato un VHD, un BLOB, una tabella, una coda o un file, non è più possibile recuperarlo. Assicurarsi che la risorsa non sia in uso.
> 
> 

## <a name="about-the-stopped-deallocated-status"></a>Informazioni sullo stato Arrestato (deallocato)
Le VM che sono state create nel modello di distribuzione classico e che sono state mantenute avranno lo stato **Arrestato (deallocato)** nel [Portale di Azure](https://portal.azure.com/) o nel [portale di Azure classico](https://manage.windowsazure.com/).

**Portale di Azure classico**:

![Stato Arrestato (deallocato) per VM nel Portale di Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

**Portale di Azure**:

![Stato Arrestato (deallocato) per VM nel Portale di Azure classico.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Lo stato "Arrestato (deallocato)" rilascia le risorse del computer, ad esempio CPU, memoria e rete. I dischi, tuttavia, vengono mantenuti, in modo che si possa rapidamente ricreare la VM se necessario. Questi dischi vengono creati all'interno di VHD, che sono supportati da Archiviazione di Azure. L'account di archiviazione possiede questi VHD e i dischi hanno un lease su di essi.

## <a name="next-steps"></a>Passaggi successivi
* [Eliminare un account di archiviazione](storage-create-storage-account.md#delete-a-storage-account)
* [Procedura: Interrompere il lease bloccato di archiviazione BLOB in Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)




<!--HONumber=Nov16_HO3-->


