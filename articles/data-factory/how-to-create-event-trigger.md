---
title: Creare trigger basati su eventi in Azure Data Factory
description: Informazioni su come creare un trigger in Azure Data Factory per l'esecuzione di una pipeline in risposta a un evento.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 10/18/2018
ms.openlocfilehash: 10f0079f47e5d2fd99b358fcc5cfb4c80aa9bd91
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84508897"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Creare un trigger che esegue una pipeline in risposta a un evento
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive i trigger basati su eventi che è possibile creare nelle pipeline di Data Factory.

Un'architettura guidata dagli eventi è un comune modello di integrazione dei dati che implica produzione, rilevamento, utilizzo e risposta agli eventi. Negli scenari di integrazione dei dati ai clienti di Data Factory è spesso richiesto di attivare pipeline basate su eventi quali l'arrivo o l'eliminazione di un file nell'account di archiviazione di Azure. Data Factory è ora integrato con [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/), che consente di attivare pipeline su un evento.

Per un'introduzione di dieci minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> L'integrazione descritta in questo articolo dipende dalla [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/). Verificare che la sottoscrizione sia registrata con il provider di risorse di Griglia di eventi. Per altre informazioni, vedere [Provider e tipi di risorse](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). È necessario essere in grado di eseguire l'azione *Microsoft. EventGrid/eventSubscriptions/**. Questa azione fa parte del ruolo predefinito collaboratore sottoscrizione evento di EventGrid.

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory

Questa sezione illustra come creare un trigger di evento nell'interfaccia utente di Azure Data Factory.

1. Passare al **canvas di creazione**

1. Nell'angolo in basso a sinistra fare clic sul pulsante **Trigger**

1. Fare clic su **+ Nuovo** per aprire il riquadro di spostamento laterale per la di creazione di trigger

