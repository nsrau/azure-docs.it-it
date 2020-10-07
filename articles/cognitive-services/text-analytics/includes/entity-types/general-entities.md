---
title: Entità denominate generali
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 630f04bf2cc9e7de6331f9d25754a20fe2327d76
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779393"
---
Quando si inviano richieste all'endpoint, vengono restituite le categorie di entità seguenti `/entities/recognition/general` .

| Category   | Subcategory | Descrizione                          | Versione modello di avvio                                                    | Note |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| Persona     | N/D         | Nomi delle persone.  | `2019-10-01`  | Restituito anche da NER v 2.1 |
| PersonType | N/D         | Tipi di processo o ruoli contenuti da una persona. | `2020-02-01` | |
|Location    | N/D         | Punti di interesse naturali e umani, strutture, funzionalità geografiche e entità geopolitiche     |  `2019-10-01` | Restituito anche da NER v 2.1 |
|Location     | Entità geopolitica (GPE)        | Città, Paesi o aree geografiche.      | `2020-02-01` | |
|Location     | Structural                       | Strutture artificiali. | `2020-04-01` | |
|Location     | Geografica       | Funzionalità geografiche e naturali, ad esempio fiumi, oceani e deserti. |  `2020-04-01` | |
|Organizzazione  | N/D | Società, gruppi politici, bande musicali, sport clubs, enti governativi e organizzazioni pubbliche.  | `2019-10-01` | Le nazionalità e le religioni non sono incluse in questo tipo di entità. Restituito anche da NER v 2.1 |
|Organizzazione | Medicina | Società e gruppi medicali. | `2020-04-01` |  |
|Organizzazione | Scambio scorte | Gruppi di scambio azionario. | `2020-04-01` | |
| Organizzazione | Sport | Organizzazioni correlate allo sport. | `2020-04-01` |  |
| Event  | N/D | Eventi cronologici, di social networking e naturalmente in corso. | `2020-02-01` |  |
| Event  | Culturale | Eventi culturali e festivi. | `2020-04-01` | |
| Event  | Natural | Eventi che si verificano naturalmente. | `2020-04-01` |  |
| Event  | Sport | Eventi sportivi.  | `2020-04-01` | |
| Prodotto | N/D | Oggetti fisici di diverse categorie. | `2020-02-01` | |
| Prodotto | Elaborazione dei prodotti | Elaborazione di prodotti. |  `2020-02-01 ` | |
| Competenza | N/D | Funzionalità, competenze o esperienza. | `2020-02-01` |  |
| Indirizzo | N/D | Indirizzi di posta elettronica completi.  | `2020-04-01` |  |
| PhoneNumber | N/D | Numeri di telefono (solo numeri di telefono US e UE). | `2019-10-01` | Restituito anche da NER v 2.1 |
| E-mail | N/D | Indirizzi di posta elettronica. | `2019-10-01` | Restituito anche da NER v 2.1 |
| URL | N/D | URL per siti Web. | `2019-10-01` | Restituito anche da NER v 2.1  |
| IP | N/D | Indirizzi IP di rete. | `2019-10-01` | Restituito anche da NER v 2.1 |
| Datetime | N/D | Date e ore del giorno. | `2019-10-01` | Restituito anche da NER v 2.1 | 
| DataOra | Data | Date calendario. | `2019-10-01` | Restituito anche da NER v 2.1 |
| DataOra | Ora | Ora del giorno | `2019-10-01` | Restituito anche da NER v 2.1 |
| DataOra | DateRange | Intervalli di date. | `2019-10-01` | Restituito anche da NER v 2.1 |
| DataOra | Intervallo di tempo | Intervalli di tempo. | `2019-10-01` | Restituito anche da NER v 2.1 |
| DataOra | Durata | Durate | `2019-10-01` | Restituito anche da NER v 2.1 |
| DataOra | Impostazione | Set, ripetuto volte. |  `2019-10-01` | Restituito anche da NER v 2.1 |
| Quantità | N/D | Numeri e quantità numeriche. | `2019-10-01` | Restituito anche da NER v 2.1  |
| Quantity | Numero | Numeri. | `2019-10-01` | Restituito anche da NER v 2.1 |
| Quantity | Percentuale | Percentuali.| `2019-10-01` | Restituito anche da NER v 2.1 |
| Quantity | Ordinale | Numeri ordinali. | `2019-10-01` | Restituito anche da NER v 2.1 |
| Quantity | Età | Età. | `2019-10-01` |  Restituito anche da NER v 2.1 |
| Quantity | Valuta | Valute. | `2019-10-01` | Restituito anche da NER v 2.1 |
| Quantity | Dimensione | Dimensioni e misurazioni. | `2019-10-01` | Restituito anche da NER v 2.1 |
| Quantity | Temperature | Temperature. | `2019-10-01` | Restituito anche da NER v 2.1 |
