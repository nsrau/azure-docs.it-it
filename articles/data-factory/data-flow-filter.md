---
title: Trasformazione Filtro per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Filtro per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234420"
---
# <a name="azure-data-factory-filter-transformation"></a>Trasformazione di filtro di Azure data factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Filtro consente di filtrare le righe. Creare un'espressione che definisce la condizione di filtro. Fare clic nella casella di testo per avviare il generatore di espressioni. Nel generatore di espressioni creare un'espressione di filtro per controllare le righe del flusso di dati corrente che possono passare (tramite filtro) alla trasformazione successiva. Considerare la trasformazione di filtro come la clausola WHERE di un'istruzione SQL.

## <a name="filter-on-loanstatus-column"></a>Filtrare loan_status colonna:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrare in base alla colonna year nella demo Movies:

```
year > 1980
```

## <a name="next-steps"></a>Passaggi successivi

Provare a una colonna di trasformazione, filtro di [selezionare trasformazione](data-flow-select.md)
