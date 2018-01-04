---
title: Controlli supportati disponibili per la preparazione dei dati per Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento elenca tutti i controlli disponibili per la preparazione dati di Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: ff5fcbc6df8cb07e0b98b877f20d981d6bef5117
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Controlli supportati per la preparazione dati di Azure Machine Learning - Anteprima
Questo documento descrive il set di controlli disponibili in questa anteprima.

## <a name="the-halo-effect"></a>Effetto alone 
Alcuni controlli supportano l'effetto alone. Questo effetto usa due colori diversi per mostrare immediatamente i cambiamenti apportati con una trasformazione. Il grigio rappresenta il valore precedente alla trasformazione più recente e il blu mostra il valore corrente. L'effetto può essere abilitato e disabilitato nelle opzioni.

## <a name="graphical-filtering"></a>Filtro grafico 
Alcuni controlli consentono di filtrare i dati usando il controllo stesso come editor. L'uso del controllo come editor implica la selezione di elementi grafici e quindi l'uso della barra degli strumenti in alto a destra nella finestra del controllo per filtrare i valori selezionati in modo da visualizzarli o meno. 

## <a name="column-statistics"></a>Statistiche della colonna
Questo controllo fornisce un'ampia gamma di statistiche diverse relative alla colonna numerica, Le statistiche includono le misure seguenti: 
- Minima
- Quartile inferiore
- Valore mediano
- Quartile superiore
- Massima
- Media
- Deviazione standard


### <a name="options"></a>Opzioni 
- Nessuna

## <a name="histogram"></a>Istogramma 
Calcola e visualizza un istogramma di una singola colonna numerica. Il numero predefinito di bucket viene calcolato usando la regola di Scott. La regola, tuttavia, può essere sottoposta a override tramite le opzioni.

Questo controllo supporta l'effetto alone.


### <a name="options"></a>Opzioni
- Numero minimo di bucket (si applica anche quando è selezionato il bucket predefinito)
- Numero predefinito di bucket (regola di Scott) 
- Mostra alone
- Sovrimpressione del tracciato di densità del kernel (kernel gaussiano) 


### <a name="actions"></a>Azioni
Questo controllo supporta il filtro tramite bucket, che può includere bucket di selezione multipla o singola. Applicare i filtri come descritto in precedenza.

## <a name="value-counts"></a>Conteggi di valore
Questo controllo visualizza una tabella con la frequenza dei valori della colonna selezionata. Per impostazione predefinita vengono visualizzati i primi sei valori, ma il limite può essere modificato in qualsiasi numero. È anche possibile impostare la visualizzazione del conteggio dalla fine anziché dall'inizio. Questo controllo supporta l'effetto alone.

### <a name="options"></a>Opzioni 
- Numero di valori superiori
- Decrescente
- Include valori null o errore
- Mostra alone


### <a name="actions"></a>Azioni 
Questo controllo consente di filtrare per barre, a selezione singola o multipla. Applicare i filtri come descritto in precedenza.

## <a name="box-plot"></a>Box plot 
Box plot con baffo di una colonna numerica.

### <a name="options"></a>Opzioni 
- Colonna Raggruppa per

## <a name="scatter-plot"></a>Grafico a dispersione
Un grafico a dispersione per due colonne numeriche. I dati vengono sottocampionati per motivi di prestazioni. Le dimensioni del campione possono essere sottoposte a override nelle opzioni.

### <a name="options"></a>Opzioni  
- Colonna Asse X
- Colonna Asse Y
- Dimensioni del campione
- Colonna Raggruppa per


## <a name="time-series"></a>Serie temporale
Un grafico a linee con riconoscimento temporale sull'asse X.

### <a name="options"></a>Opzioni
- Colonna data
- Colonna numerica
- Dimensioni del campione


### <a name="actions"></a>Azioni
Questo controllo supporta la funzione di filtro tramite la selezione di un intervallo sul grafico mediante trascinamento. Dopo aver completato la selezione, applicare i filtri come descritto in precedenza.


## <a name="map"></a>Mappa 
Una mappa con punti tracciati, presupponendo che siano state specificate latitudine e longitudine. È necessario selezionare prima la latitudine.

### <a name="options"></a>Opzioni
- Colonna Latitudine
- Colonna Longitudine
- Clustering su
- Colonna Raggruppa per


### <a name="actions"></a>Azioni
Questo controllo supporta la funzione di filtro tramite selezione di punti sulla mappa. Premere **CTRL** e quindi fare clic e trascinare per formare un quadrato intorno ai punti. Applicare i filtri come descritto in precedenza.

È possibile modificare rapidamente le dimensioni della mappa perché visualizzi solo i punti possibili premendo la **E** sul lato sinistro della mappa stessa.


## <a name="pattern-frequency"></a>Frequenza dei modelli 

Questo controllo mostra un elenco di modelli nella colonna stringa selezionata. I modelli vengono rappresentati usando una sintassi come quella delle espressioni regolari. Al passaggio del mouse sul modello vengono visualizzati gli esempi dei valori rappresentati da tale modello. Oltre ai modelli, vengono visualizzate anche le coperture approssimative in termini di percentuale.

![Immagine del controllo dei modelli](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>Opzioni
- Numero di valori superiori
- Decrescente
- Mostra alone

### <a name="actions"></a>Azioni
Questo controllo supporta l'applicazione di filtri in base ai modelli visualizzati. Premere **CTRL** e quindi selezionare le barre piene nel controllo dei modelli. Applicare i filtri come descritto in precedenza. Come risultato dell'azione dell'utente, viene aggiunto un passaggio di filtro avanzato. È possibile visualizzare e modificare il codice Python generato richiamando l'opzione di modifica del passaggio di filtro avanzato.
