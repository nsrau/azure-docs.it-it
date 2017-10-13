---
title: Controlli supportati disponibili per la preparazione dei dati per Microsoft Azure Machine Learning | Microsoft Docs
description: Questo documento elenca tutti i controlli disponibili per la preparazione dei dati di Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: bc14c051fb0f518b1cff2236a61d24cb052700f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="supported-inspectors-for-this-preview"></a>Controlli supportati per questa anteprima
Questo documento descrive il set di controlli disponibili in questa anteprima.

## <a name="the-halo-effect"></a>Effetto alone 
Alcuni controlli supportano l'effetto alone, che usa due colori diversi per mostrare immediatamente i cambiamenti apportati con una trasformazione. Il grigio rappresenta il valore precedente alla trasformazione più recente, il blu mostra il valore corrente. L'effetto può essere abilitato o disabilitato nelle opzioni.

## <a name="graphical-filtering"></a>Filtro grafico 
Alcuni controlli consentono di filtrare i dati usando il controllo stesso come editor. Ciò implica la selezione di elementi grafici e quindi l'utilizzo della barra degli strumenti che si trova in alto a destra nella finestra del controllo per filtrare o meno i valori selezionati. 

## <a name="column-statistics"></a>Statistiche della colonna
Questo controllo fornisce un'ampia gamma di statistiche diverse relative alla colonna numerica, che includono;
- Minima
- Quartile inferiore
- Valore mediano
- Quartile superiore
- Massima
- Media
- Deviazione standard


### <a name="options"></a>Opzioni 
- Nessuno

## <a name="histogram"></a>Istogramma 
Calcola e visualizza un istogramma di una singola colonna numerica. Il numero predefinito di bucket viene calcolato usando la regola di Scott, che può essere sovrascritta nelle opzioni.
Questo controllo supporta l'effetto alone.


### <a name="options"></a>Opzioni
- Numero minimo di bucket (si applica anche quando è selezionato il bucket predefinito)
- Numero predefinito di bucket (regola di Scott) 
- Mostra alone
- Sovrapposizione tracciato di densità del kernel (Kernel gaussiano) 


### <a name="actions"></a>Azioni
Il controllo consente di filtrare per bucket, bucket singoli o a selezione multipla. Applicare i filtri come descritto in precedenza.

## <a name="value-counts"></a>Conteggi di valore
Questo controllo visualizza una tabella con la frequenza dei valori della colonna selezionata. Per impostazione predefinita vengono visualizzati i primi sei valori, ma il limite può essere modificato in qualsiasi numero o conteggio, sempre a partire dai valori superiori e non da quelli inferiori. Questo controllo supporta l'effetto alone.

### <a name="options"></a>Opzioni 
- Numero di valori superiori
- Decrescente
- Include valori null o errore
- Mostra alone


### <a name="actions"></a>Azioni 
Questo controllo consente di filtrare per barre, barre singole o a selezione multipla. Applicare i filtri come descritto in precedenza.

## <a name="box-plot"></a>Box plot 
Box plot con baffo di una colonna numerica

### <a name="options"></a>Opzioni 
- Raggruppa per colonna

## <a name="scatter-plot"></a>Grafico a dispersione
Grafico a dispersione per due colonne numeriche; i dati vengono campionati in modo decrescente per motivi di prestazioni, la dimensione del campione può essere sovrascritta nelle opzioni.

### <a name="options"></a>Opzioni  
- Colonna asse X
- Colonna asse Y
- Dimensione del campione
- Raggruppa per colonna


## <a name="time-series"></a>Serie temporali
Un grafico a linee con riconoscimento orario sull'asse X

### <a name="options"></a>Opzioni
- Colonna data
- Colonna numerica
- Dimensione del campione


### <a name="actions"></a>Azioni
Questo controllo consente di filtrare con un metodo di trascinamento della selezione, per selezionare un intervallo nel grafico. Dopo aver completato la selezione, applicare i filtri come descritto in precedenza.


## <a name="map"></a>Mappa 
Una mappa con punti tracciati presupponendo che sono state specificate latitudine e longitudine. È necessario selezionare prima la latitudine.

### <a name="options"></a>Opzioni
- Colonna latitudine
- Colonna longitudine
- Con cluster su
- Raggruppa per colonna


### <a name="actions"></a>Azioni
Questo controllo consente di filtrare con la selezione del punto sulla mappa. Premere il tasto CTRL, quindi fare clic e trascinare l'elemento con il mouse per formare un quadrato intorno ai punti. Applicare i filtri come descritto in precedenza.
È possibile modificare rapidamente le dimensioni della mappa affinché mostri tutti i punti possibili premendo la **E** sul lato sinistro della mappa.
