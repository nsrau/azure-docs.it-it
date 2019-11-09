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
ms.openlocfilehash: 0b1d9fad2992397a3a6768d0f5e7ff26a400a2b3
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889317"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Creare un trigger che esegue una pipeline in risposta a un evento

Questo articolo descrive i trigger basati su eventi che è possibile creare nelle pipeline di Data Factory.

Un'architettura guidata dagli eventi è un comune modello di integrazione dei dati che implica produzione, rilevamento, utilizzo e risposta agli eventi. Gli scenari di integrazione dei dati richiedono spesso Data Factory ai clienti di attivare pipeline in base a eventi quali l'arrivo o l'eliminazione di un file nell'account di archiviazione di Azure. Data Factory è ora integrato con [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/), che consente di attivare pipeline su un evento.

Per un'introduzione di dieci minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> L'integrazione descritta in questo articolo dipende dalla [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/). Verificare che la sottoscrizione sia registrata con il provider di risorse di Griglia di eventi. Per altre informazioni, vedere [Provider e tipi di risorse](../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory

Questa sezione illustra come creare un trigger di evento all'interno dell'interfaccia utente di Azure Data Factory.

1. Passa all' **area di disegno di creazione**

1. Nell'angolo inferiore sinistro fare clic sul pulsante **trigger** .

1. Fare clic su **+ nuovo** per aprire la finestra di esplorazione del lato creazione trigger

1. Seleziona **evento** del tipo di trigger

    ![Creare un nuovo trigger di evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Selezionare l'account di archiviazione dall'elenco a discesa sottoscrizione di Azure o manualmente usando l'ID risorsa dell'account di archiviazione. Scegliere il contenitore in cui si desidera che si verifichino gli eventi. La selezione del contenitore è facoltativa, ma tenere presente che la selezione di tutti i contenitori può causare un numero elevato di eventi.

   > [!NOTE]
   > Il trigger di evento supporta attualmente solo gli account di archiviazione Azure Data Lake Storage Gen2 e per utilizzo generico versione 2. A causa di una limitazione di griglia di eventi di Azure, Azure Data Factory supporta solo un massimo di 500 trigger di evento per account di archiviazione.

1. Il **percorso del BLOB inizia con** e il **percorso BLOB termina con** le proprietà consente di specificare i contenitori, le cartelle e i nomi di BLOB per cui si desidera ricevere gli eventi. Per il trigger di evento è necessario definire almeno una di queste proprietà. È possibile usare svariati modelli per le due proprietà **Percorso BLOB inizia con** e **Percorso BLOB termina con**, come mostrato negli esempi più avanti in questo articolo.

    * Il **percorso del BLOB inizia con:** Il percorso del BLOB deve iniziare con un percorso di cartella. I valori validi includono `2018/` e `2018/april/shoes.csv`. Non è possibile selezionare questo campo se non è selezionato alcun contenitore.
    * Il **percorso BLOB termina con:** Il percorso del BLOB deve terminare con un nome file o un'estensione. I valori validi includono `shoes.csv` e `.csv`. Il nome del contenitore e della cartella sono facoltativi, ma, quando specificati, devono essere separati da un segmento `/blobs/`. Un contenitore denominato "Orders", ad esempio, può avere un valore `/orders/blobs/2018/april/shoes.csv`. Per specificare una cartella in qualsiasi contenitore, omettere il carattere "/". Ad esempio, `april/shoes.csv` attiverà un evento in tutti i file denominati `shoes.csv` nella cartella a denominata ' April ' in tutti i contenitori. 

1. Consente di indicare se il trigger risponderà a un evento **BLOB creato** , a un evento **BLOB eliminato** o a entrambi. Nel percorso di archiviazione specificato ogni evento attiverà le pipeline Data Factory associate al trigger.

    ![Configurare il trigger di evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Consente di indicare se il trigger ignora i BLOB con zero byte.

1. Dopo aver configurato il trigger, fare clic su Avanti **: Anteprima dati**. Questa schermata mostra i BLOB esistenti corrispondenti alla configurazione del trigger di evento. Assicurarsi di avere filtri specifici. La configurazione di filtri troppo ampi può corrispondere a un numero elevato di file creati/eliminati e può influire significativamente sui costi. Una volta verificate le condizioni di filtro, fare clic su **fine**.

    ![Anteprima dati trigger evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Per alleghi una pipeline a questo trigger, passare all'area di disegno della pipeline e fare clic su **Aggiungi trigger** e selezionare **nuovo/modifica**. Quando viene visualizzato il NAV laterale, fare clic sull'elenco a discesa **Scegli trigger...** e selezionare il trigger creato. Fare clic su **Avanti: Anteprima dati** per verificare che la configurazione sia corretta, quindi **accanto** a convalida l'anteprima dei dati è corretta.

1. Se la pipeline dispone di parametri, è possibile specificarli nel trigger che esegue il NAV sul lato parametro. Il trigger di evento acquisisce il percorso della cartella e il nome file del BLOB nelle proprietà `@triggerBody().folderPath` e `@triggerBody().fileName`. Per usare i valori di queste proprietà in una pipeline, è necessario mappare le proprietà per i parametri della pipeline. Dopo il mapping delle proprietà per i parametri, è possibile accedere ai valori acquisiti dal trigger attraverso l'espressione `@pipeline().parameters.parameterName` attraverso la pipeline. Al termine, fare clic su **fine** .

    ![Mapping di proprietà di un trigger ai parametri della pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Nell'esempio precedente, il trigger viene configurato in modo da essere attivato quando viene creato un percorso BLOB che termina con. csv nella cartella Sample-testing nel contenitore Sample-Data. Le proprietà **folderPath** e **filename** acquisiscono il percorso del nuovo BLOB. Ad esempio, quando MoviesDB. csv viene aggiunto al percorso Sample-Data/Event-testing, `@triggerBody().folderPath` ha un valore `sample-data/event-testing` e `@triggerBody().fileName` ha il valore `moviesDB.csv`. Questi valori vengono mappati nell'esempio ai parametri della pipeline `sourceFolder` e `sourceFile` che possono essere usati in tutta la pipeline come `@pipeline().parameters.sourceFolder` e `@pipeline().parameters.sourceFile` rispettivamente.

## <a name="json-schema"></a>Schema JSON

La tabella seguente offre una panoramica degli elementi dello schema correlati ai trigger basati su elenchi:

| **Elemento JSON** | **Descrizione** | **Tipo** | **Valori consentiti** | **Obbligatorio** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ID risorsa di Azure Resource Manager dell'account di archiviazione. | String | ID Azure Resource Manager | Sì |
| **eventi** | Tipo di eventi che provocano l'attivazione del trigger. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sì, qualsiasi combinazione di questi valori. |
| **blobPathBeginsWith** | Il percorso del BLOB deve iniziare con il modello fornito per l'attivazione del trigger. Ad esempio, `/records/blobs/december/` attiva il trigger solo per i BLOB nella cartella `december` nel contenitore `records`. | String   | | È necessario specificare un valore per almeno una di queste proprietà: `blobPathBeginsWith` o `blobPathEndsWith`. |
| **blobPathEndsWith** | Il percorso del BLOB deve terminare con il modello fornito per l'attivazione del trigger. Ad esempio, `december/boxes.csv` attiva il trigger solo per i BLOB denominati `boxes` in una cartella `december`. | String   | | È necessario specificare un valore per almeno una di queste proprietà: `blobPathBeginsWith` o `blobPathEndsWith`. |
| **ignoreEmptyBlobs** | Indica se i BLOB a zero byte attiverà un'esecuzione della pipeline. Per impostazione predefinita, questo valore è impostato su true. | Booleano | true o false | No |

## <a name="examples-of-event-based-triggers"></a>Esempi di trigger basati su eventi

Questa sezione contiene alcuni esempi di impostazioni di trigger basati su eventi.

> [!IMPORTANT]
> È necessario includere il segmento `/blobs/` del percorso, come illustrato negli esempi seguenti, ogni volta che si specifica il contenitore e la cartella, il contenitore e il file o il contenitore, la cartella e il file. Per **blobPathBeginsWith**, l'interfaccia utente Data Factory aggiungerà automaticamente `/blobs/` tra la cartella e il nome del contenitore nel codice JSON del trigger.

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
Per informazioni dettagliate sui trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md#triggers).
