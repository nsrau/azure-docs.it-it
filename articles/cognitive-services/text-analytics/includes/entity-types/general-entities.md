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
ms.openlocfilehash: a4c911fa077c2ec332974fb2f0c5abcdef21307c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284125"
---
## <a name="general-entity-types"></a>Tipi di entità generali:

### <a name="person"></a>Person
Nomi riconosciuti e altre persone nel testo.
Linguaggi:
* Anteprima pubblica: `English`

| Nome sottotipo | DESCRIZIONE             |
|--------------|-------------------------|
| N/D          | Nomi riconosciuti, ad esempio `Bill Gates`, `Marie Curie` |

### <a name="location"></a>Location

Punti di interesse, strutture e funzionalità geografiche naturali e umane.

Linguaggi:


* Anteprima pubblica: `English`

| Nome sottotipo | DESCRIZIONE                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/D          | località, ad esempio `Atlantic Ocean`, `library`, `Eiffel Tower``Statue of Liberty` |

### <a name="organization"></a>Organizzazione  

Organizzazioni, aziende, agenzie e altri gruppi di persone riconosciute. Ad esempio: società, gruppi politici, bande musicali, sport clubs, enti governativi e organizzazioni pubbliche. Le nazionalità e le religioni non sono incluse in questo tipo di entità. Linguaggi: 

* Anteprima pubblica: `English`

| Nome sottotipo | DESCRIZIONE                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| N/D          | organizzazioni, ad esempio `Microsoft``NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>Numero di telefono

Numeri di telefono (solo numeri di telefono degli Stati Uniti). 

Linguaggi:


* Anteprima pubblica: `English`

| Nome sottotipo | DESCRIZIONE                                  |
|----------|----------------------------------------------|
| N/D         | Numeri di telefono degli Stati Uniti, ad esempio `(312) 555-0176`. |

### <a name="email"></a>Email

Indirizzo di posta elettronica. 

Linguaggi:


* Anteprima pubblica: `English`

| Nome sottotipo | DESCRIZIONE                                  |
|----------|----------------------------------------------|
| N/D         | Indirizzo di posta elettronica, ad esempio `support@contoso.com` |

### <a name="url"></a>URL

URL Internet.

Linguaggi:


* Anteprima pubblica: `English`

| Nome sottotipo | DESCRIZIONE                                           |
|----------|-------------------------------------------------------|
| N/D         | URL per siti Web, ad esempio `https://www.bing.com`. |

###  <a name="number"></a>NUMBER

Numeri e quantità numeriche. 

Linguaggi:


* Anteprima pubblica: `English`

| Nome sottotipo    | esempi                     |
|-------------|------------------------------|
| N/D         | `6`, `six`                   |
| Percentuale  | `50%`, `fifty percent`       |
| Ordinale     | `2nd`, `second`              |
| Valuta    | `$10.99`, `€30.00`           |
| Dimensione   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
