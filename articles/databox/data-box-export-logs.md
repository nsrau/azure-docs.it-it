---
title: Rilevare e registrare Azure Data Box, Azure Data Box Heavy eventi per l'ordine di esportazione | Microsoft Docs
description: Viene descritto come rilevare e registrare eventi nelle varie fasi dell'Azure Data Box e Azure Data Box Heavy ordine di esportazione.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 0ef3135f572b00176ff2a114fd02db82c7a05bca
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86258392"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>Rilevamento e registrazione degli eventi per il Azure Data Box e Azure Data Box Heavy gli ordini di esportazione

Un ordine di esportazione Data Box o Data Box Heavy esegue i passaggi seguenti: ordine, configurazione, copia dati, ritorno e cancellazione dei dati. Corrispondente a ogni passaggio nell'ordine, è possibile eseguire più azioni per controllare l'accesso all'ordine, controllare gli eventi, tenere traccia dell'ordine e interpretare i vari log generati.

Questo articolo descrive in dettaglio i vari meccanismi o strumenti disponibili per tenere traccia degli ordini di esportazione e controllarli per Data Box o Data Box Heavy. Le informazioni contenute in questo articolo si applicano sia a Data Box sia a Data Box Heavy. Nelle sezioni successive, tutti i riferimenti a Data Box si applicano anche a Data Box Heavy.

Nella tabella seguente viene illustrato un riepilogo dei Data Box passaggi dell'ordine di esportazione e degli strumenti disponibili per tenere traccia e controllare l'ordine durante ogni passaggio.

