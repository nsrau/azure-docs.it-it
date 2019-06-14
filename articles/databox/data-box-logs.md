---
title: Tenere traccia e log di Azure Data Box, gli eventi di Azure dati casella pesante | Microsoft Docs
description: Viene descritto come tenere traccia e registra gli eventi nelle varie fasi dell'ordine pesante finestra dati di Azure e Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 108d17d3e0ca5f32648f9d4f6cf4b5f9a2984d0c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66495813"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Rilevamento e la registrazione eventi di Azure Data Box e Azure dati casella pesante

Un ordine di Data Box o elevato di finestra di dati passa attraverso questa procedura: ordinare, configurare, dati copiare, restituiranno, caricare in Azure e verificare e cancellazione dei dati. Corrispondente a ogni passaggio nell'ordine, è possibile eseguire più azioni per controllare l'accesso all'ordine, gli eventi di controllo, tenere traccia dell'ordine e interpretare i vari registri che vengono generati.

Nella tabella seguente mostra un riepilogo dei passaggi di ordine di Data Box o elevato di finestra di dati e gli strumenti disponibili per tenere traccia e controllare l'ordine durante ogni passaggio.

| Fase dell'ordine di casella dei dati       | Strumento per tenere traccia e di controllo                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Crea ordine               | [Impostare il controllo di accesso dell'ordine tramite RBAC](#set-up-access-control-on-the-order)                                                    |
| Ordine di elaborazione            | [Tenere traccia dell'ordine](#track-the-order) tramite <ul><li> Portale di Azure </li><li> Sito Web del vettore di spedizione </li><li>Notifiche tramite posta elettronica</ul> |
| Configurare il dispositivo              | Dispositivo credenziali di accesso connesso [i log attività](#query-activity-logs-during-setup)                                              |
| Copia di dati nel dispositivo        | [Vista *error.xml* file](#view-error-log-during-data-copy) per la copia dati                                                             |
| Preparare per la spedizione            | [Esaminare i file della distinta base](#inspect-bom-during-prepare-to-ship) o i file manifesto nel dispositivo                                      |
| Caricamento dei dati in Azure       | [Revisione *copylogs* ](#review-copy-log-during-upload-to-azure) per gli errori durante i dati di caricamento in Data Center di Azure                         |
| Cancellazione dei dati dal dispositivo   | [Visualizzare la catena di custodia log](#get-chain-of-custody-logs-after-data-erasure) inclusi i log di controllo e cronologia degli ordini                                                   |

Questo articolo descrive in dettaglio i vari meccanismi o gli strumenti disponibili per tenere traccia e controllare l'ordine di Data Box o elevato di finestra di dati. Le informazioni contenute in questo articolo si applicano a entrambi, Data Box e pesante finestra di dati. Nelle sezioni successive, tutti i riferimenti a Data Box si applicano anche a pesanti finestra di dati.

## <a name="set-up-access-control-on-the-order"></a>Impostare il controllo di accesso dell'ordine

È possibile controllare chi può accedere il tuo ordine quando l'ordine viene inizialmente creato. Configurare i ruoli di controllo di accesso basato sui ruoli (RBAC) in vari ambiti per controllare l'accesso all'ordine di Data Box. Un ruolo RBAC determina il tipo di accesso: lettura / scrittura, sola lettura, lettura / scrittura a un sottoinsieme di operazioni.

I due ruoli che possono essere definiti per il servizio Azure Data Box sono:

- **Lettore di dati finestra** -hanno accesso in lettura a un ordine di base a quanto definito dall'ambito. Possono solo visualizzare i dettagli di un ordine. Essi non è possibile accedere a eventuali altri dettagli relativi agli account di archiviazione o modificare i dettagli dell'ordine, ad esempio indirizzo e così via.
- **Collaboratore alla finestra di dati** -può creare solo un ordine per trasferire i dati in un account di archiviazione specificato *se hanno già accesso in scrittura a un account di archiviazione*. Se non hanno accesso a un account di archiviazione, essi non è possibile creare anche un ordine di Data Box per copiare i dati nell'account di. Questo ruolo non definisce alcun account di archiviazione correlato, né concede le autorizzazioni di accesso agli account di archiviazione.  

Per limitare l'accesso a un ordine, è possibile:

- Assegnare un ruolo a livello di ordine. L'utente abbia tali autorizzazioni solo come definito dai ruoli di interagire con tale ordine di Data Box specifico solo e nient'altro.
- Assegnare un ruolo a livello di gruppo di risorse, l'utente può accedere a tutti gli ordini Data Box all'interno di un gruppo di risorse.

Per altre informazioni sull'uso RBAC suggerita, vedere [procedure consigliate per RBAC](../role-based-access-control/overview.md#best-practice-for-using-rbac).

## <a name="track-the-order"></a>Monitorare l'ordine

È possibile tenere traccia dell'ordine tramite il portale di Azure e il sito Web del vettore di spedizione. I meccanismi seguenti sono in grado di tenere traccia dell'ordine di Data Box in qualsiasi momento:

- Per tenere traccia dell'ordine quando il dispositivo si trova nel Data Center di Azure o locale, vedere le **ordine di Data Box > Panoramica** nel portale di Azure.

    ![Visualizzare lo stato dell'ordine e al rilevamento no](media/data-box-logs/overview-view-status-1.png)

- Per tenere traccia dell'ordine, mentre il dispositivo è in transito, visitare il sito Web del vettore a livello di area, ad esempio, sito Web di UPS negli Stati Uniti. Specificare il numero di tracciabilità associato il tuo ordine.
- Data Box invia inoltre notifiche tramite posta elettronica ogni volta che le modifiche dello stato dell'ordine basano su messaggi di posta elettronica forniti quando l'ordine è stato creato. Per un elenco di tutti gli stati degli ordini di Data Box, vedere [visualizzare lo stato dell'ordine](data-box-portal-admin.md#view-order-status). Per modificare le impostazioni di notifica associate all'ordine, vedere [modifica i dettagli della notifica](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Log attività di query durante l'installazione

- Il Data Box arriva in locale in uno stato bloccato. È possibile usare le credenziali del dispositivo disponibili nel portale di Azure per il tuo ordine.  

    Quando una Data Box è configurato, potrebbe essere necessario sapere che hanno tutti accesso le credenziali del dispositivo. Per determinare chi ha avuto accesso il **credenziali del dispositivo** pannello, è possibile eseguire una query dei log attività.  Qualsiasi azione che richiede l'accesso alle **dettagli del dispositivo > credenziali** blade viene registrato nei log attività come `ListCredentials` azione.

    ![Eseguire query sui log attività](media/data-box-logs/query-activity-log-1.png)

- Ogni accesso in Data Box è registrato in tempo reale. Tuttavia, queste informazioni sono disponibile solo nel [log di controllo](#audit-logs) dopo che l'ordine è stato completato correttamente.

## <a name="view-error-log-during-data-copy"></a>Visualizza log degli errori durante la copia dei dati

Durante la copia dei dati per Data Box o elevato di finestra di dati, viene generato un file di errore se si verificano problemi con i dati da copiare.

### <a name="errorxml-file"></a>File Error.Xml

Assicurarsi che i processi di copia è sono completata senza errori. Se sono presenti errori durante il processo di copia, scaricare i log dal **Connetti e copia** pagina.

- Se è stato copiato un file che è allineati in una cartella di disco gestito nella casella di dati non 512 byte, il file non viene caricato come blob di pagine per l'account di archiviazione di staging. Verrà visualizzato un errore nei log. Rimuovere il file e copiare un file allineato su 512 byte.
- Se è stato copiato un file VHDX, o un disco rigido virtuale dinamico o un disco rigido virtuale differenze (questi file non sono supportati), si verrà visualizzato un errore nei log.

Di seguito è riportato un esempio del *error.xml* per diversi errori durante la copia di dischi gestiti.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Di seguito è riportato un esempio del *error.xml* per errori diversi quando si copiano i BLOB di pagine.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Di seguito è riportato un esempio del *error.xml* per errori diversi quando si copiano i BLOB in blocchi.

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

Di seguito è riportato un esempio del *error.xml* diversi errori durante la copia di file di Azure.

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

In ognuno dei casi precedenti, risolvere gli errori prima di procedere al passaggio successivo. Per altre informazioni sugli errori ricevuti durante la copia dei dati in Data Box tramite i protocolli SMB o NFS, visitare [problemi di risolvere i problemi di Data Box e dati casella pesante](data-box-troubleshoot.md). Per informazioni sugli errori ricevuti durante la copia dei dati in Data Box tramite REST, vedere [problemi di archiviazione di risolvere i problemi di dati finestra Blob](data-box-troubleshoot-rest.md).

## <a name="inspect-bom-during-prepare-to-ship"></a>Esaminare i BOM durante la preparazione per la spedizione

Durante la preparazione per la spedizione, un elenco di file noti come la distinta base (BOM) o file manifesto viene creato.

- Utilizzare questo file per verificare i nomi effettivi e il numero di file che sono stati copiati nel Data box.
- Usare questo file per verificare le dimensioni effettive dei file.
- Verificare che il *crc64* corrisponde a una stringa diversa da zero. <!--A null value for crc64 indicates that there was a reparse point error)-->

Per altre informazioni sugli errori ricevuti durante la preparazione inviare, passare a [problemi di risolvere i problemi di Data Box e dati casella pesante](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>File della distinta base o manifesto

Il BOM o il file manifesto contiene l'elenco di tutti i file vengono copiati nel dispositivo Data Box. Il file della distinta base ha nomi di file e le dimensioni corrispondenti, nonché il valore di checksum. Viene creato un file della distinta base separato per i BLOB in blocchi, BLOB di pagine, file di Azure per la copia tramite le API REST e per la copia a managed disks in Data Box. È possibile scaricare i file della distinta base dal web locale dell'interfaccia utente del dispositivo durante la preparazione per la spedizione.

Inoltre, questi file si trovano nel dispositivo Data Box e vengono caricati nell'account di archiviazione associato in Data Center di Azure di.

### <a name="bom-file-format"></a>Formato di file della distinta base

File della distinta base o manifesto ha il formato generale seguente:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Ecco un esempio di un manifesto generato quando i dati siano stati copiati nella condivisione di blob di blocco in Data Box.

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

Il BOM o i file manifesto vengono inoltre copiati nell'account di archiviazione di Azure. È possibile usare il carattere BOM o manifesto i file per verificare che i file caricati in Azure corrispondono ai dati nel Data box è stati copiati.

## <a name="review-copy-log-during-upload-to-azure"></a>Esaminare i log di copia durante il caricamento in Azure

Durante il caricamento dei dati in Azure, un *copylog* viene creato.

### <a name="copylog"></a>Copylog

Per ogni ordine di elaborazione, viene creato il servizio Data Box *copylog* nell'account di archiviazione associato. Il *copylog* ha il numero totale di file che sono stati caricati e il numero di file che hanno generato errori durante i dati di copia da Data Box per l'account di archiviazione di Azure.

Un calcolo di controllo di ridondanza ciclico (CRC) viene eseguito durante il caricamento in Azure. Il CRC dalla copia dei dati e dopo il caricamento dei dati devono essere confrontati. Un errore CRC indica che i file corrispondenti non è riuscito a caricare.

Per impostazione predefinita, i log vengono scritti in un contenitore denominato copylog. I log vengono archiviati con la convenzione di denominazione seguente:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Il percorso copylog verrà visualizzato anche nella **Panoramica** pannello per il portale.

![Percorso copylog nel Pannello di panoramica al termine](media/data-box-logs/copy-log-path-1.png)

L'esempio seguente viene descritto il formato generale di un file copylog per una casella di dati di caricamento completata correttamente:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

Caricamento in Azure possa anche completare con errori.

![Percorso copylog nel Pannello di panoramica quando è stata completata con errori](media/data-box-logs/copy-log-path-2.png)

Di seguito è riportato un esempio di un copylog in cui il caricamento completato con errori:

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


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Ottenere catena di log custodia dopo la cancellazione dei dati

Dopo che i dati vengono cancellati dai dischi Data Box in base alle linee guida NIST SP 800-88 revisione 1, la catena di custodia log sono disponibili. Questi log includono i log di controllo e la cronologia degli ordini. Il BOM o i file manifesto vengono inoltre copiati con i log di controllo.

### <a name="audit-logs"></a>Log di controllo

I log di controllo contengono informazioni su power-on e condividere l'accesso in Data Box o pesante finestra di dati quando è esterno al Data Center di Azure. Questi log si trovano in: `storage-account/azuredatabox-chainofcustodylogs`

Ecco un esempio del log di controllo da una Data Box:

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

Cronologia degli ordini è disponibile nel portale di Azure. Se l'ordine è stata completata e la pulizia dei dispositivi (cancellazione dei dati dai dischi) è stata completata, quindi accedere al tuo ordine di dispositivo e passare a **Order details**. ** Scaricare la cronologia degli ordini** opzione è disponibile. Per altre informazioni, vedere [scaricare la cronologia degli ordini](data-box-portal-admin.md#download-order-history).

Se si scorre la cronologia degli ordini, vedere:

- Vettore di informazioni di rilevamento per il dispositivo.
- Gli eventi con *SecureErase* attività. Questi eventi corrispondono per la cancellazione dei dati sul disco.
- Collegamenti di log di dati finestra. I percorsi per i *log di controllo*, *copylogs*, e *BOM* vengono presentati i file.

Ecco un esempio del log della cronologia dell'ordine dal portale di Azure:

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

- Informazioni su come [risolvere i problemi nel Data Box e dati casella pesante](data-box-troubleshoot.md).
