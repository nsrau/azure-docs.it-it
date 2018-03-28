---
title: Usare gli strumenti di Analisi di flusso di Azure per Visual Studio per configurare l'integrazione continua e il processo di distribuzione | Microsoft Docs
description: Esercitazione sullo sviluppo di strumenti di Analisi di flusso per Visual Studio per creare ed eseguire il debug di processi Edge di analisi di flusso.
keywords: visual studio, NuGet, DevOps, processi Edge, Analisi di flusso
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
editor: ''
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/13/2018
ms.author: sujie
ms.openlocfilehash: 9362b201fbabc9f8f43647dfd8ac62986b5b6790
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="develop-stream-analytics-edge-jobs-by-using-visual-studio-tools"></a>Sviluppare processi Edge di analisi di flusso usando gli strumenti di Visual Studio

Questa esercitazione illustra come usare gli strumenti di Analisi di flusso per Visual Studio per creare ed eseguire il debug di processi Edge di analisi di flusso. Dopo aver creato e testato il processo, è possibile passare al portale di Azure per distribuirlo ai dispositivi. 

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

* Installare [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) o [Visual Studio 2013 Update 4](https://www.microsoft.com/download/details.aspx?id=45326). Sono supportate le edizioni Enterprise (Ultimate/Premium), Professional e Community. L'edizione Express non è supportata.  

* Seguire le [istruzioni di installazione](stream-analytics-tools-for-visual-studio-edge-jobs.md) per installare gli strumenti di Analisi di flusso per Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Creare un progetto Edge di analisi di flusso 

In Visual Studio selezionare **File** > **Nuovo** > **Progetto**. Passare all'elenco **Modelli** a sinistra > espandere **Analisi di flusso di Azure** > **Stream Analytics Edge** (Analisi di flusso - Edge)  > **Azure Stream Analytics Edge Application** (Applicazione Edge di Analisi di flusso). Specificare un nome per il progetto, il percorso e il nome della soluzione, quindi scegliere **OK**.

![Nuovo progetto Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

Dopo avere creato il progetto, passare a **Esplora soluzioni** per visualizzare la gerarchia di cartelle.

![Visualizzazione Esplora soluzioni](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Scegliere la sottoscrizione corretta

1. Nel menu **Visualizza** di Visual Studio selezionare **Esplora server**.  

2. Fare clic con il pulsante destro del mouse su **Azure** > scegliere **Connetti a sottoscrizione di Microsoft Azure** > e quindi accedere con l'account di Azure.

## <a name="define-inputs"></a>Definire gli input

1. In **Esplora soluzioni** espandere il nodo **Input**. Dovrebbe essere presente un input denominato **EdgeInput.json**. Fare doppio clic per visualizzare le relative impostazioni.  

2. Assicurarsi che Tipo di origine sia impostato su **Flusso dati** > Origine sia impostato su **Hub Edge** > Formato di serializzazione eventi sia impostato su **Json** > e Codifica sia impostato su **UTF8**. Facoltativamente, è possibile rinominare l'**Alias di input**. In questo esempio viene lasciato invariato. Se si rinomina l'alias di input, usare il nome specificato quando si definisce la query. Selezionare **Salva** per salvare le impostazioni.  
   ![Configurazione di input](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Definire gli output

1. In **Esplora soluzioni** espandere il nodo **Output**. Dovrebbe essere presente un input denominato **EdgeOutput.json**. Fare doppio clic per visualizzare le relative impostazioni.  

2. Assicurarsi che Sink sia impostato su **Hub Edge** > Formato di serializzazione eventi sia impostato su **Json** > Codifica sia impostato su **UTF8** > e Formato sia impostato su **Matrice**. Facoltativamente, è possibile rinominare l'**Alias di output**. In questo esempio viene lasciato invariato. Se si rinomina l'alias di output, usare il nome specificato quando si definisce la query. Selezionare **Salva** per salvare le impostazioni. 
   ![Configurazione di output](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Definire la query di trasformazione

I processi di analisi di flusso distribuiti negli ambienti Edge supportano la maggior parte delle [informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396), tuttavia le operazioni seguenti non sono ancora supportate per i processi Edge: 


|**Categoria**  | **Comando**  |
|---------|---------|
|Operatori geospaziali |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|Altri operatori | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>DISTINCT</li><li>Parametro di espressione nell'operatore COUNT</li><li>Microsecondo nelle funzioni di data e ora</li><li>UDA JavaScript (questa funzionalità è ancora in anteprima per i processi distribuiti nel cloud)</li></ul>   |

Quando si crea un processo Edge nel portale, il compilatore visualizzerà automaticamente un avviso se si usa un operatore non supportato.

Da Visual Studio definire la query di trasformazione seguente nell'editor di query (**file script.asaql**)

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Testare il processo in locale

Per testare la query in locale, è necessario caricare i dati di esempio. È possibile ottenere dati di esempio scaricando i dati di registrazione dal [repository GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) e salvandoli nel computer locale. 

1. Per caricare i dati di esempio > fare clic con il pulsante destro del mouse sul file **EdgeInput.json** file > scegliere **Aggiungi input locale**  

2. Nella finestra popup > fare clic su **Sfoglia** per passare ai dati di esempio nel percorso locale > scegliere **Salva**.
   ![Configurazione di input locale](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Verrà aggiunto automaticamente un file denominato **local_EdgeInput.json** nella cartella degli input.  
4. È possibile eseguirlo in locale o inviarlo ad Azure. Per testare la query > selezionare **Esecuzione locale**.  
   ![Opzioni di esecuzione](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. La finestra del prompt dei comandi mostra lo stato del processo. Quando il processo viene eseguito correttamente, viene creata una cartella dal nome simile a "2018-02-23-11-31-42" nel percorso della cartella del progetto "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42". Passare al percorso della cartella per visualizzare i risultati nella cartella locale:

   È anche possibile accedere al portale di Azure e verificare che il processo sia stato creato. 

   ![Cartella risultati](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Inviare il processo ad Azure

1. Prima di inviare il processo ad Azure, è necessario connettersi alla sottoscrizione di Azure. Aprire **Esplora server** > fare clic con il pulsante destro del mouse su **Azure** > **Connetti alla sottoscrizione di Microsoft Azure** > accedere alla sottoscrizione di Azure.  

2. Per inviare il processo ad Azure, passare all'editor di query > selezionare **Invia a Azure**.  

3. Si apre una finestra popup in cui è possibile scegliere di aggiornare un processo Edge esistente o crearne uno nuovo. Quando si aggiorna un processo esistente, questo sostituirà tutte la configurazione di processo. In questo scenario si pubblicherà un nuovo processo. Selezionare **Create a New Azure Stream Analytics Job** (Crea un nuovo progetto di Analisi di flusso di Azure) > immettere un nome per il processo simile a **MyASAEdgeJob** > scegliere **Sottoscrizione**, **Gruppo di risorse** e **Percorso** > Selezionare **Invia**.

   ![Inviare ad Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   Ora che il processo Edge di analisi di flusso è stato creato, è possibile fare riferimento all'esercitazione [Eseguire processi su IoT Edge](stream-analytics-edge.md) per informazioni su come distribuirla ai dispositivi. 

## <a name="manage-the-job"></a>Gestire il processo 

È possibile visualizzare lo stato e il diagramma del processo da Esplora server. In **Esplora server** > **Analisi di flusso** > espandere la sottoscrizione e il gruppo di risorse in cui è stato distribuito il processo Edge. È possibile visualizzare il processo MyASAEdgeJob con lo stato  **Creato**. Espandere il nodo del processo e fare doppio clic su di esso per aprire la visualizzazione del processo.

![Opzioni di Esplora server](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
La finestra di visualizzazione di processo offre la possibilità di eseguire operazioni quali l'aggiornamento del processo, l'eliminazione del processo, l'apertura del processo dal portale di Azure e così via.

![Diagramma del processo e altre opzioni](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Azure IoT Edge](../iot-edge/how-iot-edge-works.md)
* [Esercitazione su Analisi di flusso di Azure in IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Invio di commenti e suggerimenti al team tramite questo sondaggio](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