| Data Box fase dell'ordine di esportazione       | Strumento di rilevamento e controllo                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Creare un ordine               | [Configurare il controllo di accesso nell'ordine tramite RBAC](#set-up-access-control-on-the-order) <br> [Abilita log dettagliato nell'ordine](#enable-verbose-log-in-the-order)                                                    |
| Ordine elaborato            | [Tenere traccia dell'ordine](#track-the-order) <ul><li> Portale di Azure </li><li> Sito Web del vettore di spedizione </li><li>Notifiche di posta elettronica</ul> |
| Configurare il dispositivo              | Credenziali del dispositivo accesso ai [log attività](#query-activity-logs-during-setup) registrate              |
| Copia dei dati dal dispositivo        | [Esaminare i log di copia](#copy-log) <br> [Esaminare i log dettagliati](#verbose-log) prima di copiare i dati            |
| Cancellazione dei dati dal dispositivo   | [Visualizza la catena dei log di custodia](#get-chain-of-custody-logs-after-data-erasure) , inclusi i log di controllo e la cronologia degli ordini                |


## <a name="set-up-access-control-on-the-order"></a>Configurare il controllo di accesso nell'ordine

È possibile controllare chi può accedere all'ordine quando viene creato per la prima volta. Configurare i ruoli di controllo degli accessi in base al ruolo (RBAC) in diversi ambiti per controllare l'accesso all'ordine di Data Box. Un ruolo di controllo degli accessi in base al ruolo determina il tipo di accesso, ovvero di lettura/scrittura, di sola lettura, di lettura e scrittura a un subset di operazioni.

I due ruoli che possono essere definiti per il servizio Azure Data Box sono:

- **Data Box Reader** -ha accesso in sola lettura a uno o più ordini definiti dall'ambito. Possono solo visualizzare i dettagli di un ordine. Non possono accedere ad altri dettagli correlati ad account di archiviazione o modificare i dettagli dell'ordine, ad esempio indirizzo e così via.
- **Collaboratore data box** : può creare solo un ordine per trasferire i dati a un account di archiviazione specifico *se hanno già accesso in scrittura a un account di archiviazione*. Se non hanno accesso a un account di archiviazione, non possono neanche creare un Data Box ordine di copiare i dati nell'account. Questo ruolo non definisce le autorizzazioni relative agli account di archiviazione né concede l'accesso agli account di archiviazione.  

Per limitare l'accesso a un ordine, è possibile:

- Assegnare un ruolo a livello di ordine. L'utente dispone solo delle autorizzazioni in base a quanto definito dai ruoli per interagire solo con l'ordine specifico Data Box e nient'altro.
- Assegnare un ruolo a livello di gruppo di risorse, l'utente ha accesso a tutti gli ordini di Data Box all'interno di un gruppo di risorse.

Per altre informazioni sull'uso di RBAC suggerito, vedere [procedure consigliate per](../role-based-access-control/best-practices.md)il controllo degli accessi in base al ruolo

## <a name="enable-verbose-log-in-the-order"></a>Abilita log dettagliato nell'ordine

Quando si inserisce un ordine di esportazione per Data Box, è possibile abilitare la raccolta di log dettagliato. Ecco la schermata dell'ordine in cui è possibile abilitare il log dettagliato:

![Seleziona l'opzione di esportazione](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

Quando si seleziona l'opzione **Includi log dettagliato** , viene generato un file di log dettagliato durante la copia dei dati dall'account di archiviazione di Azure. Questo log contiene un elenco di tutti i file che sono stati esportati correttamente.      

Per ulteriori informazioni sull'ordine di esportazione, vedere [creare un ordine di esportazione per data box](data-box-deploy-export-ordered.md)

## <a name="track-the-order"></a>Monitorare l'ordine

È possibile tenere traccia dell'ordine tramite il portale di Azure e tramite il sito Web del vettore di spedizione. Per tenere traccia dell'ordine di Data Box in qualsiasi momento, è necessario disporre dei meccanismi seguenti:

- Per tenere traccia dell'ordine quando il dispositivo si trova nel Data Center di Azure o in locale, passare alla **Panoramica dell'ordine di Data Box >** in portale di Azure.

    ![Visualizza stato ordine e rilevamento No](media/data-box-logs/overview-view-status-1.png)

- Per tenere traccia dell'ordine mentre il dispositivo è in transito, visitare il sito Web del vettore regionale, ad esempio il sito Web UPS in Microsoft. Fornire il numero di tracciabilità associato all'ordine.
- Data Box inoltre invia notifiche tramite posta elettronica ogni volta che lo stato dell'ordine cambia in base ai messaggi di posta elettronica forniti al momento della creazione dell'ordine. Per un elenco di tutti gli Stati dell'ordine di Data Box, vedere [visualizzare lo stato dell'ordine](data-box-portal-admin.md#view-order-status). Per modificare le impostazioni di notifica associate all'ordine, vedere [modificare i dettagli della notifica](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Log attività query durante l'installazione

- Il Data Box arriva in locale in uno stato bloccato. È possibile usare le credenziali del dispositivo disponibili nell'portale di Azure per l'ordine.  

    Quando viene configurata una Data Box, potrebbe essere necessario conoscere l'utente che ha eseguito l'accesso alle credenziali del dispositivo. Per determinare chi ha eseguito l'accesso al pannello delle **credenziali del dispositivo** , è possibile eseguire una query sui log attività.  Qualsiasi azione che prevede l'accesso ai **Dettagli del dispositivo > pannello credenziali** viene registrato nei log attività come `ListCredentials` azione.

    ![Eseguire query sui log attività](media/data-box-logs/query-activity-log-1.png)

- Ogni accesso all'Data Box viene registrato in tempo reale. Tuttavia, queste informazioni sono disponibili solo nella [catena di log di controllo di custodia](#chain-of-custody-audit-logs) dopo che l'ordine è stato completato correttamente.

## <a name="view-logs-during-data-copy"></a>Visualizzare i log durante la copia dei dati

Prima di copiare i dati dal Data Box, è possibile scaricare ed esaminare il *log di copia* e il *log dettagliato* per i dati copiati nel data box. Questi log vengono generati quando i dati vengono copiati dall'account di archiviazione in Azure al Data Box. 

### <a name="copy-log"></a>Copia log

Prima di copiare i dati dal Data Box, scaricare il log di copia dalla pagina **Connetti e copia** .

Di seguito è riportato un esempio di output del *log di copia* quando non sono presenti errori e tutti i file sono stati copiati durante la copia dei dati da Azure al dispositivo data box.

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
Di seguito è riportato un esempio di output quando il *log di copia* contiene errori e alcuni dei file non sono stati copiati da Azure.

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

Per esportare i file sono disponibili le opzioni seguenti: 

- È possibile trasferire i file che non possono essere copiati in rete. 
- Se la dimensione dei dati è maggiore della capacità del dispositivo utilizzabile, viene eseguita una copia parziale e tutti i file che non sono stati copiati sono elencati in questo log. È possibile utilizzare questo log come XML di input per creare un nuovo ordine di Data Box e quindi copiare tali file.

### <a name="verbose-log"></a>Log dettagliato

Il *log dettagliato* contiene un elenco di tutti i file che sono stati esportati correttamente dall'account di archiviazione di Azure. Il log contiene anche le dimensioni del file e il calcolo del checksum.

Il log dettagliato contiene le informazioni nel formato seguente:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Di seguito è riportato un esempio di output del log dettagliato. 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

I log dettagliati vengono copiati anche nell'account di archiviazione di Azure. Per impostazione predefinita, i log vengono scritti in un contenitore denominato `copylog`. I log vengono archiviati con la convenzione di denominazione seguente:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

Il percorso del log di copia viene visualizzato anche nel pannello **Panoramica** del portale.

<!-- add a screenshot-->

È possibile usare questi log per verificare che i file copiati da Azure corrispondano ai dati copiati nel server locale. 

Usare il file di log dettagliato:

- Per eseguire la verifica in base ai nomi effettivi e al numero di file copiati dal Data Box.
- Per verificare le dimensioni effettive dei file.
- Per verificare che *crc64* corrisponda a una stringa diversa da zero. Un calcolo del controllo di ridondanza ciclico (CRC) viene eseguito durante l'esportazione da Azure. Il CRC dall'esportazione e dopo la copia dei dati dal Data Box al server locale può essere confrontato. Una mancata corrispondenza CRC indica che non è stato possibile copiare correttamente i file corrispondenti.


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Ottenere la catena di log di custodia dopo la cancellazione dei dati

Dopo che i dati sono stati cancellati dai dischi Data Box in base alle linee guida del NIST SP 800-88 Revisione 1, è disponibile la catena dei log di custodia. Questi log includono la catena di log di controllo di custodia e la cronologia degli ordini. Il BOM o i file manifesto vengono copiati anche con i log di controllo.

### <a name="chain-of-custody-audit-logs"></a>Catena di log di controllo di custodia

La catena di log di controllo di custodia contiene informazioni sull'accensione e l'accesso alle condivisioni nel Data Box o Data Box Heavy quando si trova all'esterno del Data Center di Azure. Questi log si trovano in:`storage-account/azuredatabox-chainofcustodylogs`

Di seguito è riportato un esempio di log di controllo da un Data Box:

```output
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

La cronologia degli ordini è disponibile in portale di Azure. Se l'ordine è completo e la pulizia del dispositivo (cancellazione dei dati dai dischi) è completa, passare all'ordine del dispositivo e passare a **Order Details**. L'opzione **Scarica cronologia ordini** è disponibile. Per ulteriori informazioni, vedere [scaricare la cronologia degli ordini](data-box-portal-admin.md#download-order-history).

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




