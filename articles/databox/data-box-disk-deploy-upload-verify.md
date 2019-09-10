---
title: "Esercitazione: Verificare il caricamento dei dati da Azure Data Box Disk all'account di archiviazione | Microsoft Docs"
description: Usare questa esercitazione per informazioni su come verificare i dati caricati da Azure Data Box Disk all'account di archiviazione di Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/04/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: fc3145ee0b60402026389863b94d21da4b3e4123
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70307770"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Esercitazione: Verificare il caricamento dei dati da Azure Data Box Disk

Questa è l'ultima esercitazione della serie: Distribuire Azure Data Box Disk. In questa esercitazione si approfondiranno i seguenti argomenti:

> [!div class="checklist"]
> * Verificare il caricamento dei dati in Azure
> * Cancellazione dei dati dai dischi di Data Box

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di aver completato l'[Esercitazione: Restituire Azure Data Box Disk](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

Dopo il ritiro dei dischi da parte del vettore, lo stato dell'ordine nel portale diventa **Ritirato**. Viene visualizzato anche un ID di traccia.

![Dischi prelevati](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Quando Microsoft riceve e analizza i dischi, lo stato del processo diventa **Ricevuto**. 

![Dischi ricevuti](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

I dati vengono copiati automaticamente quando i dischi vengono collegati a un server nel data center di Azure. A seconda delle dimensioni dei dati, l'operazione di copia potrebbe richiedere da alcune ore a giorni per il completamento. È possibile monitorare lo stato di avanzamento del processo di copia nel portale.

Al termine della copia, lo stato dell'ordine diventa **Completato**.

![Copia dei dati completata](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Se la copia viene completata con errori, vedere [Risolvere gli errori di caricamento](data-box-disk-troubleshoot-upload.md).

Verificare la presenza dei dati negli account di archiviazione prima di eliminarli dall'origine. I dati possono trovarsi in:

- Account di archiviazione di Azure. Quando si copiano i dati in Data Box, a seconda del tipo, i dati vengono caricati in uno dei percorsi seguenti nell'account di archiviazione di Azure.

  - Per i BLOB in blocchi e i BLOB di pagine: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Per File di Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    In alternativa, è possibile passare all'account di archiviazione di Azure nel portale di Azure e procedere da questa posizione.

- Gruppi di risorse di dischi gestiti. Quando si creano dischi gestiti, i dischi rigidi virtuali vengono caricati come BLOB di pagine e quindi convertiti in dischi gestiti. I dischi gestiti vengono associati ai gruppi di risorse specificati al momento della creazione dell'ordine.

  - Se la copia nei dischi gestiti in Azure ha avuto esito positivo, è possibile passare a **Dettagli ordine** nel portale di Azure e prendere nota del gruppo di risorse specificato per i dischi gestiti.

      ![Visualizzare i dettagli dell'ordine](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Passare al gruppo di risorse annotato e individuare i dischi gestiti.

      ![Gruppo di risorse per i dischi gestiti](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Se è stato copiato un file VHDX oppure un disco rigido virtuale differenze o dinamico, questo verrà caricato nell'account di archiviazione di staging come BLOB in blocchi. Passare all'**account di archiviazione di staging > BLOB** e quindi selezionare il contenitore appropriato (StandardSSD, StandardHDD o PremiumSSD). I file VHDX o i dischi rigidi virtuali verranno visualizzati come BLOB in blocchi nell'account di archiviazione di staging.
  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificare il caricamento dei dati in Azure

Dopo il caricamento in Azure, verificare che i dati si trovino nell'account o negli account di archiviazione prima di eliminarli dall'origine. I dati possono trovarsi in:

- Account di archiviazione di Azure. Quando si copiano i dati in Data Box, a seconda del tipo, i dati vengono caricati in uno dei percorsi seguenti nell'account di archiviazione di Azure.

    - **Per i BLOB in blocchi e i BLOB di pagine**: https://<nome_account_archiviazione>.blob.core.windows.net/<containername>/files/a.txt

    - **Per File di Azure**: https://<nome_account_archiviazione>.file.core.windows.net/<sharename>/files/a.txt

- Gruppi di risorse di dischi gestiti. Quando si creano dischi gestiti, i dischi rigidi virtuali vengono caricati come BLOB di pagine e quindi convertiti in dischi gestiti. I dischi gestiti vengono associati ai gruppi di risorse specificati al momento della creazione dell'ordine.

::: zone-end

Per verificare che i dati siano stati caricati in Azure, seguire questa procedura:

1. Passare all'account di archiviazione associato all'ordine del disco.
2. Passare a **Servizio Blob > Esplora BLOB**. Viene presentato l'elenco di contenitori. In modo corrispondente alla sottocartella creata nelle cartelle *BlockBlob* e *PageBlob*, contenitori con lo stesso nome vengono creati nell'account di archiviazione.
    Se i nomi delle cartelle non sono conformi alle convenzioni di denominazione di Azure, il caricamento dei dati in Azure avrà esito negativo.

3. Per verificare che sia stato caricato l'intero set di dati, usare Microsoft Azure Storage Explorer. Collegare l'account di archiviazione corrispondente all'ordine di Data Box Disk e quindi esaminare l'elenco dei contenitori BLOB. Selezionare un contenitore, fare clic su **Altro** e quindi fare clic su **Statistiche cartella**. Nel riquadro **Attività** vengono visualizzate le statistiche per la cartella, inclusi il numero di BLOB e le dimensioni totali dei BLOB. Le dimensioni totali dei BLOB devono corrispondere alle dimensioni del set di dati.

    ![Statistiche delle cartelle in Storage Explorer](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Cancellazione dei dati dai dischi di Data Box

Dopo aver completato la copia e aver verificato che i dati siano disponibili nell'account di archiviazione di Azure, i dischi vengono cancellati in modo sicuro conformemente allo standard NIST.

::: zone target="docs"

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati illustrati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Verificare il caricamento dei dati in Azure
> * Cancellazione dei dati dai dischi di Data Box


Passare alla guida pratica successiva per scoprire come gestire i dischi di Data Box tramite il portale di Azure.

> [!div class="nextstepaction"]
> [Usare il portale di Azure per amministrare Azure Data Box Disk](./data-box-portal-ui-admin.md)

::: zone-end




