---
title: Entità denominate per informazioni personali
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086879"
---
## <a name="personal-information-entity-types"></a>Tipi di entità di informazioni personali:

### <a name="person"></a>Persona
Riconoscere i nomi di persona nel testo.

Lingue:
* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                               | Disponibile a partire dalla versione del modello |
|--------------|-----------------------------------------------------------|----------------------------------------|
| N/D          | Nomi riconosciuti, ad esempio `Bill Gates`, `Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organization  

Riconosci organizzazioni, aziende, agenzie, aziende, Club e altri gruppi di persone.

Lingue: 

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                                                                       | Disponibile a partire dalla versione del modello|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| N/D          | organizzazioni, ad esempio `Microsoft`, `NASA``National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Numero di telefono

Numeri di telefono (solo numeri di telefono degli Stati Uniti). 

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                    | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------------|----------------------------------------|
| N/D          | Numeri di telefono degli Stati Uniti, ad esempio `(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Email

Indirizzo di posta elettronica. 

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                      | Disponibile a partire dalla versione del modello |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Indirizzo di posta elettronica, ad esempio `support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL

URL Internet.

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                                          | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------------------|----------------------------------------|
| N/D          | URL per siti Web, ad esempio `https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>Indirizzo IP

Indirizzo protocollo Internet

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                              | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------|----------------------------------------|
| N/D          | Indirizzo di rete, ad esempio `10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Quantità 

Quantità numeriche

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                   | Disponibile a partire dalla versione del modello |
|--------------|-------------------------------|----------------------------------------|
| Tempo trascorso          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Entità di data e ora

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                   | Disponibile a partire dalla versione del modello |
|--------------|-------------------------------|----------------------------------------|
| Data         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>Coordinate GPS UE

 Coordinate GPS per le località all'interno dell'Unione europea. 

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                               | Disponibile a partire dalla versione del modello |
|--------------|-------------------------------------------|----------------------------------------|
| N/D          | Coordinate GPS all'interno dell'Unione europea | `2019-10-01`                           |

### <a name="azure-information"></a>Informazioni su Azure

Informazioni su Azure identificabili, incluse le informazioni di autenticazione e le stringhe di connessione. 

* Disponibile a partire dalla versione del modello `2019-10-01`.

Lingue:

* Anteprima pubblica: `English`

| Nome sottotipo                          | Descrizione                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Chiave di autenticazione di Azure DocumentDB             | Chiave di autorizzazione per un server DocumentDB di Azure.                           |
| Stringa di connessione del database IAAS di Azure | Stringa di connessione per un database di infrastruttura distribuita come servizio (IaaS) di Azure. |
| Stringa di connessione SQL di Azure           | Stringa di connessione per un database SQL di Azure.                                |
| Stringa di connessione Azure Internet           | Stringa di connessione per Internet delle cose di Azure.                        |
| Password dell'impostazione di pubblicazione di Azure        | Password per le impostazioni di pubblicazione di Azure.                                        |
| Stringa di connessione della cache Redis di Azure   | Stringa di connessione per una cache di Azure per Redis.                             |
| SAS di Azure                             | Stringa di connessione per il software as a Service (SAS) di Azure.                     |
| Stringa di connessione del bus di servizio di Azure   | Stringa di connessione per un bus di servizio di Azure.                                |
| Chiave dell'account di archiviazione di Azure             | Chiave dell'account per un account di archiviazione di Azure.                                   |
| Chiave dell'account di archiviazione di Azure (generica)   | Chiave dell'account generico per un account di archiviazione di Azure.                           |
| Stringa di connessione SQL Server          | Stringa di connessione per SQL Server.                                         |

### <a name="identification"></a>Identificazione

* Disponibile a partire dalla versione del modello `2019-10-01`.

Lingue:

* Anteprima pubblica: `English`

#### <a name="financial-account-identification"></a>Identificazione account finanziario

