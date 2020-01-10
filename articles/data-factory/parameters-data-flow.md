---
title: Flussi di dati di mapping di parametrizzazione
description: Informazioni su come parametrizzare un flusso di dati di mapping da pipeline di data factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c589cfeab7a812e09ce7f7620e93b72bd362859a
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746151"
---
# <a name="parameterizing-mapping-data-flows"></a>Flussi di dati di mapping di parametrizzazione

Il mapping di flussi di dati in Azure Data Factory supporta l'utilizzo di parametri. È possibile definire parametri all'interno della definizione del flusso di dati, che è quindi possibile usare in tutte le espressioni. I valori dei parametri possono essere impostati dalla pipeline chiamante tramite l'attività Esegui flusso di dati. Sono disponibili tre opzioni per l'impostazione dei valori nelle espressioni di attività flusso di dati:

* Usare il linguaggio delle espressioni del flusso di controllo della pipeline per impostare un valore dinamico
* Usare il linguaggio delle espressioni del flusso di dati per impostare un valore dinamico
* Usare il linguaggio delle espressioni per impostare un valore letterale statico

Usare questa funzionalità per rendere i flussi di dati generici, flessibili e riutilizzabili. Con questi parametri è possibile parametrizzare le impostazioni e le espressioni del flusso di dati.

> [!NOTE]
> Per utilizzare le espressioni del flusso di controllo della pipeline, il parametro del flusso di dati deve essere di tipo String.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Creazione di parametri in un flusso di dati di mapping

Per aggiungere parametri al flusso di dati, fare clic sulla parte vuota dell'area di disegno del flusso di dati per visualizzare le proprietà generali. Nel riquadro impostazioni viene visualizzata una scheda denominata **Parameter**. Selezionare **nuovo** per generare un nuovo parametro. Per ogni parametro, è necessario assegnare un nome, selezionare un tipo e, facoltativamente, impostare un valore predefinito.

![Creare parametri del flusso di dati](media/data-flow/create-params.png "Creare parametri del flusso di dati")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Utilizzare parametri in un flusso di dati di mapping 

È possibile fare riferimento ai parametri in qualsiasi espressione del flusso di dati. I parametri iniziano con $ e non sono modificabili. È possibile trovare l'elenco dei parametri disponibili all'interno di generatore di espressioni nella scheda **parametri** .

![Espressione parametro flusso di dati](media/data-flow/parameter-expression.png "Espressione parametro flusso di dati")

È possibile aggiungere rapidamente parametri aggiuntivi selezionando **nuovo parametro** e specificando il nome e il tipo.

![Espressione parametro flusso di dati](media/data-flow/new-parameter-expression.png "Espressione parametro flusso di dati")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Passaggio di un nome di colonna come parametro

Un modello comune consiste nel passare un nome di colonna come valore di parametro. Per fare riferimento alla colonna associata al parametro, utilizzare la funzione `byName()`. Ricordarsi di eseguire il cast della colonna al tipo appropriato con una funzione di cast, ad esempio `toString()`.

Se ad esempio si desidera eseguire il mapping di una colonna di stringa basata su un parametro `columnName`, è possibile aggiungere una trasformazione colonna derivata uguale a `toString(byName($columnName))`.

![Passaggio di un nome di colonna come parametro](media/data-flow/parameterize-column-name.png "Passaggio di un nome di colonna come Paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Assegnare valori di parametro da una pipeline

Dopo aver creato il flusso di dati con parametri, è possibile eseguirlo da una pipeline con l'attività Esegui flusso di dati. Dopo aver aggiunto l'attività nell'area di disegno della pipeline, verranno visualizzati i parametri del flusso di dati disponibili nella scheda **parametri** dell'attività.

![Impostazione di un parametro del flusso di dati](media/data-flow/parameter-assign.png "Impostazione di un parametro del flusso di dati")

Se il tipo di dati del parametro è String, quando si fa clic sulla casella di testo per impostare i valori dei parametri, è possibile scegliere di immettere una pipeline o un'espressione del flusso di dati. Se si sceglie espressione pipeline, verrà visualizzato il pannello espressione pipeline. Assicurarsi di includere le funzioni della pipeline all'interno della sintassi di interpolazione di stringhe usando `'@{<expression>}'`, ad esempio:

```'@{pipeline().RunId}'```

Se il parametro non è di tipo stringa, viene sempre visualizzato il generatore di espressioni del flusso di dati. Qui è possibile immettere qualsiasi espressione o valore letterale che si desidera corrisponda al tipo di dati del parametro. Di seguito sono riportati esempi di espressione del flusso di dati e una stringa letterale dal generatore di espressioni:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Ogni flusso di dati di mapping può includere qualsiasi combinazione di parametri dell'espressione del flusso di dati e della pipeline. 

![Esempio di parametri del flusso di dati](media/data-flow/parameter-example.png "Esempio di parametri del flusso di dati")



## <a name="next-steps"></a>Passaggi successivi
* [Attività Esegui flusso di dati](control-flow-execute-data-flow-activity.md)
* [Espressioni del flusso di controllo](control-flow-expression-language-functions.md)
