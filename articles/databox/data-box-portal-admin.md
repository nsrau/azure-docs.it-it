---
title: Gestire Azure Data Box, Azure Data Box Heavy tramite portale di Azure | Microsoft Docs
description: Viene descritto come utilizzare il portale di Azure per amministrare i Azure Data Box e Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 978d1de158f93b06d08ff93605405573000b6b6e
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92123716"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Usare il portale di Azure per amministrare i Azure Data Box e Azure Data Box Heavy

Questo articolo si applica sia a Azure Data Box sia a Azure Data Box Heavy. Questo articolo descrive alcuni dei flussi di lavoro complessi e le attività di gestione che possono essere eseguite sul dispositivo Azure Data Box. È possibile gestire il dispositivo Data Box tramite la portale di Azure o tramite l'interfaccia utente Web locale.

Questo articolo è incentrato sulle attività che è possibile eseguire con il portale di Azure. Usare il portale di Azure per gestire gli ordini, gestire Data Box dispositivo e tenere traccia dello stato dell'ordine mentre procede fino al completamento.

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]


## <a name="cancel-an-order"></a>Annullare un ordine

Per vari motivi potrebbe essere necessario annullare un ordine effettuato. 

Per gli ordini di importazione ed esportazione è possibile annullare l'ordine solo prima dell'elaborazione dell'ordine. Dopo che l'ordine è stato elaborato e Data Box dispositivo è stato preparato, non è possibile annullare l'ordine.

Seguire questa procedura per annullare un ordine.

1.  Passare a **Panoramica > Annulla**.

    ![Annullamento ordine 1](media/data-box-portal-admin/cancel-order1.png)

2.  Immettere un motivo per l'annullamento dell'ordine.  

    ![Annullamento ordine 2](media/data-box-portal-admin/cancel-order2.png)

3.  Dopo l'annullamento dell'ordine il portale ne aggiorna lo stato e lo visualizza come **Annullato**.

## <a name="clone-an-order"></a>Clonare un ordine

La clonazione è utile in determinate situazioni. Ad esempio, un utente ha usato Data Box per trasferire alcuni dati. Quando vengono generati più dati, è necessario un altro dispositivo Data Box per trasferire i dati in Azure. In questo caso è possibile semplicemente clonare lo stesso ordine.

> [!IMPORTANT]
> La clonazione non è disponibile per gli ordini di esportazione. È possibile clonare solo gli ordini di importazione.

Per clonare un ordine di importazione, seguire questa procedura.

1.  Passare a **Panoramica > Clona**. 

    ![Clonazione ordine 1](media/data-box-portal-admin/clone-order1.png)

2.  Tutti i dettagli dell'ordine rimangono invariati. Il nome dell'ordine è quello dell'ordine originale con l'aggiunta di *-Clone*. Selezionare la casella di controllo per confermare di avere esaminato le informazioni sulla privacy. Scegliere **Crea**.

Il clone viene creato in pochi minuti e il portale viene aggiornato per mostrare il nuovo ordine.


## <a name="delete-order"></a>Eliminare un ordine

È possibile eliminare un ordine quando l'ordine è stato completato. L'ordine contiene informazioni personali, ad esempio nome e cognome, indirizzo e informazioni di contatto. Queste informazioni personali vengono eliminate quando l'ordine viene eliminato.

È possibile eliminare solo gli ordini completati o annullati. Eseguire la procedura seguente per eliminare un ordine.

1. Passare a **tutte le risorse**. Cercare l'ordine.

2. Fare clic sull'ordine da eliminare e passare a **Panoramica**. Nella barra dei comandi fare clic su **Elimina**.

    ![Eliminazione ordine 1 in Data Box](media/data-box-portal-admin/delete-order1.png)

3. Immettere il nome dell'ordine quando viene richiesto di confermare l'eliminazione dell'ordine. Fare clic su **Elimina**.

## <a name="download-shipping-label"></a>Scaricare l'etichetta di spedizione

Potrebbe essere necessario scaricare l'etichetta indirizzo se la visualizzazione elettronica dell'etichetta del Data Box non funziona e non viene visualizzata l'etichetta indirizzo di restituzione. Non è presente alcuna visualizzazione E-Ink sul Data Box Heavy e pertanto il flusso di lavoro non si applica ai Data Box Heavy.

Eseguire la procedura seguente per scaricare un'etichetta di spedizione.

