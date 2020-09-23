---
title: Eseguire il training del modello Vowpal Wabbit
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Train Vowpal Wabbit Model per creare un modello di Machine Learning usando un'istanza di Vowpal Wabbit.
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 6bc9f69440be772910ea8200b5ccf7d5a5122ae6
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907803"
---
# <a name="train-vowpal-wabbit-model"></a>Eseguire il training del modello Vowpal Wabbit
Questo articolo descrive come usare il modulo **Train Vowpal Wabbit Model** in Azure Machine Learning Designer per creare un modello di Machine Learning usando Vowpal Wabbit.  

Per usare Vowpal Wabbit per Machine Learning, formattare l'input in base ai requisiti di Wabbit di Vowpal e preparare i dati nel formato richiesto. Usare questo modulo per specificare gli argomenti della riga di comando Vowpal Wabbit. 

Quando si esegue la pipeline, un'istanza di Vowpal Wabbit viene caricata nella fase di esecuzione dell'esperimento insieme ai dati specificati. Al termine del training, il modello viene serializzato nuovamente nell'area di lavoro. È possibile utilizzare immediatamente il modello per assegnare un punteggio ai dati. 

Per eseguire il training incrementale di un modello esistente sui nuovi dati, connettere un modello salvato alla porta di input del **modello Vowpal Wabbit con training preliminare** del **modello Train Vowpal Wabbit**e aggiungere i nuovi dati all'altra porta di input.  

## <a name="what-is-vowpal-wabbit"></a>Informazioni su Vowpal Wabbit  

Vowpal Wabbit (VW) è un Framework di machine learning veloce e parallelo sviluppato per l'elaborazione distribuita da Yahoo! Ricerca. In seguito è stato trasferito in Windows e adattato da John Langford (Microsoft Research) per l'elaborazione scientifica nelle architetture parallele.  

Le funzionalità di Vowpal Wabbit importanti per Machine Learning includono l'apprendimento continuo (apprendimento online), la riduzione della dimensionalità e l'apprendimento interattivo. Vowpal Wabbit è anche una soluzione per i problemi che si verificano quando non è possibile adattare i dati del modello alla memoria.  

Gli utenti primari di Vowpal Wabbit sono data scientist che hanno usato in precedenza il Framework per le attività di Machine Learning, ad esempio la classificazione, la regressione, la modellazione di argomenti o la scomposizione di matrici. Il wrapper di Azure per Vowpal Wabbit ha caratteristiche di prestazioni molto simili per la versione locale, quindi è possibile usare le potenti funzionalità e le prestazioni native di Vowpal Wabbit e pubblicare facilmente il modello sottoposto a training come servizio operativo.  

Il modulo [feature hashing](feature-hashing.md) include anche la funzionalità fornita da Vowpal Wabbit, che consente di trasformare i set di caratteri di testo in funzionalità binarie usando un algoritmo di hash.  

## <a name="how-to-configure-vowpal-wabbit-model"></a>Come configurare il modello Wabbit di Vowpal  

In questa sezione viene descritto come eseguire il training di un nuovo modello e come aggiungere nuovi dati a un modello esistente.

A differenza di altri moduli in progettazione, questo modulo specifica entrambi i parametri del modulo ed è il training del modello. Se si dispone di un modello esistente, è possibile aggiungerlo come input facoltativo per eseguire il training incrementale del modello.

