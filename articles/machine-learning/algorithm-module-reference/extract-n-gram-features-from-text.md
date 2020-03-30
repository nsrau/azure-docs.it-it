---
title: Estrarre le funzioni N-Gram dal riferimento al modulo testo
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Estrai N-Gram in Azure Machine Learning per featurizzare i dati di testo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: efe09c1d516b37c23b024e07ae387772fa7e5992
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477613"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Estrarre le funzioni N-Gram dal riferimento al modulo testo

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima). Utilizzare il modulo Estrai funzioni N-Gram dal testo per ottenere dati *di* testo non strutturati. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Configurazione del modulo Estrai funzioni N-Gram dal testo

Il modulo supporta i seguenti scenari per l'utilizzo di un dizionario n-grammo:

* [Creare un nuovo dizionario a n grammi](#create-a-new-n-gram-dictionary) da una colonna di testo libero.

* [Utilizzare un set esistente di funzioni di testo](#use-an-existing-n-gram-dictionary) per featurizzare una colonna di testo libero.

* [Assegnare un punteggio o pubblicare un modello](#score-or-publish-a-model-that-uses-n-grams) che utilizza n-grammi.

### <a name="create-a-new-n-gram-dictionary"></a>Creare un nuovo dizionario a n grammi

1.  Aggiungere il modulo Estrai funzionalità N-Gram dal testo alla pipeline e connettere il set di dati con il testo da elaborare.

1.  Utilizzare la **colonna Testo** per scegliere una colonna di tipo stringa contenente il testo da estrarre. Poiché i risultati sono dettagliati, è possibile elaborare una sola colonna alla volta.

1. Impostare **Modalità vocabolario su** **Crea** per indicare che si sta creando un nuovo elenco di feature a n grammi. 

1. Impostare **la dimensione N-Grams** per indicare la dimensione *massima* degli n grammi da estrarre e conservare. 

    Ad esempio, se si immettono 3, verranno creati unigrammi, bigrammi e trigrammi.

1. **Funzione di ponderazione** specifica come compilare il vettore di funzionalità del documento e come estrarre il vocabolario dai documenti.

    * **Peso binario**: Assegna un valore di presenza binario agli n-grammi estratti. Il valore di ogni n-grammo è 1 quando è presente nel documento e 0 in caso contrario.

    * **TF Weight**: Assegna un punteggio di frequenza a termine (TF) agli ngrammi estratti. Il valore di ogni n-grammo è la frequenza di occorrenza nel documento.

    * **Peso IDF**: Assegna un punteggio IDF (Inverse Document Frequency) agli n-grammi estratti. Il valore per ogni n-grammo è il registro della dimensione del corpo diviso per la frequenza di occorrenza nell'intero corpus.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **Peso TF-IDF**: Assegna un punteggio di frequenza/frequenza di documento inversa (TF/IDF) agli ngrammi estratti. Il valore di ogni n-grammo è il suo punteggio TF moltiplicato per il suo punteggio IDF.

1. Impostare **Lunghezza minima parola** sul numero minimo di lettere che possono essere utilizzate in ogni singola *parola* in un grammo.

1. Utilizzare **Lunghezza massima parola** per impostare il numero massimo di lettere che possono essere utilizzate in una singola *parola* in un grammo.

    Per impostazione predefinita, sono consentiti fino a 25 caratteri per parola o token.

1. Utilizzare **La frequenza assoluta** del documento minimo di n grammi per impostare le occorrenze minime necessarie per qualsiasi n-grammo da includere nel dizionario n-gram. 

    Ad esempio, se si utilizza il valore predefinito 5, qualsiasi n-grammo deve essere visualizzato almeno cinque volte nel corpo per essere incluso nel dizionario n grammi. 

1.  Impostare Il rapporto massimo del **documento di n grammi** sul rapporto massimo tra il numero di righe che contengono un particolare n-grammo, oltre il numero di righe nel corpo complessivo.

    Ad esempio, un rapporto di 1 indicherebbe che, anche se uno specifico n-grammo è presente in ogni riga, lo n-grammo può essere aggiunto al dizionario n-gram. Più in genere, una parola che si trova in ogni riga viene considerata una parola non omeno e verrebbe rimossa. Per filtrare le parole noise dipendenti dal dominio, provare a ridurre questo rapporto.

    > [!IMPORTANT]
    > La frequenza di occorrenza di determinate parole non è uniforme. Varia da documento a documento. Ad esempio, se stai analizzando i commenti dei clienti su un prodotto specifico, il nome del prodotto potrebbe essere molto alta e vicino a una parola rumore, ma essere un termine significativo in altri contesti.

1. Selezionare l'opzione **Normalizza vettori di feature ngrammi** per normalizzare i vettori di feature. Se questa opzione è attivata, ogni vettore di feature n grammi viene diviso per la norma L2.

1. Inviare la pipeline.

### <a name="use-an-existing-n-gram-dictionary"></a>Utilizzare un dizionario a n grammi esistente

1.  Aggiungere il modulo Estrai funzionalità N-Gram dal testo alla pipeline e connettere il set di dati con il testo che si desidera elaborare alla porta del set di **dati.**

1.  Utilizzare la **colonna Testo** per selezionare la colonna di testo contenente il testo da featurizzare. Per impostazione predefinita, il modulo seleziona tutte le colonne di tipo **stringa**. Per ottenere risultati ottimali, elaborare una singola colonna alla volta.

1. Aggiungere il set di dati salvato che contiene un dizionario a n grammi generato in precedenza e connetterlo alla porta del **vocabolario di input.** È inoltre possibile connettere l'output del **vocabolario Result** di un'istanza a monte del modulo Estrai funzionalità N-Gram da testo.

1. Per **modalità Vocabolario**, selezionare l'opzione di aggiornamento **ReadOnly** dall'elenco a discesa.

   L'opzione **ReadOnly** rappresenta il corpo di input per il vocabolario di input. Anziché calcolare le frequenze dei termini dal nuovo set di dati di testo (nell'input a sinistra), i pesi n grammi del vocabolario di input vengono applicati così come sono.

   > [!TIP]
   > Utilizzare questa opzione quando si contrassegna un classificatore di testo.

1.  Per tutte le altre opzioni, vedere le descrizioni delle proprietà nella [sezione precedente.](#create-a-new-n-gram-dictionary)

1.  Inviare la pipeline.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Assegnare un punteggio o pubblicare un modello che utilizza n-grammi

1.  Copiare il modulo **Estrai funzionalità N-Gram dal** flusso di dati di training al flusso di dati di punteggio.

1.  Connettere l'output **del vocabolario dei** risultati dal flusso di dati di training al **vocabolario** di input nel flusso di dati del punteggio.

1.  Nel flusso di lavoro di assegnazione dei punteggi, modificare il modulo Estrai funzionalità N-Gram dal testo e impostare il **parametro Modalità vocabolario** su **ReadOnly**. Lascia stare lo stesso.

1.  Per pubblicare la pipeline, salvare **Result Vocabulary** come set di dati.

1.  Collegare il set di dati salvato al modulo Estrai funzioni N-Gram dal testo nel grafico dei punteggi.

## <a name="results"></a>Risultati

Il modulo Estrai funzioni N-Gram da testo crea due tipi di output: 

* **Set di risultati**: Questo output è un riepilogo del testo analizzato combinato con gli n grammi estratti. Le colonne non selezionate nell'opzione **Colonna di** testo vengono passate all'output. Per ogni colonna di testo analizzata, il modulo genera queste colonne:

  * **Matrice di occorrenze n-grammi**: Il modulo genera una colonna per ogni n-grammo trovato nel corpo totale e aggiunge un punteggio in ogni colonna per indicare il peso dello n-grammo per quella riga. 

* **Vocabolario dei risultati**: Il vocabolario contiene il dizionario n-grammo effettivo, insieme ai punteggi di frequenza dei termini generati come parte dell'analisi. È possibile salvare il set di dati per riutilizzarlo con un set di input diverso o per un aggiornamento successivo. È inoltre possibile riutilizzare il vocabolario per la modellazione e il punteggio.

### <a name="result-vocabulary"></a>Vocabolario dei risultati

Il vocabolario contiene il dizionario n-gram con i punteggi di frequenza del termine che vengono generati come parte dell'analisi. I punteggi DF e IDF vengono generati indipendentemente dalle altre opzioni.

+ **ID**: Un identificatore generato per ogni ngramma univoco.
+ **NGram**: Lo n-grammo. Gli spazi o altri separatori di parola vengono sostituiti dal carattere di sottolineatura.
+ **DF**: Il punteggio di frequenza del termine per il grammo n nel corpus originale.
+ **IDF**: Il punteggio di frequenza del documento inverso per lo n-grammo nel corpo originale.

È possibile aggiornare manualmente questo set di dati, ma è possibile introdurre errori. Ad esempio:

* Viene generato un errore se il modulo trova righe duplicate con la stessa chiave nel vocabolario di input. Assicurarsi che non vi siano due righe nel vocabolario che abbiano la stessa parola.
* Lo schema di input dei set di dati del vocabolario deve corrispondere esattamente, inclusi i nomi delle colonne e i tipi di colonna. 
* La colonna **ID** e la colonna **DF** devono essere di tipo Integer. 
* La colonna **IDF** deve essere di tipo float.

> [!Note]
> Non collegare direttamente l'output dei dati al modulo Train Model. È consigliabile rimuovere le colonne di testo libero prima di essere inserite nel modello di training. In caso contrario, le colonne di testo libero verranno considerate come feature categoriche.

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning.