1. Selezionare il tipo di trigger **Evento**

    ![Creare un nuovo trigger di evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Selezionare l'account di archiviazione nell'elenco a discesa della sottoscrizione di Azure o manualmente usando l'ID risorsa dell'account di archiviazione. Scegliere il contenitore in cui si vuole che si verifichino gli eventi. La selezione del contenitore è facoltativa, ma tenere presente che se si selezionano tutti i contenitori il numero di eventi può essere elevato.

   > [!NOTE]
   > Il trigger di evento supporta attualmente solo gli account di archiviazione Azure Data Lake Storage Gen2 e per utilizzo generico versione 2. È necessario disporre almeno dell'accesso *proprietario* per l'account di archiviazione.  A causa di una limitazione di Griglia di eventi di Azure, Azure Data Factory supporta solo un massimo di 500 trigger di evento per account di archiviazione.

1. Le proprietà **Percorso BLOB inizia con** e **Percorso BLOB termina con** consentono di specificare i contenitori, le cartelle e i nomi di BLOB per cui si vogliono ricevere eventi. Per il trigger di evento è necessario definire almeno una di queste proprietà. È possibile usare svariati modelli per le due proprietà **Percorso BLOB inizia con** e **Percorso BLOB termina con**, come mostrato negli esempi più avanti in questo articolo.

    * **Il percorso del BLOB inizia con:** il percorso del BLOB deve iniziare con un percorso di cartella. I valori validi includono `2018/` e `2018/april/shoes.csv`. Se non è selezionato un contenitore, questo campo non è selezionabile.
    * **Il percorso del BLOB termina con:** il percorso del BLOB deve terminare con un nome file o un'estensione. I valori validi includono `shoes.csv` e `.csv`. Il nome del contenitore e della cartella sono facoltativi, ma, se specificati, devono essere separati da un segmento `/blobs/`. Ad esempio, il valore di un contenitore denominato 'orders' è `/orders/blobs/2018/april/shoes.csv`. Per specificare una cartella in qualsiasi contenitore, omettere il carattere '/' iniziale. Ad esempio, con `april/shoes.csv` verrà attivato un evento in tutti i file denominati `shoes.csv` nella cartella denominata 'april' presente in qualsiasi contenitore. 

1. Scegliere se il trigger deve rispondere a un evento **Blob created** (BLOB creato), **Blob deleted** (BLOB eliminato) o entrambi. Nel percorso di archiviazione specificato ogni evento attiverà le pipeline di Data Factory associate al trigger.

    ![Configurare il trigger di evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Scegliere se il trigger deve ignorare i BLOB con zero byte.

1. Dopo aver configurato il trigger, fare clic su **Avanti: Anteprima dati**. Questa schermata mostra i BLOB esistenti corrispondenti alla configurazione del trigger di evento. Assicurarsi che siano presenti filtri specifici. La configurazione di filtri troppo generici può causare la creazione o l'eliminazione di un numero elevato di file e influire significativamente sui costi. Dopo aver verificato le condizioni di filtro, fare clic su **Fine**.

    ![Anteprima dati del trigger di evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Per associare una pipeline a questo trigger, passare al canvas della pipeline e fare clic su **Add trigger** (Aggiungi trigger) e selezionare **New/Edit** (Nuovo/Modifica). Quando viene visualizzato il riquadro di spostamento laterale, fare clic sull'elenco a discesa **Choose trigger** (Scegli trigger) e selezionare il trigger creato. Fare clic su **Avanti: Anteprima dati** per verificare che la configurazione sia corretta e quindi su **Avanti** per verificare che l'anteprima dei dati sia corretta.

1. Se alla pipeline sono associati parametri, è possibile specificarli nel riquadro di spostamento laterale dei parametri di esecuzione del trigger. Il trigger di evento acquisisce il nome file e il percorso della cartella del blob nelle proprietà `@trigger().outputs.body.folderPath` e `@trigger().outputs.body.fileName`. Per usare i valori di queste proprietà in una pipeline, è necessario mappare le proprietà per i parametri della pipeline. Dopo il mapping delle proprietà per i parametri, è possibile accedere ai valori acquisiti dal trigger attraverso l'espressione `@pipeline().parameters.parameterName` attraverso la pipeline. Al termine, fare clic su **Fine**.

    ![Mapping di proprietà di un trigger ai parametri della pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Nell'esempio precedente il trigger viene configurato in modo da essere attivato quando viene creato un percorso BLOB che termina con l'estensione csv nella cartella event-testing del contenitore sample-data. Le proprietà **folderPath** e **fileName** acquisiscono il percorso del nuovo BLOB. Ad esempio, quando si aggiunge MoviesDB.csv viene aggiunto al percorso sample-data/event-testing, il valore di `@trigger().outputs.body.folderPath` è `sample-data/event-testing` e il valore di `@trigger().outputs.body.fileName` è `moviesDB.csv`. Nell'esempio questi valori vengono associati ai parametri di pipeline `sourceFolder` e `sourceFile` che possono essere usati in tutta la pipeline rispettivamente come `@pipeline().parameters.sourceFolder` e `@pipeline().parameters.sourceFile`.

## <a name="json-schema"></a>Schema JSON

La tabella seguente offre una panoramica degli elementi dello schema correlati ai trigger basati su elenchi:

| **Elemento JSON** | **Descrizione** | **Tipo** | **Valori consentiti** | **Obbligatorio** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **ambito** | ID risorsa di Azure Resource Manager dell'account di archiviazione. | string | ID Azure Resource Manager | Sì |
| **eventi** | Tipo di eventi che provocano l'attivazione del trigger. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sì, qualsiasi combinazione di questi valori. |
| **blobPathBeginsWith** | Il percorso del BLOB deve iniziare con il modello fornito per l'attivazione del trigger. Ad esempio, `/records/blobs/december/` attiva il trigger solo per i BLOB nella cartella `december` nel contenitore `records`. | string   | | È necessario specificare un valore per almeno una di queste proprietà: `blobPathBeginsWith` o `blobPathEndsWith`. |
| **blobPathEndsWith** | Il percorso del BLOB deve terminare con il modello fornito per l'attivazione del trigger. Ad esempio, `december/boxes.csv` attiva il trigger solo per i BLOB denominati `boxes` in una cartella `december`. | string   | | È necessario specificare un valore per almeno una di queste proprietà: `blobPathBeginsWith` o `blobPathEndsWith`. |
| **ignoreEmptyBlobs** | Indica se con i BLOB a zero byte verrà attivata un'esecuzione della pipeline. Per impostazione predefinita, questa opzione è impostata su true. | Boolean | true o false | No |

## <a name="examples-of-event-based-triggers"></a>Esempi di trigger basati su eventi

Questa sezione contiene alcuni esempi di impostazioni di trigger basati su eventi.

> [!IMPORTANT]
> È necessario includere il segmento `/blobs/` del percorso, come illustrato negli esempi seguenti, ogni volta che si specifica il contenitore e la cartella, il contenitore e il file o il contenitore, la cartella e il file. Per **blobPathBeginsWith** l'interfaccia utente di Data Factory aggiungerà automaticamente `/blobs/` tra il nome della cartella e quello nome del contenitore nel codice JSON del trigger.

| Proprietà | Esempio | Descrizione |
|---|---|---|
| **Il percorso del BLOB inizia con** | `/containername/` | Riceve gli eventi per qualsiasi BLOB nel contenitore. |
| **Il percorso del BLOB inizia con** | `/containername/blobs/foldername/` | Riceve gli eventi per qualsiasi BLOB nel contenitore `containername` e nella cartella `foldername`. |
| **Il percorso del BLOB inizia con** | `/containername/blobs/foldername/subfoldername/` | È anche possibile fare riferimento a una sottocartella. |
| **Il percorso del BLOB inizia con** | `/containername/blobs/foldername/file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` nella cartella `foldername` nel contenitore `containername`. |
| **Il percorso del BLOB termina con** | `file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` in qualsiasi percorso. |
| **Il percorso del BLOB termina con** | `/containername/blobs/file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` nel contenitore `containername`. |
| **Il percorso del BLOB termina con** | `foldername/file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` nella cartella `foldername` in qualsiasi contenitore. |

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sui trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
