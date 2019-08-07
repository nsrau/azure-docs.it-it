---
title: Introduzione a Deep Learning e Machine Learning
titleSuffix: Azure Machine Learning service
description: Informazioni sulla relazione tra Deep Learning e machine learning e sul modo in cui rientrano nella categoria di intelligenza artificiale. Questo articolo descrive il modo in cui l'apprendimento avanzato risolve scenari quali il rilevamento delle frodi, il riconoscimento vocale e facciale, l'analisi dei sentimenti e la previsione delle serie temporali.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 4b66d015e8a182e305a36dacaa2e082288a3e19e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840534"
---
# <a name="introduction-to-deep-learning-vs-machine-learning"></a>Introduzione a Deep Learning e Machine Learning

Questo articolo consente di comprendere la relazione tra Deep Learning e machine learning. Verranno illustrate le differenze tra i due concetti e il modo in cui rientrano nella categoria più ampia di intelligenza artificiale. Infine, l'articolo descrive il modo in cui l'apprendimento avanzato può essere applicato a scenari reali, ad esempio il rilevamento delle frodi, il riconoscimento vocale e facciale, l'analisi dei sentimenti e la previsione delle serie temporali.

## <a name="how-do-deep-learning-machine-learning-and-ai-relate-to-one-another"></a>In che modo l'apprendimento avanzato, l'apprendimento automatico e l'intelligenza artificiale sono correlati tra loro?

