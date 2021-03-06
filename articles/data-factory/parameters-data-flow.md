---
title: Impostazione dei parametri per i flussi di dati di mapping
description: Informazioni su come parametrizzare un flusso di dati di mapping da pipeline di data factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 564c7cf6e9627db08d543b964ce476e71bfb473d
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040751"
---
# <a name="parameterizing-mapping-data-flows"></a>Impostazione dei parametri per i flussi di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Il mapping di flussi di dati in Azure Data Factory supporta l'utilizzo di parametri. Definire i parametri all'interno della definizione del flusso di dati e usarli in tutte le espressioni. I valori dei parametri vengono impostati dalla pipeline chiamante tramite l'attività Esegui flusso di dati. Sono disponibili tre opzioni per l'impostazione dei valori nelle espressioni di attività flusso di dati:

* Usare il linguaggio delle espressioni del flusso di controllo della pipeline per impostare un valore dinamico
* Usare il linguaggio delle espressioni del flusso di dati per impostare un valore dinamico
* Usare il linguaggio delle espressioni per impostare un valore letterale statico

Usare questa funzionalità per rendere i flussi di dati generici, flessibili e riutilizzabili. Con questi parametri è possibile parametrizzare le impostazioni e le espressioni del flusso di dati.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Creazione di parametri in un flusso di dati di mapping

Per aggiungere parametri al flusso di dati, fare clic sulla parte vuota dell'area di disegno del flusso di dati per visualizzare le proprietà generali. Nel riquadro impostazioni viene visualizzata una scheda denominata **Parameter** . Selezionare **nuovo** per generare un nuovo parametro. Per ogni parametro, è necessario assegnare un nome, selezionare un tipo e, facoltativamente, impostare un valore predefinito.

![Creare parametri del flusso di dati](media/data-flow/create-params.png "Creare parametri del flusso di dati")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Utilizzare parametri in un flusso di dati di mapping 

È possibile fare riferimento ai parametri in qualsiasi espressione del flusso di dati. I parametri iniziano con $ e non sono modificabili. È possibile trovare l'elenco dei parametri disponibili all'interno di generatore di espressioni nella scheda **parametri** .

![Screenshot mostra i parametri disponibili nella scheda parametri.](media/data-flow/parameter-expression.png "Espressione parametro flusso di dati")

È possibile aggiungere rapidamente parametri aggiuntivi selezionando **nuovo parametro** e specificando il nome e il tipo.

![Screenshot mostra i parametri nella scheda parametri con i nuovi parametri aggiunti.](media/data-flow/new-parameter-expression.png "Espressione parametro flusso di dati")

## <a name="assign-parameter-values-from-a-pipeline"></a>Assegnare valori di parametro da una pipeline

Una volta creato un flusso di dati con parametri, è possibile eseguirlo da una pipeline con l'attività Esegui flusso di dati. Dopo aver aggiunto l'attività nell'area di disegno della pipeline, verranno visualizzati i parametri del flusso di dati disponibili nella scheda **parametri** dell'attività.

Quando si assegnano valori di parametro, è possibile usare il [linguaggio delle espressioni della pipeline](control-flow-expression-language-functions.md) o il linguaggio delle espressioni del flusso di [dati](data-flow-expression-functions.md) in base ai tipi Spark. Ogni flusso di dati di mapping può includere qualsiasi combinazione di parametri dell'espressione del flusso di dati e della pipeline.

