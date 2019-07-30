---
title: Usare Azure Data Box Heavy per eseguire la migrazione del contenuto di una condivisione file in SharePoint Online| Microsoft Docs
description: Usare questa esercitazione per apprendere come eseguire la migrazione del contenuto di una condivisione file in Share Point Online usando Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: 4955b28dff3193a95950912562cc3b6ec789479d
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325279"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>Usare Azure Data Box Heavy per eseguire la migrazione del contenuto di una condivisione file in SharePoint Online

Usare Azure Data Box Heavy e lo Strumento di migrazione di SharePoint per eseguire facilmente la migrazione del contenuto della condivisione file in SharePoint Online e OneDrive. Con Data Box Heavy è possibile eliminare la dipendenza dal collegamento WAN (Wide Area Network) per il trasferimento dei dati.

Microsoft Azure Data Box è un servizio che consente di ordinare un dispositivo da portale di Microsoft Azure. È quindi possibile copiare terabyte di dati dai propri server al dispositivo. Dopo aver rispedito il dispositivo a Microsoft, i dati vengono copiati in Azure. A seconda delle dimensioni dei dati da trasferire, è possibile scegliere tra:

- [Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) con 35 TB di capacità utilizzabile per ordine per set di dati medio/piccoli.
- [Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) con 80 TB di capacità utilizzabile per ordine per set di dati medio/grandi.
- [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-overview) con 770 TB di capacità utilizzabile per ordine per set di dati grandi.

Questo articolo illustra nello specifico come usare Data Box Heavy per eseguire la migrazione del contenuto della condivisione file in SharePoint Online.

## <a name="requirements-and-costs"></a>Requisiti e costi

### <a name="for-data-box-heavy"></a>Per Data Box Heavy

- Data Box Heavy è disponibile solo per le offerte di sottoscrizione Contratto Enterprise (EA), Cloud Solution Provider (CSP) o Azure Sponsorship. Se il tipo di sottoscrizione in uso non è elencato, contattare il supporto tecnico Microsoft per aggiornare la sottoscrizione oppure vedere [Prezzi di Azure](https://azure.microsoft.com/pricing/).
- L'uso di Data Box Heavy è soggetto al pagamento di una tariffa. Per altre informazioni, vedere [Prezzi per la famiglia di prodotti Azure Data Box](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>Per SharePoint Online

- Vedere [Uso dello Strumento di migrazione di SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>Panoramica del flusso di lavoro

Questo flusso di lavoro implica l'esecuzione di procedure sia in Azure Data Box Heavy che in Share Point Online.
I passaggi seguenti sono relativi ad Azure Data Box Heavy.

1. Ordinare Azure Data Box Heavy.
2. Ricevere e configurare il dispositivo.
3. Copiare i dati dalla condivisione file locale in una cartella per File di Azure nel dispositivo.
4. Completata la copia, rispedire il dispositivo seguendo le istruzioni.
5. Attendere che i dati vengano caricati completamente in Azure.

I passaggi seguenti sono relativi a SharePoint Online.

6. Creare una macchina virtuale nel portale di Azure e montarvi la condivisione file di Azure.
7. Installare lo Strumento di migrazione di SharePoint nella macchina virtuale di Azure.
8. Eseguire lo Strumento di migrazione di SharePoint usando la condivisione file di Azure come *origine*.
9. Completare i passaggi finali dello strumento.
10. Verificare e confermare i dati.

## <a name="use-data-box-heavy-to-copy-data"></a>Usare Data Box Heavy per copiare i dati

Seguire questa procedura per copiare i dati in Data Box Heavy.

1. [Ordinare Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Dopo averlo ricevuto, [configurare Data Box Heavy](data-box-heavy-deploy-set-up.md). Occorrerà cablare e configurare entrambi i nodi sul dispositivo.
3. [Copiare i dati in Azure Data Box Heavy](data-box-heavy-deploy-copy-data.md). Durante la copia accertarsi di:

    - Usare solo la cartella *StorageAccountName_AzFile* in Data Box Heavy per copiare i dati. I dati devono infatti essere inseriti in una condivisione file di Azure, non in BLOB in blocchi o in BLOB di pagine.
    - Copiare i file in una cartella all'interno della cartella *StorageAccountName_AzFile*. Una sottocartella all'interno di *StorageAccountName_AzFile* determina la creazione di una condivisione file. La copia effettuata direttamente nella cartella *StorageAccountName_AzFile* non riesce e i file vengono caricati come BLOB in blocchi. Questa condivisione file verrà montata nella macchina virtuale al passaggio successivo.
    - Copiare i dati in entrambi i nodi di Data Box Heavy.
3. Eseguire [Prepara per la spedizione](data-box-heavy-deploy-picked-up.md#prepare-to-ship) sul dispositivo. Una preparazione per la spedizione corretta assicura il corretto caricamento dei file in Azure.
4. [Restituire il dispositivo](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Verificare il caricamento dei dati in Azure](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure).

## <a name="use-spmt-to-migrate-data"></a>Usare lo Strumento di migrazione di SharePoint per eseguire la migrazione dei dati

Dopo aver ricevuto conferma dal team responsabile dei dati di Azure dell'avvenuta copia dei dati, è possibile procedere con la migrazione a SharePoint Online.

Per livelli ottimali di prestazioni e connettività, è consigliabile creare una macchina virtuale di Azure.

1. Accedere al portale di Azure e quindi [creare una macchina virtuale](../virtual-machines/windows/quick-create-portal.md).
2. [Montare la condivisione file di Azure nella macchina virtuale](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Scaricare lo Strumento di migrazione di SharePoint](https://spmtreleasescus.blob.core.windows.net/install/default.htm) e installarlo nella macchina virtuale di Azure.
4. Avviare lo Strumento di migrazione di SharePoint. Fare clic su **Accedi** e immettere il nome utente e la password di Office 365.
5. In **Dove sono i dati?**  selezionare **Condivisione file**. Immettere il percorso della condivisione file di Azure in cui si trovano i dati.
6. Seguire i prompt rimanenti come di consueto, immettendo anche la posizione di destinazione. Per altre informazioni, vedere [Uso dello Strumento di migrazione di SharePoint](https://docs.microsoft.com/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - La velocità di inserimento dei dati in SharePoint Online dipende da diversi fattori, indipendentemente dal fatto che i dati siano già in Azure. Conoscere questi fattori aiuterà a pianificare e ottimizzare l'efficienza della migrazione.  Per altre informazioni, vedere [Procedure consigliate per velocizzare la migrazione in SharePoint e OneDrive](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - La migrazione dei dati in SharePoint Online comporta il rischio di perdere le autorizzazioni esistenti sui file, oltre che determinati metadati, come *Creato da* e *Data ultima modifica*.

## <a name="next-steps"></a>Passaggi successivi

[Ordinare Data Box Heavy](./data-box-heavy-deploy-ordered.md)