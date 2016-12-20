---
title: Aggiungere il connettore Box alle app per la logica | Documentazione Microsoft
description: Panoramica del connettore Box con i parametri dell&quot;API REST
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 949579cf-a81c-4790-9ef5-fe39b4fbd0c5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 71f9dd111ebdbe885f33d162b2ea320dfaa167bb
ms.openlocfilehash: e211d0878a7f9bd43c95c727935ad883fae0db3a


---
# <a name="get-started-with-the-box-connector"></a>Introduzione al connettore Box
Connettersi a Box per creare ed eliminare file e così via. 

> [!NOTE]
> Questa versione dell'articolo si applica alla versione dello schema 2015-08-01-preview delle app per la logica.
> 
> 

Con Box è possibile:

* Creare il flusso aziendale in base ai dati ottenuti da Box. 
* Usare i trigger quando un file viene creato o aggiornato.
* Usare le azioni per copiare un file, eliminarlo e così via. Queste azioni ottengono una risposta e quindi rendono l'output disponibile per altre azioni. Ad esempio, quando un file viene modificato in Box, è possibile usare il file e la posta elettronica tramite Office 365.

Per aggiungere un'operazione nelle app per la logica, vedere [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Box include i trigger e le azioni seguenti.

| Trigger | Azioni |
| --- | --- |
| <ul><li>Quando un file viene creato</li><li>Quando un file viene modificato</li></ul> |<ul><li>Crea file</li><li>Quando un file viene creato</li><li>Copia file</li><li>Elimina file</li><li>Estrai archivio in una cartella</li><li>Recupera contenuto di file tramite ID</li><li>Recupera contenuto di file tramite percorso</li><li>Recupera metadati di file tramite ID</li><li>Recupera metadati di file tramite percorso</li><li>Aggiorna file</li><li>Quando un file viene modificato</li></ul> |

Tutti i connettori supportano dati nei formati JSON e XML.

## <a name="create-a-connection-to-box"></a>Creare una connessione a Box
Quando si aggiunge questo connettore alle app per la logica, è necessario autorizzare le app per la logica per la connessione a Box.

> [!INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]
> 
> 

Dopo aver creato la connessione, immettere le proprietà di Box. Il **riferimento all'API REST** in questo argomento descrive tali proprietà.

> [!TIP]
> È possibile usare la stessa connessione di Box in altre app per la logica.
> 
> 

## <a name="swagger-rest-api-reference"></a>Riferimento all'API REST di Swagger
Si applica alla versione 1.0.

### <a name="create-file"></a>Crea file
Carica un file in Box.  
```POST: /datasets/default/files```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |Sì |query |Nessuno |Percorso della cartella per caricare il file in Box |
| Nome |string |Sì |query |Nessuno |Nome del file da creare in Box |
| body |string(binary) |Sì |body |Nessuno |Contenuto del file da creare in Box |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="when-a-file-is-created"></a>Quando un file viene creato
Quando un nuovo file viene creato in una cartella di Box, attiva un flusso.  
```GET: /datasets/default/triggers/onnewfile```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| folderId |string |Sì |query |Nessuno |Identificatore univoco della cartella in Box. |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="copy-file"></a>Copia file
Copia un file in Box.  
```POST: /datasets/default/copyFile```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| source |string |Sì |query |Nessuno |URL del file di origine |
| destination |string |Sì |query |Nessuno |Percorso file di destinazione in Box, incluso nome file di destinazione |
| overwrite |boolean |No |query |Nessuno |Sovrascrive il file di destinazione se è impostata su 'true' |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="delete-file"></a>Elimina file
Elimina un file da Box.  
```DELETE: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |Nessuno |Identificatore univoco del file da eliminare da Box |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="extract-archive-to-folder"></a>Estrai archivio in una cartella
Estrae un file di archivio in una cartella di Box, ad esempio con estensione zip.  
```POST: /datasets/default/extractFolderV2```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| source |string |Sì |query | |Percorso del file di archivio |
| destination |string |Sì |query | |Percorso in Box in cui estrarre il contenuto dell'archivio |
| overwrite |boolean |No |query | |Sovrascrive i file di destinazione se è impostata su 'true' |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-file-content-using-id"></a>Recupera contenuto di file tramite ID
Recupera il contenuto dei file da Box tramite ID.  
```GET: /datasets/default/files/{id}/content```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |Nessuno |Identificatore univoco del file in Box |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-file-content-using-path"></a>Recupera contenuto di file tramite percorso
Recupera il contenuto dei file da Box tramite percorso.  
```GET: /datasets/default/GetFileContentByPath```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| path |string |Sì |query |Nessuno |Percorso univoco del file in Box |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-file-metadata-using-id"></a>Recupera metadati di file tramite ID
Recupera i metadati dei file da Box tramite ID file.  
```GET: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |Nessuno |Identificatore univoco del file in Box |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="get-file-metadata-using-path"></a>Recupera metadati di file tramite percorso
Recupera i metadati dei file da Box tramite percorso.  
```GET: /datasets/default/GetFileByPath```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| path |string |Sì |query |Nessuno |Percorso univoco del file in Box |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="update-file"></a>Aggiorna file
Aggiorna un file in Box.  
```PUT: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |Nessuno |Identificatore univoco del file da aggiornare in Box |
| body |string(binary) |Sì |body |Nessuno |Contenuto del file da aggiornare in Box |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

### <a name="when-a-file-is-modified"></a>Quando un file viene modificato
Quando un file viene modificato in una cartella di Box, attiva un flusso.  
```GET: /datasets/default/triggers/onupdatedfile```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| folderId |string |Sì |query |Nessuno |Identificatore univoco della cartella in Box. |

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

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).




<!--HONumber=Nov16_HO3-->


