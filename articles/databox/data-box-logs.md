---
title: Rilevare e registrare Azure Data Box Azure Data Box Heavy eventi | Microsoft Docs
description: Viene descritto come tenere traccia degli eventi e registrarli nelle varie fasi dell'Azure Data Box e Azure Data Box Heavy ordine.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 8fecc00a970f0e706dc6240eaec593fd54968ff8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934206"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Registrazione degli eventi e di rilevamento per il Azure Data Box e Azure Data Box Heavy

Un Data Box o Data Box Heavy ordine esegue i passaggi seguenti: ordine, configurazione, copia dei dati, ritorno, caricamento in Azure, verifica e cancellazione dei dati. Corrispondente a ogni passaggio nell'ordine, è possibile eseguire più azioni per controllare l'accesso all'ordine, controllare gli eventi, tenere traccia dell'ordine e interpretare i vari log generati.

Nella tabella seguente viene illustrato un riepilogo dei passaggi per l'ordine di Data Box o Data Box Heavy e gli strumenti disponibili per tenere traccia e controllare l'ordine durante ogni passaggio.

| Fase Data Box Order       | Strumento di rilevamento e controllo                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Crea ordine               | [Configurare il controllo di accesso nell'ordine tramite RBAC](#set-up-access-control-on-the-order)                                                    |
| Ordine elaborato            | [Tenere traccia dell'ordine](#track-the-order) <ul><li> Portale di Azure </li><li> Sito Web del vettore di spedizione </li><li>Notifiche tramite posta elettronica</ul> |
| Configurare il dispositivo              | Credenziali del dispositivo accesso ai [log attività](#query-activity-logs-during-setup) registrate                                              |
| Copia dei dati nel dispositivo        | [Visualizzare i file *Error. XML* ](#view-error-log-during-data-copy) per la copia dei dati                                                             |
| Prepara per la spedizione            | [Esaminare i file DBA](#inspect-bom-during-prepare-to-ship) o i file manifesto nel dispositivo                                      |
| Caricamento dei dati in Azure       | [Esaminare i log di copia](#review-copy-log-during-upload-to-azure) per individuare eventuali errori durante il caricamento dei dati nel Data Center di Azure                         |
| Cancellazione dei dati dal dispositivo   | [Visualizza la catena dei log di custodia](#get-chain-of-custody-logs-after-data-erasure) , inclusi i log di controllo e la cronologia degli ordini                |

Questo articolo descrive in dettaglio i vari meccanismi o strumenti disponibili per tenere traccia e controllare Data Box o Data Box Heavy ordine. Le informazioni contenute in questo articolo si applicano sia a Data Box sia a Data Box Heavy. Nelle sezioni successive, tutti i riferimenti a Data Box si applicano anche a Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Configurare il controllo di accesso nell'ordine

È possibile controllare chi può accedere all'ordine quando viene creato per la prima volta. Configurare i ruoli di controllo degli accessi in base al ruolo (RBAC) in diversi ambiti per controllare l'accesso all'ordine di Data Box. Un ruolo di controllo degli accessi in base al ruolo determina il tipo di accesso, ovvero di lettura/scrittura, di sola lettura, di lettura e scrittura a un subset di operazioni.

I due ruoli che possono essere definiti per il servizio Azure Data Box sono:

- **Data Box Reader** -ha accesso in sola lettura a uno o più ordini definiti dall'ambito. Possono solo visualizzare i dettagli di un ordine. Non possono accedere ad altri dettagli correlati ad account di archiviazione o modificare i dettagli dell'ordine, ad esempio indirizzo e così via.
- **Collaboratore data box** : può creare solo un ordine per trasferire i dati a un account di archiviazione specifico *se hanno già accesso in scrittura a un account di archiviazione*. Se non hanno accesso a un account di archiviazione, non possono neanche creare un Data Box ordine di copiare i dati nell'account. Questo ruolo non definisce le autorizzazioni relative agli account di archiviazione né concede l'accesso agli account di archiviazione.  

Per limitare l'accesso a un ordine, è possibile:

- Assegnare un ruolo a livello di ordine. L'utente dispone solo delle autorizzazioni in base a quanto definito dai ruoli per interagire solo con l'ordine specifico Data Box e nient'altro.
- Assegnare un ruolo a livello di gruppo di risorse, l'utente ha accesso a tutti gli ordini di Data Box all'interno di un gruppo di risorse.

Per ulteriori informazioni sull'utilizzo di RBAC suggerito, vedere [procedure consigliate per RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Monitorare l'ordine

È possibile tenere traccia dell'ordine tramite il portale di Azure e tramite il sito Web del vettore di spedizione. Per tenere traccia dell'ordine di Data Box in qualsiasi momento, è necessario disporre dei meccanismi seguenti:

- Per tenere traccia dell'ordine quando il dispositivo si trova nel Data Center di Azure o in locale, passare alla **Panoramica dell'ordine di Data Box >** in portale di Azure.

    ![Visualizza stato ordine e rilevamento No](media/data-box-logs/overview-view-status-1.png)

- Per tenere traccia dell'ordine mentre il dispositivo è in transito, visitare il sito Web del vettore regionale, ad esempio il sito Web UPS in Microsoft. Fornire il numero di tracciabilità associato all'ordine.
- Data Box inoltre invia notifiche tramite posta elettronica ogni volta che lo stato dell'ordine cambia in base ai messaggi di posta elettronica forniti al momento della creazione dell'ordine. Per un elenco di tutti gli Stati dell'ordine di Data Box, vedere [visualizzare lo stato dell'ordine](data-box-portal-admin.md#view-order-status). Per modificare le impostazioni di notifica associate all'ordine, vedere [modificare i dettagli della notifica](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Log attività query durante l'installazione

- Il Data Box arriva in locale in uno stato bloccato. È possibile usare le credenziali del dispositivo disponibili nell'portale di Azure per l'ordine.  

    Quando viene configurata una Data Box, potrebbe essere necessario conoscere l'utente che ha eseguito l'accesso alle credenziali del dispositivo. Per determinare chi ha eseguito l'accesso al pannello delle **credenziali del dispositivo** , è possibile eseguire una query sui log attività.  Qualsiasi azione che prevede l'accesso ai **Dettagli del dispositivo > Pannello credenziali** viene registrato nei log attività `ListCredentials` come azione.

    ![Eseguire query sui log attività](media/data-box-logs/query-activity-log-1.png)

- Ogni accesso all'Data Box viene registrato in tempo reale. Tuttavia, queste informazioni sono disponibili solo nei [log di controllo](#audit-logs) dopo che l'ordine è stato completato correttamente.

## <a name="view-error-log-during-data-copy"></a>Visualizza log degli errori durante la copia dei dati

Durante la copia dei dati in Data Box o Data Box Heavy, viene generato un file di errore se si verificano problemi con i dati copiati.

### <a name="errorxml-file"></a>Errore. XML (file)

Verificare che i processi di copia siano finiti senza errori. Se si verificano errori durante il processo di copia, scaricare i log dalla pagina **Connetti e copia** .

- Se è stato copiato un file che non è di 512 byte allineato a una cartella del disco gestito nel Data Box, il file non viene caricato come BLOB di pagine nell'account di archiviazione di staging. Verrà visualizzato un errore nei log. Rimuovere il file e copiare un file allineato su 512 byte.
- Se è stato copiato un VHDX o un disco rigido virtuale dinamico o un disco rigido virtuale differenze (questi file non sono supportati), verrà visualizzato un errore nei log.

Di seguito è riportato un esempio di *Error. XML* per diversi errori durante la copia nei dischi gestiti.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Di seguito è riportato un esempio di *Error. XML* per diversi errori durante la copia nei BLOB di pagine.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Di seguito è riportato un esempio di *Error. XML* per diversi errori durante la copia in BLOB in blocchi.

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

Di seguito è riportato un esempio di *Error. XML* per diversi errori durante la copia in file di Azure.

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

In ognuno dei casi precedenti, risolvere gli errori prima di procedere con il passaggio successivo. Per ulteriori informazioni sugli errori ricevuti durante la copia dei dati Data Box tramite i protocolli SMB o NFS, vedere [risolvere i problemi relativi data box e data box Heavy](data-box-troubleshoot.md). Per informazioni sugli errori ricevuti durante la copia dei dati in Data Box tramite REST, vedere risolvere i problemi relativi a [Data Box archiviazione BLOB](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Controllare la DBA durante la preparazione alla spedizione

Durante la preparazione per la spedizione, viene creato un elenco di file noti come la distinta base (BOM) o il file manifesto.

- Utilizzare questo file per verificare i nomi effettivi e il numero di file copiati nella Data Box.
- Utilizzare questo file per verificare le dimensioni effettive dei file.
- Verificare che *crc64* corrisponda a una stringa diversa da zero. <!--A null value for crc64 indicates that there was a reparse point error)-->

Per ulteriori informazioni sugli errori ricevuti durante la preparazione alla spedizione, vedere la pagina relativa alla [risoluzione dei problemi data box e data box Heavy](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>BOM o file manifesto

Il BOM o il file manifesto contiene l'elenco di tutti i file copiati nel dispositivo Data Box. Il file BOM include i nomi di file e le dimensioni corrispondenti, oltre al checksum. Viene creato un file BOM separato per i BLOB in blocchi, i BLOB di pagine, File di Azure, per la copia tramite le API REST e per la copia su Managed disks nel Data Box. È possibile scaricare i file DBA dall'interfaccia utente Web locale del dispositivo durante la preparazione per la spedizione.

Questi file si trovano anche nel dispositivo Data Box e vengono caricati nell'account di archiviazione associato nel Data Center di Azure.

### <a name="bom-file-format"></a>Formato file BOM

Il file BOM o manifesto ha il formato generale seguente:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Di seguito è riportato un esempio di un manifesto generato quando i dati sono stati copiati nella condivisione BLOB in blocchi sul Data Box.

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

I file BOM o manifest vengono copiati anche nell'account di archiviazione di Azure. È possibile usare i file BOM o manifest per verificare che i file caricati in Azure corrispondano ai dati copiati nella Data Box.

## <a name="review-copy-log-during-upload-to-azure"></a>Esaminare il log di copia durante il caricamento in Azure

Durante il caricamento dei dati in Azure, viene creato un log di copia.

### <a name="copy-log"></a>Copia log

Per ogni ordine elaborato, il servizio Data Box crea il log di copia nell'account di archiviazione associato. Il log di copia contiene il numero totale di file caricati e il numero di file che hanno avuto errore durante la copia dei dati da Data Box all'account di archiviazione di Azure.

Un calcolo del controllo di ridondanza ciclico (CRC) viene eseguito durante il caricamento in Azure. CRC dalla copia dei dati e dopo il confronto del caricamento dei dati. Una mancata corrispondenza del CRC indica che non è stato possibile caricare i file corrispondenti.

Per impostazione predefinita, i log vengono scritti in un `copylog`contenitore denominato. I log vengono archiviati con la convenzione di denominazione seguente:

[https://login.microsoftonline.com/common/](`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`).

Il percorso del log di copia viene visualizzato anche nel pannello **Panoramica** del portale.

![Percorso per copiare il pannello di panoramica al termine](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Caricamento completato 

Nell'esempio seguente viene descritto il formato generale di un log di copia per un caricamento Data Box completato correttamente:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Caricamento completato con errori 

Il caricamento in Azure può anche essere completato con errori.

![Percorso per copiare il pannello di panoramica quando è stato completato con errori](media/data-box-logs/copy-log-path-2.png)

Di seguito è riportato un esempio di un log di copia in cui il caricamento è stato completato con errori:

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

Il caricamento in Azure viene completato con avvisi se i dati contengono nomi di contenitore/BLOB/file non conformi alle convenzioni di denominazione di Azure e i nomi sono stati modificati per caricare i dati in Azure.

![Percorso per copiare il pannello di panoramica quando è stato completato con avvisi](media/data-box-logs/copy-log-path-3.png)

Di seguito è riportato un esempio di un log di copia in cui i contenitori che non sono conformi alle convenzioni di denominazione di Azure sono stati rinominati durante il caricamento dei dati in Azure.

I nuovi nomi univoci per i contenitori sono nel `DataBox-GUID` formato e i dati per il contenitore vengono inseriti nel nuovo contenitore rinominato. Il log di copia specifica il vecchio e il nuovo nome del contenitore per il contenitore.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Di seguito è riportato un esempio di un log di copia in cui i BLOB o i file che non sono conformi alle convenzioni di denominazione di Azure sono stati rinominati durante il caricamento dei dati in Azure. I nuovi nomi di BLOB o file vengono convertiti in SHA256 digest del percorso relativo del contenitore e caricati nel percorso in base al tipo di destinazione. La destinazione può essere costituita da BLOB in blocchi, BLOB di pagine o File di Azure.

`copylog` Specifica il vecchio e il nuovo BLOB o nome file e il percorso in Azure.

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

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Ottenere la catena di log di custodia dopo la cancellazione dei dati

Dopo che i dati sono stati cancellati dai dischi Data Box in base alle linee guida del NIST SP 800-88 Revisione 1, è disponibile la catena dei log di custodia. Questi log includono i log di controllo e la cronologia degli ordini. Il BOM o i file manifesto vengono copiati anche con i log di controllo.

### <a name="audit-logs"></a>Log di controllo

I log di controllo contengono informazioni su come accendere e accedere alle condivisioni nel Data Box o Data Box Heavy quando si trova all'esterno del Data Center di Azure. Questi log si trovano in:`storage-account/azuredatabox-chainofcustodylogs`

Di seguito è riportato un esempio di log di controllo da un Data Box:

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


## <a name="download-order-history"></a>Scarica cronologia ordini

La cronologia degli ordini è disponibile in portale di Azure. Se l'ordine è completo e la pulizia del dispositivo (cancellazione dei dati dai dischi) è completa, passare all'ordine del dispositivo e passare a **Order**details.  **Cronologia degli ordini di download**  è disponibile l'opzione. Per ulteriori informazioni, vedere [scaricare la cronologia degli ordini](data-box-portal-admin.md#download-order-history).

Scorrendo la cronologia degli ordini, viene visualizzato quanto segue:

- Informazioni di rilevamento del vettore per il dispositivo.
- Eventi con attività *SecureErase* . Questi eventi corrispondono alla cancellazione dei dati sul disco.
- Collegamenti di log Data Box. Vengono presentati i percorsi per i *log di controllo*, i *log di copia*e i file *DBA* .

Di seguito è riportato un esempio di log della cronologia degli ordini da portale di Azure:

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

- Informazioni su come [risolvere i problemi relativi alla data box e data box Heavy](data-box-troubleshoot.md).
