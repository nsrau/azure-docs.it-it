---
title: Aggiungere il connettore di archiviazione BLOB di Azure alle app per la logica | Microsoft Docs
description: Panoramica del connettore di archiviazione BLOB di Azure con i parametri dell'API REST
services: ''
documentationcenter: ''
author: MandiOhlinger
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/25/2016
ms.author: mandia

---
# Introduzione al connettore di archiviazione BLOB di Azure
Archiviazione BLOB di Azure è un servizio per l'archiviazione di grandi quantità di dati non strutturati. Nell'archivio BLOB di Azure è possibile eseguire diverse azioni, ad esempio, caricare, aggiornare, ottenere ed eliminare BLOB.

Con Archiviazione BLOB di Azure:

* Il flusso di lavoro si crea caricando nuovi progetti o recuperando file aggiornati di recente.
* Le azioni consentono di ottenere i metadati del file, eliminare un file, copiare file e altro ancora. Ad esempio, quando viene aggiornato uno strumento in un sito Web di Azure (trigger), viene aggiornato un file nell'archivio BLOB (azione).

Questo argomento illustra come usare il connettore di archiviazione BLOB in un'app per la logica e fornisce un elenco delle azioni.

> [!NOTE]
> Questa versione dell'articolo si applica alla la disponibilità generale delle app per la logica.
> 
> 