1.  Passare a **Panoramica > Scarica etichetta di spedizione**. Questa opzione è disponibile solo dopo la spedizione del dispositivo. 

    ![Scaricare l'etichetta di spedizione](media/data-box-portal-admin/download-shipping-label.png)

2.  Questa azione scarica la seguente etichetta per la spedizione di ritorno. Salvare l'etichetta e stamparla. Ripiegare e inserire l'etichetta nel manicotto chiaro del dispositivo. Assicurarsi che l'etichetta sia visibile. Rimuovere tutti gli adesivi sul dispositivo relativi alla spedizione precedente.

    ![Esempio di etichetta di spedizione](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Modificare l'indirizzo di spedizione

Potrebbe essere necessario modificare l'indirizzo di spedizione dopo che è stato effettuato l'ordine. Questa opzione è disponibile solo fino alla spedizione del dispositivo. Dopo la spedizione del dispositivo, questa opzione non sarà più disponibile.

Eseguire la procedura seguente per modificare l'ordine.

1. Passare a **Dettagli ordine > Modifica indirizzo di spedizione**.

    ![Modifica indirizzo di spedizione 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. Modificare e convalidare l'indirizzo di spedizione e quindi salvare le modifiche.

    ![Modifica indirizzo di spedizione 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Modificare i dettagli della notifica

Potrebbe essere necessario cambiare gli utenti che riceveranno i messaggi di posta elettronica sullo stato dell'ordine. Ad esempio, un utente deve essere informato quando il dispositivo viene recapitato o ritirato. Potrebbe essere necessario informare un altro utente quando la copia dei dati è completata, in modo che possa verificare che i dati siano nell'account di archiviazione di Azure prima di eliminarli dall'origine. In questi casi è possibile modificare i dettagli di notifica.

Eseguire la procedura seguente per modificare i dettagli di notifica.

1. Passare a **Dettagli ordine > Modifica dettagli notifica**.

    ![Modifica dettagli notifica 1](media/data-box-portal-admin/edit-notification-details1.png)

2. È possibile ora modificare i dettagli di notifica e quindi salvare le modifiche.
 
    ![Modifica dettagli notifica 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>Scaricare la cronologia ordini

Dopo il completamento dell'ordine per Data Box, i dati nei dischi del dispositivo vengono cancellati. Quando la pulizia del dispositivo è stata completata, è possibile scaricare la cronologia ordini nel portale di Azure.

Eseguire i passaggi seguenti per scaricare la cronologia ordini.

1. Nell'ordine per Data Box passare a **Panoramica**. Assicurarsi che l'ordine sia completo. Se l'ordine è completo e la pulizia del dispositivo è stata completata, passare a **Dettagli ordine**. L'opzione **Scarica cronologia ordini** è disponibile.

    ![Scaricare la cronologia ordini](media/data-box-portal-admin/download-order-history-1.png)

2. Fare clic su **Scarica cronologia ordini**. Nella cronologia scaricata sono registrati i documenti di accompagnamento del corriere. Sono presenti due set di log corrispondenti ai due nodi in una Data Box Heavy. Se si scorre fino alla fine di questo log, sono visualizzati i collegamenti a:
    
   - **Copiare i log** : l'elenco dei file che hanno avuto errore durante la copia dei dati da data box all'account di archiviazione di Azure (ordine di importazione) o viceversa (ordine *di* esportazione).
   - **Log di controllo** : contengono informazioni su come accendere e accedere alle condivisioni nel data box quando si trova all'esterno del Data Center di Azure.
   - **File DBA nell'ordine di importazione** : l'elenco dei file (noto anche come manifesto del file) che è possibile scaricare durante **prepara per la spedizione** e i nomi di file, le dimensioni dei file e i checksum del file.
   - **Log dettagliati nell'ordine di esportazione** : l'elenco dei file con i nomi file, le dimensioni dei file e il calcolo del checksum quando i dati sono stati copiati dagli account di archiviazione di Azure al data box.

   Di seguito è riportato un esempio di cronologia degli ordini da un ordine di importazione.

    ```output
    -------------------------------
    Microsoft Data Box Order Report
    -------------------------------
    Name                                               : DataBoxTestOrder                              
    StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
    DeviceType                                         : DataBox                                           
    -------------------
    Data Box Activities
    -------------------
    Time(UTC)                 | Activity                       | Status          | Description  
    
    10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
    11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
    11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
    11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
    11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
    11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
    11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
    11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
    11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
    11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
    11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
    11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
    11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
    11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
    11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
    11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
    1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
    1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
    1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
    1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
    1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
    1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
    1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
    1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
    1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
    1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
    1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
    1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
    1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
    1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
    1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
    1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
    1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
    1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
    2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
    2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
    2/4/2019 8:01:10 PM       | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

    ------------------
    Data Box Log Links
    ------------------

    Account Name         : Gus                                                       
    Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
    Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
    BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
    ```

    È quindi possibile passare all'account di archiviazione e visualizzare i log di copia.

![Log negli account di archiviazione](media/data-box-portal-admin/logs-in-storage-acct-2.png)

È anche possibile visualizzare la catena di log di custodia che includono i log di controllo e i file della distinta base.

![Log negli account di archiviazione 2](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>Visualizzare lo stato dell'ordine

Quando viene modificato lo stato del dispositivo nel portale, si riceve una notifica tramite un messaggio di posta elettronica.

### <a name="statuses-for-import-order"></a>Stati per l'ordine di importazione

Ecco gli Stati per un ordine di importazione.

|Stato dell'ordine |Descrizione |
|---------|---------|
|Ordinato     | L'ordine è stato effettuato. <br>Se il dispositivo è disponibile, Microsoft identifica un dispositivo per la spedizione e ne prepara l'imballaggio. <br> Se il dispositivo non è immediatamente disponibile, l'ordine verrà elaborato quando il dispositivo risulterà nuovamente disponibile. L'ordine potrebbe richiedere da diversi giorni a due mesi per l'elaborazione. Se l'ordine non può essere soddisfatto entro 90 giorni, viene annullato e l'utente riceve una notifica.         |
|Elaborato     | L'elaborazione dell'ordine è stata completata. In base all'ordine, il dispositivo viene preparato per la spedizione nel data center.         |
|Spedito     | L'ordine è stato spedito. Usare l'ID di traccia visualizzato nell'ordine nel portale per tenere traccia della spedizione.        |
|Recapitato     | La spedizione è stata recapitata all'indirizzo specificato.        |
|Ritirato     |La spedizione di restituzione è stata prelevata e scansionata dal trasportatore.         |
|Ricevuto     | Il dispositivo viene ricevuto e scansionato nel data center di Azure. <br> Dopo il controllo della spedizione viene avviato il caricamento del dispositivo.      |
|Copia dati     | La copia dei dati è in corso. Tenere traccia dello stato della copia dell'ordine nel portale di Azure. <br> Attendere il completamento della copia dei dati. |
|Completato       |L'ordine è stato completato.<br> Verificare che i dati siano in Azure prima di eliminare i dati locali dai server.         |
|Operazione completata con errori| La copia dei dati è stata completata ma si sono verificati errori durante la copia. <br> Esaminare i log di copia usando il percorso specificato nel portale di Azure. Vedere [esempi di log di copia quando il caricamento è stato completato con errori](./data-box-logs.md#upload-completed-with-errors).   |
|Completato con avvisi| La copia dei dati è stata completata, ma i dati sono stati modificati. I dati contengono errori di BLOB o di file non critici corretti cambiando il nome del file o del BLOB. <br> Esaminare i log di copia usando il percorso specificato nel portale di Azure. Prendere nota delle modifiche apportate ai dati. Vedere [esempi di log di copia quando il caricamento è stato completato con avvisi](./data-box-logs.md#upload-completed-with-warnings).   |
|Cancellati            |L'ordine è stato annullato. <br> L'ordine è stato annullato o si è verificato un errore e il servizio ha annullato l'ordine. Se l'ordine non può essere soddisfatto entro 90 giorni, viene anche annullato e l'utente riceve una notifica.     |
|Eseguire la pulizia | I dati nei dischi del dispositivo vengono cancellati. La pulizia del dispositivo viene considerata completa quando la cronologia ordini è disponibile per il download nel portale di Azure.|

### <a name="statuses-for-export-order"></a>Stati per l'ordine di esportazione

Ecco gli Stati per un ordine di esportazione.

|Stato dell'ordine |Descrizione |
|---------|---------|
|Ordinato     | L'ordine di esportazione è stato inserito correttamente. <br>Se il dispositivo è disponibile, Microsoft identifica un dispositivo per la spedizione e ne prepara l'imballaggio. <br> Se il dispositivo non è immediatamente disponibile, l'ordine verrà elaborato quando il dispositivo risulterà nuovamente disponibile. L'ordine potrebbe richiedere da diversi giorni a due mesi per l'elaborazione. Se l'ordine non può essere soddisfatto entro 90 giorni, viene annullato e l'utente riceve una notifica.         |
|Cancellati            |L'ordine è stato annullato. <br> L'ordine è stato annullato (è possibile annullare solo prima dell'elaborazione dell'ordine) oppure si è verificato un errore e il servizio ha annullato l'ordine. Se l'ordine non può essere soddisfatto entro 90 giorni, viene anche annullato e l'utente riceve una notifica.     |
|Elaborato     | L'elaborazione dell'ordine è stata completata. In base all'ordine, il dispositivo viene preparato per la copia dei dati nel Data Center. Vengono create le condivisioni del dispositivo.         |
|La copia dei dati è in corso     | La copia dei dati dagli account di archiviazione di Azure specificati al dispositivo è in corso. Tenere traccia dello stato della copia dell'ordine nel portale di Azure. <br> Attendere il completamento della copia dei dati. |
|Copia completata     | La copia dei dati dagli account di archiviazione di Azure specificati al dispositivo è stata completata. Un file di log dettagliato (se l'opzione è stata abilitata nell'ordine) e viene creato un log di copia nell'account di archiviazione. Il log dettagliato contiene le informazioni su tutti i file (nome, percorso, checksum di calcolo) che vengono copiati nel dispositivo. Il log di copia contiene il riepilogo del processo di copia, incluso un elenco di file che non è stato possibile copiare a causa di eventuali errori.<br> I dati dell'account di archiviazione rimangono invariati. |
|Copia completata con errori| La copia dei dati è stata completata ma si sono verificati errori durante la copia. <br> Esaminare i log di copia nell'account di archiviazione di Azure usando il percorso fornito nella portale di Azure. Vedere [esempi di log di copia quando il download è stato completato con errori](./data-box-logs.md#upload-completed-with-errors).   |
|Copia completata con avvisi| La copia dei dati dall'account di archiviazione di Azure è stata completata, ma i dati contengono errori non critici. <br> Esaminare i log di copia usando il percorso specificato nel portale di Azure. Prendere nota degli errori non critici. Vedere [esempi di log di copia quando il download è stato completato con avvisi](./data-box-logs.md#upload-completed-with-warnings).   |
|Copia non riuscita con errori| La copia dei dati dall'account di archiviazione di Azure non è riuscita e l'ordine è terminato. Un dispositivo non verrà spedito.<br> Esaminare i log di copia nell'account di archiviazione di Azure usando il percorso fornito nella portale di Azure. Vedere [esempi di log di copia quando il download non è riuscito con errori](./data-box-logs.md#upload-completed-with-errors).   |
|Spedito     |L'ordine è stato spedito. Usare l'ID di traccia visualizzato nell'ordine nel portale per tenere traccia della spedizione.        |
|Recapitato     |La spedizione è stata recapitata all'indirizzo specificato.        |
|Ritirato     |La spedizione di restituzione è stata prelevata e scansionata dal trasportatore.         |
|Ricevuto     | Il dispositivo viene ricevuto e scansionato nel data center di Azure. <br> La spedizione viene controllata.      |
|Completato           |L'ordine è completo.     |
|Eseguire la pulizia | I dati nei dischi del dispositivo vengono cancellati. La pulizia del dispositivo viene considerata completa quando la cronologia ordini è disponibile per il download nel portale di Azure.|

> [!NOTE]
> Se il processo di copia per esportare i dati da account di archiviazione di Azure a Data Box viene completato con errori o avvisi, il dispositivo viene ancora fornito. Solo in caso di errore di copia, l'ordine viene terminato e il dispositivo non viene spedito.


Se si usa il trasporto gestito autonomamente, dopo aver completato la copia e prima di ricevere il dispositivo, vengono visualizzati gli Stati seguenti, anziché quelli indicati nella tabella precedente:

|Stato dell'ordine |Descrizione |
|---------|---------|
|Pronto per il ritiro nel Data Center di Azure      |Il dispositivo è pronto per essere selezionato nel Data Center di Azure.        |
|Ritirato    |Il dispositivo è stato selezionato.         |
|Pronto per la ricezione nel Data Center di Azure     |Il dispositivo è pronto per la ricezione nel Data Center di Azure.        |
|Ricevuto     |Il dispositivo viene ricevuto nel Data Center di Azure.      |





## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [risolvere data box e data box Heavy problemi](data-box-troubleshoot.md).