| Nome sottotipo               | Descrizione                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Numeri di Routing ABA        | Numeri di routing di transito American Banker Association (ABA).                  |
| Codice SWIFT                 | Codici SWIFT per informazioni sulle istruzioni di pagamento.                           |
| Carta di credito                | Numeri di carta di credito.                                                       |
| Codice IBAN                  | Codici IBAN per informazioni sulle istruzioni di pagamento.                            |

#### <a name="government-and-country-specific-identification"></a>Identificazione di enti pubblici e paesi specifici

Le entità seguenti sono raggruppate ed elencate in base al paese:

Argentina
* Numero di identità nazionale (DNI)

Australia
* Numero di file di imposta 
* ID licenza del driver
* ID Passport
* Numero di account medico
* numeri di conto bancario (ad esempio, controllo, risparmi e account di debito)

Belgio
* Numero nazionale

Brasile
* Numero di entità legali (CNPJ)
* Numero CPF
* Scheda ID nazionale (RG)

Canada
* ID Passport
* ID licenza del driver
* Numero di assicurazioni sull'integrità
* Numero ID di integrità personale (PHIN)
* Numero di previdenza sociale
* numeri di conto bancario (ad esempio, controllo, risparmi e account di debito)

Cile
* Numero di carta di identità 

Cina
* Numero di carta di identità
* Numero della scheda ID residente (PRC)

Croazia
* Numero di scheda ID
* Numero ID personale (OIB)

Repubblica Ceca
* Numero di carta ID nazionale

Danimarca
* Numero ID personale

Unione europea (UE)
* Numero ID nazionale
* ID Passport
* ID licenza del driver
* Numero di previdenza sociale (SSN) o ID equivalente
* Numero di identificazione fiscale (TIN) UE
* Numero di carta di debito UE

Finlandia
* Numero ID nazionale
* ID Passport

France
* Scheda ID nazionale (CNI)
* Numero di previdenza sociale (INSEE)
* ID Passport
* ID licenza del driver

Germany
* Numero di scheda ID
* ID Passport
* ID licenza del driver

Grecia 
* Numero di carta ID nazionale

RAS di Hong Kong
* Numero scheda ID (HKID)

India
* Numero account permanente (PAN)
* Numero ID univoco (Aadhaar)

Indonesia
* Numero di carta ID (KTP)

Irlanda
* Numero di servizi pubblici personali (PPS)

Israele
* ID nazionale
* numeri di conto bancario (ad esempio, controllo, risparmi e account di debito)

Italia
* ID licenza del driver

Japan
* Numero di registrazione residente
* Numero di carta di residenza
* ID licenza del driver
* Numero di assicurazioni sociali (SIN)
* ID Passport
* numeri di conto bancario (ad esempio, controllo, risparmi e account di debito)

Malaysia
* Numero di scheda ID

Paesi Bassi
* Numero del servizio Citizen (del BSN)

Nuova Zelanda
* Numero di integrità del Ministero

Norvegia
* Numero di scheda ID

Filippine
* Numero ID multiuso unificato

Polonia
* Numero di scheda ID
* ID nazionale (PESEL)
* ID Passport

Portogallo 
* Numero di carta Citizen

Arabia Saudita
* ID nazionale

Singapore
* Numero NRIC (National Registration ID card)

Sudafrica
* Numero ID
* Numero di registrazione residente

Corea del Sud
* Numero di registrazione residente

Spagna 
* Codice fiscale

Svezia
* ID nazionale
* ID Passport

Taiwan 
* ID nazionale
* Numero di certificato residente (ARC/TARC Tax)
* ID Passport

Thailandia
* Codice di identificazione della popolazione

Regno Unito
* ID Passport
* ID licenza del driver
* Numero assicurativo nazionale (NINO)
* Numero di Servizio integrità nazionale (NHS)

Stati Uniti
* Codice fiscale
* ID licenza del driver
* ID Passport
* Numero di rollup elettorale
* Numero ID IVA singolo
* Numero DEA (Drug Enforcement Agency)
* numeri di conto bancario (ad esempio, controllo, risparmi e account di debito)