Per altre informazioni sulle app per la logica, vedere [Informazioni sulle app per la logica](../app-service-logic/app-service-logic-what-are-logic-apps.md) e [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Connettersi all'archivio BLOB di Azure
Prima che l'app per la logica possa accedere a qualsiasi servizio, è necessario creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app per la logica e un altro servizio. Ad esempio, per connettersi a un account di archiviazione si crea prima una *connessione* all'archivio BLOB. Per creare una connessione, immettere le credenziali usate normalmente per accedere al servizio a cui connettersi. Con Archiviazione di Azure immettere quindi le credenziali dell'account di archiviazione per creare la connessione.

#### Creare la connessione
> [!INCLUDE [Creare una connessione all'archivio BLOB di Azure](../../includes/connectors-create-api-azureblobstorage.md)]
> 
> 

## Usare un trigger
Questo connettore non include trigger. Usare altri trigger per avviare l'app per la logica, come un trigger di ricorrenza, un trigger Webhook HTTP, i trigger disponibili con altri connettori e altri ancora. La sezione [Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md) illustra un esempio.

## Usare un'azione
Un'azione è un'operazione eseguita dal flusso di lavoro e definita in un'app per la logica.

1. Selezionare il segno più. Sono disponibili varie opzioni: **Aggiungi un'azione**, **Aggiungi una condizione** e le opzioni in **Altro**.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. Selezionare **Aggiungi un'azione**.
3. Nella casella di testo digitare "blob" per ottenere l'elenco di tutte le azioni disponibili.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png)
4. Nell'esempio, scegliere **AzureBlob - Ottieni metadati file in base al percorso**. Se esiste già una connessione, fare clic sul pulsante **...** (Mostra selezione) per selezionare un file.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    Se viene richiesto di inserire le informazioni di connessione, immettere i dettagli per creare la connessione. La sezione [Creare la connessione](connectors-create-api-azureblobstorage.md#create-the-connection) di questo argomento descrive queste proprietà.
   
   > [!NOTE]
   > In questo esempio si ottengono i metadati di un file. Per visualizzare i metadati, aggiungere un'altra azione che crea un nuovo file tramite un altro connettore. Ad esempio, aggiungere un'azione OneDrive che crea un nuovo file "test" in base ai metadati.
   > 
   > 
5. Scegliere **Salva** nell'angolo in alto a sinistra della barra degli strumenti per salvare le modifiche. L'app per la logica viene salvata e può essere abilitata automaticamente.

> [!TIP]
> [Storage Explorer](http://storageexplorer.com/) è uno strumento ideale per gestire più account di archiviazione.
> 
> 

## Dettagli tecnici
## Azioni del BLOB di archiviazione
| Azione | Descrizione |
| --- | --- |
| [Ottieni metadati file](connectors-create-api-azureblobstorage.md#get-file-metadata) |Questa operazione ottiene i metadati del file tramite l'ID file. |
| [Aggiorna file](connectors-create-api-azureblobstorage.md#update-file) |Questa operazione aggiorna un file. |
| [Elimina file](connectors-create-api-azureblobstorage.md#delete-file) |Questa operazione elimina un file. |
| [Recupera metadati di file tramite percorso](connectors-create-api-azureblobstorage.md#get-file-metadata-using-path) |Questa operazione ottiene i metadati del file tramite il percorso. |
| [Ottieni contenuto di file tramite percorso](connectors-create-api-azureblobstorage.md#get-file-content-using-path) |Questa operazione ottiene il contenuto del file tramite il percorso. |
| [Ottieni contenuto di file](connectors-create-api-azureblobstorage.md#get-file-content) |Questa operazione ottiene il contenuto del file tramite l'ID. |
| [Crea file](connectors-create-api-azureblobstorage.md#create-file) |Questa operazione carica un file. |
| [Copia file](connectors-create-api-azureblobstorage.md#copy-file) |Questa operazione copia un file nell'archivio BLOB di Azure. |
| [Estrai archivio in una cartella](connectors-create-api-azureblobstorage.md#extract-archive-to-folder) |Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP. |

### Informazioni dettagliate sulle azioni
In questa sezione sono riportati i dettagli relativi a ogni azione, incluse le proprietà di input obbligatorie o facoltative e quelle di output corrispondenti associate al connettore.

#### Ottieni metadati file
Questa operazione ottiene i metadati del file tramite l'ID file.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| id* |File |Selezionare un file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |stringa |

#### Aggiorna file
Questa operazione aggiorna un file.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| id* |File |Selezionare un file |
| body* |Contenuto del file |Contenuto del file da aggiornare |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

#### Elimina file
Questa operazione elimina un file.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| id* |File |Selezionare un file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Recupera metadati di file tramite percorso
Questa operazione ottiene i metadati del file tramite il percorso.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| path* |Percorso del file |Selezionare un file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

#### Ottieni contenuto di file tramite percorso
Questa operazione ottiene il contenuto del file tramite il percorso.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| path* |Percorso del file |Selezionare un file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Ottieni contenuto di file
Questa operazione ottiene il contenuto del file tramite l'ID.

| Nome proprietà | Tipo di dati | Descrizione |
| --- | --- | --- |
| id* |string |Selezionare un file |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
Nessuna.

#### Crea file
Questa operazione carica un file.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| folderPath* |Percorso della cartella |Seleziona una cartella |
| name* |Nome file |Nome del file da caricare |
| body* |Contenuto del file |Contenuto del file da caricare |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

#### Copia file
Questa operazione copia un file nell'archivio BLOB di Azure.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| source* |URL di origine |Specificare l'URL del file di origine |
| destination* |Percorso file di destinazione |Specificare il percorso del file di destinazione, incluso il nome file di destinazione |
| overwrite |Sovrascrivere? |Sovrascrivere il file di destinazione esistente (true/false)? |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |stringa |

#### Estrai archivio in una cartella
Questa operazione estrae un file di archivio in una cartella, ad esempio ZIP.

| Nome proprietà | Nome visualizzato | Descrizione |
| --- | --- | --- |
| source* |Percorso file di archiviazione di origine |Selezionare un file di archivio |
| destination* |Percorso cartella di destinazione |Selezionare il contenuto da estrarre |
| overwrite |Sovrascrivere? |Sovrascrivere il file di destinazione esistente (true/false)? |

L'asterisco (*) indica che la proprietà è obbligatoria.

##### Dettagli output
BlobMetadata

| Nome proprietà | Tipo di dati |
| --- | --- |
| ID |string |
| Nome |string |
| DisplayName |string |
| Path |string |
| LastModified |string |
| Dimensione |integer |
| MediaType |string |
| IsFolder |boolean |
| ETag |string |
| FileLocator |string |

## Risposte HTTP
Quando si effettuano chiamate alle diverse azioni, è possibile ottenere determinate risposte. La tabella seguente indica le risposte e le relative descrizioni:

| Nome | Descrizione |
| --- | --- |
| 200 |OK |
| 202 |Accepted |
| 400 |Bad Request |
| 401 |Non autorizzata |
| 403 |Accesso negato |
| 404 |Non trovato |
| 500 |Errore interno del server. Si è verificato un errore sconosciuto |
| default |Operazione non riuscita. |

## Passaggi successivi
[Creare un'app per la logica](../app-service-logic/app-service-logic-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell'[elenco di API](apis-list.md).

<!---HONumber=AcomDC_0727_2016-->