![Screenshot mostra la scheda parametri con l'espressione del flusso di dati selezionata per il valore di param.](media/data-flow/parameter-assign.png "Impostazione di un parametro del flusso di dati")

### <a name="pipeline-expression-parameters"></a>Parametri dell'espressione pipeline

I parametri delle espressioni della pipeline consentono di fare riferimento a variabili di sistema, funzioni, parametri della pipeline e variabili simili ad altre attività della pipeline. Quando si fa clic su **espressione pipeline** , si aprirà un NAV laterale che consente di immettere un'espressione usando il generatore di espressioni.

![Screenshot che mostra il riquadro Generatore di espressioni.](media/data-flow/parameter-pipeline.png "Impostazione di un parametro del flusso di dati")

Quando vi viene fatto riferimento, i parametri della pipeline vengono valutati e il relativo valore viene utilizzato nel linguaggio delle espressioni del flusso di dati. Il tipo di espressione della pipeline non deve corrispondere al tipo di parametro del flusso di dati. 

#### <a name="string-literals-vs-expressions"></a>Espressioni di confronto tra valori letterali stringa

Quando si assegna un parametro dell'espressione della pipeline di tipo String, per impostazione predefinita verranno aggiunte le virgolette e il valore verrà valutato come valore letterale. Per leggere il valore del parametro come espressione del flusso di dati, selezionare la casella espressione accanto al parametro.

![Screenshot mostra l'espressione del riquadro parametri flusso di dati selezionata per un parametro.](media/data-flow/string-parameter.png "Impostazione di un parametro del flusso di dati")

Se il parametro del flusso `stringParam` di dati fa riferimento a un parametro della pipeline con valore `upper(column1)` . 

- Se l'espressione è selezionata, `$stringParam` restituisce il valore di Column1 all maiuscole.
- Se Expression non è selezionato (comportamento predefinito),  `$stringParam` restituisce `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Passaggio di timestamp

Nel linguaggio delle espressioni della pipeline, le variabili di sistema come le `pipeline().TriggerTime` funzioni e come i `utcNow()` timestamp restituiti come stringhe nel formato ' aaaa-mm-gg \' T \' hh: mm: SS. SSSSSSZ'. Per convertirli in parametri del flusso di dati di tipo timestamp, usare l'interpolazione di stringhe per includere il timestamp desiderato in una `toTimestamp()` funzione. Ad esempio, per convertire l'ora del trigger della pipeline in un parametro del flusso di dati, è possibile usare `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![Screenshot mostra la scheda parametri in cui è possibile immettere un'ora di attivazione.](media/data-flow/parameter-timestamp.png "Impostazione di un parametro del flusso di dati")

> [!NOTE]
> I flussi di dati possono supportare solo cifre fino a 3 millisecondi. La `left()` funzione viene utilizzata per tagliare le cifre aggiuntive.

#### <a name="pipeline-parameter-example"></a>Esempio di parametro pipeline

Supponiamo di avere un parametro integer `intParam` che fa riferimento a un parametro della pipeline di tipo String, `@pipeline.parameters.pipelineParam` . 

![Screenshot mostra la scheda parametri con i parametri denominati stringParam e intParam.](media/data-flow/parameter-pipeline-2.png "Impostazione di un parametro del flusso di dati")

`@pipeline.parameters.pipelineParam` viene assegnato un valore di `abs(1)` in fase di esecuzione.

![Screenshot mostra la scheda parametri con il valore di b s (1) selezionato.](media/data-flow/parameter-pipeline-4.png "Impostazione di un parametro del flusso di dati")

Quando `$intParam` si fa riferimento a un oggetto in un'espressione, ad esempio una colonna derivata, viene valutato `abs(1)` return `1` . 

![Screenshot che mostra il valore delle colonne.](media/data-flow/parameter-pipeline-3.png "Impostazione di un parametro del flusso di dati")

### <a name="data-flow-expression-parameters"></a>Parametri dell'espressione del flusso di dati

Seleziona **espressione flusso di dati** consente di aprire il generatore di espressioni del flusso di dati. Sarà possibile fare riferimento a funzioni, ad altri parametri e a qualsiasi colonna dello schema definita nel flusso di dati. Questa espressione verrà valutata come se fosse a cui viene fatto riferimento.

> [!NOTE]
> Se si passa un'espressione non valida o si fa riferimento a una colonna dello schema che non esiste in tale trasformazione, il parametro restituirà null.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Passaggio di un nome di colonna come parametro

Un modello comune consiste nel passare un nome di colonna come valore di parametro. Se la colonna è definita nello schema del flusso di dati, è possibile farvi riferimento direttamente come espressione stringa. Se la colonna non è definita nello schema, utilizzare la `byName()` funzione. Ricordarsi di eseguire il cast della colonna al tipo appropriato con una funzione di cast, ad esempio `toString()` .

Se ad esempio si desidera eseguire il mapping di una colonna di stringa basata su un parametro `columnName` , è possibile aggiungere una trasformazione colonna derivata uguale a `toString(byName($columnName))` .

![Passaggio di un nome di colonna come parametro](media/data-flow/parameterize-column-name.png "Passaggio di un nome di colonna come parametro")

## <a name="next-steps"></a>Passaggi successivi
* [Attività Esegui flusso di dati](control-flow-execute-data-flow-activity.md)
* [Espressioni del flusso di controllo](control-flow-expression-language-functions.md)
