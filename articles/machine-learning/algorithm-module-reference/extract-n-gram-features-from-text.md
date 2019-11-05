---
title: Informazioni di riferimento sul modulo di testo per estrarre le funzionalità di N-Gram
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Extract N-Gram nell'Azure Machine Learning per trasforma i dati di testo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 71c1afd294b880f68849b283ea1a4b058d744801
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497852"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Informazioni di riferimento sul modulo di testo per estrarre le funzionalità di N-Gram

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima). Usare il modulo Extract N-Gram features from text per *trasforma* i dati di testo non strutturati. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Configurazione delle funzionalità di estrazione di N-Gram dal modulo di testo

Il modulo supporta i seguenti scenari per l'uso di un dizionario n-Gram:

* Consente di [creare un nuovo dizionario n-Gram](#create-a-new-n-gram-dictionary) da una colonna di testo libero.

* [Usare un set di funzionalità di testo esistente](#use-an-existing-n-gram-dictionary) per trasforma una colonna di testo disponibile.

* Assegnare [un punteggio o pubblicare un modello](#score-or-publish-a-model-that-uses-n-grams) che usa n-grammi.

### <a name="create-a-new-n-gram-dictionary"></a>Crea un nuovo dizionario n-Gram

1.  Aggiungere le funzionalità Estrai N-Gram dal modulo testo alla pipeline e connettere il set di dati che contiene il testo che si desidera elaborare.

1.  Utilizzare la **colonna di testo** per scegliere una colonna di tipo stringa contenente il testo da estrarre. Poiché i risultati sono dettagliati, è possibile elaborare una sola colonna alla volta.

1. Impostare la **modalità del vocabolario** su **Crea** per indicare che si sta creando un nuovo elenco di funzionalità di n-Gram. 

1. Impostare **n-grammi Dimensioni** per indicare la dimensione *massima* dei n-grammi da estrarre e archiviare. 

    Ad esempio, se si immette 3, unigrammi, bigrammi e trigrammi verranno creati.

1. La **funzione di ponderazione** specifica come compilare il vettore della funzionalità del documento e come estrarre il vocabolario dai documenti.

    * **Peso binario**: assegna un valore di presenza binario ai n-grammi estratti. Il valore per ogni n-grammo è 1 quando esiste nel documento e 0 in caso contrario.

    * **Tf Weight**: assegna un punteggio di frequenza del termine (TF) ai n-grammi estratti. Il valore per ogni n-grammo è la frequenza di occorrenza nel documento.

    * **IDF Weight**: assegna un punteggio IDF (inverso Document Frequency) ai n-grammi estratti. Il valore per ogni n-grammo è il log della dimensione del Corpus diviso per la relativa frequenza di occorrenza nell'intero corpus.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **Tf-IDF Weight**: assegna un punteggio di frequenza/inversione di frequenza del documento (TF/IDF) ai n-grammi estratti. Il valore per ogni n-grammo è il Punteggio di TF moltiplicato per il Punteggio IDF.

1. Impostare la **lunghezza minima della parola** sul numero minimo di lettere che possono essere usate in una *singola parola* in un n-grammo.

1. Usare la **lunghezza massima della parola** per impostare il numero massimo di lettere che è possibile usare in una *singola parola* in un n-grammo.

    Per impostazione predefinita, sono consentiti fino a 25 caratteri per parola o token.

1. Usare la **frequenza assoluta del documento n-grammo minimo** per impostare le occorrenze minime necessarie per l'inclusione di n-grammi nel dizionario n-Gram. 

    Se, ad esempio, si usa il valore predefinito 5, qualsiasi n-gramma deve essere visualizzato almeno cinque volte nel corpus per essere incluso nel dizionario n-Gram. 

1.  Impostare un **rapporto massimo di n-grammi di documenti** sul rapporto massimo del numero di righe che contengono un particolare n-grammo, per il numero di righe del Corpus complessivo.

    Un rapporto 1 indica, ad esempio, che, anche se in ogni riga è presente un n-gramma specifico, è possibile aggiungere il n-gramma al dizionario n-Gram. In genere, una parola che si verifica in ogni riga verrebbe considerata una parola non significativa e verrebbe rimossa. Per filtrare le parole non significative dipendenti dal dominio, provare a ridurre questo rapporto.

    > [!IMPORTANT]
    > La frequenza di occorrenza di parole specifiche non è uniforme. Varia da documento a documento. Se, ad esempio, si analizzano i commenti dei clienti su un prodotto specifico, il nome del prodotto potrebbe essere molto frequenza e avvicinarsi a una parola non significativa, ma essere un termine significativo in altri contesti.

1. Selezionare l'opzione **normalizza i vettori di funzionalità n-Gram** per normalizzare i vettori di funzionalità. Se questa opzione è abilitata, ogni vettore di funzionalità n-Gram viene diviso per la norma L2.

1. Eseguire la pipeline.

### <a name="use-an-existing-n-gram-dictionary"></a>Usa un dizionario n-Gram esistente

1.  Aggiungere le funzionalità Estrai N-Gram dal modulo testo alla pipeline e connettere il set di dati che contiene il testo che si desidera elaborare sulla porta del **set di dati** .

1.  Utilizzare la **colonna di testo** per selezionare la colonna di testo contenente il testo che si desidera trasforma. Per impostazione predefinita, il modulo Seleziona tutte le colonne di tipo **String**. Per ottenere risultati ottimali, elaborare una singola colonna alla volta.

1. Aggiungere il set di dati salvato che contiene un dizionario n-Gram generato in precedenza e connetterlo alla porta del **vocabolario di input** . È anche possibile connettere l'output del **vocabolario dei risultati** di un'istanza upstream del modulo Extract N-Gram features from text.

1. Per la **modalità vocabolario**, selezionare l'opzione di aggiornamento **ReadOnly** dall'elenco a discesa.

   L'opzione **ReadOnly** rappresenta il corpo di input per il vocabolario di input. Invece di calcolare le frequenze dei termini dal nuovo set di dati di testo (sull'input di sinistra), i pesi n-grammi dal vocabolario di input vengono applicati così come sono.

   > [!TIP]
   > Usare questa opzione quando si esegue il Punteggio di un classificatore di testo.

1.  Per tutte le altre opzioni, vedere le descrizioni delle proprietà nella [sezione precedente](#create-a-new-n-gram-dictionary).

1.  Eseguire la pipeline.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Assegnare un punteggio o pubblicare un modello che usa n-grammi

1.  Copiare il modulo **Extract N-Gram features from text** dal flusso dei flussi di training al flusso di calcolo di assegnazione dei punteggi.

1.  Connettere l'output del **vocabolario dei risultati** dal flusso di dati di training al **vocabolario di input** nel flusso di dati di assegnazione dei punteggi.

1.  Nel flusso di lavoro di assegnazione dei punteggi modificare il modulo Extract N-Gram features from text e impostare il parametro della **modalità vocabolario** su **ReadOnly**. Lasciare invariati gli altri.

1.  Per pubblicare la pipeline, salvare il **vocabolario dei risultati** come un set di dati.

1.  Connettere il set di dati salvato al modulo Extract N-Gram features from text nel grafico di assegnazione dei punteggi.

## <a name="results"></a>Risultati

Il modulo Extract N-Gram features from text consente di creare due tipi di output: 

* **Set di dati dei risultati**: questo output è un riepilogo del testo analizzato combinato con i n-grammi estratti. Le colonne non selezionate nell'opzione **colonna di testo** vengono passate all'output. Per ogni colonna di testo analizzata, il modulo genera le colonne seguenti:

  * **Matrice di occorrenze n-Gram**: il modulo genera una colonna per ogni n-grammo trovato nel corpus totale e aggiunge un punteggio in ogni colonna per indicare lo spessore dell'n-grammo per la riga. 

* **Vocabolario dei risultati**: il vocabolario contiene il dizionario di n-grammi effettivo, insieme al termine punteggi di frequenza generati come parte dell'analisi. È possibile salvare il set di dati per il riutilizzo con un diverso set di input o per un aggiornamento successivo. È anche possibile riutilizzare il vocabolario per la modellazione e l'assegnazione dei punteggi.

### <a name="result-vocabulary"></a>Vocabolario risultati

Il vocabolario contiene il dizionario n-Gram con i punteggi di frequenza dei termini generati come parte dell'analisi. I punteggi DF e IDF vengono generati indipendentemente dalle altre opzioni.

+ **ID**: identificatore generato per ogni n-grammo univoco.
+ **NGram**: n-gramma. Spazi o altri separatori di parola vengono sostituiti dal carattere di sottolineatura.
+ **DF**: il termine Punteggio di frequenza per l'n-grammo nel corpus originale.
+ **IDF**: Punteggio di frequenza del documento inverso per l'n-grammo nel corpus originale.

È possibile aggiornare manualmente il set di dati, ma è possibile che si verifichino errori. Ad esempio:

* Viene generato un errore se il modulo trova righe duplicate con la stessa chiave nel vocabolario di input. Assicurarsi che nessuna delle due righe nel vocabolario abbia la stessa parola.
* Lo schema di input dei set di dati del vocabolario deve corrispondere esattamente, inclusi i nomi di colonna e i tipi di colonna. 
* La colonna **ID** e la colonna **DF** devono essere di tipo Integer. 
* La colonna **IDF** deve essere di tipo float.

> [!Note]
> Non connettere direttamente l'output dei dati al modulo Train Model. È necessario rimuovere le colonne di testo libero prima di essere inserite nel modello di training. In caso contrario, le colonne di testo libero verranno considerate come funzionalità categoriche.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning.
