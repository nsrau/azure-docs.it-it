---
title: Creare la prima funzione in Azure con Visual Studio Code
description: Creare e pubblicare in Azure una semplice funzione attivata tramite HTTP usando l'estensione Funzioni di Azure in Visual Studio Code.
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842257"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>Avvio rapido: Creare un progetto Funzioni di Azure con Visual Studio Code

In questo articolo si userà Visual Studio Code per creare una funzione che risponde alle richieste HTTP. Dopo aver testato il codice in locale, verrà distribuito nell'ambiente serverless di Funzioni di Azure. Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure. 

È inoltre disponibile una [versione basata su interfaccia della riga di comando](functions-create-first-azure-function-azure-cli.md) di questo articolo.

## <a name="prerequisites"></a>Prerequisites

+ [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms). 
::: zone pivot="programming-language-csharp"
+ [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) per Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), versioni Active LTS e Maintenance LTS (8.11.1 e 10.14.1 consigliate).
::: zone-end
::: zone pivot="programming-language-python"
+ [Python 3.7](https://www.python.org/downloads/release/python-375/) o [Python 3.6](https://www.python.org/downloads/release/python-368/), supportato da Funzioni di Azure. Python 3.8 non è ancora supportato. 

+ [Estensione Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) per Visual Studio Code.
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)

+ [Estensione PowerShell per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell). 
::: zone-end

