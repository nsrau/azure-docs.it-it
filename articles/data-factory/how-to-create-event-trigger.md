---
title: Creare trigger basati su eventi in Azure Data FactoryCreate event-based triggers in Azure Data Factory
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
ms.openlocfilehash: d697fb8afe3e92dfe54eb5d89a2ef59425cb0cde
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414919"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>Creare un trigger che esegue una pipeline in risposta a un evento
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive i trigger basati su eventi che è possibile creare nelle pipeline di Data Factory.

Un'architettura guidata dagli eventi è un comune modello di integrazione dei dati che implica produzione, rilevamento, utilizzo e risposta agli eventi. Gli scenari di integrazione dei dati spesso richiedono ai clienti di Data Factory di attivare le pipeline in base a eventi quali l'arrivo o l'eliminazione di un file nell'account di archiviazione di Azure.Data integration scenarios often require Data Factory customers to trigger pipelines based on events such as the arrival or deletion of a file in your Azure Storage account. Data Factory è ora integrato con [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/), che consente di attivare pipeline su un evento.

Per un'introduzione di dieci minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> L'integrazione descritta in questo articolo dipende dalla [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/). Verificare che la sottoscrizione sia registrata con il provider di risorse di Griglia di eventi. Per altre informazioni, vedere [Provider e tipi di risorse](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory

Questa sezione illustra come creare un trigger di evento all'interno dell'interfaccia utente di Azure Data Factory.This section shows you how to create an event trigger within the Azure Data Factory User Interface.

1. Passare all'area di **disegno Creazione e modifica**

1. Nell'angolo in basso a sinistra, fai clic sul pulsante **Trigger**

1. Fare clic su **Nuovo** per aprire il riquadro di spostamento lato trigger di creazione

1. Selezionare il tipo di trigger **Evento**

    ![Creare un nuovo trigger di evento](media/how-to-create-event-trigger/event-based-trigger-image1.png)

1. Selezionare l'account di archiviazione dall'elenco a discesa della sottoscrizione di Azure o usare manualmente il relativo ID risorsa account di archiviazione. Scegliere il contenitore su cui si desidera che si verifichino gli eventi. La selezione dei contenitori è facoltativa, ma tenere presente che la selezione di tutti i contenitori può causare eventi.

   > [!NOTE]
   > Il trigger di evento supporta attualmente solo Azure Data Lake Storage Gen2 e gli account di archiviazione generici versione 2.The Event Trigger currently supports only Azure Data Lake Storage Gen2 and General-purpose version 2 storage accounts. A causa di una limitazione di Griglia di eventi di Azure, Azure Data Factory supporta solo un massimo di 500 trigger di evento per account di archiviazione.

1. Il **percorso BLOB inizia con** e il percorso BLOB termina **con** le proprietà consentono di specificare i contenitori, le cartelle e i nomi dei BLOB per i quali si desidera ricevere gli eventi. Il trigger di evento richiede almeno una di queste proprietà da definire. È possibile usare svariati modelli per le due proprietà **Percorso BLOB inizia con** e **Percorso BLOB termina con**, come mostrato negli esempi più avanti in questo articolo.

    * **Il percorso del BLOB inizia con:Blob path begins with:** Il percorso del BLOB deve iniziare con un percorso di cartella. I valori validi includono `2018/` e `2018/april/shoes.csv`. Questo campo non può essere selezionato se non è selezionato un contenitore.
    * **Il percorso del BLOB termina con:Blob path ends with:** Il percorso del BLOB deve terminare con un nome di file o un'estensione. I valori validi includono `shoes.csv` e `.csv`. Contenitore e nome della cartella sono facoltativi ma, `/blobs/` se specificato, devono essere separati da un segmento. Ad esempio, un contenitore denominato 'orders' può avere un valore di `/orders/blobs/2018/april/shoes.csv`. Per specificare una cartella in qualsiasi contenitore, omettere il carattere iniziale '/'. Ad esempio, `april/shoes.csv` attiverà un evento `shoes.csv` su qualsiasi file denominato nella cartella denominata 'aprile' in qualsiasi contenitore. 

1. Selezionare se il trigger risponderà a un evento **creato BLOB,** a un evento **di eliminazione BLOB** o a entrambi. Nel percorso di archiviazione specificato, ogni evento attiverà le pipeline di Data Factory associate al trigger.

    ![Configurare il trigger di evento](media/how-to-create-event-trigger/event-based-trigger-image2.png)

1. Selezionare se il trigger ignora o meno i BLOB con zero byte.

1. Dopo aver configurato il trigger, fare clic su **Avanti: Anteprima dati**. Questa schermata mostra i BLOB esistenti corrispondenti alla configurazione del trigger di evento. Assicurati di avere filtri specifici. La configurazione di filtri troppo ampi può corrispondere a un numero elevato di file creati/eliminati e può influire in modo significativo sui costi. Una volta verificate le condizioni del filtro, fare clic su **Fine**.

    ![Anteprima dei dati del trigger di evento](media/how-to-create-event-trigger/event-based-trigger-image3.png)

1. Per associare una pipeline a questo trigger, passare all'area di disegno della pipeline e fare clic su **Aggiungi trigger** e selezionare **Nuovo/Modifica**. Quando viene visualizzato il navigatore laterale, fare clic sul menu a discesa **Scegli trigger...** e selezionare il trigger creato. Fare clic su **Avanti: Anteprima dati** per verificare che la configurazione sia corretta e quindi **su Avanti** per convalidare la versione dati sia corretta.

1. Se la pipeline dispone di parametri, è possibile specificarli nel trigger esegue il navigatore laterale del parametro. Il trigger dell'evento acquisisce il percorso della `@triggerBody().folderPath` cartella `@triggerBody().fileName`e il nome file del BLOB nelle proprietà e . Per usare i valori di queste proprietà in una pipeline, è necessario mappare le proprietà per i parametri della pipeline. Dopo il mapping delle proprietà per i parametri, è possibile accedere ai valori acquisiti dal trigger attraverso l'espressione `@pipeline().parameters.parameterName` attraverso la pipeline. Al termine, fare clic su **Fine.**

    ![Mapping di proprietà di un trigger ai parametri della pipeline](media/how-to-create-event-trigger/event-based-trigger-image4.png)

Nell'esempio precedente, il trigger è configurato per essere generato quando viene creato un percorso BLOB che termina con .csv nella cartella event-testing nei dati di esempio del contenitore. Le proprietà **folderPath** e **fileName** acquisiscono il percorso del nuovo BLOB. Ad esempio, quando MoviesDB.csv viene aggiunto al `@triggerBody().folderPath` percorso sample-data/event-testing, ha un valore di `sample-data/event-testing` e `@triggerBody().fileName` ha un valore di `moviesDB.csv`. Questi valori vengono mappati nell'esempio `sourceFolder` `sourceFile` ai parametri della pipeline `@pipeline().parameters.sourceFolder` `@pipeline().parameters.sourceFile` e che possono essere utilizzati rispettivamente in tutta la pipeline.

## <a name="json-schema"></a>Schema JSON

La tabella seguente offre una panoramica degli elementi dello schema correlati ai trigger basati su elenchi:

| **Elemento JSON** | **Descrizione** | **Tipo** | **Valori consentiti** | **Obbligatorio** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ID risorsa di Azure Resource Manager dell'account di archiviazione. | string | ID Azure Resource Manager | Sì |
| **Eventi** | Tipo di eventi che provocano l'attivazione del trigger. | Array    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | Sì, qualsiasi combinazione di questi valori. |
| **blobPathBeginsWith** | Il percorso del BLOB deve iniziare con il modello fornito per l'attivazione del trigger. Ad esempio, `/records/blobs/december/` attiva il trigger solo per i BLOB nella cartella `december` nel contenitore `records`. | string   | | È necessario specificare un valore per almeno una di queste proprietà: `blobPathBeginsWith` o `blobPathEndsWith`. |
| **blobPathEndsWith** | Il percorso del BLOB deve terminare con il modello fornito per l'attivazione del trigger. Ad esempio, `december/boxes.csv` attiva il trigger solo per i BLOB denominati `boxes` in una cartella `december`. | string   | | È necessario specificare un valore per almeno una di queste proprietà: `blobPathBeginsWith` o `blobPathEndsWith`. |
| **ignoreEmptyBlobs** | Se i BLOB a zero byte attiveranno o meno un'esecuzione della pipeline. Per impostazione predefinita, questa opzione è impostata su true. | Boolean | true o false | No |

## <a name="examples-of-event-based-triggers"></a>Esempi di trigger basati su eventi

Questa sezione contiene alcuni esempi di impostazioni di trigger basati su eventi.

> [!IMPORTANT]
> È necessario includere il segmento `/blobs/` del percorso, come illustrato negli esempi seguenti, ogni volta che si specifica il contenitore e la cartella, il contenitore e il file o il contenitore, la cartella e il file. Per **blobPathBeginsWith**, l'interfaccia `/blobs/` utente di Data Factory verrà aggiunta automaticamente tra il nome della cartella e del contenitore nel trigger JSON.

| Proprietà | Esempio | Descrizione |
|---|---|---|
| **Il percorso del BLOB inizia con** | `/containername/` | Riceve gli eventi per qualsiasi BLOB nel contenitore. |
| **Il percorso del BLOB inizia con** | `/containername/blobs/foldername/` | Riceve gli eventi per qualsiasi BLOB nel contenitore `containername` e nella cartella `foldername`. |
| **Il percorso del BLOB inizia con** | `/containername/blobs/foldername/subfoldername/` | È anche possibile fare riferimento a una sottocartella. |
| **Il percorso del BLOB inizia con** | `/containername/blobs/foldername/file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` nella cartella `foldername` nel contenitore `containername`. |
| **Il percorso del BLOB termina con ** | `file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` in qualsiasi percorso. |
| **Il percorso del BLOB termina con ** | `/containername/blobs/file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` nel contenitore `containername`. |
| **Il percorso del BLOB termina con ** | `foldername/file.txt` | Riceve gli eventi per un BLOB denominato `file.txt` nella cartella `foldername` in qualsiasi contenitore. |

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sui trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md#trigger-execution).
