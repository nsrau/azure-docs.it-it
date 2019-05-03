---
title: 'Selezionare le colonne nel set di dati: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare le colonne selezionate nel modulo di set di dati nel servizio Azure Machine Learning per scegliere un subset di colonne da usare nelle operazioni downstream.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e7407f62bd3401411d56076b298bd8cd134ece62
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028096"
---
# <a name="select-columns-in-dataset-module"></a>Selezionare le colonne nel modulo di set di dati

Questo articolo descrive un modulo dell'interfaccia visiva (anteprima) per il servizio di Azure Machine Learning.

Usare questo modulo per scegliere un subset di colonne da usare nelle operazioni downstream. Il modulo non rimuove fisicamente le colonne del set di dati di origine; al contrario, viene creato un subset di colonne, molto simile a un database *view* oppure *proiezione*.

Questo modulo è utile quando è necessario limitare le colonne disponibili per un'operazione di downstream o se si desidera ridurre le dimensioni del set di dati tramite la rimozione di colonne non necessarie.

Le colonne nel set di dati vengono restituiti nello stesso ordine come i dati originali, anche se vengono specificate in un ordine diverso.

## <a name="how-to-use"></a>Utilizzo

Questo modulo non ha parametri. Utilizzare il selettore di colonna per scegliere le colonne da includere o escludere.

### <a name="choose-columns-by-name"></a>Scegliere le colonne in base al nome

Sono disponibili più opzioni per la scelta delle colonne in base al nome del modulo: 

+ Ricerca e filtro

    Scegliere il **BY NAME** opzione.

    Se si è connessi a un set di dati che è già popolato, verrà visualizzato un elenco delle colonne disponibili. Se viene visualizzata alcuna colonna, si potrebbe essere necessario eseguire i moduli upstream per visualizzare l'elenco di colonne.

    Per filtrare l'elenco, digitare nella casella di ricerca. Ad esempio, se si digita la lettera `w` nella casella di ricerca, l'elenco viene filtrato per mostrare i nomi delle colonne che contengono la lettera `w`.

    Selezionare le colonne e fare clic sul pulsante freccia destra per spostare le colonne selezionate all'elenco nel riquadro di destra.

    + Per selezionare un intervallo continuo di nomi di colonna, premere **MAIUSC + clic**.
    + Per aggiungere singole colonne per la selezione, premere **Ctrl + clic**.

    Fare clic sul pulsante di segno di spunta per salvare e chiudere.

+ Usare nomi in combinazione con altre regole

    Scegliere il **con regole** opzione.
    
    Scegliere una regola, ad esempio che mostra le colonne di tipo di dati specifico.

    Quindi, fare clic su singole colonne di tale tipo dal nome, per aggiungerli all'elenco di selezione.

+ Digitare o incollare un elenco delimitato da virgole di nomi di colonna

    Se il set di dati è ampia, potrebbe essere più facile utilizzare gli indici o generati elenchi di nomi, invece di colonne selezione singolarmente. Supponendo che sono stati preparati in anticipo l'elenco:

    1. Scegliere il **con regole** opzione. 
    2. Selezionare **alcuna colonna**, selezionare **inclusione**e quindi fare clic all'interno della casella di testo con il punto esclamativo rosso. 
    3. Incollare o digitare un elenco delimitato da virgole di nomi di colonna convalidato in precedenza. È non è possibile salvare il modulo se una colonna contiene un nome non valido, quindi assicurarsi di controllare i nomi in anticipo.
    
    È anche possibile usare questo metodo per specificare un elenco di colonne usando i relativi valori di indice. 

### <a name="choose-by-type"></a>Scegliere per tipo

Se si usa la **con regole** opzione, è possibile applicare più condizioni alle selezioni di colonna. Ad esempio, potrebbe essere necessario ottenere solo le colonne di funzionalità di un tipo di dati numerici.

Il **iniziano con** opzione determina il punto di partenza ed è importante per comprendere i risultati. 

+ Se si seleziona il **tutte le colonne** opzione, tutte le colonne vengono aggiunte all'elenco. Quindi, è necessario usare il **escludere** possibilità *rimuovere* colonne che soddisfano determinate condizioni. 

    Ad esempio, si inizia con tutte le colonne e quindi rimuovere le colonne in base al nome o dal tipo.

+ Se si seleziona il **colonne NO** opzione, l'elenco di colonne all'inizio è vuoto. È quindi specificare le condizioni *aggiungere* colonne all'elenco. 

    Se si applicano più regole, ciascuna condizione viene **additivi**. Ad esempio, si avvia senza colonne e quindi aggiungerla una regola per ottenere tutte le colonne numeriche. Nel set di dati Automobile price, verrà generato 16 colonne. Fare quindi clic sui **+** accedere per aggiungere una nuova condizione e selezionare **includono tutte le funzionalità**. Il set di dati risultante include tutte le colonne numeriche, oltre a tutte le colonne di funzionalità, tra cui alcune colonne di funzioni di stringa.

### <a name="choose-by-column-index"></a>Scegliere in base all'indice di colonna

L'indice di colonna fa riferimento all'ordine della colonna all'interno del set di dati originale.

+ Le colonne vengono numerate in modo sequenziale a partire da 1.  
+ Per ottenere un intervallo di colonne, usare un trattino. 
+ Le specifiche aperte, ad esempio `1-` o `-3` non sono consentiti.
+ I valori di indice duplicati (o i nomi di colonna) non sono consentiti e potrebbero verificarsi un errore.

Ad esempio, se che il set di dati è composta da almeno otto colonne, è in grado di incollare in uno dei seguenti esempi per restituiscono più colonne non contigue: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

l'esempio finale non produce un errore. Tuttavia, restituisce una singola istanza della colonna `4`.



### <a name="change-order-of-columns"></a>Modificare l'ordine delle colonne

L'opzione **consentire duplicati e mantenere l'ordine delle colonne nella selezione** inizia con un elenco vuoto e vengono aggiunte colonne specificati in base al nome o dall'indice. A differenza delle altre opzioni, che restituiscono sempre le colonne in base al "ordine naturale", questa opzione restituisce le colonne nell'ordine indicato dall'utente o elencarli. 

Ad esempio, è possibile invertire l'ordine delle colonne in un set di dati con le colonne Col1, Col2, Col3 e Col4 e tralasciare colonna 2, specificando uno degli elenchi seguenti:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Passaggi successivi

Vedere le [set di moduli disponibili](module-reference.md) al servizio Azure Machine Learning. 