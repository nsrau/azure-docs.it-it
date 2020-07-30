---
title: Esercitazione sui connettori di Video Indexer con App per la logica e Power Automate.
description: Questa esercitazione illustra come sbloccare nuove esperienze e opportunità di monetizzazione dei connettori di Video Indexer con App per la logica e Power Automate.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 05/01/2020
ms.openlocfilehash: 5f29e616c0643914ca28921eee481105a5feb0c5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047106"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Esercitazione: Usare Video Indexer con App per la logica e Power Automate

L'[API REST Video Indexer v2](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) di Servizi multimediali di Azure supporta la comunicazione da server a server e da client a server e consente agli utenti di Video Indexer di integrare facilmente informazioni dettagliate su video e audio nella logica dell'applicazione, sbloccando nuove esperienze e opportunità di monetizzazione.

Per semplificare ulteriormente l'integrazione, sono supportati i connettori per  [App per la logica](https://azure.microsoft.com/services/logic-apps/) e [Power Automate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/) compatibili con l'API. È possibile usare i connettori per configurare flussi di lavoro personalizzati per indicizzare ed estrarre in modo efficiente informazioni dettagliate da una grande quantità di file audio e video, senza scrivere una sola riga di codice. Inoltre, l'uso dei connettori per l'integrazione offre una maggiore visibilità sull'integrità del flusso di lavoro e un modo semplice per eseguirne il debug.  

Per iniziare rapidamente a usare i connettori di Video Indexer, viene illustrata una procedura dettagliata di un esempio di soluzione di App per la logica e Power Automate che è possibile configurare. 

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Caricare e indicizzare automaticamente i video
> * Configurare il flusso di caricamento dei file
> * Configurare il flusso di estrazione JSON

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sarà necessario anche un account di Video Indexer insieme all'accesso alle API tramite chiave API. 

Sarà necessario anche un account di Archiviazione di Azure. Prendere nota della chiave di accesso per l'account di archiviazione. Creare due contenitori, uno per archiviare i video in e l'altro per archiviare le informazioni dettagliate generate da Video Indexer.  

Successivamente, sarà necessario aprire due flussi distinti in App per la logica o Power Automate (a seconda di quale servizio si usi).  

## <a name="upload-and-index-your-video-automatically"></a>Caricare e indicizzare automaticamente i video 

Questo scenario è costituito da due flussi diversi che interagiscono tra loro. Il primo flusso viene attivato quando viene aggiunto o modificato un BLOB in un account di Archiviazione di Azure. Carica il nuovo file in Video Indexer con un URL di callback per inviare una notifica al termine dell'operazione di indicizzazione. Il secondo flusso viene attivato in base all'URL di callback e salva le informazioni dettagliate estratte in un file JSON in archiviazione di Azure. Questo approccio con due flussi viene usato per supportare in modo efficiente il caricamento asincrono e l'indicizzazione di file di grandi dimensioni. 

### <a name="set-up-the-file-upload-flow"></a>Configurare il flusso di caricamento dei file 

Il primo flusso viene attivato ogni volta che viene aggiunto un BLOB nel contenitore di Archiviazione di Azure. Una volta attivato, crea un URI di firma di accesso condiviso che è possibile usare per caricare e indicizzare il video in Video Indexer. Per iniziare, creare il flusso seguente. 

