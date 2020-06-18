---
title: Eseguire il debug di un set di competenze per l'arricchimento tramite intelligenza artificiale (anteprima)
titleSuffix: Azure Cognitive Search
description: Le sessioni di debug, accessibili tramite il portale di Azure, forniscono un ambiente di tipo IDE in cui è possibile identificare e correggere gli errori, convalidare le modifiche ed effettuare il push delle modifiche ai set di competenze nella pipeline di arricchimento tramite intelligenza artificiale. Le sessioni di debug sono disponibili in anteprima.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 04b221d772abf923d7aabfe767a6424b72ed8fb2
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83662409"
---
# <a name="debug-sessions"></a>Sessioni di debug

Sessioni di debug è un editor visivo che funziona con un set di competenze esistente nel portale di Azure. All'interno di una sessione di debug è possibile identificare e risolvere gli errori, convalidare le modifiche ed effettuare il push delle modifiche a un set di competenze di produzione nella pipeline di arricchimento tramite intelligenza artificiale.

> [!Important]
> Il supporto delle sessioni di debug per Ricerca cognitiva di Azure è disponibile [su richiesta](https://aka.ms/DebugSessions) come anteprima con accesso limitato. Le funzionalità di anteprima vengono messe a disposizione senza contratto di servizio e non sono consigliate per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Dopo aver ottenuto l'accesso all'anteprima, sarà possibile accedere alle sessioni di debug e usarle per il servizio tramite il portale di Azure.

## <a name="using-debug-sessions"></a>Uso delle sessioni di debug

Quando si avvia una sessione, il servizio crea una copia del set di competenze, dell'indicizzatore e dell'indice, in cui viene usato un singolo documento per testare il set di competenze. Le modifiche apportate all'interno della sessione vengono salvate nella sessione. Le modifiche apportate all'interno della sessione di debug non avranno effetto sul set di competenze di produzione, a meno che non venga eseguito il commit delle modifiche. Il commit delle modifiche comporterà la sovrascrittura del set di competenze di produzione.

Durante una sessione di debug è possibile modificare un set di competenze, controllare e convalidare ogni nodo nell'albero di arricchimento nel contesto di un documento specifico. Dopo aver risolto i problemi della pipeline di arricchimento, è possibile salvare le modifiche nella sessione ed eseguirne il commit in un set di competenze nell'ambiente di produzione. 

Se la pipeline di arricchimento non contiene errori, è possibile usare una sessione di debug per arricchire in modo incrementale un documento, testare e convalidare ogni modifica prima di eseguire il commit delle modifiche.

## <a name="creating-a-debug-session"></a>Creazione di una sessione di debug

Per avviare una sessione di debug, è necessario disporre di una pipeline esistente di arricchimento tramite intelligenza artificiale, che include un'origine dati, un set di competenze, un indicizzatore e un indice. Per configurare una sessione di debug, è necessario assegnare un nome alla sessione e fornire un account di archiviazione per utilizzo generico che verrà usato per memorizzare nella cache le esecuzioni delle competenze durante l'esecuzione dell'indicizzatore. Sarà anche necessario selezionare l'indicizzatore che verrà eseguito. Nell'indicizzatore sono archiviati riferimenti all'origine dati, al set di competenze e all'indice. Per impostazione predefinita, la sessione di debug viene impostata sul primo documento nell'origine dati. In alternativa, è possibile specificare un documento nell'origine dati di cui eseguire il debug.

> [!div class="mx-imgBorder"]
> ![Creazione di una sessione di debug](media/cognitive-search-debug/debug-session-new.png)

## <a name="debug-session-features"></a>Funzionalità delle sessioni di debug

La sessione di debug inizia con l'esecuzione del set di competenze nel documento selezionato. La sessione di debug registrerà metadati aggiuntivi associati a ogni operazione nel set di competenze. I metadati creati dalle esecuzioni delle competenze della pipeline determinano il set di funzionalità seguenti, che vengono quindi usate per identificare e correggere i problemi relativi al set di competenze.

## <a name="ai-enrichments"></a>Arricchimenti tramite intelligenza artificiale

Durante l'esecuzione delle competenze, un albero di arricchimenti, che rappresenta il documento, aumenta. L'uso di un albero per visualizzare gli output delle competenze o degli arricchimenti fornisce informazioni complete su tutti gli arricchimenti eseguiti. È possibile controllare l'intero documento ed esaminare ogni nodo dell'albero di arricchimento. Questa prospettiva rende più semplice dare forma agli oggetti. Questo formato fornisce anche indicazioni visive per il tipo, il percorso e il contenuto di ogni nodo nell'albero.

## <a name="skill-graph"></a>Grafo delle competenze

La visualizzazione **Grafo delle competenze** fornisce una rappresentazione visiva gerarchica del set di competenze. Il grafo è una rappresentazione dall'alto verso il basso dell'ordine in cui vengono eseguite le competenze. Le competenze che dipendono dall'output di altre competenze saranno visualizzate più in basso nel grafo. Le competenze allo stesso livello nella gerarchia possono essere eseguite in parallelo. 

Se si seleziona una competenza nel grafo, vengono evidenziate le competenze connesse, i nodi che creano i relativi input e i nodi che ne accettano gli output. Ogni nodo di una competenza visualizza il tipo, gli errori o gli avvisi e i conteggi di esecuzione. Il **grafo delle competenze** è il punto in cui viene selezionata la competenza di cui eseguire il debug o il miglioramento. Quando si seleziona una competenza, i relativi dettagli verranno visualizzati nel riquadro dei dettagli della competenza a destra del grafo.

> [!div class="mx-imgBorder"]
> ![Grafo delle competenze](media/cognitive-search-debug/skills-graph.png)

## <a name="skill-details"></a>Dettagli sulla competenza

Il riquadro dei dettagli della competenza visualizza un set di aree per l'uso di una competenza specifica, quando tale competenza viene evidenziata nel **grafo delle competenze**. È possibile esaminare e modificare i dettagli delle impostazioni della competenza. Viene fornita la definizione JSON della competenza. Vengono inoltre visualizzati i dettagli relativi all'esecuzione della competenza, nonché gli errori e gli avvisi. Le schede **Impostazioni delle competenze** ed **Editor JSON delle competenze** consentono di modificare direttamente la competenza. Il simbolo [`</>`](#expression-evaluator) apre una finestra per la visualizzazione e la modifica delle espressioni degli input e degli output delle competenze.

I controlli di input annidati nella finestra Impostazioni delle competenze possono essere usati per creare forme complesse per le proiezioni, mapping dei campi di output per un campo di tipo complesso o un input per una competenza. Se usati con l'**analizzatore di espressioni**, gli input annidati forniscono un semplice generatore di espressioni di test e convalida.

## <a name="skill-execution-history"></a>Cronologia di esecuzione delle competenze

Una competenza può essere eseguita più volte in un set di competenze per un singolo documento. Ad esempio, la competenza OCR verrà eseguita una volta per ogni immagine estratta da ciascun documento. Nel riquadro dei dettagli della competenza la scheda **Esecuzioni** visualizza la cronologia di esecuzione della competenza, fornendo informazioni più approfondite su ogni chiamata della competenza. 

La cronologia di esecuzione consente di tenere traccia di un arricchimento specifico fino alla competenza che lo ha generato. Facendo clic su un input della competenza, si passa alla competenza che ha generato tale input. Ciò consente di identificare la causa radice di un problema che può manifestarsi in una competenza a valle. 

Quando viene identificato un potenziale problema, la cronologia di esecuzione visualizza i collegamenti alle competenze che hanno generato gli input specifici, fornendo una funzionalità di analisi dello stack. Facendo clic sulla competenza associata a un input, si passa alla competenza per correggere eventuali bug o continuare a tracciare il problema specifico.

Quando si crea una competenza personalizzata o si esegue il debug di un errore con una competenza personalizzata, esiste la possibilità di generare una richiesta per una chiamata della competenza nella cronologia di esecuzione.

## <a name="enriched-data-structure"></a>Struttura dei dati arricchiti

Il riquadro **Struttura dei dati arricchiti** mostra gli arricchimenti del documento attraverso il set di competenze, specificando il contesto per ogni arricchimento e la competenza di origine. È anche possibile usare l'**analizzatore di espressioni** per visualizzare il contenuto di ogni arricchimento.

> [!div class="mx-imgBorder"]
> ![Struttura dei dati arricchiti](media/cognitive-search-debug/enriched-data-structure-display.png)

## <a name="expression-evaluator"></a>Analizzatore di espressioni

L'**analizzatore di espressioni** permette di controllare rapidamente il valore di qualsiasi percorso. Consente di modificare il percorso e di testare i risultati prima di aggiornare gli input o il contesto per una competenza o una proiezione.

## <a name="errorswarnings"></a>Errori/Avvisi

In questa finestra vengono visualizzati tutti gli errori e gli avvisi generati dal set di competenze durante l'esecuzione sul documento nella sessione di debug.

## <a name="next-steps"></a>Passaggi successivi

Ora che si conoscono gli elementi delle sessioni di debug, provare l'esercitazione per un'esperienza pratica.

> [!div class="nextstepaction"]
> [Esplorare l'esercitazione sulla funzionalità Sessioni di debug](https://docs.microsoft.com/azure/search/cognitive-search-tutorial-debug-sessions)