+ [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code. 

+ Un [account Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) con una sottoscrizione attiva. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="create-an-azure-functions-project"></a>Creare il progetto locale 

In questa sezione si userà Visual Studio Code per creare un progetto di Funzioni di Azure locale nel linguaggio scelto. Più avanti in questo articolo verrà pubblicato il codice della funzione in Azure. 

1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** selezionare l'icona **Crea nuovo progetto...** .

    ![Scegliere Crea nuovo progetto](media/functions-create-first-function-vs-code/create-new-project.png)

1. Scegliere una posizione della directory per l'area di lavoro del progetto e quindi scegliere **Seleziona**.

    > [!NOTE]
    > Questa procedura è stata progettata per il completamento all'esterno di un'area di lavoro. In questo caso, non selezionare una cartella di progetto inclusa in un'area di lavoro.

1. Quando richiesto, immettere le informazioni seguenti:

    ::: zone pivot="programming-language-csharp"

    | Prompt | valore | 
    | ------ | ----- | 
    | Selezionare un linguaggio per il progetto di funzione | C# |
    | Selezionare una versione | Funzioni di Azure v2 | 
    | Selezionare un modello per la prima funzione del progetto | Trigger HTTP | 
    | Specificare un nome di funzione | HttpExample | 
    | Specificare uno spazio dei nomi | My.Functions | 
    | Livello di autorizzazione | Anonima | 
    | Specificare come aprire il progetto | Aggiungere all'area di lavoro |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Prompt | valore | 
    | ------ | ----- | 
    | Selezionare un linguaggio per il progetto di funzione | JavaScript | 
    | Selezionare una versione | Funzioni di Azure v2 | 
    | Selezionare un modello per la prima funzione del progetto | Trigger HTTP | 
    | Specificare un nome di funzione | HttpExample | 
    | Livello di autorizzazione | Anonima | 
    | Specificare come aprire il progetto | Aggiungere all'area di lavoro |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Prompt | valore | 
    | ------ | ----- | 
    | Selezionare un linguaggio per il progetto di funzione | TypeScript | 
    | Selezionare una versione | Funzioni di Azure v2 | 
    | Selezionare un modello per la prima funzione del progetto | Trigger HTTP | 
    | Specificare un nome di funzione | HttpExample | 
    | Livello di autorizzazione | Anonima | 
    | Specificare come aprire il progetto | Aggiungere all'area di lavoro |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Prompt | valore | 
    | ------ | ----- | 
    | Selezionare un linguaggio per il progetto di funzione | PowerShell | 
    | Selezionare una versione | Funzioni di Azure v2 | 
    | Selezionare un modello per la prima funzione del progetto | Trigger HTTP | 
    | Specificare un nome di funzione | HttpExample | 
    | Livello di autorizzazione | Anonima | 
    | Specificare come aprire il progetto | Aggiungere all'area di lavoro |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Prompt | valore | 
    | ------ | ----- | 
    | Selezionare un linguaggio per il progetto di funzione | Python | 
    | Selezionare una versione | Funzioni di Azure v2 | 
    | Selezionare un alias Python per creare un ambiente virtuale | Alias Python | 
    | Selezionare un modello per la prima funzione del progetto | Trigger HTTP | 
    | Specificare un nome di funzione | HttpExample | 
    | Livello di autorizzazione | Anonima | 
    | Specificare come aprire il progetto | Aggiungere all'area di lavoro | 

    ::: zone-end

1. Visual Studio Code esegue le operazioni seguenti:

    + Crea in una nuova area di lavoro un progetto di Funzioni di Azure che contiene i file di configurazione [host.json](functions-host-json.md) e [local.settings.json](functions-run-local.md#local-settings-file). 

    ::: zone pivot="programming-language-csharp"

    + Crea una [file di libreria di classi HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project) che implementa la funzione.

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + Crea un file package.json nella cartella radice.

    + Crea una cartella HttpExample che contiene il [file di definizione function.json](functions-reference-node.md#folder-structure) e il [file index.js](functions-reference-node.md#exporting-a-function), un file Node.js che contiene il codice della funzione.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + Crea un file package.json e un file tsconfig.json nella cartella radice.

    + Crea una cartella HttpExample che contiene il [file di definizione function.json](functions-reference-node.md#folder-structure) e il [file index.ts](functions-reference-node.md#typescript), un file TypeScript che contiene il codice della funzione.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + Crea una cartella HttpExample che contiene il [file di definizione function.json](functions-reference-python.md#programming-model) e il file run.ps1 che contiene il codice della funzione.
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + Crea un file requirements.txt a livello di progetto che elenca i pacchetti richiesti da Funzioni di Azure.
    
    + Crea una cartella HttpExample che contiene il [file di definizione function.json](functions-reference-python.md#programming-model) e il file \_\_init\_\_.py che contiene il codice della funzione.
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile usare Visual Studio Code per pubblicare il progetto direttamente in Azure. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Eseguire la funzione in Azure

1. Copiare l'URL del trigger HTTP dal pannello **Output**. Di nuovo, aggiungere la stringa di query `name` come `?name=<yourname>` alla fine dell'URL ed eseguire la richiesta.

    Il formato dell'URL che chiama la funzione attivata tramite HTTP sarà simile al seguente:

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. Incollare questo nuovo URL per la richiesta HTTP nella barra degli indirizzi del browser. L'esempio seguente mostra la risposta nel browser alla richiesta GET remota restituita dalla funzione: 

    ![Risposta della funzione nel browser](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per continuare con il passaggio successivo, [Aggiungere alla funzione un'associazione alla coda di archiviazione di Azure](functions-add-output-binding-storage-queue-vs-code.md), sarà necessario mantenere tutte le risorse esistenti per poterle riutilizzare.

In caso contrario, seguire questa procedura per eliminare l'app per le funzioni e le risorse correlate per evitare l'addebito di costi aggiuntivi.

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

Per altre informazioni sui costi di Funzioni di Azure, vedere [Stima dei costi del piano a consumo](functions-consumption-costs.md).

## <a name="next-steps"></a>Passaggi successivi

È stato usato Visual Studio Code per creare un'app per le funzioni con una semplice funzione attivata tramite HTTP. Il prossimo articolo descrive come espandere questa funzione aggiungendo un binding di output. Questo binding scrive la stringa della richiesta HTTP in un messaggio in una coda dell'archiviazione code di Azure. 

> [!div class="nextstepaction"]
> [Aggiungere un binding della coda di archiviazione di Azure alla funzione](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
