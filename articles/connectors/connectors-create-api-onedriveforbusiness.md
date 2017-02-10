---
title: OneDrive for Business | Microsoft Docs
description: "Creare app per la logica in Servizio app di Azure. Connettersi a OneDrive per gestire i file. È possibile eseguire varie azioni, ad esempio creare, aggiornare, ottenere ed eliminare file."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cf9484e9-7a20-4de0-93c8-0fa132221f2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 992b4b7bab8878355cbe936b75768e67b086286e


---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>Introduzione al connettore OneDrive for Business
Connettersi a OneDrive per gestire i file. È possibile eseguire varie azioni, ad esempio creare, aggiornare, ottenere ed eliminare file.

> [!NOTE]
> Questa versione dell'articolo si applica alla versione schema 2015-08-01 di anteprima delle app per la logica.
> 
> 

Per iniziare subito a creare un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Il connettore OneDrive for Business può essere usato come azione e include trigger. Tutti i connettori supportano dati nei formati JSON e XML.

 Nel connettore OneDrive for Business sono disponibili le azioni e/o i trigger seguenti:

### <a name="onedrive-for-business-actions"></a>Azioni di OneDrive for Business
È possibile eseguire queste azioni:

| Azione | Descrizione |
| --- | --- |
| [GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata) |Recupera i metadati di un file in OneDrive for Business tramite ID |
| [UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile) |Aggiorna un file in OneDrive for Business |
| [DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile) |Elimina un file da OneDrive for Business |
| [GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath) |Recupera i metadati di un file in OneDrive for Business tramite il percorso |
| [GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath) |Recupera i contenuti di un file in OneDrive for Business tramite il percorso |
| [GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent) |Recupera i contenuti di un file in OneDrive for Business tramite l'ID |
| [CreateFile](connectors-create-api-onedriveforbusiness.md#createfile) |Carica un file in OneDrive for Business |
| [CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile) |Copia un file in OneDrive for Business |
| [ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder) |Elenca i file in una cartella di OneDrive for Business |
| [ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder) |Elenca i file nella cartella radice di OneDrive for Business |
| [ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2) |Estrae una cartella in OneDrive for Business |

### <a name="onedrive-for-business-triggers"></a>Trigger di OneDrive for Business
È possibile ascoltare questi eventi:

| Trigger | Descrizione |
| --- | --- |
| Quando un file viene creato |Attiva un flusso quando un nuovo file viene creato in una cartella di OneDrive for Business |
| Quando un file viene modificato |Attiva un flusso quando un file viene modificato in una cartella di OneDrive for Business |

## <a name="create-a-connection-to-onedrive-for-business"></a>Creare una connessione a OneDrive for Business
Per creare app per la logica con OneDrive for Business, è prima necessario creare una **connessione** e successivamente fornire i dettagli per le proprietà seguenti:

| Proprietà | Obbligatorio | Descrizione |
| --- | --- | --- |
| Token |Sì |Fornisce le credenziali per OneDrive for Business |

Dopo aver creato la connessione, è possibile usarla per eseguire le azioni e restare in ascolto dei trigger descritti in questo articolo.

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 
> [!TIP]
> È possibile usare questa connessione in altre app per la logica.
> 
> 

## <a name="reference-for-onedrive-for-business"></a>Informazioni di riferimento per OneDrive for Business
Si applica alla versione 1.0

## <a name="getfilemetadata"></a>GetFileMetadata
Recupero dei metadati di un file tramite ID: ottiene i metadati di un file in OneDrive for Business tramite ID

```GET: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |nessuno |Specifica il file |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="updatefile"></a>UpdateFile
Aggiornamento file: aggiorna un file in OneDrive for Business

```PUT: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |nessuno |Specifica il file da aggiornare |
| body | |Sì |body |nessuno |Contenuto del file da aggiornare in OneDrive for Business |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="deletefile"></a>DeleteFile
Eliminazione file: Elimina un file da OneDrive for Business

```DELETE: /datasets/default/files/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |nessuno |Specifica il file da eliminare |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="getfilemetadatabypath"></a>GetFileMetadataByPath
Recupero dei metadati di un file tramite percorso: ottiene i metadati di un file in OneDrive for Business tramite il percorso

```GET: /datasets/default/GetFileByPath```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| path |string |yes |query |nessuno |Percorso univoco del file in OneDrive for Business |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="getfilecontentbypath"></a>GetFileContentByPath
Recupero dei contenuti di un file tramite percorso: ottiene i contenuti di un file in OneDrive for Business tramite il percorso

```GET: /datasets/default/GetFileContentByPath```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| path |string |yes |query |nessuno |Percorso univoco del file in OneDrive for Business |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="getfilecontent"></a>GetFileContent
Recupero dei contenuti di un file tramite ID: ottiene i contenuti di un file in OneDrive for Business tramite ID

```GET: /datasets/default/files/{id}/content```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |nessuno |Specifica il file |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="createfile"></a>CreateFile
Creazione file: carica un file in OneDrive for Business

```POST: /datasets/default/files```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |yes |query |nessuno |Percorso della cartella per caricare il file in OneDrive for Business |
| name |string |yes |query |nessuno |Nome del file da creare in OneDrive for Business |
| body | |Sì |body |nessuno |Contenuto del file da caricare in OneDrive for Business |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="copyfile"></a>CopyFile
Copia file: copia un file in OneDrive for Business

```POST: /datasets/default/copyFile```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| source |string |yes |query |nessuno |URL del file di origine |
| destination |string |yes |query |nessuno |Percorso del file di destinazione in OneDrive for Business, incluso il nome del file di destinazione |
| overwrite |boolean |no |query |false |Sovrascrive il file di destinazione se è impostata su 'true' |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="onnewfile"></a>OnNewFile
Quando un file viene creato: attiva un flusso quando un nuovo file viene creato in una cartella di OneDrive for Business

```GET: /datasets/default/triggers/onnewfile```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| folderId |string |yes |query |nessuno |Specifica una cartella |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="onupdatedfile"></a>OnUpdatedFile
Quando un file viene modificato: attiva un flusso quando un file viene modificato in una cartella di OneDrive for Business

```GET: /datasets/default/triggers/onupdatedfile```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| folderId |string |yes |query |nessuno |Specifica una cartella |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="listfolder"></a>ListFolder
Elenco file in una cartella: elenca i file in una cartella di OneDrive for Business

```GET: /datasets/default/folders/{id}```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| id |string |Sì |path |nessuno |Specifica la cartella |

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="listrootfolder"></a>ListRootFolder
Elenco cartella radice: elenca i file nella cartella radice di OneDrive for Business

```GET: /datasets/default/folders```

Non sono disponibili parametri per questa chiamata

#### <a name="response"></a>Response
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="extractfolderv2"></a>ExtractFolderV2
Estrazione cartella: estrae una cartella in OneDrive for Business

```POST: /datasets/default/extractFolderV2```

| Nome | Tipo di dati | Obbligatorio | Posizione | Valore predefinito | Descrizione |
| --- | --- | --- | --- | --- | --- |
| source |string |yes |query |nessuno |Percorso del file di archivio |
| destination |string |yes |query |nessuno |Percorso in OneDrive for Business in cui estrarre il contenuto dell'archivio |
| overwrite |boolean |no |query |false |Sovrascrive i file di destinazione se è impostata su 'true' |

#### <a name="response"></a>Risposta
| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| default |Operazione non riuscita. |

## <a name="object-definitions"></a>Definizioni oggetto
### <a name="datasetsmetadata"></a>DataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| tabular |non definito |No |
| BLOB |non definito |No |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| una sezione source |string |No |
| displayName |stringa |No |
| urlEncoding |stringa |No |
| tableDisplayName |stringa |No |
| tablePluralName |stringa |No |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| una sezione source |string |No |
| displayName |stringa |No |
| urlEncoding |stringa |No |

### <a name="blobmetadata"></a>BlobMetadata
| Nome proprietà | Tipo di dati | Obbligatorio |
| --- | --- | --- |
| id |stringa |No |
| Name |stringa |No |
| DisplayName |string |No |
| Path |string |No |
| LastModified |stringa |No |
| Dimensione |integer |No |
| MediaType |stringa |No |
| IsFolder |boolean |No |
| ETag |stringa |No |
| FileLocator |stringa |No |

### <a name="object"></a>Oggetto
## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


