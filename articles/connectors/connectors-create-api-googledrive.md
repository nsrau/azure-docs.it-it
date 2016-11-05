---
title: Aggiungere il connettore Google Drive alle app per la logica | Microsoft Docs
description: Panoramica del connettore Google Drive con i parametri dell'API REST.
services: ''
suite: ''
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: mandia

---
# Introduzione all'API Google Drive
Connettersi a Google Drive per creare file, recuperare righe e così via. Con Google Drive è possibile:

* Creare il flusso aziendale in base ai dati ottenuti dalla ricerca.
* Usare azioni per cercare immagini, notizie e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. È ad esempio possibile cercare un video e quindi usare Twitter per pubblicarlo in un feed Twitter.

Per aggiungere un'operazione alle app per la logica, vedere [Creare una nuova app per la logica che connette servizi SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger e azioni
Google Drive include le azioni seguenti. Non sono disponibili trigger.

| Trigger | Azioni |
| --- | --- |
| None |<ul><li>Crea file</li><li>Inserisci riga</li><li>Copia file</li><li>Elimina file</li><li>Elimina riga</li><li>Estrai archivio nella cartella</li><li>Ottieni contenuto file in base all'ID</li><li>Ottieni contenuto file in base al percorso</li><li>Ottieni metadati di file tramite ID</li><li>Ottieni metadati file in base al percorso</li><li>Ottieni riga</li><li>Aggiorna file</li><li>Aggiorna riga</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.

## Creare una connessione a Google Drive
Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Google Drive.

> [!INCLUDE [Passaggi per la creazione di una connessione a Google Drive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

Dopo aver creato la connessione immettere le proprietà di Google Drive, ad esempio nome file o percorso cartella. Il **riferimento all'API REST** in questo articolo descrive tali proprietà.

> [!TIP]
> È possibile usare la stessa connessione di Google Drive in altre app per la logica.
> 
> 

## Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### Crea file
Carica un file in Google Drive. ```POST: /datasets/default/files```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |yes |query |nessuno |Percorso della cartella per caricare il file in Google Drive |
| name |string |yes |query |nessuno |Nome del file da creare in Google Drive |
| body |string(binary) |sì |body |nessuno |Contenuto del file da creare in Google Drive |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Inserisci riga
Inserisce una riga in Fogli Google. ```POST: /datasets/{dataset}/tables/{table}/items```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |yes |path |nessuno |Identificatore univoco del file di Google Sheet |
| tabella |string |yes |path |nessuno |Identificatore univoco del foglio di lavoro |
| item |ItemInternalId: string |sì |body |nessuno |Riga da inserire nel foglio specificato |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Copia file
Copia un file in Google Drive. ```POST: /datasets/default/copyFile```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| una sezione source |string |yes |query |nessuno |URL del file di origine |
| destination |string |yes |query |nessuno |Percorso file di destinazione in Google Drive incluso nome file di destinazione |
| overwrite |boolean |no |query |nessuno |Sovrascrive il file di destinazione se è impostata su 'true' |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Elimina file
Elimina un file da Google Drive. ```DELETE: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |yes |path |nessuno |Identificatore univoco del file da eliminare da Google Drive |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Elimina riga
Elimina una riga da Fogli Google. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |yes |path |nessuno |Identificatore univoco del file di Google Sheet |
| tabella |string |yes |path |nessuno |Identificatore univoco del foglio di lavoro |
| id |string |yes |path |nessuno |Identificatore univoco della riga da eliminare |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Estrai archivio in una cartella
Estrae un file di archivio in una cartella di Google Drive, ad esempio un file ZIP. ```POST: /datasets/default/extractFolderV2```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| una sezione source |string |yes |query |nessuno |Percorso del file di archivio |
| destination |string |yes |query |nessuno |Percorso in Google Drive in cui estrarre il contenuto dell'archivio |
| overwrite |boolean |no |query |nessuno |Sovrascrive i file di destinazione se è impostata su 'true' |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Recupera contenuto di file tramite ID
Recupera il contenuto di file da Google Drive in base all'ID. ```GET: /datasets/default/files/{id}/content```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |yes |path |nessuno |Identificatore univoco del file da recuperare in Google Drive |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Recupera contenuto di file tramite percorso
Recupera il contenuto di file da Google Drive in base al percorso. ```GET: /datasets/default/GetFileContentByPath```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| path |string |yes |query |nessuno |Percorso del file in Google Drive |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Recupera metadati di file tramite ID
Recupera i metadati di file da Google Drive in base all'ID. ```GET: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |yes |path |nessuno |Identificatore univoco del file in Google Drive |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Recupera metadati di file tramite percorso
Recupera i metadati di file da Google Drive in base al percorso. ```GET: /datasets/default/GetFileByPath```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| path |string |yes |query |nessuno |Percorso del file in Google Drive |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Ottenere la riga
Recupera una singola riga da Fogli Google. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |yes |path |nessuno |Identificatore univoco del file di Google Sheet |
| tabella |string |yes |path |nessuno |Identificatore univoco del foglio di lavoro |
| id |string |yes |path |nessuno |Identificatore univoco della riga da recuperare |

#### Response
| Name | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Aggiorna file
Aggiorna un file in Google Drive. ```PUT: /datasets/default/files/{id}```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Description |
| --- | --- | --- | --- | --- | --- |
| id |string |yes |path |nessuno |Identificatore univoco del file da aggiornare in Google Drive |
| body |string(binary) |sì |body |nessuno |Contenuto del file da creare in Google Drive |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### Aggiorna riga
Aggiorna una riga in Fogli Google. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Name | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| dataset |string |yes |path |nessuno |Identificatore univoco del file di Google Sheet |
| tabella |string |yes |path |nessuno |Identificatore univoco del foglio di lavoro |
| id |string |yes |path |nessuno |Identificatore univoco della riga da aggiornare |
| item |ItemInternalId: string |sì |body |nessuno |Riga con i valori aggiornati |

#### Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## Definizioni oggetti
#### DataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| tabular |non definito |no |
| BLOB |non definito |no |

#### TabularDataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| una sezione source |stringa |no |
| displayName |string |no |
| urlEncoding |stringa |no |
| tableDisplayName |string |no |
| tablePluralName |stringa |no |

#### BlobDataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| una sezione source |stringa |no |
| displayName |string |no |
| urlEncoding |stringa |no |

#### BlobMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ID |stringa |no |
| Name |stringa |no |
| DisplayName |string |no |
| Path |string |no |
| LastModified |stringa |no |
| Dimensione |integer |no |
| MediaType |stringa |no |
| IsFolder |boolean |no |
| ETag |stringa |no |
| FileLocator |string |no |

#### TableMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| name |string |no |
| title |stringa |no |
| x-ms-permission |string |no |
| schema |non definito |no |

#### TablesList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |no |

#### Tabella
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| Name |stringa |no |
| DisplayName |string |no |

#### Item
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| ItemInternalId |string |no |

#### ItemsList
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| value |array |no |

## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all'[elenco di API](apis-list.md).

<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0824_2016-->