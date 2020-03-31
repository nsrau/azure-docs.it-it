---
title: Deep learning e machine learning
titleSuffix: Azure
description: Scopri in che modo il deep learning è correlato all'apprendimento automatico e all'intelligenza artificiale. Il deep learning viene utilizzato in scenari come il rilevamento delle frodi, & il riconoscimento facciale, l'analisi del sentiment e la previsione di serie temporali.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 03/05/2020
ms.openlocfilehash: bcacf4ff66e114f65ac75f0aadd1564875f15f62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501360"
---
# <a name="deep-learning-vs-machine-learning"></a>Deep learning e machine learning

Questo articolo consente di confrontare il deep learning e l'apprendimento automatico. Imparerai come i due concetti si confrontano e come si inseriscono nella più ampia categoria di intelligenza artificiale. L'articolo descrive anche come applicare il deep learning a scenari reali come il rilevamento delle frodi, il riconoscimento vocale e facciale, l'analisi del sentiment e la previsione di serie temporali.

## <a name="deep-learning-machine-learning-and-ai"></a>Apprendimento approfondito, apprendimento automatico e iA

![Diagramma delle relazioni: AI e Machine Learning e deep learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Considerare le definizioni seguenti per comprendere il deep learning rispetto all'apprendimento automatico rispetto all'ia/AI:Consider the following definitions to understand deep learning vs.

- **Il deep learning** è un sottoinsieme dell'apprendimento automatico basato su reti neurali artificiali. Il processo di _apprendimento_ è _profondo_ perché la struttura delle reti neurali artificiali è costituita da più livelli di input, output e nascosti. Ogni livello contiene unità che trasformano i dati di input in informazioni che il livello successivo può utilizzare per una determinata attività predittiva. Grazie a questa struttura, una macchina può imparare attraverso la propria elaborazione dei dati.

- **L'apprendimento automatico** è un sottoinsieme dell'intelligenza artificiale che utilizza tecniche (come il deep learning) che consentono alle macchine di usare l'esperienza per migliorare alle attività. Il processo di _apprendimento_ si basa sulle seguenti fasi:

   1. Inserire i dati in un algoritmo. In questo passaggio è possibile fornire informazioni aggiuntive al modello, ad esempio eseguendo l'estrazione delle feature.
   1. Usare questi dati per eseguire il training di un modello.
   1. Testare e distribuire il modello.
   1. Usare il modello distribuito per eseguire un'attività predittiva automatica. In altre parole, chiamare e usare il modello distribuito per ricevere le stime restituite dal modello.

- **L'intelligenza artificiale (IA)** è una tecnica che consente ai computer di imitare l'intelligenza umana. Include l'apprendimento automatico. 
 
È importante comprendere la relazione tra idati di ai-ai-, l'apprendimento automatico e il deep learning. L'apprendimento automatico è un modo per ottenere l'intelligenza artificiale. Utilizzando le tecniche di apprendimento automatico e deep learning, è possibile creare sistemi e applicazioni per computer che eseguono attività comunemente associate all'intelligenza umana. Queste attività includono il riconoscimento delle immagini, il riconoscimento vocale e la traduzione della lingua.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Tecniche di deep learning e machine learning 

Ora che hai la panoramica dell'apprendimento automatico rispetto al deep learning, confrontiamo le due tecniche. Nell'apprendimento automatico, l'algoritmo deve essere indetto come eseguire una stima accurata utilizzando più informazioni (ad esempio, eseguendo l'estrazione delle funzionalità). Nel deep learning, l'algoritmo può imparare a fare una previsione accurata attraverso la propria elaborazione dei dati, grazie alla struttura della rete neurale artificiale.

La tabella seguente confronta le due tecniche in modo più dettagliato:

| |Tutto l'apprendimento automatico |Solo deep learning|
|---|---|---|
|  **Numero di punti dati** | Può utilizzare piccole quantità di dati per eseguire stime. | È necessario usare grandi quantità di dati di training per eseguire stime. |
|  **Dipendenze hardware** | Può funzionare su macchine di fascia bassa. Non ha bisogno di una grande quantità di potenza di calcolo. | Dipende da macchine di fascia alta. In esito un numero elevato di operazioni di moltiplicazione di matrici. Una GPU può ottimizzare in modo efficiente queste operazioni. |
|  **Processo di stabilizzazione** | Richiede che le funzioni siano identificate e create con precisione dagli utenti. | Apprendere feature di alto livello dai dati e creare nuove funzionalità da sole. |
|  **Approccio all'apprendimento** | Divide il processo di apprendimento in passaggi più piccoli. Combina quindi i risultati di ogni fase in un unico output. | Si sposta attraverso il processo di apprendimento risolvendo il problema end-to-end. |
|  **Tempo di esecuzione** | Richiede relativamente poco tempo per allenarsi, che vanno da pochi secondi a poche ore. | In genere richiede molto tempo per il training perché un algoritmo di deep learning coinvolge molti livelli. |
|  **Output** | L'output è in genere un valore numerico, ad esempio un punteggio o una classificazione. | L'output può avere più formati, ad esempio un testo, una partitura o un suono. |

## <a name="deep-learning-use-cases"></a>Casi d'uso del deep learning

A causa della struttura della rete neurale artificiale, il deep learning eccelle nell'identificazione di modelli in dati non strutturati, ad esempio immagini, suoni, video e testo. Per questo motivo, il deep learning sta rapidamente trasformando molti settori, tra cui sanità, energia, finanza e trasporti. Queste industrie stanno ora ripensando i processi aziendali tradizionali. 

Alcune delle applicazioni più comuni per il deep learning sono descritte nei paragrafi seguenti.

### <a name="named-entity-recognition"></a>Riconoscimento delle entità con nome

