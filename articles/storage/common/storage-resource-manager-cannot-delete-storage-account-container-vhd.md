---
title: Risolvere gli errori quando si eliminano account di archiviazione, contenitori o dischi rigidi virtuali di Azure in una distribuzione Resource Manager | Microsoft Docs
description: Risolvere gli errori quando si eliminano account di archiviazione, contenitori o dischi rigidi virtuali di Azure
services: storage
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 11944dd38b1cc30106c0b76a108480c018ca39d4
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds"></a>Risolvere gli errori quando si eliminano account di archiviazione, contenitori o dischi rigidi virtuali di Azure

Durante il tentativo di eliminazione di account di archiviazione, contenitori o dischi rigidi virtuali di Azure nel [portale di Azure](https://portal.azure.com) è possibile ricevere errori. Questo articolo fornisce indicazioni sulla risoluzione dei problemi in una distribuzione di Azure Resource Manager.

Se il problema di Azure non viene risolto in questo articolo, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in @AzureSupport su Twitter. È anche possibile inviare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Sintomi
### <a name="scenario-1"></a>Scenario 1
Quando si tenta di eliminare un disco rigido virtuale in un account di archiviazione di una distribuzione di Resource Manager, viene visualizzato un messaggio di errore simile al seguente:

**Non è stato possibile eliminare il BLOB 'vhds/BlobName.vhd'. Errore: sul BLOB è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta.**

Questo problema può verificarsi perché una macchina virtuale dispone di un lease sul disco rigido virtuale che si sta tentando di eliminare.

### <a name="scenario-2"></a>Scenario 2
Quando si tenta di eliminare un contenitore in un account di archiviazione di una distribuzione di Resource Manager, viene visualizzato un messaggio di errore simile al seguente:

**Non è stato possibile eliminare il contenitore di archiviazione 'vhds'. Errore: sul contenitore è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta.**

Questo problema può verificarsi perché il contenitore dispone di un disco rigido virtuale bloccato nello stato di lease.

### <a name="scenario-3"></a>Scenario 3
Quando si tenta di eliminare un account di archiviazione di una distribuzione di Resource Manager, viene visualizzato un messaggio di errore simile al seguente:

**Non è stato possibile eliminare l'account di archiviazione 'NomeAccountArchiviazione'. Errore: non è possibile eliminare l'account di archiviazione perché gli elementi sono in uso.**

Questo problema può verificarsi perché l'account di archiviazione dispone di un disco rigido virtuale in stato di lease.

## <a name="solution"></a>Soluzione 
Per risolvere questi problemi, è necessario identificare il disco rigido virtuale che sta causando l'errore e la macchina virtuale associata. Quindi, scollegare il disco rigido virtuale dalla macchina virtuale (per i dischi dati) o eliminare la macchina virtuale che usa il disco rigido virtuale (per i dischi del sistema operativo). In questo modo, viene rimosso il lease dal disco rigido virtuale e se ne consente l'eliminazione. 

Per farlo, usare uno dei metodi seguenti:

### <a name="method-1---use-azure-storage-explorer"></a>Metodo 1: usare Esplora archivi di Azure

### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>Passaggio 1: identificare il disco rigido virtuale che impedisce l'eliminazione dell'account di archiviazione

1. Eliminando l'account di archiviazione, si riceverà una finestra di dialogo con messaggio come la seguente: 

    ![Messaggio durante l'eliminazione dell'account di archiviazione](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. Controllare l'**URL del disco** per identificare l'account di archiviazione e il disco rigido virtuale che impedisce di eliminare l'account di archiviazione. Nell'esempio seguente la stringa che precede ".blob.core.windows.net" è il nome dell'account di archiviazione, mentre "SCCM2012-2015-08-28.vhd" è il nome del disco rigido virtuale.  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

### <a name="step-2-delete-the-vhd-by-using-azure-storage-explorer"></a>Passaggio 2: eliminare il disco rigido virtuale tramite Esplora archivi di Azure

1. Scaricare e installare la versione più recente di [Esplora archivi di Azure](http://storageexplorer.com/). Questo strumento è un'app autonoma di Microsoft che consente di gestire facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.
2. Aprire Esplora archivi di Azure, selezionare ![icona account](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/account.png) nella barra sinistra, selezionare l'ambiente di Azure e quindi eseguire l'accesso.

3. Selezionare tutte le sottoscrizioni oppure quella contenente l'account di archiviazione da eliminare.

    ![aggiungere sottoscrizione](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/addsub.png)

4. Passare all'account di archiviazione ottenuto in precedenza dall'URL del disco, selezionare i **contenitori BLOB** > **dischi rigidi virtuali** e cercare il disco rigido virtuale che impedisce di eliminare l'account di archiviazione.
5. Se viene trovato il disco rigido virtuale, controllare la colonna **Nome macchina virtuale** per trovare la macchina virtuale che sta usando tale disco rigido virtuale.

    ![Controllare la macchina virtuale](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/check-vm.png)

6. Rimuovere il lease dal disco rigido virtuale tramite il portale di Azure. Per ulteriori informazioni, vedere [Rimuovere il lease dal disco rigido virtuale](#remove-the-lease-from-the-vhd). 

7. Passare a Esplora archivi di Azure, fare clic con il tasto destro del mouse sul disco rigido virtuale e quindi selezionare Elimina.

8. Elimina l'account di archiviazione.

### <a name="method-2---use-azure-portal"></a>Metodo 2: Usare il portale di Azure 

#### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>Passaggio 1: identificare il disco rigido virtuale che impedisce l'eliminazione dell'account di archiviazione

1. Eliminando l'account di archiviazione, si riceverà una finestra di dialogo con messaggio come la seguente: 

    ![Messaggio durante l'eliminazione dell'account di archiviazione](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. Controllare l'**URL del disco** per identificare l'account di archiviazione e il disco rigido virtuale che impedisce di eliminare l'account di archiviazione. Nell'esempio seguente la stringa che precede ".blob.core.windows.net" è il nome dell'account di archiviazione, mentre "SCCM2012-2015-08-28.vhd" è il nome del disco rigido virtuale.  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

2. Accedere al [portale di Azure](https://portal.azure.com).
3. Scegliere **Tutte le risorse** dal menu Hub. Passare all'account di archiviazione e quindi selezionare **BLOB** > **dischi rigidi virtuali**.

    ![Schermata del portale, con l'account di archiviazione e il contenitore "vhds" evidenziato](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

4. Individuare il disco rigido virtuale ottenuto in precedenza dall'URL del disco. quindi determinare quale macchina virtuale sta usando il disco rigido virtuale. In genere, è possibile determinare la macchina virtuale che contiene il disco rigido virtuale selezionando il nome di tale disco:

Macchina virtuale nel modello di sviluppo di Resource Manager

   * I dischi sistema operativo in genere seguono questa convenzione di denominazione: VMName-YYYY-MM-DD-HHMMSS.vhd
   * I dischi dati in genere seguono questa convenzione di denominazione: VMName-YYYY-MM-DD-HHMMSS.vhd

Macchina virtuale nel modello di sviluppo classico

   * I dischi sistema operativo in genere seguono questa convenzione di denominazione: CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd
   * I dischi dati in genere seguono questa convenzione di denominazione: CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd

#### <a name="step-2-remove-the-lease-from-the-vhd"></a>Passaggio 2: Rimuovere il lease dal disco rigido virtuale

[Rimuovere il lease dal disco rigido virtuale](#remove-the-lease-from-the-vhd), quindi eliminare l'account di archiviazione.

## <a name="what-is-a-lease"></a>Che cos'è un lease?
Un lease è un blocco che può essere usato per controllare l'accesso a un BLOB (ad esempio, un disco rigido virtuale). Quando un BLOB è in stato di lease, solo i proprietari del lease possono accedere al BLOB. Un lease è importante per i motivi seguenti:

* Impedisce il danneggiamento dei dati se più proprietari tentano di scrivere nella stessa parte del BLOB nello stesso momento.
* Impedisce l'eliminazione del BLOB se in uso (ad esempio, da parte di una macchina virtuale).
* Impedisce l'eliminazione dell'account di archiviazione se in uso (ad esempio, da parte di una macchina virtuale).

### <a name="remove-the-lease-from-the-vhd"></a>Rimuovere il lease dal disco rigido virtuale
Se il disco rigido virtuale è un disco sistema operativo, è necessario eliminare la macchina virtuale per rimuovere il lease:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Dal menu **Hub** scegliere **Macchine virtuali**.
3. Selezionare la macchina virtuale che contiene un lease sul disco rigido virtuale.
4. Assicurarsi che la macchina virtuale non sia in uso e che non sia più necessaria.
5. Nella parte superiore del pannello **VM details** (Dettagli macchina virtuale) selezionare **Elimina** e quindi fare clic su **Sì** per confermare.
6. La macchina virtuale viene eliminata, ma il disco rigido virtuale può essere mantenuto. Tuttavia, nel disco rigido virtuale non deve più essere presente un lease. Il rilascio del lease può richiedere alcuni minuti. Per verificare che il lease sia stato rilasciato, passare a **Tutte le risorse** > **nome account di archiviazione** > **BLOB** > **dischi rigidi virtuali**. Nel riquadro **Proprietà BLOB** il valore **Stato lease** deve essere **Sbloccato**.

Se il disco rigido virtuale è un disco dati, scollegarlo dalla macchina virtuale per rimuovere il lease:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Dal menu **Hub** scegliere **Macchine virtuali**.
3. Selezionare la macchina virtuale che contiene un lease sul disco rigido virtuale.
4. Selezionare **Dischi** nel pannello **VM details** (Dettagli macchina virtuale).
5. Selezionare il disco dati che contiene un lease sul disco rigido virtuale. È possibile determinare il disco rigido virtuale collegato al disco controllando l'URL del disco rigido virtuale.
6. Stabilire con certezza che il disco dati non è in uso.
7. Fare clic su **Scollega** nel pannello **Dettagli disco**.
8. Il disco deve ora essere scollegato dalla macchina virtuale e nel disco rigido virtuale non deve più essere presente un lease. Il rilascio del lease può richiedere alcuni minuti. Per verificare che il lease sia stato rilasciato, passare a **Tutte le risorse** > **nome account di archiviazione** > **BLOB** > **dischi rigidi virtuali**. Nel riquadro **Proprietà BLOB** il valore **Stato lease** deve essere **Sbloccato**.

## <a name="next-steps"></a>Passaggi successivi
* [Eliminare un account di archiviazione](storage-create-storage-account.md#delete-a-storage-account)
* [Procedura: Interrompere il lease bloccato di archiviazione BLOB in Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

