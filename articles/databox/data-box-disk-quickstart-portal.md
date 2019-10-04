---
title: Guida introduttiva per Microsoft Azure Data Box Disk | Microsoft Docs
description: Usare questa guida introduttiva per distribuire rapidamente Azure Data Box Disk nel portale di Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: b07adc955b7904c6e6a3278480cec5a2be91ec35
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70232902"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Guida introduttiva: Distribuire Azure Data Box Disk usando il portale di Azure

::: zone-end

::: zone target="chromeless"

## <a name="get-started-with-azure-data-box-disk"></a>Introduzione ad Azure Data Box Disk 

::: zone-end

::: zone target="docs"

Questa guida introduttiva descrive come distribuire Azure Data Box Disk tramite il portale di Azure. Le procedure descrivono come creare rapidamente un ordine, ricevere i dischi, controllare il pacchetto, collegare i dischi e copiare i dati nei dischi per caricarli in Azure.

Per istruzioni dettagliate sulla distribuzione e sul monitoraggio, vedere [Esercitazione: Ordinare Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

::: zone-end

::: zone target="chromeless"

In questa guida vengono illustrati i passaggi per l'uso di Azure Data Box Disk nel portale di Azure. Questa guida aiuta a rispondere alle domande seguenti.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

- Assicurarsi che la sottoscrizione sia abilitata per il servizio Azure Data Box. Per abilitare la sottoscrizione per questo servizio, [iscriversi per il servizio](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Esaminare i prerequisiti**: verificare il numero di dischi e cavi, il sistema operativo e altro software.
> - **Collegare e sbloccare**: connettere il dispositivo e sbloccare il disco per la copia dei dati.
> - **Copiare i dati nel disco e convalidarli**: copiare i dati nei dischi nelle cartelle create in precedenza.
> - **Restituire i dischi**: restituire i dischi al data center di Azure in cui vengono caricati i dati nell'account di archiviazione.
> - **Verificare i dati in Azure**: verificare che i dati siano stati caricati nell'account di archiviazione prima di eliminarli dal server dei dati di origine.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Ordine

Questo passaggio richiede circa 5 minuti.

1. Creare una nuova risorsa Azure Data Box nel portale di Azure. 
2. Selezionare una sottoscrizione abilitata per questo servizio e scegliere **Importa** come tipo di trasferimento. Specificare il **paese di origine** in cui risiedono i dati e l'**area di destinazione di Azure** per il trasferimento dei dati.
3. Selezionare **Data Box Disk**. La capacità massima della soluzione è di 35 TB ed è possibile creare più ordini di dischi per dimensioni dei dati più grandi.  
4. Immettere i dettagli dell'ordine e le informazioni per la spedizione. Se il servizio è disponibile nella propria area, specificare gli indirizzi di posta elettronica per le notifiche, controllare il riepilogo e quindi creare l'ordine.

Dopo aver creato l'ordine, i dischi vengono preparati per la spedizione.

## <a name="unpack"></a>Controllare il pacchetto

Questo passaggio richiede circa 5 minuti.

I dischi di Data Box vengono spediti tramite una scatola UPS Express Box. Aprire la scatola e controllare che contenga:

- Da 1 a 5 dischi USB avvolti in pluriball.
- Un cavo di collegamento per ogni disco.
- Un'etichetta per la spedizione di ritorno.

## <a name="connect-and-unlock"></a>Collegare e sbloccare

Questo passaggio richiede circa 5 minuti.

1. Usare il cavo in dotazione per collegare il disco a un computer Windows/Linux che esegue una versione supportata. Per altre informazioni sulle versioni del sistema operativo supportate, vedere i [requisiti di sistema di Azure Data Box Disk](data-box-disk-system-requirements.md). 
2. Per sbloccare il disco:

    1. Nel portale di Azure passare a **Generale > Dettagli dispositivo** e recuperare la passkey.
    2. Scaricare ed estrarre lo strumento per sbloccare i dischi di Data Box specifici del sistema nel computer usato per copiare i dati nei dischi. 
    3. Eseguire lo strumento per sbloccare i dischi di Data Box e fornire la passkey. Per ogni reinserimento del disco, eseguire nuovamente lo strumento di sblocco e fornire la passkey. **Non usare la finestra di dialogo BitLocker o la chiave BitLocker per sbloccare il disco.** Per altre informazioni su come sbloccare i dischi, vedere [Sbloccare i dischi nel client Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) oppure [Sbloccare i dischi nel client Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. La lettera di unità assegnata al disco viene visualizzata dallo strumento. Prendere nota della lettera di unità del disco. Verrà usata nei passaggi successivi.

## <a name="copy-data-and-validate"></a>Copiare i dati e convalidarli

Il tempo per completare questa operazione dipende dalla dimensione dei dati.

1. L'unità contiene le cartelle *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk* e *DataBoxDiskImport*. Copiare i dati da importare come BLOB in blocchi tramite trascinamento della selezione nella cartella *BlockBlob*. Analogamente, trascinare e rilasciare i dati come VHD/VHDX nella cartella *PageBlob* e i dati appropriati nella cartella *AzureFile*. Copiare i dischi rigidi virtuali da caricare come dischi gestiti in una cartella in *ManagedDisk*.

    Viene creato un contenitore nell'account di archiviazione di Azure per ogni sottocartella nelle cartelle *BlockBlob* e *PageBlob*. Viene creata una condivisione file per una sottocartella in *AzureFile*.

    Tutti i file nelle cartelle *BlockBlob* e *PageBlob* vengono copiati in un contenitore predefinito `$root` per l'account di archiviazione di Azure. Copiare i file in una cartella all'interno di *AzureFile*. I file copiati direttamente nella cartella *AzureFile* hanno esito negativo e vengono caricati come BLOB in blocchi.

    > [!NOTE]
    > - Tutti i contenitori, i BLOB e i file devono essere conformi alle [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Se queste regole non vengono rispettate, il caricamento di dati in Azure avrà esito negativo.
    > - Assicurarsi che le dimensioni dei file non superino circa 4,75 TiB per i BLOB in blocchi, 8 TiB per i BLOB di pagine e 1 TiB per File di Azure.

2. **(Facoltativo ma consigliato)** Al termine della copia, è consigliabile eseguire almeno il cmdlet `DataBoxDiskValidation.cmd` disponibile nella cartella *DataBoxDiskImport* e selezionare l'opzione 1 per convalidare i file. Se il tempo lo permette, è consigliabile anche usare l'opzione 2 per generare i checksum per la convalida (il tempo necessario dipende dalle dimensioni dei dati). Questi passaggi riducono al minimo le probabilità di errori durante il caricamento dei dati in Azure.
3. Rimuovere in modo sicuro l'unità.

## <a name="ship-to-azure"></a>Spedire ad Azure

Questa operazione richiede circa 5-7 minuti.

1. Riposizionare insieme tutti i dischi nel pacchetto originale. Usare l'etichetta di spedizione inclusa. Se l'etichetta è danneggiata o è stata smarrita, scaricarla dal portale. Passare a **Panoramica** e fare clic su **Scarica etichetta di spedizione** dalla barra dei comandi.
2. Consegnare il pacchetto sigillato presso qualsiasi punto di ritiro del vettore di spedizione.  

Il servizio Data Box Disk invia una notifica di posta elettronica e aggiorna lo stato dell'ordine nel portale di Azure.

## <a name="verify-your-data"></a>Verificare i dati

Il tempo per completare questa operazione dipende dalla dimensione dei dati.

1. Quando il disco di Data Box viene connesso alla rete del data center di Azure, il caricamento dei dati in Azure viene avviato automaticamente.
2. Il servizio Azure Data Box invia notifica del completamento della copia dei dati tramite il portale di Azure.
    
    1. Controllare i log degli errori per rilevare eventuali errori e intraprendere le azioni appropriate.
    2. Verificare la presenza dei dati negli account di archiviazione prima di eliminarli dall'origine.

## <a name="clean-up-resources"></a>Pulire le risorse

Il completamento di questo passaggio richiede 2-3 minuti.

Per ripulire, è possibile annullare l'ordine di Data Box e quindi eliminarlo.

- È possibile annullare l'ordine di Data Box nel portale di Azure prima che venga elaborato. Dopo l'elaborazione, l'ordine non può essere annullato. L'ordine progredisce fino a raggiungere la fase di completamento.

    Per annullare l'ordine, passare a **Panoramica** e fare clic su **Annulla** dalla barra dei comandi.  

- È possibile eliminare l'ordine quando lo stato risulta **Completato** oppure **Annullato** nel portale di Azure.

    Per eliminare l'ordine, passare a **Panoramica** e fare clic su **Elimina** dalla barra dei comandi.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è visto come distribuire il servizio Azure Data Box Disk per facilitare l'importazione dei dati in Azure. Per altre informazioni sulla gestione di Azure Data Box Disk, passare all'esercitazione seguente:

> [!div class="nextstepaction"]
> [Usare il portale di Azure per amministrare Data Box Disk](data-box-portal-ui-admin.md)

::: zone-end
