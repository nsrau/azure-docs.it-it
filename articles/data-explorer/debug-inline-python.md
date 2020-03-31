---
title: Debug Kusto query language inline Python using VS Code - Azure Data Explorer
description: Informazioni su come eseguire il debug di Kusto query language (KQL) inline Python usando il codice VS.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444465"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Debug Kusto query language inline Python using VS Code

Azure Data Explorer supporta l'esecuzione di codice Python incorporato nel linguaggio di query Kusto usando il [plug-in python().](/azure/kusto/query/pythonplugin) Il runtime del plugin è ospitato in una sandbox, un ambiente Python isolato e sicuro. La funzionalità del plug-in python() estende le funzionalità native del linguaggio di query Kusto con l'enorme archivio dei pacchetti OSS Python. Questa estensione consente di eseguire algoritmi avanzati, ad esempio machine learning, intelligenza artificiale, statistiche e serie temporali come parte della query.

Gli strumenti del linguaggio di query Kusto non sono utili per lo sviluppo e il debug di algoritmi Python. Pertanto, sviluppare l'algoritmo sul vostro ambiente di sviluppo python-integrato preferito come Jupyter, PyCharm, VS, o VS Code. Quando l'algoritmo è completo, copia e incolla in KQL. Per migliorare e semplificare questo flusso di lavoro, Azure Data Explorer supporta l'integrazione tra Kusto Explorer o client dell'interfaccia utente Web e codice VS per la creazione e il debug di codice Python inline KQL. 

> [!NOTE]
> Questo flusso di lavoro può essere utilizzato solo per eseguire il debug di tabelle di input relativamente piccole (fino a pochi MB). Pertanto, potrebbe essere necessario limitare l'input per il debug.  Se è necessario elaborare una tabella di `| take` `| sample`grandi `where rand() < 0.x`dimensioni, limitarla per il debug utilizzando , , o .

## <a name="prerequisites"></a>Prerequisiti

1. Installare Python [Anaconda Distribuzione](https://www.anaconda.com/distribution/#download-section). In **Opzioni avanzate**selezionare Aggiungi **Anaconda alla variabile**di ambiente PATH .
2. Installare il [codice di Visual StudioInstall Visual Studio Code](https://code.visualstudio.com/Download)
3. Installare [l'estensione Python per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Eseguire la query nell'applicazione clientRun your query in your client application

1. Nell'applicazione client, anteporre a una query contenente Python inline`set query_python_debug;`
1. Consente di eseguire la query.
    * Kusto Explorer: il codice VS viene avviato automaticamente con lo script *debug_python.py.*
    * Interfaccia utente Web di Kusto: 
        1. Scaricare e salvare *debug_python.py*, *df.txt*e *kargs.txt*. Nella finestra selezionare **Consenti**. **Salvare i** file nella directory selezionata. 

            ![Web UI scarica i file pitining inline](media/debug-inline-python/webui-inline-python.png)

        1. Fare clic con il pulsante destro del mouse su *debug_python.py* e aprire con codice VS. 
        Lo script *debug_python.py* contiene il codice Python inline, dalla query KQL, preceduto dal codice del modello per inizializzare il frame di dati di input da *df.txt* e il dizionario dei parametri da *kargs.txt*.    
            
1. Nel codice VS, avviare il debugger del codice VS: **Debug Avvio** > debug (F5) , selezionare configurazione Python.In VS code, launch the VS code debugger: Debug**Start Debugging (F5)**, select **Python** configuration. Il debugger verrà avviato e si stendo automaticamente per eseguire il debug del codice inline.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Come funziona il debug Python inline nel codice VS?

1. La query viene analizzata ed eseguita `| evaluate python()` nel server fino a quando non viene raggiunta la clausola obbligatoria.
1. La sandbox Python viene richiamata ma invece di eseguire il codice, serializza la tabella di input, il dizionario dei parametri e il codice e li invia al client.
1. Questi tre oggetti vengono salvati in tre file: *df.txt*, *kargs.txt*e *debug_python.py* nella directory selezionata (Web UI) o nella directory %TEMP% del client (Kusto Explorer).
1. Viene avviato il codice VS, precaricato con il file *debug_python.py* che contiene un codice di prefisso per inizializzare df e kargs dai rispettivi file, seguito dallo script Python incorporato nella query KQL.

## <a name="query-example"></a>Esempio di query

1. Eseguire la query KQL seguente nell'applicazione client:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Vedere la tabella risultante:See the resulting table:

    | x  | x4 (in questo modo)  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Eseguire la stessa query KQL `set query_python_debug;`nell'applicazione client utilizzando:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. Viene avviato il codice VS:

    ![lanciare il codice VS](media/debug-inline-python/launch-vs-code.png)

1. VS Codice esegue il debug e stampa 'risultato' frame dati nella console di debug:

    ![Debug del codice VS](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Potrebbero esserci differenze tra l'immagine sandbox Python e l'installazione locale. [Controllare l'immagine sandbox per pacchetti specifici eseguendo una query sul plugin](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