Il riconoscimento delle entità denominate è un metodo di deep learning che accetta una parte di testo come input e la trasforma in una classe pre-specificata. Queste nuove informazioni potrebbero essere un codice postale, una data, un ID prodotto. Le informazioni possono quindi essere archiviate in uno schema strutturato per compilare un elenco di indirizzi o fungere da benchmark per un motore di convalida dell'identità.

### <a name="object-detection"></a>Rilevamento di oggetti

Il deep learning è stato applicato in molti casi d'uso per il rilevamento degli oggetti. Il rilevamento degli oggetti comprende due parti: la classificazione delle immagini e quindi la localizzazione delle immagini. La _classificazione_ delle immagini identifica gli oggetti dell'immagine, ad esempio automobili o persone. La _localizzazione dell'immagine_ fornisce la posizione specifica di questi oggetti. 

Il rilevamento di oggetti è già utilizzato in settori quali il gioco, la vendita al dettaglio, il turismo e le auto a guida autonoma.

### <a name="image-caption-generation"></a>Generazione della didascalia dell'immagine

Come il riconoscimento delle immagini, nella didascalia delle immagini, per una determinata immagine, il sistema deve generare una didascalia che descriva il contenuto dell'immagine. Quando è possibile rilevare ed etichettare gli oggetti nelle fotografie, il passaggio successivo consiste nel trasformare tali etichette in frasi descrittive. 

In genere, le applicazioni di sottotitoli delle immagini usano reti neurali convoluzionali per identificare gli oggetti in un'immagine e quindi usare una rete neurale ricorrente per trasformare le etichette in frasi coerenti.

### <a name="machine-translation"></a>Traduzione automatica

La traduzione automatica prende parole o frasi da una lingua e le traduce automaticamente in un'altra lingua. La traduzione automatica è in giro da molto tempo, ma il deep learning raggiunge risultati impressionanti in due aree specifiche: traduzione automatica del testo (e traduzione del parlato in testo) e traduzione automatica delle immagini.

Con la trasformazione dei dati appropriata, una rete neurale può comprendere i segnali di testo, audio e visivi. La traduzione automatica può essere utilizzata per identificare frammenti di suono in file audio più grandi e trascrivere la parola o l'immagine parlata come testo.

### <a name="text-analytics"></a>Analisi del testo

L'analisi del testo basata su metodi di deep learning comporta l'analisi di grandi quantità di dati di testo (ad esempio, documenti medici o ricevute di spesa), il riconoscimento di modelli e la creazione di informazioni organizzate e concise da esso.

Le aziende utilizzano il deep learning per eseguire analisi del testo per rilevare l'insider trading e la conformità alle normative governative. Un altro esempio comune è la frode assicurativa: l'analisi del testo è stata spesso utilizzata per analizzare grandi quantità di documenti per riconoscere le possibilità che un reclamo assicurativo sia frode. 

## <a name="artificial-neural-networks"></a>Reti neurali artificiali

Le reti neurali artificiali sono formate da livelli di nodi collegati. I modelli di deep learning utilizzano reti neurali con un numero elevato di livelli. 

Le sezioni seguenti esplorano le tipologie di rete neurale artificiale più popolari.

### <a name="feedforward-neural-network"></a>Rete neurale Feedforward

La rete neurale feedforward è il tipo più semplice di rete neurale artificiale. In una rete di feedforward, le informazioni si spostano in una sola direzione dal livello di input al livello di output. Le reti neurali Feedforward trasformano un input inserendolo attraverso una serie di livelli nascosti. Ogni strato è costituito da un insieme di neuroni, e ogni strato è completamente collegato a tutti i neuroni nello strato prima. L'ultimo livello completamente connesso (il livello di output) rappresenta le stime generate.

### <a name="recurrent-neural-network"></a>Rete neurale ricorrente

Le reti neurali ricorrenti sono una rete neurale artificiale ampiamente utilizzata. Queste reti salvano l'output di un livello e lo riportano al livello di input per prevedere il risultato del layer. Le reti neurali ricorrenti hanno grandi capacità di apprendimento. Sono ampiamente utilizzati per attività complesse come la previsione di serie temporali, l'apprendimento della scrittura a mano e il riconoscimento del linguaggio.

### <a name="convolutional-neural-networks"></a>Reti neurali convoluzionali

Una rete neurale convoluzionale è una rete neurale artificiale particolarmente efficace e presenta un'architettura unica. I layer sono organizzati in tre dimensioni: larghezza, altezza e profondità. I neuroni in un livello si collegano non a tutti i neuroni nello strato successivo, ma solo a una piccola regione dei neuroni dello strato. L'output finale viene ridotto a un singolo vettore di punteggi di probabilità, organizzato lungo la dimensione di profondità. 

Le reti neurali convoluzionali sono state utilizzate in aree quali il riconoscimento video, il riconoscimento delle immagini e i sistemi di raccomandazione.

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti illustrano come usare la tecnologia di deep learning in [Azure Machine Learning:](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=docs-article-lazzeri)

- [Classificare le cifre scritte a mano utilizzando un modello TensorFlow](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)

- [Classificare le cifre scritte a mano utilizzando uno stimatore TensorFlow e Keras](https://docs.microsoft.com/azure/machine-learning/how-to-train-keras?WT.mc_id=docs-article-lazzeri)

- [Classificare le immagini utilizzando un modello Pytorch](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)

- [Classificare le cifre scritte a mano utilizzando un modello Chainer](https://docs.microsoft.com/azure/machine-learning/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)

Inoltre, usa il [foglio trucco dell'algoritmo](../synapse-analytics/sql-data-warehouse/cheat-sheet.md) di Machine Learning per scegliere gli algoritmi per il tuo modello.
