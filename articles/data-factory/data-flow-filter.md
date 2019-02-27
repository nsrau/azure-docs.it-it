---
title: Trasformazione Filtro per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Filtro per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271164"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Trasformazione Filtro per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Filtro consente di filtrare le righe. Creare un'espressione che definisce la condizione di filtro. Fare clic nella casella di testo per avviare il generatore di espressioni. Nel generatore di espressioni creare un'espressione di filtro per controllare le righe del flusso di dati corrente che possono passare (tramite filtro) alla trasformazione successiva.

Ad esempio, filtrare le righe in base alla colonna loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrare in base alla colonna year nella demo Movies:

```
year > 1980
```
