---
title: Entità denominate per informazioni personali
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 2349c92fb74b546eaa929752f3d2343b9c97e6d1
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88011004"
---
> [!NOTE]
> Per rilevare `PHI` (informazioni sull'integrità protetta), usare il `domain=phi` parametro e la versione del modello `2020-04-01` o versioni successive.
>
> Ad esempio: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
Quando si inviano richieste all'endpoint, vengono restituite le categorie di entità seguenti `/v3.1-preview.1/entities/recognition/pii` .

| Categoria   | Subcategory | Descrizione                          | Versione modello di avvio | Note |
|------------|-------------|--------------------------------------|------------------------|---|
| Persona     | N/D         | Nomi delle persone.  | `2019-10-01`  | Restituito anche con `domain=phi` . |
| PersonType | N/D         | Tipi di processo o ruoli contenuti da una persona. | `2020-02-01` | |
| PhoneNumber | N/D | Numeri di telefono (solo numeri di telefono US e UE). | `2019-10-01` | Restituito anche con`domain=phi` |
|Organizzazione  | N/D | Società, gruppi politici, bande musicali, sport clubs, enti governativi e organizzazioni pubbliche.  | `2019-10-01` | Le nazionalità e le religioni non sono incluse in questo tipo di entità.  |
|Organizzazione | Medicina | Società e gruppi medicali. | `2020-04-01` |  |
|Organizzazione | Scambio scorte | Gruppi di scambio azionario. | `2020-04-01` |  |
| Organizzazione | Sport | Organizzazioni correlate allo sport. | `2020-04-01` |  |
| Indirizzo | N/D | Indirizzi di posta elettronica completi.  | `2020-04-01` | Restituito anche con `domain=phi` . |
| Coordinate GPS EU | N/D | Coordinate GPS per le località all'interno dell'Unione europea.  | `2019-10-01` |  |
| E-mail | N/D | Indirizzi di posta elettronica. | `2019-10-01` | Restituito anche con `domain=phi` .   |
| URL | N/D | URL per siti Web. | `2019-10-01` | Restituito anche con `domain=phi` . |
| IP | N/D | Indirizzi IP di rete. | `2019-10-01` | Restituito anche con `domain=phi` . |
| Datetime | N/D | Date e ore del giorno. | `2019-10-01` |  | 
| Datetime | Data | Date calendario. | `2019-10-01` | Restituito anche con `domain=phi` . |
| Quantità | N/D | Numeri e quantità numeriche. | `2019-10-01` |  |
| Quantità | Età | Età. | `2019-10-01` | | |

## <a name="azure-information"></a>Informazioni su Azure

Questa categoria di entità include informazioni di Azure identificabili, incluse le informazioni di autenticazione e le stringhe di connessione. Disponibile a partire dalla versione del modello `2019-10-01` . Non restituito con il `domain=phi` parametro.

| Subcategory                           | Descrizione                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Chiave di autenticazione di Azure DocumentDB             | Chiave di autorizzazione per un server DocumentDB di Azure.                           |
| Stringa di connessione del database IaaS di Azure e stringa di connessione di SQL di Azure | Stringa di connessione per un database di infrastruttura distribuita come servizio (IaaS) di Azure e una stringa di connessione SQL. |
| Stringa di connessione SQL di Azure           | Stringa di connessione per un database nel database SQL di Azure.                                |
| Stringa di connessione di Azure IoT           | Stringa di connessione per Internet delle cose di Azure.                        |
| Password delle impostazioni di pubblicazione di Azure        | Password per le impostazioni di pubblicazione di Azure.                                        |
| Stringa di connessione di Cache Redis di Azure   | Stringa di connessione per una cache di Azure per Redis.                             |
| Azure SAS                             | Stringa di connessione per il software as a Service (SAS) di Azure.                     |
| Stringa di connessione del bus di servizio di Azure   | Stringa di connessione per un bus di servizio di Azure.                                 |
| Chiave dell'account di archiviazione di Azure             | Chiave dell'account per un account di archiviazione di Azure.                                   |
| Chiave dell'account di archiviazione di Azure (generico)   | Chiave dell'account generico per un account di archiviazione di Azure.                           |
| Stringa di connessione di SQL Server          | Stringa di connessione per SQL Server.                                         |

## <a name="identification"></a>Identificazione

[!INCLUDE [supported identification entities](./identification-entities.md)]
