---
title: Aggiungere il connettore Google Drive alle app per la logica | Documentazione Microsoft
description: Panoramica del connettore Google Drive con i parametri dell&quot;API REST.
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2bcebc5-02d2-435b-b0da-ef53bc51c4b6
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: 5738c78d65ed0394028b90d634e5ebd4b1d5c5bc


---
# <a name="get-started-with-the-google-drive-connector"></a>Introduzione all'API Google Drive
Connettersi a Google Drive per creare file, recuperare righe e così via. Con Google Drive è possibile: 

* Creare il flusso aziendale in base ai dati ottenuti dalla ricerca. 
* Usare azioni per cercare immagini, notizie e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. È ad esempio possibile cercare un video e quindi usare Twitter per pubblicarlo in un feed Twitter.

Per aggiungere un'operazione alle app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Google Drive include le azioni seguenti. Non sono disponibili trigger. 

| Trigger | Azioni |
| --- | --- |
| Nessuno |<ul><li>Crea file</li><li>Inserisci riga</li><li>Copia file</li><li>Elimina file</li><li>Elimina riga</li><li>Estrai archivio in una cartella</li><li>Recupera contenuto di file tramite ID</li><li>Recupera contenuto di file tramite percorso</li><li>Recupera metadati di file tramite ID</li><li>Recupera metadati di file tramite percorso</li><li>Ottenere la riga</li><li>Aggiorna file</li><li>Aggiorna riga</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.

## <a name="create-the-connection-to-google-drive"></a>Creare una connessione a Google Drive
Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Google Drive.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

Dopo aver creato la connessione immettere le proprietà di Google Drive, ad esempio nome file o percorso cartella. Tali proprietà vengono descritte nelle **Informazioni di riferimento sulle API REST** in questo argomento.

> [!TIP]
> È possibile usare la stessa connessione di Google Drive in altre app per la logica.
> 
> 

## <a name="swagger-rest-api-reference"></a>Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### <a name="create-file"></a>Crea file
Carica un file in Google Drive.  
```POST: /datasets/default/files```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |yes |query |Nessuno |Percorso della cartella per caricare il file in Google Drive |
| Nome |string |yes |query |Nessuno |Nome del file da creare in Google Drive |
| body |string(binary) |Sì |body |Nessuno |Contenuto del file da creare in Google Drive |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="insert-row"></a>Inserisci riga
Inserisce una riga in Google Sheet.  
```POST: /datasets/{dataset}/tables/{table}/items```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |Nessuno |Identificatore univoco del file di Google Sheet |
| table |string |Sì |path |Nessuno |Identificatore univoco del foglio di lavoro |
| item |ItemInternalId: string |Sì |body |Nessuno |Riga da inserire nel foglio specificato |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="copy-file"></a>Copia file
Copia un file in Google Drive.  
```POST: /datasets/default/copyFile```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| source |string |yes |query |nessuno |URL del file di origine |
| destination |string |yes |query |Nessuno |Percorso file di destinazione in Google Drive incluso nome file di destinazione |
| overwrite |boolean |no |query |nessuno |Sovrascrive il file di destinazione se è impostata su 'true' |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="delete-file"></a>Elimina file
Elimina un file da Google Drive.  
```DELETE: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |Nessuno |Identificatore univoco del file da eliminare da Google Drive |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="delete-row"></a>Elimina riga
Elimina una riga da Google Sheet.  
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |Nessuno |Identificatore univoco del file di Google Sheet |
| table |string |Sì |path |Nessuno |Identificatore univoco del foglio di lavoro |
| id |string |Sì |path |Nessuno |Identificatore univoco della riga da eliminare |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="extract-archive-to-folder"></a>Estrai archivio in una cartella
Estrae un file di archivio in una cartella di Google Drive, ad esempio con estensione zip.  
```POST: /datasets/default/extractFolderV2```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| source |string |yes |query |nessuno |Percorso del file di archivio |
| destination |string |yes |query |Nessuno |Percorso in Google Drive in cui estrarre il contenuto dell'archivio |
| overwrite |boolean |no |query |nessuno |Sovrascrive i file di destinazione se è impostata su 'true' |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-file-content-using-id"></a>Recupera contenuto di file tramite ID
Recupera il contenuto di file da Google Drive tramite ID.  
```GET: /datasets/default/files/{id}/content```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |Nessuno |Identificatore univoco del file da recuperare in Google Drive |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-file-content-using-path"></a>Recupera contenuto di file tramite percorso
Recupera il contenuto di file da Google Drive tramite percorso.  
```GET: /datasets/default/GetFileContentByPath```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| path |string |yes |query |Nessuno |Percorso del file in Google Drive |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-file-metadata-using-id"></a>Recupera metadati di file tramite ID
Recupera i metadati dei file da Google Drive tramite ID.  
```GET: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |Nessuno |Identificatore univoco del file in Google Drive |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-file-metadata-using-path"></a>Recupera metadati di file tramite percorso
Recupera i metadati dei file da Google Drive tramite percorso.  
```GET: /datasets/default/GetFileByPath```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| path |string |yes |query |Nessuno |Percorso del file in Google Drive |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-row"></a>Ottenere la riga
Recupera una singola riga da Google Sheet.  
```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |Nessuno |Identificatore univoco del file di Google Sheet |
| table |string |Sì |path |Nessuno |Identificatore univoco del foglio di lavoro |
| id |string |Sì |path |Nessuno |Identificatore univoco della riga da recuperare |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="update-file"></a>Aggiorna file
Aggiorna un file in Google Drive.  
```PUT: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |Nessuno |Identificatore univoco del file da aggiornare in Google Drive |
| body |string(binary) |Sì |body |Nessuno |Contenuto del file da creare in Google Drive |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="update-row"></a>Aggiorna riga
Aggiorna una riga in Google Sheet.  
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |Sì |path |Nessuno |Identificatore univoco del file di Google Sheet |
| table |string |Sì |path |Nessuno |Identificatore univoco del foglio di lavoro |
| id |string |Sì |path |Nessuno |Identificatore univoco della riga da aggiornare |
| item |ItemInternalId: string |Sì |body |Nessuno |Riga con i valori aggiornati |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetto
#### <a name="datasetsmetadata"></a>DataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| tabular |non definito |no |
| BLOB |non definito |no |

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| una sezione source |string |no |
| displayName |string |no |
| urlEncoding |string |no |
| tableDisplayName |string |no |
| tablePluralName |string |no |

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| una sezione source |string |no |
| displayName |string |no |
| urlEncoding |string |no |

#### <a name="blobmetadata"></a>BlobMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |string |no |
| Nome |string |no |
| displayName |string |no |
| path |string |no |
| LastModified |string |no |
| Dimensione |integer |no |
| MediaType |string |no |
| IsFolder |boolean |no |
| ETag |string |no |
| FileLocator |string |no |

#### <a name="tablemetadata"></a>TableMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |string |no |
| title |string |no |
| x-ms-permission |string |no |
| schema |non definito |no |

#### <a name="tableslist"></a>TablesList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |no |

#### <a name="table"></a>tabella
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Nome |string |no |
| displayName |string |no |

#### <a name="item"></a>item
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ItemInternalId |string |no |

#### <a name="itemslist"></a>ItemsList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |no |

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all' [elenco di API](apis-list.md).

<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png



<!--HONumber=Nov16_HO3-->


