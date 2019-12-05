---
title: Eseguire il debug del linguaggio di query kusto inline Python usando VS Code-Azure Esplora dati
description: Informazioni su come eseguire il debug di Python inline di Kusto Query Language (KQL) con VS Code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: f0bf81ce6392e1650d9986d81a55eac9c416c24d
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822903"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Debug del linguaggio di query kusto inline Python con VS Code

Azure Esplora dati supporta l'esecuzione di codice Python incorporato nel linguaggio di query kusto usando il plug-in [Python ()](/azure/kusto/query/pythonplugin). Il runtime del plug-in è ospitato in una sandbox, un ambiente Python isolato e sicuro. La funzionalità di plug-in Python () estende le funzionalità native di linguaggio di query kusto con il grande archivio di pacchetti OSS Python. Questa estensione consente di eseguire algoritmi avanzati, ad esempio Machine Learning, intelligenza artificiale, statistica e Time Series, come parte della query.

Gli strumenti del linguaggio di query kusto non sono utili per lo sviluppo e il debug degli algoritmi Python. Sviluppare quindi l'algoritmo nei Integrated Development Environment Python preferiti, ad esempio Jupyter, PyCharm, VS o VS Code. Al termine dell'algoritmo, copiare e incollare in KQL. Per migliorare e semplificare questo flusso di lavoro, Azure Esplora dati supporta l'integrazione tra kusto Explorer o i client dell'interfaccia utente Web e VS Code per la creazione e il debug di codice Python inline KQL. 

> [!NOTE]
> Questo flusso di lavoro può essere utilizzato solo per eseguire il debug di tabelle di input relativamente piccole (fino a pochi MB). Pertanto, potrebbe essere necessario limitare l'input per il debug.  Se è necessario elaborare una tabella di grandi dimensioni, limitarla per il debug con `| take`, `| sample`o `where rand() < 0.x`.

## <a name="prerequisites"></a>Prerequisiti

1. Installare la [distribuzione anaconda](https://www.anaconda.com/distribution/#download-section)di Python. In **Opzioni avanzate**selezionare **Aggiungi Anaconda alla variabile di ambiente Path**.
2. Installare [Visual Studio Code](https://code.visualstudio.com/Download)
3. Installare l' [estensione Python per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Eseguire la query nell'applicazione client

1. Nell'applicazione client, anteporre al prefisso una query contenente Python inline con `set query_python_debug;`
1. Eseguire la query.
    * Kusto Explorer: VS Code viene avviato automaticamente con lo script *debug_python. py* .
    * Interfaccia utente Web di Kustori: 
        1. Scaricare e salvare *debug_python. py*, *DF. txt*e *kargs. txt*. In finestra selezionare **Consenti**. **Salva** i file nella directory selezionata. 

            ![L'interfaccia utente Web Scarica i file Python inline](media/debug-inline-python/webui-inline-python.png)

        1. Fare clic con il pulsante destro del mouse su *debug_python. py* e aprire con Visual Studio Code. 
        Lo script *debug_python. py* contiene il codice Python inline dalla query KQL, preceduto dal codice del modello per inizializzare il frame di dati di input da *DF. txt* e il dizionario dei parametri da *kargs. txt*.    
            
1. In Visual Studio Code avviare il debugger di Visual Studio Code: **debug** > **avviare il debug (F5)** , selezionare configurazione di **Python** . Il debugger verrà avviato e verrà automaticamente punto di interruzione per eseguire il debug del codice inline.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>In che modo il debug python inline in VS Code funziona?

1. La query viene analizzata ed eseguita nel server fino a quando non viene raggiunta la clausola `| evaluate python()` richiesta.
1. Il sandbox Python viene richiamato, ma anziché eseguire il codice, serializza la tabella di input, il dizionario dei parametri e il codice e li invia nuovamente al client.
1. Questi tre oggetti vengono salvati in tre file: *DF. txt*, *kargs. txt*e *debug_python. py* nella directory selezionata (interfaccia utente Web) o nella directory% Temp% del client (kusto Explorer).
1. Visual Studio Code viene avviato, precaricato con il file *debug_python. py* che contiene un codice prefisso per inizializzare DF e kargs dai rispettivi file, seguito dallo script Python incorporato nella query KQL.

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

    Vedere la tabella risultante:

    | x  | X4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Eseguire la stessa query KQL nell'applicazione client usando `set query_python_debug;`:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code viene avviato:

    ![avvia Visual Studio Code](media/debug-inline-python/launch-vs-code.png)

1. VS Code esegue il debug e stampa il frame di risultati ' Result ' nella console di debug:

    ![Debug di Visual Studio Code](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Potrebbero esserci differenze tra l'immagine di Python sandbox e l'installazione locale. [Controllare l'immagine sandbox per i pacchetti specifici eseguendo una query sul plug-in](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
