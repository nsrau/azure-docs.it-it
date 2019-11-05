---
title: 'Selezionare le colonne nel set di dati: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Select Columns in DataSet in Azure Machine Learning per scegliere un subset di colonne da usare nelle operazioni downstream.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 3511c448298aa96c95dc970d1d192869c127eb0d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497662"
---
# <a name="select-columns-in-dataset-module"></a>Selezionare le colonne nel modulo DataSet

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Usare questo modulo per scegliere un subset di colonne da usare nelle operazioni downstream. Il modulo non rimuove fisicamente le colonne dal set di dati di origine. viene invece creato un subset di colonne, in modo analogo a una *vista* o *proiezione*del database.

Questo modulo è utile quando è necessario limitare le colonne disponibili per un'operazione downstream o se si desidera ridurre le dimensioni del set di dati rimuovendo le colonne non necessarie.

Le colonne nel set di dati vengono restituite nello stesso ordine dei dati originali, anche se vengono specificate in un ordine diverso.

## <a name="how-to-use"></a>Utilizzo

Questo modulo non ha parametri. Usare il selettore di colonna per scegliere le colonne da includere o escludere.

### <a name="choose-columns-by-name"></a>Scegliere le colonne in base al nome

Sono disponibili più opzioni nel modulo per la scelta delle colonne in base al nome: 

+ Filtrare e cercare

    Fare clic sull'opzione **per nome** .

    Se è stato connesso un set di dati già popolato, verrà visualizzato un elenco di colonne disponibili. Se non viene visualizzata alcuna colonna, potrebbe essere necessario eseguire i moduli upstream per visualizzare l'elenco di colonne.

    Per filtrare l'elenco, digitare nella casella di ricerca. Se ad esempio si digita la lettera `w` nella casella di ricerca, l'elenco viene filtrato in modo da visualizzare i nomi delle colonne che contengono la lettera `w`.

    Selezionare le colonne e fare clic sul pulsante freccia destra per spostare le colonne selezionate nell'elenco nel riquadro di destra.

    + Per selezionare un intervallo continuo di nomi di colonna, premere **MAIUSC + clic**.
    + Per aggiungere singole colonne alla selezione, premere **CTRL + clic**.

    Fare clic sul segno di spunta per salvare e chiudere.

+ Utilizzare nomi in combinazione con altre regole

    Fare clic sull'opzione **with Rules** .
    
    Scegliere una regola, ad esempio la visualizzazione di colonne di un tipo di dati specifico.

    Quindi, fare clic su singole colonne di quel tipo per nome, per aggiungerle all'elenco di selezione.

+ Digitare o incollare un elenco delimitato da virgole di nomi di colonna

    Se il set di dati è ampio, potrebbe essere più semplice usare gli indici o gli elenchi di nomi generati, anziché selezionare le colonne singolarmente. Supponendo che l'elenco sia stato preparato in anticipo:

    1. Fare clic sull'opzione **with Rules** . 
    2. Selezionare **Nessuna colonna**, selezionare **Includi**e quindi fare clic all'interno della casella di testo con il punto esclamativo rosso. 
    3. Incollare o digitare un elenco delimitato da virgole di nomi di colonna convalidati in precedenza. Non è possibile salvare il modulo se una colonna ha un nome non valido. Assicurarsi prima di tutto di controllare i nomi.
    
    È inoltre possibile utilizzare questo metodo per specificare un elenco di colonne utilizzando i relativi valori di indice. 

### <a name="choose-by-type"></a>Scegli per tipo

Se si utilizza l'opzione **with Rules** , è possibile applicare più condizioni alle selezioni di colonna. Ad esempio, potrebbe essere necessario ottenere solo le colonne di funzionalità di un tipo di dati numerico.

L'opzione **Begin with** determina il punto di partenza ed è importante per comprendere i risultati. 

+ Se si seleziona l'opzione **tutte le colonne** , tutte le colonne vengono aggiunte all'elenco. Quindi, è necessario utilizzare l'opzione **Escludi** per *rimuovere* le colonne che soddisfano determinate condizioni. 

    Ad esempio, è possibile iniziare con tutte le colonne, quindi rimuovere le colonne in base al nome o al tipo.

+ Se si seleziona l'opzione **No Columns** , l'elenco di colonne viene avviato come vuoto. È quindi possibile specificare le condizioni per *aggiungere* colonne all'elenco. 

    Se si applicano più regole, ogni condizione è **additiva**. Si immagini, ad esempio, di iniziare senza colonne, quindi aggiungere una regola per ottenere tutte le colonne numeriche. Nel set di dati del prezzo automobile, che restituisce 16 colonne. Quindi, fare clic sul segno di **+** per aggiungere una nuova condizione e selezionare **Includi tutte le funzionalità**. Il set di dati risultante include tutte le colonne numeriche, oltre a tutte le colonne di funzionalità, incluse alcune colonne della funzionalità di stringa.

### <a name="choose-by-column-index"></a>Seleziona per indice colonna

L'indice di colonna si riferisce all'ordine della colonna all'interno del set di dati originale.

+ Le colonne sono numerate in modo sequenziale a partire da 1.  
+ Per ottenere un intervallo di colonne, usare un trattino. 
+ Non sono consentite specifiche aperte, ad esempio `1-` o `-3`.
+ I valori di indice o i nomi di colonna duplicati non sono consentiti e potrebbero verificarsi errori.

Ad esempio, supponendo che il set di dati includa almeno otto colonne, è possibile incollare uno degli esempi seguenti per restituire più colonne non contigue: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

Nell'esempio finale non viene generato un errore. restituisce tuttavia una singola istanza della colonna `4`.



### <a name="change-order-of-columns"></a>Modificare l'ordine delle colonne

L'opzione **Consenti duplicati e Mantieni ordine colonne nella selezione** inizia con un elenco vuoto e aggiunge le colonne specificate in base al nome o all'indice. A differenza di altre opzioni, che restituiscono sempre colonne nell'ordine naturale, questa opzione restituisce le colonne nell'ordine in cui sono state denominate o elencate. 

Ad esempio, in un set di dati con le colonne col1, Col2, col3 e Col4, è possibile invertire l'ordine delle colonne e omettere la colonna 2, specificando uno degli elenchi seguenti:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 