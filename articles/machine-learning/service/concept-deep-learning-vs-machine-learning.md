---
title: Apprendimento avanzato e Machine Learning
titleSuffix: Azure Machine Learning service
description: Informazioni sull'apprendimento avanzato e sull'apprendimento automatico e sul modo in cui entrambi i concetti sono correlati all'intelligenza artificiale. L'apprendimento avanzato può essere applicato in scenari come il rilevamento delle frodi, il riconoscimento vocale e facciale, l'analisi dei sentimenti e la previsione delle serie temporali.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 3b0546c42ddd5252b35d1a77d7b152733beec233
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240951"
---
# <a name="deep-learning-vs-machine-learning"></a>Apprendimento avanzato e Machine Learning

Questo articolo consente di confrontare apprendimento avanzato e machine learning. Verranno illustrate le differenze tra i due concetti e il modo in cui rientrano nella categoria più ampia di intelligenza artificiale. L'articolo descrive anche il modo in cui l'apprendimento avanzato può essere applicato a scenari reali come il rilevamento delle frodi, il riconoscimento vocale e facciale, l'analisi dei sentimenti e la previsione delle serie temporali.

## <a name="deep-learning-machine-learning-and-ai"></a>Deep Learning, Machine Learning e intelligenza artificiale

![Diagramma delle relazioni: Confronto tra intelligenza artificiale e machine learning e apprendimento avanzato](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Prendere in considerazione le seguenti definizioni per comprendere l'apprendimento avanzato rispetto a Machine Learning e INTELLIGENZA ARTIFICIALE

- **Deep Learning** è un subset di machine learning basato su reti neurali artificiali. Il _processo di apprendimento_ è _profondo_ perché la struttura delle reti neurali artificiali è costituita da più livelli di input, output e nascosto. Ogni livello contiene unità che trasformano i dati di input in informazioni che il livello successivo può utilizzare per una determinata attività predittiva. Grazie a questa struttura, un computer può apprendere tramite la propria elaborazione dei dati.

- **Machine Learning** è un subset di intelligenza artificiale che usa tecniche, ad esempio l'apprendimento avanzato, che consentono ai computer di usare l'esperienza per migliorare le attività. Il _processo di apprendimento_ è basato sui passaggi seguenti:

   1. Inserire i dati in un algoritmo. In questo passaggio è possibile fornire ulteriori informazioni al modello, ad esempio eseguendo l'estrazione delle funzionalità.
   1. Usare questi dati per eseguire il training di un modello.
   1. Testare e distribuire il modello.
   1. Utilizzare il modello distribuito per eseguire un'attività predittiva automatica. In altre parole, chiamare e utilizzare il modello distribuito per ricevere le stime restituite dal modello.

- **Intelligenza artificiale (ai)** è una tecnica che consente ai computer di simulare l'intelligenza umana. Include machine learning. 
 
È importante comprendere la relazione tra intelligenza artificiale, Machine Learning e apprendimento avanzato. Machine Learning è un modo per ottenere l'intelligenza artificiale. Grazie alle tecniche di apprendimento automatico e apprendimento avanzato, è possibile creare sistemi di computer e applicazioni che eseguono attività comunemente associate all'intelligence umana. Queste attività includono la percezione visiva, il riconoscimento vocale, il decisionale e la traduzione della lingua.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Tecniche di apprendimento avanzato rispetto a Machine Learning 

Ora che si ha una panoramica di machine learning rispetto a Deep Learning, verranno confrontate le due tecniche. Nell'apprendimento automatico, è necessario che all'algoritmo venga indicato come eseguire una stima accurata utilizzando altre informazioni, ad esempio eseguendo l'estrazione delle funzionalità. Nell'apprendimento avanzato l'algoritmo può apprendere come eseguire una stima accurata tramite la propria elaborazione dei dati, grazie alla struttura di rete neurale artificiale.

Nella tabella seguente vengono confrontate le due tecniche in modo più dettagliato:

| |Tutti i computer Learning |Solo Deep Learning|
|---|---|---|
|  **Numero di punti dati** | Consente di utilizzare piccole quantità di dati fornite dagli utenti. | Richiede una grande quantità di dati di training per creare conclusioni concise. |
|  **Dipendenze hardware** | Può funzionare nei computer di fascia bassa. Non è necessaria una grande quantità di potenza di calcolo. | Dipende da computer di fascia alta. Esegue intrinsecamente un numero elevato di operazioni di moltiplicazione di matrici. Una GPU può ottimizzare in modo efficiente queste operazioni. |
|  **Processo conteggi** | Richiede che le funzionalità siano identificate e create accuratamente dagli utenti. | Impara le funzionalità di alto livello dai dati e crea nuove funzionalità da sola. |
|  **Approccio alla formazione** | Divide le attività in piccole parti e quindi combina i risultati ricevuti in un'unica conclusione. | Risolve il problema su base end-to-end. |
|  **Tempo di esecuzione** | Richiede tempo relativamente breve per eseguire il training, che varia da pochi secondi ad alcune ore. | Richiede un tempo insolitamente lungo per il training perché un algoritmo di apprendimento avanzato implica molti parametri. |
|  **Output** | L'output è in genere un valore numerico, ad esempio un punteggio o una classificazione. | L'output può essere un testo, un punteggio, un elemento o un suono. |

## <a name="deep-learning-use-cases"></a>Casi d'uso di Deep Learning

Grazie alla struttura di rete neurale artificiale, l'apprendimento avanzato eccelle nell'identificare i modelli nei dati non strutturati, ad esempio immagini, audio, video e testo. Per questo motivo, l'apprendimento avanzato sta trasformando rapidamente molti settori, tra cui Healthcare, energia, finanza e trasporto. Questi settori ora ripensano i tradizionali processi aziendali. 

Alcune delle applicazioni più comuni per l'apprendimento approfondito sono descritte nei paragrafi seguenti.

### <a name="named-entity-recognition"></a>Riconoscimento di entità denominate

Un uso delle reti di formazione approfondita è il riconoscimento delle entità denominate, ovvero un modo per estrarre determinati tipi di informazioni da dati non strutturati e senza etichetta. Queste informazioni possono essere persone, luoghi, aziende o cose. Le informazioni possono quindi essere archiviate in uno schema strutturato per creare un elenco di indirizzi o fungere da benchmark per un motore di convalida delle identità.

### <a name="object-detection"></a>Rilevamento di oggetti

L'apprendimento avanzato è stato applicato in molti casi d'uso di rilevamento oggetti. Il rilevamento degli oggetti è costituito da due parti: classificazione delle immagini e quindi localizzazione delle immagini. _Classificazione_ immagini identifica gli oggetti dell'immagine, ad esempio automobili o persone. La _localizzazione_ delle immagini fornisce la posizione specifica di questi oggetti. 

Il rilevamento degli oggetti è già in uso in settori quali giochi, vendite al dettaglio, turismo e auto autoguidate.

### <a name="image-caption-generation"></a>Generazione didascalia immagine

Come il riconoscimento delle immagini, nel didascalia delle immagini, per una determinata immagine, il sistema deve generare una didascalia che descrive il contenuto dell'immagine. Quando è possibile rilevare ed etichettare oggetti nelle fotografie, il passaggio successivo consiste nel trasformare tali etichette in frasi coerenti e descrittive. In genere, i sistemi di didascalia delle immagini usano reti neurali di convoluzione molto grandi per rilevare gli oggetti nelle fotografie e quindi usano una rete neurale ricorrente (RNN) per trasformare le etichette in frasi coerenti.

### <a name="machine-translation"></a>Traduzione automatica

La traduzione automatica accetta parole, frasi o frasi da una lingua e le converte automaticamente in un'altra lingua. La traduzione automatica del computer è rimasta da molto tempo, ma l'apprendimento avanzato raggiunge risultati impressionanti in due aree specifiche: la traduzione automatica del testo (e la traduzione di riconoscimento vocale) e la traduzione automatica delle immagini. 

Con la trasformazione dei dati appropriata, una rete profonda può comprendere i segnali di testo, audio e visivi. La traduzione automatica può essere usata per identificare frammenti di suoni in file audio di dimensioni maggiori e per trascrivere la parola o l'immagine pronunciata come testo.

### <a name="text-analytics"></a>Analisi del testo

Una delle attività più importanti di apprendimento avanzato è l'e-Discovery. Le aziende usano analisi del testo basate su Deep learning per rilevare il trading di insider e la conformità alle normative governative. Gli hedge fund usano analisi del testo per eseguire il drill-down in repository di documenti di grandi dimensioni per ottenere informazioni approfondite sulle prestazioni di investimento future e sui sentimenti del mercato. Il caso d'uso per analisi del testo basato sull'apprendimento avanzato è basato sulla possibilità di analizzare grandi quantità di dati di testo ed eseguire analisi o produrre aggregazioni.

## <a name="artificial-neural-networks"></a>Reti neurali artificiali

Le reti neurali artificiali sono costituite da livelli di nodi connessi. I modelli di apprendimento avanzato utilizzano reti neurali con un numero elevato di livelli. 

Le sezioni seguenti illustrano le più diffuse tipologie di reti neurali artificiali.

### <a name="feedforward-neural-network"></a>Rete neurale feedforward

La rete neurale feedforward è il tipo di base della rete neurale artificiale. In una rete feedforward, le informazioni viaggiano in una sola direzione dal livello di input al livello di output. Feedforward neural network trasforma un input inserendolo in una serie di livelli nascosti. Ogni livello è costituito da un set di neuroni e ogni livello è completamente connesso a tutti i neuroni nel livello precedente. L'ultimo livello completamente connesso, ovvero il livello di output, rappresenta le stime generate.

### <a name="recurrent-neural-network"></a>Rete neurale ricorrente

Le reti neurali ricorrenti sono una rete neurale artificiale ampiamente utilizzata. Queste reti salvano l'output di un livello e lo reinviano al livello di input per consentire la stima del risultato del livello. Le reti neurali ricorrenti hanno ottime capacità di apprendimento. Sono ampiamente usati per attività complesse come l'apprendimento della grafia e il riconoscimento del linguaggio.

### <a name="convolutional-neural-networks"></a>Reti neurali di convoluzione

Una rete neurale di convoluzione è una rete neurale artificiale particolarmente efficace e presenta un'architettura univoca. I livelli sono organizzati in tre dimensioni: larghezza, altezza e profondità. I neuroni di un livello non si connettono a tutti i neuroni nel livello successivo, ma solo a una piccola area dei neuroni del livello. L'output finale viene ridotto a un singolo vettore di punteggi di probabilità, organizzati lungo la dimensione di profondità. 

Le reti neurali di convoluzione sono state usate in aree quali il riconoscimento e la classificazione delle immagini.

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti illustrano come usare la tecnologia Deep learning nel [servizio Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/?WT.mc_id=docs-article-lazzeri):

- [Classificare le cifre scritte a mano usando un modello TensorFlow](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)
- [Classificare le cifre scritte a mano usando uno strumento di stima TensorFlow e keras](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-keras?WT.mc_id=docs-article-lazzeri)
- [Classificare le immagini usando un modello Pytorch](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)
- [Classificare le cifre scritte a mano usando un modello Chainer](https://docs.microsoft.com/en-us/azure/machine-learning/service/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)
