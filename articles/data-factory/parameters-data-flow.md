---
title: Impostazione dei parametri per i flussi di dati di mapping
description: Informazioni su come parametrizzare un flusso di dati di mapping dalle pipeline di data factoryLearn how to parameterize a mapping data flow from data factory pipelines
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 82660cdb4ab6523bae7608fe3b071f20cb3603f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419171"
---
# <a name="parameterizing-mapping-data-flows"></a>Impostazione dei parametri per i flussi di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Il mapping dei flussi di dati in Azure Data Factory supporta l'uso di parametri. È possibile definire parametri all'interno della definizione del flusso di dati, che è quindi possibile usare in tutte le espressioni. I valori dei parametri possono essere impostati dalla pipeline di chiamata tramite l'attività Esegui flusso di dati. Sono disponibili tre opzioni per l'impostazione dei valori nelle espressioni di attività del flusso di dati:You have three options for setting the values in the data flow activity expressions:

* Usare il linguaggio delle espressioni del flusso di controllo della pipeline per impostare un valore dinamicoUse the pipeline control flow expression language to set a dynamic value
* Usare il linguaggio delle espressioni del flusso di dati per impostare un valore dinamicoUse the data flow expression language to set a dynamic value
* Usare uno dei linguaggi di espressione per impostare un valore letterale staticoUse either expression language to set a static literal value

Utilizzare questa funzionalità per rendere i flussi di dati generici, flessibili e riutilizzabili. È possibile parametrizzare le impostazioni e le espressioni del flusso di dati con questi parametri.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Creare parametri in un flusso di dati di mappingCreate parameters in a mapping data flow

Per aggiungere parametri al flusso di dati, fare clic sulla parte vuota dell'area di disegno del flusso di dati per visualizzare le proprietà generali. Nel riquadro delle impostazioni verrà visualizzata una scheda denominata **Parametro**. Selezionare **Nuovo** per generare un nuovo parametro. Per ogni parametro, è necessario assegnare un nome, selezionare un tipo e, facoltativamente, impostare un valore predefinito.

![Creare parametri del flusso di datiCreate Data Flow parameters](media/data-flow/create-params.png "Creare parametri del flusso di datiCreate Data Flow parameters")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Usare i parametri in un flusso di dati di mappingUse parameters in a mapping data flow 

È possibile fare riferimento ai parametri in qualsiasi espressione del flusso di dati. I parametri iniziano con il carattere e non sono modificabili. L'elenco dei parametri disponibili è disponibile all'interno del Generatore di espressioni nella scheda **Parametri.**

![Espressione dei parametri del flusso di datiData flow parameter expression](media/data-flow/parameter-expression.png "Espressione dei parametri del flusso di datiData flow parameter expression")

È possibile aggiungere rapidamente altri parametri selezionando **Nuovo parametro** e specificando il nome e il tipo.

![Espressione dei parametri del flusso di datiData flow parameter expression](media/data-flow/new-parameter-expression.png "Espressione dei parametri del flusso di datiData flow parameter expression")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Passaggio di un nome di colonna come parametroPassing in a column name as a parameter

Un modello comune consiste nel passare un nome di colonna come valore di parametro. Per fare riferimento alla colonna associata al parametro, utilizzare la `byName()` funzione . Ricordarsi di eseguire il cast della colonna `toString()`nel tipo appropriato con una funzione di cast come .

Ad esempio, se si desidera eseguire il `columnName`mapping di una colonna stringa `toString(byName($columnName))`in base a un parametro , è possibile aggiungere una trasformazione di colonna derivata uguale a .

![Passaggio di un nome di colonna come parametroPassing in a column name as a parameter](media/data-flow/parameterize-column-name.png "Passaggio di un nome di colonna come parametroPassing in a column name as a paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Assegnare valori di parametro da una pipelineAssign parameter values from a pipeline

Dopo aver creato il flusso di dati con parametri, è possibile eseguirlo da una pipeline con l'attività Esegui flusso di dati. Dopo aver aggiunto l'attività al canvas della pipeline, verranno visualizzati i parametri del flusso di dati disponibili nella scheda **Parametri** dell'attività.

![Impostazione di un parametro Flusso di datiSetting a Data Flow parameter](media/data-flow/parameter-assign.png "Impostazione di un parametro Flusso di datiSetting a Data Flow parameter")

Se il tipo di dati del parametro è stringa, quando si fa clic sulla casella di testo per impostare i valori dei parametri, è possibile scegliere di immettere una pipeline o un'espressione del flusso di dati. Se si sceglie l'espressione della pipeline, verrà visualizzato il riquadro espressione della pipeline. Assicurarsi di includere le funzioni della `'@{<expression>}'`pipeline all'interno della sintassi di interpolazione delle stringhe utilizzando , ad esempio:

```'@{pipeline().RunId}'```

Se il parametro non è di tipo stringa, verrà sempre visualizzato il Generatore di espressioni flusso di dati. Qui è possibile immettere qualsiasi espressione o valore letterale che si desidera corrisponda al tipo di dati del parametro. Di seguito sono riportati esempi di espressione del flusso di dati e una stringa letterale dal generatore di espressioni:Below are examples of data flow expression and a literal string from the expression builder:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Ogni flusso di dati di mapping può avere qualsiasi combinazione di parametri di pipeline e espressione del flusso di dati. 

![Esempio di parametri del flusso di datiData flow parameters sample](media/data-flow/parameter-example.png "Esempio di parametri del flusso di datiData flow parameters sample")



## <a name="next-steps"></a>Passaggi successivi
* [Eseguire l'attività del flusso di datiExecute data flow activity](control-flow-execute-data-flow-activity.md)
* [Espressioni del flusso di controlloControl flow expressions](control-flow-expression-language-functions.md)
