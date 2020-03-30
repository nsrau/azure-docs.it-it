---
title: 'Selezionare le colonne nel set di dati: riferimento al moduloSelect Columns in Dataset: Module Reference'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Seleziona colonne nel set di dati in Azure Machine Learning per scegliere un subset di colonne da usare nelle operazioni downstream.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 714148815fa4ff543ecbf86ab21e8d51fe775543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153775"
---
# <a name="select-columns-in-dataset-module"></a>Selezione di colonne nel modulo Set di datiSelect Columns in Dataset module

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per scegliere un sottoinsieme di colonne da utilizzare nelle operazioni a valle. Il modulo non rimuove fisicamente le colonne dal set di dati di origine. crea invece un sottoinsieme di colonne, in modo analogo a una *vista* di database o a *una proiezione.*

Questo modulo è utile quando è necessario limitare le colonne disponibili per un'operazione a valle o se si desidera ridurre le dimensioni del set di dati rimuovendo le colonne non necessarie.

Le colonne nel set di dati vengono restituite nello stesso ordine dei dati originali, anche se vengono specificate in un ordine diverso.

## <a name="how-to-use"></a>Utilizzo

Questo modulo non ha parametri. Utilizzare il selettore di colonna per scegliere le colonne da includere o escludere.

### <a name="choose-columns-by-name"></a>Scegliere le colonne in base al nome

Nel modulo sono disponibili diverse opzioni per la scelta delle colonne in base al nome: 

+ Filtrare e cercare

    Fare clic sull'opzione **BY NAME.**

    Se è stato connesso un set di dati già popolato, deve essere visualizzato un elenco di colonne disponibili. Se non viene visualizzata alcuna colonna, potrebbe essere necessario eseguire moduli a monte per visualizzare l'elenco delle colonne.

    Per filtrare l'elenco, digitare nella casella di ricerca. Ad esempio, se si `w` digita la lettera nella casella di ricerca, l'elenco viene filtrato in modo da visualizzare i nomi di colonna che contengono la lettera `w`.

    Selezionare le colonne e fare clic sul pulsante freccia destra per spostare le colonne selezionate nell'elenco nel riquadro di destra.

    + Per selezionare un intervallo continuo di nomi di colonna, premere **Maiusc e fare clic su**.
    + Per aggiungere singole colonne alla selezione, premere **CTRL e fare clic su**.

    Fare clic sul pulsante con il segno di spunta per salvare e chiudere.

+ Utilizzare nomi in combinazione con altre regole

    Fare clic sull'opzione **WITH RULES.**
    
    Scegliere una regola, ad esempio la visualizzazione di colonne di un tipo di dati specifico.

    Quindi, fare clic su singole colonne di quel tipo per nome, per aggiungerle all'elenco di selezione.

+ Digitare o incollare un elenco di nomi di colonna separati da virgole

    Se il set di dati è ampio, potrebbe essere più semplice usare indici o elenchi di nomi generati, anziché selezionare le colonne singolarmente. Supponendo che tu abbia preparato l'elenco in anticipo:

    1. Fare clic sull'opzione **WITH RULES.** 
    2. Selezionare **Nessuna colonna**, selezionare **Includi**e quindi fare clic all'interno della casella di testo con il punto esclamativo rosso. 
    3. Incollare o digitare un elenco separato da virgole di nomi di colonna convalidati in precedenza. Non è possibile salvare il modulo se una colonna ha un nome non valido, quindi assicurati di controllare i nomi in anticipo.
    
    È inoltre possibile utilizzare questo metodo per specificare un elenco di colonne utilizzando i relativi valori di indice. 

### <a name="choose-by-type"></a>Scegli per tipo

Se si utilizza l'opzione **WITH RULES,** è possibile applicare più condizioni alle selezioni di colonna. Ad esempio, potrebbe essere necessario ottenere solo colonne di entità geografiche di un tipo di dati numerico.

L'opzione **BEGIN WITH** determina il punto di partenza ed è importante per comprendere i risultati. 

+ Se si seleziona l'opzione **TUTTE LE COLONNE,** tutte le colonne vengono aggiunte all'elenco. Quindi, è necessario utilizzare l'opzione **Escludi** per *rimuovere* le colonne che soddisfano determinate condizioni. 

    Ad esempio, è possibile iniziare con tutte le colonne e quindi rimuovere le colonne in base al nome o al tipo.

+ Se si seleziona l'opzione **NO COLUMNS,** l'elenco delle colonne inizia vuoto. Specificare quindi le condizioni per *aggiungere* colonne all'elenco. 

    Se si applicano più regole, ogni condizione è **additiva**. Ad esempio, si supponga di iniziare senza colonne e quindi di aggiungere una regola per ottenere tutte le colonne numeriche. Nel set di dati Prezzo automobile, che restituisce 16 colonne. Quindi, fare **+** clic sul segno per aggiungere una nuova condizione e selezionare **Includi tutte le funzionalità**. Il set di dati risultante include tutte le colonne numeriche, oltre a tutte le colonne della feature, incluse alcune colonne di entità geografiche.

### <a name="choose-by-column-index"></a>Scegli per indice di colonna

L'indice di colonna fa riferimento all'ordine della colonna all'interno del set di dati originale.

+ Le colonne vengono numerate in sequenza a partire da 1.  
+ Per ottenere un intervallo di colonne, utilizzare un trattino. 
+ Specifiche aperte come `1-` o `-3` non sono consentite.
+ I valori di indice duplicati (o nomi di colonna) non sono consentiti e potrebbero generare un errore.

Ad esempio, supponendo che il set di dati conlepresenti almeno otto colonne, è possibile incollare uno degli esempi seguenti per restituire più colonne non contigue:For example, assuming your dataset has at least eight columns, you could paste in any of the following examples to return multiple non-contiguo columns: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

l'esempio finale non genera un errore; tuttavia, restituisce una `4`singola istanza di column .



### <a name="change-order-of-columns"></a>Modificare l'ordine delle colonne

L'opzione **Consenti duplicati e mantieni l'ordine delle colonne nella selezione** inizia con un elenco vuoto e aggiunge le colonne specificate in base al nome o all'indice. A differenza di altre opzioni, che restituiscono sempre le colonne nel loro "ordine naturale", questa opzione restituisce le colonne nell'ordine in cui sono stati assegnati o elencati. 

Ad esempio, in un set di dati con le colonne Col1, Col2, Col3 e Col4, è possibile invertire l'ordine delle colonne e omettere la colonna 2, specificando uno degli elenchi seguenti:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 