![Diagramma delle relazioni: Confronto tra intelligenza artificiale e machine learning e apprendimento avanzato](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Per comprendere la differenza tra Deep Learning e machine learning rispetto a AI, informazioni su ciascuno di questi campi di Study:

- **Deep Learning** è un subset di machine learning basato su reti neurali artificiali che consentono a un computer di eseguire il training autonomo. In questo caso, il _processo di apprendimento_ viene chiamato _Deep_, perché la struttura delle reti neurali artificiali è costituita da più livelli di input, output e Hidden. Ogni livello contiene unità che trasformano i dati di input in informazioni che il livello successivo può utilizzare per eseguire una determinata attività predittiva. Grazie a questa struttura, un computer è in grado di apprendere tramite la propria elaborazione dei dati.

- **Machine Learning** è un subset di intelligenza artificiale che include tecniche, ad esempio l'apprendimento avanzato, che consentono ai computer di migliorare le attività con esperienza. In questo caso, il _processo di apprendimento_ è basato sui passaggi seguenti:

    1. Inserire un algoritmo con i dati fornendogli altre informazioni, ad esempio eseguendo l'estrazione della funzionalità.
    2. Usare questi dati per eseguire il training di un modello.
    3. Testare e distribuire il modello.
    4. Utilizzare il modello distribuito per eseguire una determinata attività predittiva automatica.

- **Intelligenza artificiale (ai)** è una tecnica che consente ai computer di simulare l'intelligenza umana. Include machine learning. È importante comprendere la relazione tra _intelligenza artificiale e machine learning rispetto a Deep Learning_: Machine Learning è un modo per ottenere l'intelligenza artificiale, ovvero mediante l'uso di tecniche di apprendimento automatico e apprendimento avanzato è possibile creare sistemi di computer e applicazioni in grado di eseguire attività comunemente associate all'intelligence umana, ad esempio percezione visiva, riconoscimento vocale, processo decisionale e traduzione tra i linguaggi.

## <a name="comparison-of-deep-learning-vs-machine-learning"></a>Confronto tra Deep Learning e Machine Learning

Ora che si dispone della Panoramica di machine learning e di Deep Learning, confrontare le due tecniche. Nel caso di Machine Learning, è necessario che nell'algoritmo venga indicato come eseguire una stima accurata fornendo ulteriori informazioni; mentre, nel caso dell'apprendimento avanzato, l'algoritmo è in grado di apprendere che tramite la propria elaborazione dei dati grazie alla struttura di rete neurale artificiale.

Nella tabella seguente vengono confrontate le due tecniche in modo più dettagliato:

| |Tutti i computer Learning |Solo Deep Learning|
|---|---|---|
|  **Numero di punti dati** | Può utilizzare quantità di dati ridotte fornite dagli utenti | Richiede una grande quantità di dati di training per creare conclusioni concise |
|  **Dipendenze hardware** | Può funzionare nei computer di fascia bassa. Non è necessaria una grande quantità di potenza di calcolo | Dipende da computer di fascia alta. Esegue intrinsecamente un numero elevato di operazioni di moltiplicazione di matrici. Queste operazioni possono essere ottimizzate in modo efficiente tramite una GPU |
|  **Processo conteggi** | Richiede che le funzionalità siano identificate e create accuratamente dagli utenti | Apprende le funzionalità di alto livello dai dati e crea nuove funzionalità da solo |
|  **Approccio alla formazione** | Divide le attività in piccole parti, quindi combina i risultati ricevuti in un'unica conclusione | Risolve il problema su base end-to-end |
|  **Tempo di esecuzione** | Il training richiede meno tempo, compreso tra pochi secondi e alcune ore | Richiede un tempo insolitamente lungo per eseguire il training, perché sono presenti molti parametri in un algoritmo di apprendimento avanzato |
|  **Output** | L'output è in genere un valore numerico, ad esempio un punteggio o una classificazione | L'output può essere qualsiasi elemento da un punteggio, un testo, un elemento o un suono |

## <a name="deep-learning-use-cases-what-problems-does-it-solve"></a>Casi d'uso di Deep Learning: Quali problemi risolve?

Grazie alla struttura di rete neurale artificiale, l'apprendimento avanzato eccelle nell'identificare i modelli nei dati non strutturati, ad esempio immagini, audio, video e testo. Per questo motivo, trasforma rapidamente molti settori, tra cui Healthcare, energia, tecnofinanza, trasporto e altri, per ripensare ai processi aziendali tradizionali. Alcune delle applicazioni più comuni per l'apprendimento approfondito sono descritte nei paragrafi seguenti.

### <a name="named-entity-recognition"></a>Riconoscimento di entità denominate

Un uso delle reti di formazione approfondita è il riconoscimento delle entità denominate, ovvero un modo per estrarre da dati non strutturati e senza etichetta determinati tipi di informazioni come persone, luoghi, aziende o cose. Tali informazioni possono quindi essere archiviate in uno schema strutturato per creare un elenco di indirizzi o fungere da benchmark per un motore di convalida delle identità.

### <a name="object-detection"></a>Rilevamento di oggetti

L'apprendimento avanzato è stato applicato in molti casi d'uso di rilevamento oggetti. Il rilevamento degli oggetti è in realtà un processo in due parti: classificazione delle immagini e quindi localizzazione delle immagini. La classificazione delle immagini determina quali sono gli oggetti dell'immagine, ad esempio un'auto o una persona, mentre la localizzazione delle immagini fornisce la posizione specifica di questi oggetti. Il rilevamento degli oggetti è già in uso nei settori di giochi, vendite al dettaglio, turismo e auto-guida.

### <a name="image-caption-generation"></a>Generazione didascalia immagine

Analogamente all'attività di riconoscimento delle immagini, la didascalia delle immagini è l'attività in cui, per una determinata immagine, il sistema deve generare una didascalia che descrive il contenuto dell'immagine. Quando è possibile rilevare oggetti nelle fotografie e generare etichette per tali oggetti, è possibile notare che il passaggio successivo consiste nel trasformare tali etichette in una descrizione di frase coerente. In genere, i sistemi comportano l'utilizzo di reti neurali di convoluzione molto grandi per il rilevamento di oggetti nelle fotografie, quindi una rete neurale ricorrente (RNN) per trasformare le etichette in una frase coerente.

### <a name="machine-translation"></a>Traduzione automatica

La traduzione automatica accetta parole, frasi o frasi da una lingua e le converte automaticamente in un'altra lingua. La traduzione automatica del computer è rimasta da molto tempo, ma l'apprendimento avanzato sta ottenendo risultati ottimali in due aree specifiche, ovvero la traduzione automatica del testo (e il riconoscimento vocale) e la traduzione automatica delle immagini. Con la trasformazione dei dati appropriata, una rete approfondita è in grado di comprendere i segnali di testo, audio e visivi. La traduzione automatica può essere usata per identificare frammenti di suoni in file audio di dimensioni maggiori e per trascrivere la parola o l'immagine pronunciata come testo.

### <a name="text-analytics"></a>Analisi del testo

Una delle attività più importanti che l'apprendimento avanzato può eseguire è l'e-Discovery. Le aziende usano l'analisi del testo basata sull'apprendimento avanzato per il rilevamento delle transazioni Insider e la conformità alle normative governative. Gli hedge fund usano analisi del testo per eseguire il drill-down in repository di documenti di grandi dimensioni per ottenere informazioni approfondite sulle prestazioni di investimento future e sui sentimenti del mercato. Il caso d'uso per analisi del testo basata sull'apprendimento avanzato è basato sulla possibilità di analizzare grandi quantità di dati di testo per eseguire analisi o produrre aggregazioni.

## <a name="what-are-artificial-neural-networks"></a>Che cosa sono le reti neurali artificiali?

Una rete neurale artificiale è un modello di apprendimento avanzato ispirato dal modo in cui le reti neurali biologiche nelle informazioni sul processo del cervello umano. Sono disponibili più tipi di reti neurali artificiali attualmente in uso. Le tipologie di rete neurale artificiali più diffuse sono illustrate di seguito.

### <a name="feedforward-neural-network"></a>Rete neurale feedforward

La rete neurale feedforward è il tipo di base della rete neurale artificiale, in cui le informazioni passano in una sola direzione dal livello di input al livello di output. Feedforward neural network trasforma un input inserendolo in una serie di livelli nascosti. Ogni livello è costituito da un set di neuroni, in cui ogni livello è completamente connesso a tutti i neuroni nel livello precedente. Infine, è disponibile un ultimo livello completamente connesso, ovvero il livello di output, che rappresenta le stime generate.

### <a name="recurrent-neural-network"></a>Rete neurale ricorrente

Le reti neurali ricorrenti sono un tipo più diffuso di rete neurale artificiale, che funziona in base al principio di salvataggio dell'output di un livello e di ritorno al livello di input per consentire la stima del risultato del livello. Queste reti neurali hanno maggiori capacità di apprendimento e sono ampiamente usate per attività più complesse, ad esempio per l'apprendimento della grafia o il riconoscimento della lingua.

### <a name="convolutional-neural-networks"></a>Reti neurali di convoluzione

Una rete neurale di convoluzione è un tipo particolarmente efficace di rete neurale artificiale, che presenta un'architettura univoca. In primo luogo, i livelli sono organizzati in tre dimensioni: larghezza, altezza e profondità. I neuroni di un livello, inoltre, non si connettono a tutti i neuroni nel livello successivo, ma solo a una piccola area. Infine, l'output finale verrà ridotto a un singolo vettore di punteggi di probabilità, organizzati lungo la dimensione di profondità. Queste reti neurali sono state usate in aree quali il riconoscimento e la classificazione delle immagini.

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti illustrano come usare la tecnologia Deep learning nel [servizio Azure Machine Learning](/azure/machine-learning/service/):

- [Classificare le cifre scritte a mano con un modello TensorFlow](how-to-train-tensorflow.md)
- [Classificare le cifre scritte a mano con uno strumento di stima TensorFlow e keras](how-to-train-keras.md)
- [Classificare le immagini con un modello Pytorch](how-to-train-pytorch.md)
- [Classificare le cifre scritte a mano con un modello Chainer](how-to-train-pytorch.md)