![Flusso di caricamento di file](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Per configurare il primo flusso, è necessario specificare la chiave API di Video Indexer e le credenziali di Archiviazione di Azure. 

![Archivio BLOB di Azure](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Nome della connessione e chiave API](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

Quando è possibile connettersi agli account di Archiviazione di Azure e di Video Indexer, passare al trigger "Quando viene aggiunto o modificato un BLOB" e selezionare il contenitore in cui verranno inseriti i file video. 

![Contenitore](./media/logic-apps-connector-tutorial/container.png)

Passare quindi all'azione "Crea URI di firma di accesso condiviso in base al percorso" e selezionare il percorso dell'elenco di file nelle opzioni di contenuto dinamico.  

![URI di firma di accesso condiviso in base al percorso](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Inserire [la località e l'ID dell'account](./video-indexer-use-apis.md#account-id) per ottenere il token dell'account di Video Indexer.

![Ottenere il token di accesso all'account](./media/logic-apps-connector-tutorial/account-access-token.png)

Per "Upload video and index" (Carica video e indice), inserire i parametri richiesti e l'URL del video. Selezionare "Aggiungi nuovo parametro" e quindi URL callback. 

![Caricare e indicizzare](./media/logic-apps-connector-tutorial/upload-and-index.png)

Per il momento, lasciare vuoto il campo URL callback. Verrà aggiunto solo dopo avere completato il secondo flusso in cui viene creato l'URL di callback. 

È possibile usare il valore predefinito per gli altri parametri o impostarli in base alle esigenze. 

Fare clic su "Salva" e procedere alla configurazione del secondo flusso per estrarre le informazioni dopo il caricamento e l'indicizzazione. 

## <a name="set-up-the-json-extraction-flow"></a>Configurare il flusso di estrazione JSON 

Una volta completato il caricamento e l'indicizzazione con il primo flusso, verrà inviata una richiesta HTTP con l'URL di callback corretto per attivare il secondo flusso. Verranno quindi recuperate le informazioni dettagliate generate da Video Indexer. In questo esempio l'output del processo di indicizzazione verrà archiviato in Archiviazione di Azure.  Tuttavia, è possibile scegliere quale operazione eseguire con l'output.  

Creare il secondo flusso separato dal primo. 

![Flusso di estrazione JSON](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Per configurare questo flusso, è necessario specificare di nuovo la chiave API di Video Indexer e le credenziali di Archiviazione di Azure. Sarà necessario aggiornare gli stessi parametri come per il primo flusso. 

Per il trigger, viene visualizzato un campo URL POST HTTP. L'URL verrà generato solo dopo il salvataggio del flusso, anche se sarà eventualmente necessario, come descritto più avanti. 

Inserire [la località e l'ID dell'account](./video-indexer-use-apis.md#account-id) per ottenere il token dell'account di Video Indexer.  

Passare all'azione "Get Video Index" (Ottieni indice video) e inserire i parametri richiesti. Per l'ID video, inserire l'espressione triggerOutputs()['queries']['id'] 

![Informazioni sull'azione di Video Indexer](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Questa espressione indica al connettore di ottenere l'ID video dall'output del trigger. In questo caso, l'output del trigger sarà l'output dell'azione "Upload video and index" (Carica video e indice) del primo trigger. 

Passare all'azione "Crea BLOB" e selezionare il percorso della cartella in cui salvare le informazioni dettagliate. Impostare il nome del BLOB da creare. Per il contenuto del BLOB, inserire l'espressione body('Get_Video_Index') 

![Azione di creazione del BLOB](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Questa espressione accetta l'output dell'azione "Get Video Index" (Ottieni indice video) da questo flusso. 

Fare clic su "Salva flusso". 

Una volta salvato il flusso, nel trigger viene creato un URL POST HTTP. Copiare l'URL dal trigger. 

![Salvare il trigger dell'URL](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Tornare ora al primo flusso e incollare l'URL nell'azione "Upload video and index" (Carica video e indice) per il parametro URL callback. 

Assicurarsi che entrambi i flussi siano stati salvati ed è fatta. 

Provare la soluzione di App per la logica o Power Automate appena creata aggiungendo un video al contenitore BLOB di Azure e dopo alcuni minuti verificare se le informazioni dettagliate vengono visualizzate nella cartella di destinazione. 

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato questa esercitazione, se necessario è possibile mantenere attiva questa soluzione di App per la logica e Power Automate. Se invece si sceglie di non mantenerla attiva e non si vogliono ricevere addebiti, disattivare entrambi i flussi se si usa Power Automate. Disabilitare entrambi i flussi se si usa App per la logica. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato solo un esempio di connettori di Video Indexer. È possibile usare i connettori di Video Indexer per qualsiasi chiamata API fornita da Video Indexer, ad esempio per caricare e recuperare informazioni dettagliate, tradurre i risultati, ottenere widget incorporabili e anche personalizzare i modelli. Inoltre, è possibile scegliere di attivare queste azioni in base a diverse origini, ad esempio gli aggiornamenti ai repository di file o i messaggi di posta elettronica inviati. È quindi possibile scegliere di fare in modo che i risultati vengano aggiornati nell'infrastruttura o nell'applicazione pertinente oppure di generare un numero qualsiasi di elementi di azione.  

> [!div class="nextstepaction"]
> [Usare l'API Video Indexer](video-indexer-use-apis.md)
