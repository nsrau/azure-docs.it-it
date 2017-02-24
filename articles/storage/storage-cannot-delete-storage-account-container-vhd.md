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
ms.date: 02/08/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: d3d59e19ff654a953be43706dce926c4450c6179
ms.openlocfilehash: 6493230295dbfc939df3daf8504a7d8662083f51


---
# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Risolvere i problemi eliminando account di archiviazione di Azure, contenitori o dischi rigidi virtuali in una distribuzione classica
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

Durante il tentativo di eliminazione di account di Archiviazione di Azure, contenitori o VHD nel [Portale di Azure](https://portal.azure.com/) o nel [portale di Azure classico](https://manage.windowsazure.com/) è possibile che vengano visualizzati errori. I problemi possono essere causati dalle circostanze seguenti:

* Quando si elimina una VM, il disco e il VHD non vengono automaticamente eliminati. Questo potrebbe essere il motivo dell'errore di eliminazione dell'account di archiviazione. Il disco non viene eliminato perché sia possibile utilizzarlo per montare un’altra VM.
* Sul disco o sul BLOB associato al disco è ancora attivo un lease.
* È ancora presente un'immagine di VM che usa un BLOB, un contenitore o un account di archiviazione.

Se il problema riguardante Azure non è trattato in questo articolo, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in @AzureSupport su Twitter. È anche possibile inviare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Sintomi
La sezione seguente elenca alcuni errori comuni che potrebbero essere visualizzati quando si tenta di eliminare gli account di Archiviazione di Azure, contenitori o VHD.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Scenario 1: Impossibile eliminare un account di archiviazione
Quando si passa all'account di archiviazione classico del [portale Azure](https://portal.azure.com/) e si seleziona **Elimina**, è possibile che venga visualizzato un elenco di oggetti che impediscono l'eliminazione dell'account di archiviazione:

  ![Immagine dell'errore in fase di eliminazione dell'account di archiviazione](./media/storage-cannot-delete-storage-account-container-vhd/newerror.png)

Quando si passa all'account di archiviazione nel [portale di Azure classico](https://manage.windowsazure.com/) e si seleziona **Delete**, potrebbe essere visualizzato uno dei messaggi di errore seguenti:

- *L'account di archiviazione StorageAccountName contiene immagini di VM. Assicurarsi di rimuovere queste immagini prima di eliminare questo account di archiviazione.*

- *Non è stato possibile eliminare l'account di archiviazione <nome-account-archiviazione-vm>. Impossibile eliminare l'account di archiviazione <nome-account-archiviazione-vm>: "<nome-account-archiviazione-vm> presenta alcune immagini e/o dischi attivi. Assicurarsi di rimuovere tali immagini e/o dischi prima di eliminare l'account di archiviazione.*

- *L'account di archiviazione<nome-account-archiviazione-vm> presenta alcune immagini e/o dischi attivi, ad esempio xxxxxxxxx- xxxxxxxxx-O-209490240936090599. Assicurarsi di rimuovere tali immagini e/o dischi prima di eliminare l'account di archiviazione.*

- *L'account di archiviazione <nome-account-archiviazione-vm> presenta 1 contenitore con un'immagine attiva e/o elementi di disco. Assicurarsi che gli elementi locali siano rimossi dall'archivio immagini prima di eliminare l'account di archiviazione*.

- *Invio non riuscito L'account di archiviazione <nome-account-archiviazione-vm> presenta 1 contenitore con un'immagine attiva e/o elementi di disco. Assicurarsi che gli elementi locali siano rimossi dall'archivio immagini prima di eliminare l'account di archiviazione. Quando si tenta di eliminare un account di archiviazione con dischi associati ancora attivi, viene visualizzato un messaggio in cui si chiede di eliminare i dischi attivi*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Scenario 2. Impossibile eliminare un contenitore
Quando si tenta di eliminare il contenitore di archiviazione, potrebbe essere visualizzato l'errore seguente:

*Non è stato possibile eliminare il contenitore di archiviazione<container name>. Errore: sul contenitore è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta*.

Oppure

*I dischi di macchina virtuale seguenti usano BLOB in questo contenitore, pertanto il contenitore non può essere eliminato: VirtualMachineDiskName1, VirtualMachineDiskName2, ...*

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Scenario 3. Impossibile eliminare un VHD
Dopo l'eliminazione di una VM, se si tenta di eliminare i BLOB relativi ai VHD associati, potrebbe essere visualizzato il messaggio seguente:

*Non è stato possibile eliminare il BLOB ''percorso/XXXXXX-XXXXXX-os-1447379084699.vhd''. Errore: sul BLOB è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta.*

Oppure

I*l BLOB 'BlobName.vhd' è in uso come disco di macchina virtuale 'VirtualMachineDiskName', pertanto non può essere eliminato.*

## <a name="solution"></a>Soluzione
Per risolvere i problemi più comuni, provare una delle procedure seguenti:

### <a name="step-1-delete-any-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Passaggio 1: eliminare eventuali dischi che impediscono l'eliminazione dell'account di archiviazione, del contenitore o del disco rigido virtuale
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Selezionare **MACCHINA VIRTUALE** > **DISCHI**.

    ![Immagine di dischi all'interno di macchine virtuali nel Portale di Azure classico.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)
3. Individuare i dischi associati all'account di archiviazione, al contenitore o al VHD che si vuole eliminare. Controllando la posizione del disco, è possibile trovare l'account di archiviazione, il contenitore e il VHD associati.

    ![Immagine che mostra le informazioni sulla posizione per i dischi nel Portale di Azure classico](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)
4. Eliminare i dischi usando uno dei metodi seguenti:

  - Se nel campo **Collegato a** del disco non sono elencate VM, è possibile eliminare il disco direttamente.

  - Nel caso di un disco dati, seguire questa procedura:

    1. Controllare il nome della VM a cui è collegato il disco.
    2. Passare a **Macchine virtuali** > **Istanze** e individuare la VM.
    3. Assicurarsi che nessuno stia usando il disco.
    4. Selezionare **Scollega disco** nella parte inferiore del portale per scollegare il disco.
    5. Passare a **Macchine virtuali** > **Dischi** e attendere che il campo **Collegato a** diventi vuoto. Ciò indica che il disco è stato scollegato correttamente dalla VM.
    6. Selezionare **Elimina** in basso in **Macchine virtuali** > **Dischi** per eliminare il disco.

  - Se il disco è un disco del sistema operativo (il valore del campo **Contiene sistema operativo** è Windows o simile) ed è collegato a una VM, seguire questa procedura per eliminare la VM. Il disco del sistema operativo non può essere scollegato, quindi è necessario eliminare la VM per rilasciare il lease.

    1. Controllare il nome della macchina virtuale a cui è collegato il disco dati.  
    2. Passare a **Macchine virtuali** > **Istanze** e quindi selezionare la VM a cui è collegato il disco.
    3. Assicurarsi che la macchina virtuale non sia in uso e che non sia più necessaria.
    4. Selezionare la VM a cui è collegato il disco e quindi selezionare **Elimina** > **Elimina dischi collegati**.
    5. Passare a **Macchine virtuali** > **Dischi** e attendere che il disco scompaia.  L'operazione potrebbe impiegare qualche minuto e potrebbe essere necessario aggiornare la pagina.
    6. Se il disco non scompare, attendere che il campo **Collegato a** diventi vuoto. Ciò indica che il disco è stato scollegato completamente dalla VM.  Selezionare quindi il disco e poi **Elimina** nella parte inferiore della pagina per eliminare il disco.


   > [!NOTE]
   > Se un disco è collegato a una VM, non sarà possibile eliminarlo. I dischi vengono scollegati da una VM eliminata in modo asincrono. Dopo l'eliminazione della VM la cancellazione di questo campo potrebbe richiedere alcuni minuti.
   >
   >


### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Passaggio 2: Eliminare eventuali immagini di VM che impediscono l'eliminazione dell'account di archiviazione o del contenitore
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/).
2. Selezionare **MACCHINA VIRTUALE** > **IMMAGINI** e quindi eliminare le immagini associate all'account di archiviazione, al contenitore o al disco rigido virtuale.

    Provare quindi di nuovo a eliminare l'account di archiviazione, il contenitore o il VHD.

> [!WARNING]
> Assicurarsi di eseguire il backup di tutti gli elementi da salvare prima di eliminare l'account. Quando si elimina un disco rigido virtuale, un BLOB, una tabella, una coda o un file, l'elemento viene eliminato definitivamente. Assicurarsi che la risorsa non sia in uso.
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



<!--HONumber=Feb17_HO2-->


