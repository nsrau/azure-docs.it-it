---
title: Entità denominate generali
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: e81a1de02c112abd5c52f0f83404a615d738e01c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508067"
---
## <a name="general-entity-types"></a>Tipi di entità generali:

### <a name="person"></a>Person
Nomi riconosciuti e altre persone nel testo.
Linguaggi:
* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione             |
|--------------|-------------------------|
| N/D          | Nomi riconosciuti, ad esempio `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Percorso

Punti di interesse, strutture e funzionalità geografiche naturali e umane.

Linguaggi:


* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/D          | località, ad esempio `Atlantic Ocean`, `library`, `Eiffel Tower``Statue of Liberty` |

### <a name="organization"></a>Organizzazione  

Organizzazioni, aziende, agenzie e altri gruppi di persone riconosciute. Ad esempio: società, gruppi politici, bande musicali, sport clubs, enti governativi e organizzazioni pubbliche. Le nazionalità e le religioni non sono incluse in questo tipo di entità. Linguaggi: 

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/D          | organizzazioni, ad esempio `Microsoft``NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Numero di telefono

Numeri di telefono. 

Linguaggi:


* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                  |
|----------|----------------------------------------------|
| N/D         | Numeri di telefono, ad esempio `+1 123-123-123`. |

### <a name="url"></a>URL

URL Internet.

Linguaggi:


* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                           |
|----------|-------------------------------------------------------|
| N/D         | URL per siti Web, ad esempio `https://www.bing.com`. |

###  <a name="number"></a>Number

Numeri e quantità numeriche. 

Linguaggi:


* Anteprima pubblica: `English`

| Nome sottotipo    | Esempi                     |
|-------------|------------------------------|
| N/D         | `6`, `six`                   |
| Percentuale  | `50%`, `fifty percent`       |
| Ordinale     | `2nd`, `second`              |
| Valuta    | `$10.99`, `€30.00`           |
| Dimensione   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
