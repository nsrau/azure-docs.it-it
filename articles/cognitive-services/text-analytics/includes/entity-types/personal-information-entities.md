---
title: Entità denominate per informazioni personali
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: 3aa4da9a9cf3d1d4b664e81f1fd18f2b225d731d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799937"
---
## <a name="personal-information-entity-types"></a>Tipi di entità di informazioni personali:

### <a name="phone-number"></a>Numero di telefono

Numeri di telefono. 

Linguaggi:

* Anteprima pubblica: `English`

| Nome sottotipo           | Descrizione                                           |
|------------------------|-------------------------------------------------------|
| N/D                    | Numeri di telefono, ad esempio `+1 123-123-123`.          |
| Numero di telefono dell'Unione europea        | Numeri di telefono specifici dell'Unione europea.         |
| Numero di telefono cellulare dell'UE | Numeri di telefono cellulare specifici dell'Unione europea. |

### <a name="eu-gps-coordinates"></a>Coordinate GPS UE

 Coordinate GPS per le località all'interno dell'Unione europea. 

Linguaggi:

* Anteprima pubblica: `English`

| Nome sottotipo | Descrizione                               |
|--------------|-------------------------------------------|
| N/D          | Coordinate GPS all'interno dell'Unione europea |

### <a name="azure-information"></a>Informazioni su Azure

Informazioni su Azure identificabili, incluse le informazioni di autenticazione e le stringhe di connessione. 

Linguaggi:

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

Linguaggi:

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
* Codice fiscale 
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

Repubblica ceca
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

Francia
* Scheda ID nazionale (CNI)
* Numero di previdenza sociale (INSEE)
* ID Passport
* ID licenza del driver

Germania
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

Giappone
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

Sud Africa
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
