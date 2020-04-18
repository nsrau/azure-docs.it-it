---
title: Esiste la trasformazione nel mapping del flusso di datiExists transformation in mapping data flow
description: Verificare la presenza di righe esistenti usando la trasformazione exists nel flusso di dati di mapping di Azure Data FactoryCheck for existing rows using the exists transformation in Azure Data Factory mapping data flow
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: a303c8fa1e23460fb906232eedb6bfb1930b4bc9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606472"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Esiste la trasformazione nel mapping del flusso di datiExists transformation in mapping data flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La trasformazione exists è una trasformazione del filtro di riga che controlla se i dati sono presenti in un'altra origine o flusso. Il flusso di output include tutte le righe nel flusso sinistro che esistono o non esistono nel flusso di destra. La trasformazione esistente ```SQL WHERE EXISTS``` ```SQL WHERE NOT EXISTS```è simile a e .

## <a name="configuration"></a>Configurazione

1. Scegli quale flusso di dati stai controllando l'esistenza nell'elenco a discesa **Flusso a destra.**
1. Specificare se si sta cercando i dati esistenti o meno nell'impostazione **Tipo di consissioni.**
1. Selezionare se si desidera o meno **un'espressione personalizzata.**
1. Scegliere le colonne chiave da confrontare con le condizioni esistenti. Per impostazione predefinita, il flusso di dati cerca l'uguaglianza tra una colonna in ogni flusso. Per eseguire il confronto tramite un valore calcolato, passare il mouse sull'elenco a discesa delle colonne e selezionare **Colonna calcolata**.

![Impostazioni esistenti](media/data-flow/exists.png "esiste 1")

### <a name="multiple-exists-conditions"></a>Esistono più condizioni

Per confrontare più colonne di ogni flusso, aggiungere una nuova condizione esistente facendo clic sull'icona più accanto a una riga esistente. Ogni condizione aggiuntiva è unita da un'istruzione "and". Il confronto di due colonne equivale all'espressione seguente:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Espressione personalizzata

Per creare un'espressione in formato libero contenente operatori diversi da "and" e "uguale a", selezionare il campo **Espressione personalizzata.** Immettere un'espressione personalizzata tramite il generatore di espressioni del flusso di dati facendo clic sulla casella blu.

![Esiste impostazioni personalizzate](media/data-flow/exists1.png "esiste personalizzato")

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Esempio

L'esempio seguente è `checkForChanges` una trasformazione `NameNorm2` esistente `TypeConversions`denominata che accetta il flusso sinistro e il flusso destro.  La condizione exists `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` è l'espressione `EMPID` che `Region` restituisce true se entrambe le colonne e in ogni flusso corrispondono. Come stiamo controllando l'esistenza, `negate` è falso. Non stiamo abilitando alcuna trasmissione `broadcast` nella `'none'`scheda Ottimizza così ha valore .

Nell'esperienza utente di Data Factory questa trasformazione è simile all'immagine seguente:In the Data Factory UX, this transformation looks like the below image:

![Esempio exists](media/data-flow/exists-script.png "Esempio exists")

Lo script del flusso di dati per questa trasformazione si trova nel frammento di codice seguente:The data flow script for this transformation is in the snippet below:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Passaggi successivi

Trasformazioni simili sono [Ricerca](data-flow-lookup.md) e [Join](data-flow-join.md).
