---
title: Creare la prima funzione durevole in Funzioni di Azure con PowerShell
description: Creare e pubblicare un'istanza di Azure Durable Functions in PowerShell con Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 08/10/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: edd02085abe63b124082255247362f096248ba82
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91317260"
---
# <a name="create-your-first-durable-function-in-powershell"></a>Creare la prima funzione durevole in PowerShell

*Durable Functions* è un'estensione di [Funzioni di Azure](../functions-overview.md) che consente di scrivere funzioni con stato in un ambiente senza server. L'estensione gestisce automaticamente lo stato, i checkpoint e i riavvii.

Questo articolo spiega come l'estensione di Funzioni di Azure in Visual Studio Code per creare e testare una funzione durevole "hello world" in locale.  Questa funzione orchestrerà e concatenerà le chiamate ad altre funzioni. Il codice della funzione verrà quindi pubblicato in Azure.

![Esecuzione di una funzione durevole in Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* Installare [Visual Studio Code](https://code.visualstudio.com/download).

* Installare l'[estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per VS Code.

* Assicurarsi di avere la versione più recente di [Azure Functions Core Tools](../functions-run-local.md).

* Durable Functions richiede un account di archiviazione di Azure. È necessaria una sottoscrizione di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Creare il progetto locale 

In questa sezione si userà Visual Studio Code per creare un progetto di Funzioni di Azure locale. 

1. In Visual Studio Code premere F1 o CTRL/CMD+MAIUSC+P per aprire il riquadro comandi. Nel riquadro comandi cercare e selezionare `Azure Functions: Create New Project...`.

    ![Creare una funzione](media/quickstart-js-vscode/functions-create-project.png)

1. Scegliere una cartella vuota come posizione del progetto e quindi **Seleziona**.

1. Seguire le istruzioni e specificare le informazioni seguenti:

    | Prompt | Valore | Descrizione |
    | ------ | ----- | ----------- |
    | Selezionare un linguaggio per il progetto di app per le funzioni | PowerShell | Creare un progetto Funzioni PowerShell in locale. |
    | Selezionare una versione | Azure Functions v3 (Funzioni di Azure v3) | Questa opzione viene visualizzata solo quando gli strumenti Core Tools non sono già installati. In questo caso, gli strumenti Core Tools vengono installati la prima volta che si esegue l'app. |
    | Selezionare un modello per la prima funzione del progetto | Skip for now (Ignora per ora) | |
    | Specificare come aprire il progetto | Open in current window (Apri nella finestra corrente) | Riapre VS Code nella cartella selezionata. |

Se necessario, Visual Studio Code installa Azure Functions Core Tools. Crea inoltre un progetto di app per le funzioni in una cartella. Questo progetto contiene i file di configurazione [host.json](../functions-host-json.md) e [local.settings.json](../functions-run-local.md#local-settings-file).

Viene inoltre creato un file package.json nella cartella radice.

### <a name="configure-function-app-to-use-powershell-7"></a>Configurare l'app per le funzioni per l'uso di PowerShell 7

Aprire il file *local.settings.json* e verificare che un'impostazione denominata `FUNCTIONS_WORKER_RUNTIME_VERSION` sia impostata su `~7`. Se non è presente o è impostata su un altro valore, aggiornare il contenuto del file.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

## <a name="create-your-functions"></a>Creare le funzioni

L'app Durable Functions più semplice contiene le tre funzioni seguenti.

* *Funzione dell'agente di orchestrazione*: descrive un flusso di lavoro che orchestra le altre funzioni.
* *Funzione dell'attività*: chiamata dalla funzione dell'agente di orchestrazione, esegue le operazioni e restituisce facoltativamente un valore.
* *Funzione client*: normale funzione di Azure che avvia una funzione dell'agente di orchestrazione. Questo esempio usa una funzione attivata tramite HTTP.

### <a name="orchestrator-function"></a>Funzione dell'agente di orchestrazione

Per creare il codice della funzione durevole nel progetto si usa un modello.

1. Nel riquadro comandi cercare e selezionare `Azure Functions: Create Function...`.

1. Seguire le istruzioni e specificare le informazioni seguenti:

    | Prompt | Valore | Descrizione |
    | ------ | ----- | ----------- |
    | Select a template for your function (Selezionare un modello per la funzione) | Durable Functions Orchestrator (anteprima) | Creare un'orchestrazione Durable Functions |
    | Specificare un nome di funzione | HelloOrchestrator | Nome della funzione durevole |

È stato aggiunto un agente di orchestrazione per coordinare le funzioni dell'attività. Aprire *HelloOrchestrator/run.ps1* per visualizzare la funzione dell'agente di orchestrazione. Ogni chiamata al cmdlet `Invoke-ActivityFunction` richiama una funzione dell'attività denominata `Hello`.

Si aggiungerà ora la funzione dell'attività `Hello` cui viene fatto riferimento.

### <a name="activity-function"></a>Funzione dell'attività

1. Nel riquadro comandi cercare e selezionare `Azure Functions: Create Function...`.

1. Seguire le istruzioni e specificare le informazioni seguenti:

    | Prompt | Valore | Descrizione |
    | ------ | ----- | ----------- |
    | Select a template for your function (Selezionare un modello per la funzione) | Durable Functions Activity (anteprima) | Creare una funzione dell'attività |
    | Specificare un nome di funzione | Ciao | Nome della funzione dell'attività |

È stata aggiunta la funzione dell'attività `Hello` richiamata dall'agente di orchestrazione. Aprire *Hello/run.ps1* per verificare che accetti un nome come input e restituisca un messaggio di saluto. In una funzione dell'attività si eseguiranno azioni come una chiamata al database o un calcolo.

Si aggiungerà infine una funzione attivata tramite HTTP che avvia l'orchestrazione.

### <a name="client-function-http-starter"></a>Funzione client (Http Starter)

1. Nel riquadro comandi cercare e selezionare `Azure Functions: Create Function...`.

1. Seguire le istruzioni e specificare le informazioni seguenti:

    | Prompt | Valore | Descrizione |
    | ------ | ----- | ----------- |
    | Select a template for your function (Selezionare un modello per la funzione) | Durable Functions Http Starter (anteprima) | Creare una funzione Http Starter |
    | Specificare un nome di funzione | HttpStart | Nome della funzione dell'attività |
    | Livello di autorizzazione | Anonima | A scopo dimostrativo, consentire la chiamata della funzione senza autenticazione |

È stata aggiunta una funzione attivata tramite HTTP che avvia un'orchestrazione. Aprire *HttpStart/run.ps1* per verificare che venga usato il cmdlet `Start-NewOrchestration` per avviare una nuova orchestrazione. Verrà quindi usato il cmdlet `New-OrchestrationCheckStatusResponse` per restituire una risposta HTTP contenente gli URL che possono essere usati per monitorare e gestire la nuova orchestrazione.

È ora disponibile un'app Durable Functions eseguibile in locale e distribuibile in Azure.

## <a name="test-the-function-locally"></a>Testare la funzione in locale

Azure Functions Core Tools consente di eseguire un progetto Funzioni di Azure nel computer di sviluppo locale. Viene richiesto di installare questi strumenti al primo avvio di un'app per le funzioni da Visual Studio Code.

1. Per testare la funzione, impostare un punto di interruzione nel codice della funzione dell'attività `Hello` (*Hello/run.ps1*). Premere F5 o selezionare `Debug: Start Debugging` nel riquadro comandi per avviare il progetto di app per le funzioni. L'output dagli strumenti di base viene visualizzato nel pannello **Terminale**.

    > [!NOTE]
    > Per altre informazioni sul debug, vedere [Diagnostica in Durable Functions](durable-functions-diagnostics.md#debugging).

1. Per l'esecuzione di Durable Functions è necessario un account di archiviazione di Azure. Quando VS Code richiede di selezionare un account di archiviazione, scegliere **Select storage account** (Seleziona account di archiviazione).

    ![Crea account di archiviazione](media/quickstart-js-vscode/functions-select-storage.png)

1. Seguire le istruzioni e specificare le informazioni seguenti per creare un nuovo account di archiviazione in Azure.

    | Prompt | Valore | Descrizione |
    | ------ | ----- | ----------- |
    | Seleziona sottoscrizione | *nome della sottoscrizione* | Selezionare la sottoscrizione ad Azure |
    | Select a storage account (Selezionare un account di archiviazione) | Creare un nuovo account di archiviazione. |  |
    | Enter the name of the new storage account (Immettere il nome del nuovo account di archiviazione) | *nome univoco* | Nome dell'account di archiviazione da creare |
    | Selezionare un gruppo di risorse | *nome univoco* | Nome del gruppo di risorse da creare |
    | Selezionare una località | *region* | Selezionare un'area nelle vicinanze |

1. Nel pannello **Terminale** copiare l'endpoint dell'URL della funzione attivata da HTTP.

    ![Output locale di Azure](media/quickstart-js-vscode/functions-f5.png)

1. Usare il browser o uno strumento come [Postman](https://www.getpostman.com/) o [cURL](https://curl.haxx.se/) per inviare una richiesta HTTP POST all'endpoint URL. Sostituire l'ultimo segmento con il nome della funzione dell'agente di orchestrazione (`HelloOrchestrator`). L'URL sarà simile a `http://localhost:7071/api/orchestrators/HelloOrchestrator`.

   La risposta è il risultato iniziale della funzione HTTP che informa che l'orchestrazione durevole è stata avviata correttamente. Non è ancora il risultato finale dell'orchestrazione. La risposta include alcuni URL utili. Per il momento si eseguirà una query relativa allo stato dell'orchestrazione.

1. Copiare il valore dell'URL per `statusQueryGetUri`, incollarlo nella barra degli indirizzi del browser ed eseguire la richiesta. In alternativa è possibile continuare a usare Postman per inviare la richiesta GET.

   La richiesta eseguirà una query per determinare lo stato dell'istanza di orchestrazione. Si otterrà una risposta conclusiva in cui si specifica che l'istanza è stata completata e contenente l'output o i risultati della funzione durevole. L'aspetto sarà simile al seguente: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Per interrompere il debug, premere **MAIUSC+F5** in VS Code.

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile pubblicare il progetto in Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Pubblicare il progetto in Azure

In questa sezione verrà creata un'app per le funzioni con le risorse correlate nella sottoscrizione di Azure e quindi verrà distribuito il codice. 

> [!IMPORTANT]
> La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure. 


1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** scegliere il pulsante **Deploy to function app...** (Distribuisci nell'app per le funzioni...).

    ![Pubblicare il progetto in Azure](../../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare la cartella**: scegliere una cartella dall'area di lavoro o selezionarne una che contenga l'app per le funzioni. Questa opzione non verrà visualizzata se è già stata aperta un'app per le funzioni valida.

    + **Selezionare la sottoscrizione**: scegliere la sottoscrizione da usare. Questa opzione non è visibile se è disponibile una sola sottoscrizione.

    + **Selezionare l'app per le funzioni in Azure**: Scegliere `+ Create new Function App`. Non scegliere l'opzione `Advanced`, che non è illustrata in questo articolo.
      
    + **Immettere un nome univoco a livello globale per l'app per le funzioni**: digitare un nome valido in un percorso URL. Il nome digitato viene convalidato per assicurarsi che sia univoco in Funzioni di Azure. 

    + **Selezionare un runtime**: scegliere la versione di PowerShell in esecuzione in locale. Per verificare la versione in uso, eseguire il comando `pwsh -version`.

        > [!NOTE]
        > L'estensione VS Code di Funzioni di Azure potrebbe non supportare ancora PowerShell 7. Se PowerShell 7 non è disponibile come opzione, selezionare PowerShell 6.x per il momento e [aggiornare manualmente la versione](#update-function-app-ps7) dopo la creazione dell'app per le funzioni.

    + **Selezionare una località per le nuove risorse**:  per prestazioni ottimali, scegliere un'[area](https://azure.microsoft.com/regions/) vicina. 
    
1.  Al termine, nella sottoscrizione vengono create le risorse di Azure seguenti con i nomi basati sul nome dell'app per le funzioni:
    
    + Un gruppo di risorse, ovvero un contenitore logico di risorse correlate.
    + Un account di archiviazione di Azure Standard, che mantiene lo stato e altre informazioni sul progetto.
    + Un piano a consumo, che definisce l'host sottostante per l'app per le funzioni serverless. 
    + Un'app per le funzioni, che fornisce l'ambiente per l'esecuzione del codice della funzione. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione delle risorse all'interno dello stesso piano di hosting.
    + Un'istanza di Application Insights connessa all'app per le funzioni, che tiene traccia dell'utilizzo della funzione serverless.

    Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica.

1. <a name="update-function-app-ps7"></a>Se non è stato possibile selezionare *PowerShell 7* in precedenza durante la creazione dell'app per le funzioni, premere F1 (oppure CTRL/CMD+MAIUSC+P) per aprire il riquadro comandi. Nel riquadro comandi cercare e selezionare `Azure Functions: Upload Local Settings...`. Seguire le istruzioni visualizzate per selezionare l'app per le funzioni creata. Se viene richiesto di sovrascrivere le impostazioni esistenti, selezionare *No a tutti*.
    
1. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create. Se non si riesce a visualizzare la notifica, selezionare l'icona della campana nell'angolo in basso a destra per visualizzarla di nuovo.

    ![Notifica di creazione completata](../../../includes/media/functions-publish-project-vscode/function-create-notifications.png)


## <a name="test-your-function-in-azure"></a>Testare la funzione in Azure

1. Copiare l'URL del trigger HTTP dal pannello **Output**. L'URL che chiama la funzione attivata tramite HTTP deve essere nel formato `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Incollare questo nuovo URL per la richiesta HTTP nella barra degli indirizzi del browser. Quando si usa l'app pubblicata si dovrebbe ottenere la stessa risposta di stato restituita in precedenza.

## <a name="next-steps"></a>Passaggi successivi

Si è usato Visual Studio Code per creare e pubblicare un'app per le funzioni durevoli in PowerShell.

> [!div class="nextstepaction"]
> [Informazioni sui criteri comuni delle funzioni durevoli](durable-functions-overview.md#application-patterns)