+ [Preparare i dati di input in uno dei formati richiesti](#prepare-the-input-data)
+ [Eseguire il training di un nuovo modello](#create-and-train-a-vowpal-wabbit-model)
+ [Eseguire il training incrementale di un modello esistente](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>Preparare i dati di input

Per eseguire il training di un modello usando questo modulo, il set di dati di input deve essere costituito da una singola colonna di testo in uno dei due formati supportati: **SVMLight** o **VW**. Questo non significa che Vowpal Wabbit analizza solo i dati di testo, solo che le funzionalità e i valori devono essere preparati nel formato di file di testo richiesto.  

I dati possono essere letti da due tipi di set di dati, set di dati del file o set di dati tabulari. Entrambi i set di impostazioni devono essere in formato SVMLight o VW. Il vantaggio del formato di dati di Vowpal Wabbit è che non richiede un formato a colonne. Questo consente di risparmiare spazio quando si gestiscono dati di tipo sparse. Per ulteriori informazioni su questo formato, vedere la [pagina wiki di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format).  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>Creare ed eseguire il training di un modello Vowpal Wabbit

1. Aggiungere il modulo **Train Vowpal Wabbit Model** all'esperimento. 
  
2. Aggiungere il set di dati di training e connetterlo ai **dati di training**. Se il set di dati di training è una directory che contiene il file di dati di training, specificare il nome del file di dati di training con il **nome del file di dati di training**. Se il set di dati di training è un singolo file, lasciare vuoto **il nome del file di dati di training** .

3. Nella casella di testo **argomenti VW** Digitare gli argomenti della riga di comando per il file eseguibile Wabbit di Vowpal.

     Ad esempio, è possibile aggiungere *`–l`* per specificare la velocità di apprendimento o *`-b`* per indicare il numero di bit di hashing.  

     Per ulteriori informazioni, vedere la sezione [parametri di Vowpal Wabbit](#supported-and-unsupported-parameters) .  

4. **Nome del file di dati di training**: digitare il nome del file che contiene i dati di input. Questo argomento viene utilizzato solo quando il set di dati di training è una directory.

5. **Specifica tipo di file**: indica il formato usato dai dati di training. Vowpal Wabbit supporta questi due formati di file di input:  

    - **VW** rappresenta il formato interno usato da Vowpal Wabbit. Per informazioni dettagliate, vedere la [pagina wiki di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format) . 
    - **SVMLight** è un formato usato da altri strumenti di machine learning. 

6. **File del modello leggibile di output**: selezionare l'opzione se si vuole che il modulo salvi il modello leggibile nei record di esecuzione. Questo argomento corrisponde al `--readable_model` parametro nella riga di comando di VW.  

7. **Output file hash invertito**: selezionare l'opzione se si vuole che il modulo salvi la funzione di hashing invertita in un file nei record di esecuzione. Questo argomento corrisponde al `--invert_hash` parametro nella riga di comando di VW.  

8. Inviare la pipeline.

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>Ripetere il training di un modello Wabbit Vowpal esistente

Vowpal Wabbit supporta il training incrementale tramite l'aggiunta di nuovi dati a un modello esistente. Esistono due modi per ottenere un modello esistente per la ripetizione del training:

+ Usare l'output di un altro modulo **Train Vowpal Wabbit Model** nella stessa pipeline.  
  
+ Individuare un modello salvato nella categoria **set di impostazioni** del riquadro di spostamento a sinistra della finestra di progettazione e trascinarlo nella pipeline.  

1. Aggiungere il modulo **Train Vowpal Wabbit Model** alla pipeline.  
2. Connettere il modello sottoposto a training precedente alla porta di input del **modello Vowpal Wabbit con training preliminare** del modulo.
3. Connettere i nuovi dati di training alla porta di input dei **dati di training** del modulo.
4. Nel riquadro dei parametri del **modello Train Vowpal Wabbit**specificare il formato dei nuovi dati di training e anche il nome del file di dati di training se il set di dati di input è una directory.
5. Selezionare le opzioni * * file modello leggibile output * * e **output file hash invertito** se è necessario salvare i file corrispondenti nei record di esecuzione.

6. Inviare la pipeline.  
7. Selezionare il modulo e selezionare **registra set di dati** nella scheda **output + log** nel riquadro destro per mantenere il modello aggiornato nell'area di lavoro Azure Machine Learning.  Se non si specifica un nuovo nome, il modello aggiornato sovrascrive il modello salvato esistente.

## <a name="technical-notes"></a>Note tecniche

Questa sezione contiene informazioni dettagliate sull'implementazione, suggerimenti e risposte alle domande più frequenti.

### <a name="advantages-of-vowpal-wabbit"></a>Vantaggi di Vowpal Wabbit

Vowpal Wabbit consente un apprendimento estremamente veloce rispetto alle funzioni non lineari come gli n-grammi.  

Vowpal Wabbit usa tecniche di *apprendimento online*, come i valori descent con sfumatura stocastica (SGD, Stochastic Gradient Descent), per adattare un modello un record alla volta. In questo modo esegue l'iterazione molto velocemente sui dati non elaborati e può sviluppare un valido predittore più rapidamente della maggior parte degli altri modelli. Questo approccio evita inoltre di dover leggere tutti i dati di training in memoria.  

Vowpal Wabbit converte tutti i dati in hash, non solo i dati di testo, ma anche le altre variabili categoriche. L'uso degli hash rende più efficiente la ricerca dei pesi di regressione, che è fondamentale per la discesa della sfumatura stocastica efficace.  

###  <a name="supported-and-unsupported-parameters"></a>Parametri supportati e non supportati 

In questa sezione viene descritto il supporto per i parametri della riga di comando Vowpal Wabbit in Azure Machine Learning Designer. 

In genere, sono supportati tutti gli argomenti tranne un set limitato di argomenti. Per un elenco completo degli argomenti, usare la [pagina wiki di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments).    

I parametri seguenti non sono supportati:

-   Opzioni di input/output specificate in [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     Queste proprietà sono già configurate automaticamente dal modulo.  
  
-   Inoltre, qualsiasi opzione che genera più output o accetta più input non è consentita. Sono inclusi *`--cbt`* , *`--lda`* e *`--wap`* .  
  
-   Sono supportati solo gli algoritmi di apprendimento supervisionato. Pertanto, queste opzioni non sono supportate: *`–active`* , `--rank` e *`--search`* così via. 

### <a name="restrictions"></a>Restrizioni

Poiché l'obiettivo del servizio è supportare utenti esperti di Vowpal Wabbit, i dati di input devono essere preparati in anticipo utilizzando il formato di testo nativo Vowpal Wabbit, anziché il formato del set di dati utilizzato da altri moduli.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 
