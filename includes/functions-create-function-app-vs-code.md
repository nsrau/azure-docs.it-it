---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444600"
---
## <a name="create-an-azure-functions-project"></a>Creare il progetto di Funzioni di Azure con una funzione 

Il modello di progetto Funzioni di Azure in Visual Studio Code crea un progetto che può essere pubblicato in un'app per le funzioni in Azure. Un'app per le funzioni consente di raggruppare le funzioni come un'unità logica per la gestione, la distribuzione e la condivisione delle risorse.

1. In Visual Studio Code premere F1 per aprire il riquadro comandi. Nel riquadro comandi cercare e selezionare `Azure Functions: Create new project...`.

1. Scegliere una posizione della directory per l'area di lavoro del progetto e quindi scegliere **Seleziona**.

    > [!NOTE]
    > Questa procedura è stata progettata per il completamento all'esterno di un'area di lavoro. In questo caso, non selezionare una cartella di progetto inclusa in un'area di lavoro.

1. Seguire le istruzioni e specificare le informazioni seguenti:

    | Prompt | Valore | DESCRIZIONE |
    | ------ | ----- | ----------- |
    | Selezionare un linguaggio per il progetto di app per le funzioni | C# o JavaScript | Questo articolo supporta C# e JavaScript. Per Python, consultare [questo articolo su Python](https://code.visualstudio.com/docs/python/tutorial-azure-functions). Per PowerShell, consultare [questo articolo su PowerShell](../articles/azure-functions/functions-create-first-function-powershell.md).  |
    | Selezionare un modello per la prima funzione del progetto | Trigger HTTP | Creare una funzione attivata tramite HTTP in nella nuova app per le funzioni. |
    | Specificare un nome di funzione | HttpTrigger | Premere Invio per usare il nome predefinito. |
    | Specificare uno spazio dei nomi | My.Functions | (Solo per C#) Le librerie di classi C# devono avere uno spazio dei nomi.  |
    | Livello di autorizzazione | Funzione | Richiede un [tasto funzione](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) per chiamare l'endpoint HTTP della funzione. |
    | Specificare come aprire il progetto | Aggiungere all'area di lavoro | Crea l'app per le funzioni nell'area di lavoro corrente. |

Visual Studio Code crea il progetto di app per le funzioni in una nuova area di lavoro. Questo progetto contiene i file di configurazione [host.json](../articles/azure-functions/functions-host-json.md) e [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file), oltre ad altri file di progetto specifici per il linguaggio. 

Una nuova funzione attivata tramite HTTP viene creata anche nella cartella HttpTrigger del progetto dell'app di funzione.