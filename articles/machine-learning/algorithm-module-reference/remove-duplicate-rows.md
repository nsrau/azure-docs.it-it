---
title: 'Rimuovi righe duplicate: riferimento modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Rimuovi righe duplicate in Azure Machine Learning per rimuovere potenziali duplicati da un set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 490d3305abcbcd906a0f727d736db8cab7e4287e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456022"
---
# <a name="remove-duplicate-rows-module"></a>Modulo Rimuovi righe duplicate

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Utilizzare questo modulo per rimuovere potenziali duplicati da un set di dati.

Si supponga, ad esempio, che i dati siano simili ai seguenti e rappresentino più record per i pazienti. 

| ID paziente | Initials| Sesso|Age|Ammesso|
|----|----|----|----|----|
|1|F.m.| M| 53| Gen|
|2| F.A.M.| M| 53| Gen|
|3| F.A.M.| M| 24| Gen|
|3| F.m.| M| 24| Feb|
|4| F.m.| M| 23| Feb|
| | F.m.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Chiaramente, questo esempio ha più colonne con dati potenzialmente duplicati. La loro effettiva duplicazione dipende dalla conoscenza dei dati da parte dell'utente. 

+ Ad esempio, potresti sapere che molti pazienti hanno lo stesso nome. Non si eliminerebbero i duplicati utilizzando colonne nome, ma solo la colonna **ID.** In questo modo, vengono filtrate solo le righe con valori ID duplicati, indipendentemente dal fatto che i pazienti abbiano lo stesso nome o meno.

+ In alternativa, è possibile decidere di consentire duplicati nel campo ID e utilizzare un'altra combinazione di file per trovare record univoci, ad esempio nome, cognome, età e sesso.  

Per impostare i criteri per indicare se una riga è duplicata o meno, specificare una singola colonna o un set di colonne da utilizzare come **chiavi**. Due righe sono considerate duplicati solo quando i valori in **tutte le** colonne chiave sono uguali. Se una riga ha un valore mancante per **le chiavi**, non verranno considerate righe duplicate. Ad esempio, se Sesso ed età sono impostati come Chiavi nella tabella precedente, le righe 6 e 7 non sono righe duplicate dato che hanno un valore mancante in Età.

Quando si esegue il modulo, viene creato un set di dati candidato e viene restituito un set di righe senza duplicati nel set di colonne specificato.

> [!IMPORTANT]
> Il set di dati di origine non viene modificato. questo modulo crea un nuovo set di dati che viene filtrato per escludere i duplicati, in base ai criteri specificati.

## <a name="how-to-use-remove-duplicate-rows"></a>Come utilizzare Rimuovi righe duplicate

1. Aggiungere il modulo alla pipeline. Il modulo **Rimuovi righe duplicate** è disponibili in **Trasformazione dati**, **Manipolazione**.  

2. Connettere il set di dati che si desidera verificare la presenza di righe duplicate.

3. Nel riquadro **Proprietà,** in **Espressione filtro selezione colonne chiave,** fare clic su Avvia **selettore**di colonne per scegliere le colonne da utilizzare per l'identificazione dei duplicati.

    In questo contesto, **Key** non significa un identificatore univoco. Tutte le colonne selezionate utilizzando il Selettore di colonna vengono designate come **colonne chiave**. Tutte le colonne non selezionate sono considerate colonne non chiave. La combinazione di colonne selezionate come chiavi determina l'univocità dei record. (Si consideri come un'istruzione SQL che utilizza più join di parità.)

    Esempi:

    + "Voglio assicurarmi che gli ID siano univoci": scegli solo la colonna ID.
    + "Voglio assicurarmi che la combinazione di nome, cognome e ID sia univoca": seleziona tutte e tre le colonne.

4. Utilizzare la casella di controllo **Mantieni prima riga duplicata** per indicare la riga da restituire quando vengono trovati i duplicati:

    + Se l'opzione è selezionata, viene restituita la prima riga e altre vengono eliminate. 
    + Se si deseleziona questa opzione, l'ultima riga duplicata viene mantenuta nei risultati e altre vengono eliminate. 

5. Inviare la pipeline.

6. Per esaminare i risultati, fare clic con il pulsante destro del mouse sul modulo e selezionare **Visualizza**. 

> [!TIP]
> Se i risultati sono difficili da comprendere o se si desidera escludere alcune colonne dalla considerazione, è possibile rimuovere le colonne utilizzando il modulo [Seleziona colonne nel set di dati.](./select-columns-in-dataset.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 