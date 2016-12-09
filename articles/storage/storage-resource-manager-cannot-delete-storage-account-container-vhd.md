---
title: Risolvere gli errori quando si eliminano account di archiviazione, contenitori o dischi rigidi virtuali di Azure in una distribuzione Resource Manager | Microsoft Docs
description: Risolvere gli errori quando si eliminano account di archiviazione, contenitori o dischi rigidi virtuali di Azure in una distribuzione di Resource Manager
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75d5e82e2f4e747747f24376239e23f6512f916a


---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Risolvere gli errori quando si eliminano account di archiviazione, contenitori o dischi rigidi virtuali di Azure in una distribuzione di Resource Manager
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

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

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>Passaggio 1: Identificare il problema del disco rigido virtuale e della macchina virtuale associata
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Dal menu **Hub** scegliere **Tutte le risorse**. Passare all'account di archiviazione che si vuole eliminare e quindi selezionare **BLOB** > **dischi rigidi virtuali**.
   
    ![Schermata del portale, con l'account di archiviazione e il contenitore "vhds" evidenziato](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)
3. Controllare le proprietà di ogni disco rigido virtuale nel contenitore. Individuare il disco rigido virtuale in stato **Con lease** e quindi determinare quale macchina virtuale sta usando il disco rigido virtuale. In genere, è possibile determinare la macchina virtuale che contiene il disco rigido virtuale selezionando il nome di tale disco:
   
   * I dischi del sistema operativo in genere seguono questa convenzione di denominazione: VMNameYYYYMMDDHHMMSS.vhd
   * I dischi dati in genere seguono questa convenzione di denominazione: VMName-YYYYMMDD-HHMMSS.vhd
     
     ![Schermata delle informazioni di contenitore nel portale, con il nome della macchina virtuale e lo stato di lease "Bloccato e "Con lease" evidenziato](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>Passaggio 2: Rimuovere il lease dal disco rigido virtuale
Per eliminare la macchina virtuale che usa il disco rigido virtuale (per i dischi del sistema operativo):

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Dal menu **Hub** scegliere **Macchine virtuali**.
3. Selezionare la macchina virtuale che contiene un lease sul disco rigido virtuale.
4. Assicurarsi che la macchina virtuale non sia in uso e che non sia più necessaria.
5. Nella parte superiore del pannello **VM details** (Dettagli macchina virtuale) selezionare **Elimina** e quindi fare clic su **Sì** per confermare.
6. La macchina virtuale viene eliminata, ma il disco rigido virtuale viene mantenuto. Tuttavia, nel disco rigido virtuale non deve più essere presente un lease. Il rilascio del lease può richiedere alcuni minuti. Per verificare che il lease sia stato rilasciato, passare a **Tutte le risorse** > **nome account di archiviazione** > **BLOB** > **dischi rigidi virtuali**. Nel riquadro **Proprietà BLOB** il valore **Stato lease** deve essere **Sbloccato**.

Per scollegare il disco rigido virtuale dalla macchina virtuale che lo usa (per i dischi dati):

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Dal menu **Hub** scegliere **Macchine virtuali**.
3. Selezionare la macchina virtuale che contiene un lease sul disco rigido virtuale.
4. Selezionare **Dischi** nel pannello **VM details** (Dettagli macchina virtuale).
5. Selezionare il disco dati che contiene un lease sul disco rigido virtuale. È possibile determinare il disco rigido virtuale collegato al disco controllando l'URL del disco rigido virtuale.
6. Stabilire con certezza che il disco dati non è in uso.
7. Fare clic su **Scollega** nel pannello **Dettagli disco**.
8. Il disco deve ora essere scollegato dalla macchina virtuale e nel disco rigido virtuale non deve più essere presente un lease. Il rilascio del lease può richiedere alcuni minuti. Per verificare che il lease sia stato rilasciato, passare a **Tutte le risorse** > **nome account di archiviazione** > **BLOB** > **dischi rigidi virtuali**. Nel riquadro **Proprietà BLOB** il valore **Stato lease** deve essere **Sbloccato**.

## <a name="what-is-a-lease"></a>Che cos'è un lease?
Un lease è un blocco che può essere usato per controllare l'accesso a un BLOB (ad esempio, un disco rigido virtuale). Quando un BLOB è in stato di lease, solo i proprietari del lease possono accedere al BLOB. Un lease è importante per i motivi seguenti:

* Impedisce il danneggiamento dei dati se più proprietari tentano di scrivere nella stessa parte del BLOB nello stesso momento.
* Impedisce l'eliminazione del BLOB se in uso (ad esempio, da parte di una macchina virtuale).
* Impedisce l'eliminazione dell'account di archiviazione se in uso (ad esempio, da parte di una macchina virtuale).

## <a name="next-steps"></a>Passaggi successivi
* [Eliminare un account di archiviazione](storage-create-storage-account.md#delete-a-storage-account)
* [Procedura: Interrompere il lease bloccato di archiviazione BLOB in Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)




<!--HONumber=Nov16_HO3-->


