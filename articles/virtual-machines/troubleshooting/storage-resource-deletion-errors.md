---
title: Risolvere gli errori di eliminazione delle risorse di archiviazione su macchine virtuali Linux in Azure | Microsoft Docs
description: Risoluzione dei problemi in caso di eliminazione delle risorse di archiviazione contenenti dischi rigidi virtuali collegati.
keywords: ''
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 1de70b3ddea84fc0067a0e20ec613f01024f0ed4
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50748035"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Risolvere gli errori di eliminazione delle risorse di archiviazione

In alcuni casi potrebbe verificarsi uno degli errori seguenti durante il tentativo di eliminare un account di archiviazione, un contenitore o un BLOB di Azure in una distribuzione di Azure Resource Manager:

>**Non è stato possibile eliminare l'account di archiviazione 'NomeAccountArchiviazione'. Errore: non è possibile eliminare l'account di archiviazione perché gli elementi sono in uso.**

>**Non è stato possibile eliminare # di # contenitori:<br>dischi rigidi virtuali: Sul contenitore è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta.**

>**Non è stato possibile eliminare # di # BLOB:<br>BlobName.vhd: Sul BLOB è ancora attivo un lease. Nessun ID lease è stato specificato nella richiesta.**

I dischi rigidi virtuali usati in macchine virtuali di Azure sono file con estensione vhd archiviati come BLOB di pagine in un account di archiviazione Standard o Premium in Azure. Per altre informazioni sui dischi di Azure, vedere [About unmanaged and managed disk storage for Microsoft Azure Windows VMs](../linux/about-disks-and-vhds.md) (Informazioni sull'archiviazione di dischi gestiti e non gestiti per le VM Linux di Microsoft Azure). 

Azure impedisce l'eliminazione di un disco collegato a una macchina virtuale per evitare il danneggiamento. Impedisce anche l'eliminazione di contenitori e account di archiviazione che dispongono di un BLOB di pagine collegato a una macchina virtuale. 

Il processo per eliminare un account di archiviazione, un contenitore o un BLOB quando si verifica uno di questi errori è: 
1. [Identificare i BLOB collegati a una macchina virtuale](#step-1-identify-blobs-attached-to-a-vm)
2. [Eliminare le macchine virtuali con un **disco del sistema operativo** collegato](#step-2-delete-vm-to-detach-os-disk)
3. [Scollegare tutti i **dischi dati** dalle restanti macchine virtuali](#step-3-detach-data-disk-from-the-vm)

Dopo avere completato questi passaggi, riprovare a eliminare l'account di archiviazione, il contenitore o il BLOB.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Passaggio 1: identificare il BLOB collegato a una macchina virtuale

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scenario 1: eliminazione di un BLOB - identificare la macchina virtuale collegata
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutte le risorse** dal menu Hub. Passare all'account di archiviazione, in **Servizio BLOB** selezionare **Contenitori** e andare al BLOB da eliminare.
3. Se **Stato lease** del BLOB è **Con lease** fare clic con il pulsante destro del mouse e scegliere **Modifica metadati** per aprire il riquadro Metadati BLOB. 

    ![Screenshot del portale, con evidenziati i BLOB dell'account di archiviazione e l'opzione clic con il pulsante destro del mouse > "Modifica metadati"](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. Nel riquadro Metadati BLOB controllare e prendere nota del valore di **MicrosoftAzureCompute_VMName**. Questo valore indica il nome della macchina virtuale a cui è collegato il disco rigido virtuale (vedere **Importante** se questo campo non esiste).
5. Nel riquadro Metadati BLOB controllare e prendere nota del valore di **MicrosoftAzureCompute_DiskType**. Questo valore identifica se il disco collegato è un disco dati o del sistema operativo (vedere **Importante** se questo campo non esiste). 

     ![Schermata del portale, con il riquadro "Metadati BLOB" dell'account di archiviazione aperto](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Se il tipo di disco BLOB è **OSDisk**, seguire il [Passaggio 2: eliminare la macchina virtuale per scollegare il disco del sistema operativo](#step-2-delete-vm-to-detach-os-disk). In alternativa, se il tipo di disco BLOB è **DataDisk**, seguire i passaggi in [Passaggio 3: scollegare il disco dati dalla macchina virtuale](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Se **MicrosoftAzureCompute_VMName** e **MicrosoftAzureCompute_DiskType** non vengono visualizzati nei metadati del BLOB, significa che il BLOB è esplicitamente in stato di lease e non è collegato a una macchina virtuale. I BLOB in stato di lease non possono essere eliminati se prima non si interrompe il lease. Per interrompere il lease, fare clic con il pulsante destro del mouse sul BLOB e scegliere **Interrompi lease**. I BLOB in stato di lease che non sono collegati a una macchina virtuale impediscono l'eliminazione del BLOB, ma non impediscono l'eliminazione dell'account di archiviazione o del contenitore.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scenario 2: eliminazione di un contenitore - identificare tutti i BLOB all'interno del contenitore collegati alle macchine virtuali
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutte le risorse** dal menu Hub. Passare all'account di archiviazione, in **Servizio BLOB** selezionare **Contenitori** e individuare il contenitore da eliminare.
3. Fare clic per aprire il contenitore e visualizzare l'elenco di BLOB in esso contenuti. Identificare tutti i BLOB con Tipo BLOB = **BLOB di pagine** e Stato lease = **Con lease** da questo elenco. Seguire lo [Scenario 1](#step-1-identify-blobs-attached-to-a-vm) per identificare la macchina virtuale associata a ognuno di questi BLOB.

    ![Schermata del portale, con evidenziati i BLOB dell'account di archiviazione e lo "Stato lease" con "Con lease"](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Seguire il [Passaggio 2](#step-2-delete-vm-to-detach-os-disk) e il [Passaggio 3](#step-3-detach-data-disk-from-the-vm) per eliminare le macchine virtuali con **OSDisk** e scollegare **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scenario 3: eliminazione di un account di archiviazione: identificare tutti i BLOB nell'account di archiviazione collegati alle macchine virtuali
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutte le risorse** dal menu Hub. Passare all'account di archiviazione e in **Servizio BLOB** selezionare **Blob**.
3. Nel riquadro **Contenitori** identificare tutti i contenitori in cui **Stato lease** corrisponde a **Con lease** e seguire lo [Scenario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) per ogni contenitore **Con lease**.
4. Seguire il [Passaggio 2](#step-2-delete-vm-to-detach-os-disk) e il [Passaggio 3](#step-3-detach-data-disk-from-the-vm) per eliminare le macchine virtuali con **OSDisk** e scollegare **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Passaggio 2: eliminare la macchina virtuale per scollegare il disco del sistema operativo
Se il disco rigido virtuale è un disco del sistema operativo, è necessario eliminare la macchina virtuale prima di poter eliminare il disco rigido virtuale collegato. Dopo avere completato questi passaggi, non sarà necessaria alcuna operazione aggiuntiva per i dischi dati collegati alla stessa macchina virtuale:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Dal menu Hub scegliere **Macchine virtuali**.
3. Selezionare la macchina virtuale a cui è collegato il disco rigido virtuale.
4. Assicurarsi che la macchina virtuale non sia in uso e che non sia più necessaria.
5. Nella parte superiore del riquadro **Dettagli macchina virtuale** selezionare **Elimina** e quindi fare clic su **Sì** per confermare.
6. La macchina virtuale viene eliminata, ma il disco rigido virtuale può essere mantenuto. Tuttavia, il disco rigido virtuale non deve più essere collegato a una macchina virtuale né avere un lease. Il rilascio del lease può richiedere alcuni minuti. Per verificare che il lease venga rilasciato, selezionare il percorso del BLOB e nel riquadro **Proprietà BLOB** l'opzione **Stato lease** deve essere **Disponibile**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Passaggio 3: scollegare il disco dati dalla macchina virtuale
Se il disco rigido virtuale è un disco dati, scollegarlo dalla macchina virtuale per rimuovere il lease:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Dal menu Hub scegliere **Macchine virtuali**.
3. Selezionare la macchina virtuale a cui è collegato il disco rigido virtuale.
4. Selezionare **Dischi** nel riquadro **Dettagli macchina virtuale**.
5. Selezionare il disco dati da eliminare a cui è collegato il disco rigido virtuale. È possibile determinare il BLOB collegato al disco controllando l'URL del disco rigido virtuale.
6. È possibile verificare il percorso del BLOB facendo clic sul disco per controllare il percorso nel campo **URI VHD**.
7. Selezionare **Modifica** nella parte superiore del riquadro **Dischi**.
8. Fare clic sull'**icona di scollegamento** del disco dati da eliminare.

     ![Schermata del portale, con il riquadro "Metadati BLOB" dell'account di archiviazione aperto](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Selezionare **Salva**. Il disco a questo punto è scollegato dalla macchina virtuale e non esiste più un lease per il disco rigido virtuale. Il rilascio del lease può richiedere alcuni minuti. Per verificare che il lease sia stato rilasciato, selezionare il percorso del BLOB e nel riquadro **Proprietà BLOB** il valore di **Stato lease** deve essere **Sbloccato** o **Disponibile**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

