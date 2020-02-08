---
title: Entità denominate generali
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: f95d7613926cf332a498cca84563dbc0ebcbbe9b
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086875"
---
## <a name="general-entity-types"></a>Tipi di entità generali:

### <a name="person"></a>Persona

Riconoscere i nomi di persona nel testo.

Lingue:
* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                                      | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------------------------------|---------------------------------------|
| N/D          | Nomi di persona riconosciuti, ad esempio `Bill Gates`, `Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
Tipo di processo o ruolo utilizzato da una persona.

Lingue:
* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                                                                | Disponibile a partire dalla versione del modello |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Tipi di processo, ad esempio `civil engineer`, `salesperson`, `chef`, `librarian``nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Location

Punti di riferimento naturali e umani, strutture, funzionalità geografiche e entità geopolitiche.

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo              | Descrizione                                                                              | Disponibile a partire dalla versione del modello |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| N/D                       | località, ad esempio `Atlantic Ocean`, `library`, `Eiffel Tower``Statue of Liberty`  | `2019-10-01`                           |
| Entità geopolitica (GPE) | Città, Paesi, Stati, ad esempio `Seattle`, `Pennsylvania`, `South Africa``Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organization  

Organizzazioni, aziende, agenzie e altri gruppi di persone riconosciute. Ad esempio: società, gruppi politici, bande musicali, sport clubs, enti governativi e organizzazioni pubbliche. Le nazionalità e le religioni non sono incluse in questo tipo di entità. 

Lingue: 

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                                                                             | Disponibile a partire dalla versione del modello |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | organizzazioni, ad esempio `Microsoft`, `NASA`, `National Oceanic and Atmospheric Administration``VOA` | `2019-10-01`                           |

### <a name="event"></a>Evento  

Eventi cronologici, sociali e naturali.  

Lingue: 

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                                            | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| N/D          | Eventi come `wedding`, `hurricane`, `car accident`, `solar eclipse``American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Prodotto  

Oggetti fisici di diverse categorie.  

Lingue: 

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                                                        | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| N/D          | Ad esempio, `Microsoft Surface laptop`, `sunglasses`, `motorcycle`, `bag``Xbox` | `2020-02-01`                           |
| Calcolo    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Competenza  

Entità che descrive una funzionalità o un'esperienza.  

Lingue: 

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                                                 | Disponibile a partire dalla versione del modello |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| N/D          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Numero di telefono

Numeri di telefono (solo numeri di telefono degli Stati Uniti). 

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                    | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------------|----------------------------------------|
| N/D          | Numeri di telefono degli Stati Uniti, ad esempio `(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

Indirizzo di posta elettronica. 

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                      | Disponibile a partire dalla versione del modello |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Indirizzo di posta elettronica, ad esempio `support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

URL Internet.

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                          | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------------------|----------------------------------------|
| N/D          | URL per siti Web, ad esempio `https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>Indirizzo IP

Indirizzo protocollo Internet

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                              | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------|----------------------------------------|
| N/D          | Indirizzo di rete, ad esempio `10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Entità di data e ora. 

* Disponibile a partire dalla versione del modello `2019-10-01`

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo    | Esempi                     |
|-------------|------------------------------|
| N/D         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Data  | `May 2nd, 2017`, `05/02/2017`       |
| Tempo     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Durata | `2.5 minutes`, `one and a half hours`         |
| Configurazione | `every Saturday`         |

###  <a name="quantity"></a>Quantità

Numeri e quantità numeriche. 

* Disponibile a partire dalla versione del modello `2019-10-01`

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo    | Esempi                     |
|-------------|------------------------------|
| NUMBER         | `6`, `six`                   |
| Percentuale  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Tempo trascorso         | `90 day old`, `30 years old` |
| Currency    | `$10.99`, `€30.00`           |
| Dimension   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
