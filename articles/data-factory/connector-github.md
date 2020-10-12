---
title: Connettersi a GitHub
description: Usare GitHub per specificare i riferimenti alle entità di Common Data Model
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 0e17580524d6a67934aed83c6f745583b92e2422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84771037"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Usare GitHub per leggere i riferimenti alle entità di Common Data Model

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Il connettore GitHub in Azure Data Factory viene usato solo per ricevere lo schema di riferimento dell'entità per il formato [Common Data Model](format-common-data-model.md) nel flusso di dati di mapping.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di GitHub sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su **GitHub**. | sì
| userName | Nome utente GitHub | sì |
| password | Password di GitHub | sì |

## <a name="next-steps"></a>Passaggi successivi

Creare un [set](data-flow-source.md) di dati di origine nel flusso di dati del mapping.