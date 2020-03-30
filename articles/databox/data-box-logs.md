---
title: Tenere traccia e registrare gli eventi di Azure Data Box, Azure Data Box Heavy Documenti Microsoft
description: Viene descritto come tenere traccia e registrare gli eventi nelle varie fasi dell'ordine Azure Data Box e Azure Data Box Heavy.Describes how to track and log events at the various stages of your Azure Data Box and Azure Data Box Heavy order.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 72e1d3b0ad72b1e68b88eb0550cbe839ade9d929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260020"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Monitoraggio e registrazione degli eventi per Azure Data Box e Azure Data Box Heavy

Un ordine Data Box o Data Box Heavy passa attraverso i passaggi seguenti: ordine, configurazione, copia dei dati, restituzione, caricamento in Azure e verifica e cancellazione dei dati. In corrispondenza di ogni passaggio nell'ordine, è possibile eseguire più azioni per controllare l'accesso all'ordine, controllare gli eventi, tenere traccia dell'ordine e interpretare i vari log generati.

La tabella seguente mostra un riepilogo dei passaggi dell'ordine pesante di Data Box o Data Box e degli strumenti disponibili per tenere traccia e controllare l'ordine durante ogni passaggio.

| Fase dell'ordine della casella dati       | Strumento per tenere traccia e controllo                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Creare un ordine               | [Configurare il controllo di accesso sull'ordine tramite il controllo degli accessi in base al ruoloSet up access control on the order via RBAC](#set-up-access-control-on-the-order)                                                    |
| Ordine elaborato            | [Tenere traccia dell'ordine](#track-the-order) tramite <ul><li> Portale di Azure </li><li> Sito Web del vettore di spedizione </li><li>Notifiche di posta elettronica</ul> |
| Configurare il dispositivo              | Accesso delle credenziali del dispositivo registrato nei [log attivitàDevice](#query-activity-logs-during-setup) credentials access log in Activity logs                                              |
| Copia dei dati sul dispositivo        | [Visualizzare i file *error.xml* ](#view-error-log-during-data-copy) per la copia dei dati                                                             |
| Preparare per la spedizione            | [Esaminare i file della distinta componenti](#inspect-bom-during-prepare-to-ship) o i file manifesto nel dispositivo                                      |
| Caricamento dei dati in AzureData upload to Azure       | [Esaminare i log di copia](#review-copy-log-during-upload-to-azure) per individuare gli errori durante il caricamento dei dati nel data center di AzureReview copy logs for errors during data upload at Azure datacenter                         |
| Cancellazione dei dati dal dispositivo   | [Visualizzare i registri della catena di custodia,](#get-chain-of-custody-logs-after-data-erasure) inclusi i registri di controllo e la cronologia degli ordini                |

In questo articolo vengono descritti in dettaglio i vari meccanismi o strumenti disponibili per tenere traccia e controllare l'ordine Data Box o Data Box Heavy. Le informazioni contenute in questo articolo si applicano a entrambi, Data Box e Data Box Heavy. Nelle sezioni successive, tutti i riferimenti a Data Box si applicano anche a Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Impostare il controllo di accesso nell'ordine

Puoi controllare chi può accedere al tuo ordine quando l'ordine viene creato per la prima volta. Impostare i ruoli di controllo degli accessi in base al ruolo in vari ambiti per controllare l'accesso all'ordine della casella di dati. Un ruolo RBAC determina il tipo di accesso: lettura/scrittura, sola lettura, lettura-scrittura in un sottoinsieme di operazioni.

I due ruoli che possono essere definiti per il servizio Azure Data Box sono:The two roles that can be defined for the Azure Data Box service are:

- **Lettore casella dati:** consente di accedere in sola lettura a un ordine o a uno o più ordini definiti dall'ambito. Possono visualizzare solo i dettagli di un ordine. Non possono accedere ad altri dettagli relativi agli account di archiviazione o modificare i dettagli dell'ordine, ad esempio l'indirizzo e così via.
- **Collaboratore casella dati:** è possibile creare un ordine per trasferire i dati in un determinato account di archiviazione *solo se dispongono già dell'accesso*in scrittura a un account di archiviazione. Se non hanno accesso a un account di archiviazione, non possono nemmeno creare un ordine di Data Box per copiare i dati nell'account. Questo ruolo non definisce le autorizzazioni correlate all'account di archiviazione né concede l'accesso agli account di archiviazione.  

Per limitare l'accesso a un ordine, è possibile:

- Assegnare un ruolo a livello di ordine. L'utente dispone solo di tali autorizzazioni definite dai ruoli per interagire solo con l'ordine specifico di Data Box e nient'altro.
- Assegnare un ruolo a livello di gruppo di risorse, l'utente ha accesso a tutti gli ordini di Data Box all'interno di un gruppo di risorse.

Per ulteriori informazioni sull'utilizzo suggerito del controllo degli accessi in base al ruolo, vedere [Procedure consigliate per il controllo degli accessi in base](../role-based-access-control/overview.md#best-practice-for-using-rbac)al ruolo.

## <a name="track-the-order"></a>Monitorare l'ordine

È possibile tenere traccia dell'ordine tramite il portale di Azure e il sito Web del vettore di spedizione. I seguenti meccanismi sono disponibili per tenere traccia dell'ordine di Data Box in qualsiasi momento:

- Per tenere traccia dell'ordine quando il dispositivo si trova nel data center di Azure o nei locali, passare **all'ordine di Data Box > Panoramica** nel portale di Azure.To track the order when the device is in Azure datacenter or your premises, go to your Data Box order > Overview in Azure portal.

    ![Visualizzare lo stato dell'ordine e la tracciabilità no](media/data-box-logs/overview-view-status-1.png)

- Per tenere traccia dell'ordine mentre il dispositivo è in transito, visitare il sito Web dell'operatore regionale, ad esempio UPS negli Stati Uniti. Specificare il numero di tracciabilità associato all'ordine.
- Data Box invia anche notifiche e-mail ogni volta che lo stato dell'ordine cambia in base alle e-mail fornite al momento della creazione dell'ordine. Per un elenco di tutti gli stati dell'ordine della casella di dati, vedere [Visualizzare lo stato dell'ordine](data-box-portal-admin.md#view-order-status). Per modificare le impostazioni di notifica associate all'ordine, vedere [Modificare i dettagli](data-box-portal-admin.md#edit-notification-details)della notifica .

## <a name="query-activity-logs-during-setup"></a>Query log attività durante l'installazione

- La tua Data Box arriva nei tuoi locali in uno stato bloccato. È possibile usare le credenziali del dispositivo disponibili nel portale di Azure per l'ordine.  

    Quando è configurata una casella dati, potrebbe essere necessario sapere chi ha eseguito l'accesso alle credenziali del dispositivo. Per individuare chi ha eseguito l'accesso al pannello **Credenziali dispositivo,** è possibile eseguire una query nei log attività.  Qualsiasi azione che comporta l'accesso **ai dettagli del** `ListCredentials` dispositivo > pannello Credenziali viene registrato nei log attività come azione.

    ![Eseguire query sui log attività](media/data-box-logs/query-activity-log-1.png)

- Ogni accesso alla casella dati viene registrato in tempo reale. Tuttavia, queste informazioni sono disponibili nei registri di [controllo](#audit-logs) solo dopo il completamento dell'ordine.

## <a name="view-error-log-during-data-copy"></a>Visualizzare il log degli errori durante la copia dei datiView error log during data copy

Durante la copia dei dati in Data Box o Data Box Heavy, viene generato un file di errore in caso di problemi con i dati copiati.

### <a name="errorxml-file"></a>File Error.xml

Assicurarsi che i processi di copia siano stati completati senza errori. Se si verificano errori durante il processo di copia, scaricare i log dalla pagina **Connetti e copia**.

- Se è stato copiato un file che non è allineato a una cartella del disco gestito nella casella di dati, il file non viene caricato come BLOB di pagine nell'account di archiviazione di gestione temporanea. Verrà visualizzato un errore nei log. Rimuovere il file e copiare un file allineato su 512 byte.
- Se è stato copiato un disco rigido virtuale o un disco rigido virtuale dinamico o un disco rigido virtuale differenze (questi file non sono supportati), verrà visualizzato un errore nei log.

Di seguito è riportato un esempio del *file error.xml* per errori diversi durante la copia su dischi gestiti.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Ecco un esempio del *file error.xml* per errori diversi durante la copia nei BLOB di pagine.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Ecco un esempio del *file error.xml* per errori diversi durante la copia in BLOB a blocchi.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Ecco un esempio del *file error.xml* per errori diversi durante la copia in File di Azure.Here is a sample of the error.xml for different errors when copying to Azure Files.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

In ognuno dei casi precedenti, risolvere gli errori prima di procedere al passaggio successivo. Per ulteriori informazioni sugli errori ricevuti durante la copia dei dati in Data Box tramite protocolli SMB o NFS, vedere Risolvere i problemi relativi a [Data Box e Data Box Heavy](data-box-troubleshoot.md). Per informazioni sugli errori ricevuti durante la copia dei dati in Data Box tramite REST, vedere Risolvere i problemi di [archiviazione BLOB di Data Box](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Ispezionare la distinta base durante la preparazione alla spedizione

Durante la preparazione, viene creato un elenco di file noti come distinta base (BOM) o file manifesto.

- Utilizzare questo file per verificare i nomi effettivi e il numero di file copiati nella casella di dati.
- Utilizzare questo file per verificare le dimensioni effettive dei file.
- Verificare che *il crc64* corrisponda a una stringa diversa da zero. <!--A null value for crc64 indicates that there was a reparse point error)-->

Per ulteriori informazioni sugli errori ricevuti durante la preparazione alla spedizione, vedere Risolvere i problemi relativi a [Data Box e Data Box Heavy](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>Distinta materiali o file manifesto

Il file DELLA distinta materiali o del manifesto contiene l'elenco di tutti i file copiati nel dispositivo Data Box. Il file DELLA distinta materiali ha i nomi dei file e le dimensioni corrispondenti, nonché il checksum. Viene creato un file di distinta componenti separato per i BLOB in blocco, i BLOB di pagine, i file di Azure per la copia tramite le API REST e per la copia nei dischi gestiti nella casella di dati. È possibile scaricare i file della distinta componenti dall'interfaccia utente Web locale del dispositivo durante la preparazione alla spedizione.

Questi file risiedono anche nel dispositivo Data Box e vengono caricati nell'account di archiviazione associato nel data center di Azure.These files also reside on the Data Box device and are uploaded to the associated storage account in the Azure datacenter.

### <a name="bom-file-format"></a>Formato di file della distinta componenti

Il formato generale del file BOM o manifesto ha il seguente formato generale:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Ecco un esempio di manifesto generato quando i dati sono stati copiati nella condivisione BLOB in blocchi nella casella dati.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Anche i file del manifesto o del contenitore vengono copiati nell'account di archiviazione di Azure.The BOM or manifest files are also copied to the Azure storage account. È possibile usare i file della distinta materiali o del manifesto per verificare che i file caricati in Azure corrispondano ai dati copiati nella casella dati.

## <a name="review-copy-log-during-upload-to-azure"></a>Esaminare il log di copia durante il caricamento in AzureReview copy log during upload to Azure

Durante il caricamento dei dati in Azure, viene creato un log di copia.

### <a name="copy-log"></a>Copia registro

Per ogni ordine elaborato, il servizio Data Box crea il log di copia nell'account di archiviazione associato. Il log di copia ha il numero totale di file caricati e il numero di file con errori durante la copia dei dati da Data Box all'account di archiviazione di Azure.The copy log has the total number of files that were uploaded and the number of files that errored out during the data copy from Data Box to your Azure storage account.

Durante il caricamento in Azure viene eseguito un calcolo crC (Cyclic Redundancy Check). I CRC dalla copia dei dati e dopo il caricamento dei dati vengono confrontati. Una mancata corrispondenza CRC indica che il caricamento dei file corrispondenti non è riuscito a caricare.

Per impostazione predefinita, i log vengono scritti in un contenitore denominato `copylog`. I registri vengono archiviati con la seguente convenzione di denominazione:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Il percorso del log di copia viene visualizzato anche nel pannello **Panoramica** del portale.

![Percorso per copiare il log nel pannello Panoramica al termine del completamento](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Caricamento completato 

Nell'esempio seguente viene descritto il formato generale di un log di copia per un caricamento di una casella di dati che è stato completato correttamente:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Caricamento completato con errori 

Il caricamento in Azure può anche essere completo di errori.

![Percorso per copiare il log nel pannello Panoramica quando viene completato con errori](media/data-box-logs/copy-log-path-2.png)

Di seguito è riportato un esempio di log di copia in cui il caricamento è stato completato con errori:Here is an example of a copy log where the upload completed with errors:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Caricamento completato con avvisi

Upload to Azure completes with warnings if your data had container/blob/file names that didn't conform to Azure naming conventions and the names were modified to upload the data to Azure.

![Percorso per copiare il log nel pannello Panoramica quando viene completato con avvisi](media/data-box-logs/copy-log-path-3.png)

Ecco un esempio di log di copia in cui i contenitori che non erano conformi alle convenzioni di denominazione di Azure sono stati rinominati durante il caricamento dei dati in Azure.Here is an example of a copy log where the containers that did not conform to Azure naming conventions were renamed during the data upload to Azure.

I nuovi nomi univoci per `DataBox-GUID` i contenitori sono nel formato e i dati per il contenitore vengono inseriti nel nuovo contenitore rinominato. Il log di copia specifica il nome del contenitore precedente e del nuovo.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Ecco un esempio di log di copia in cui i BLOB o i file che non erano conformi alle convenzioni di denominazione di Azure sono stati rinominati durante il caricamento dei dati in Azure.Here is an example of a copy log where the blobs or files that did not conform to Azure naming conventions, were renamed during the data upload to Azure. I nuovi nomi BLOB o file vengono convertiti in digest SHA256 del percorso relativo al contenitore e vengono caricati nel percorso in base al tipo di destinazione. La destinazione può essere BLOB in blocchi, BLOB di pagine o file di Azure.The destination can be block blobs, page blobs, or Azure Files.

L'oggetto `copylog` specifica il BLOB o il nuovo nome di file e il percorso in Azure.The specifies the old and the new blob or file name and the path in Azure.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Ottenere i registri della catena di custodia dopo la cancellazione dei datiGet chain of custod y logs after data erasure

Dopo che i dati sono stati cancellati dai dischi Data Box in base alle linee guida del NIST SP 800-88 Revision 1, sono disponibili i registri della catena di custodia. Questi registri includono i registri di controllo e la cronologia degli ordini. Anche i file della distinta materiali o del manifesto vengono copiati con i registri di controllo.

### <a name="audit-logs"></a>Log di controllo

I log di controllo contengono informazioni su come accendere e accedere alle condivisioni nella casella dati o in Data Box Heavy quando si trova al di fuori del data center di Azure.Audit logs contain information on how to power on and access shares on the Data Box or Data Box Heavy when it is outside of Azure datacenter. Questi registri si trovano in:`storage-account/azuredatabox-chainofcustodylogs`

Di seguito è riportato un esempio del log di controllo da una casella di dati:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Scaricare la cronologia ordini

La cronologia degli ordini è disponibile nel portale di Azure.Order history is available in Azure portal. Se l'ordine è completo e la pulizia del dispositivo (cancellazione dei dati dai dischi) è completa, quindi vai al tuo ordine del dispositivo e vai a **Dettagli ordine**. L'opzione **Scarica cronologia ordini** è disponibile. Per ulteriori informazioni, consultate [Scaricare la cronologia degli ordini.](data-box-portal-admin.md#download-order-history)

Se scorri la cronologia degli ordini, vedrai:

- Informazioni di tracciamento dell'operatore per il dispositivo.
- Eventi con attività *SecureErase.* Questi eventi corrispondono alla cancellazione dei dati sul disco.
- Collegamenti al registro della casella dati. Vengono visualizzati i percorsi dei registri di *controllo,* *i registri*di copia e i file *BOM.*

Ecco un esempio del log della cronologia degli ordini dal portale di Azure:Here is a sample of the order history log from Azure portal:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come risolvere i problemi relativi a [Data Box e Data Box Heavy](data-box-troubleshoot.md).
