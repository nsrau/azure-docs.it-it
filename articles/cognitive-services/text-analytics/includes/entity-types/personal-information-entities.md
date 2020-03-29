---
title: Entità denominate per Informazioni personali
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77086879"
---
## <a name="personal-information-entity-types"></a>Tipi di entità di informazioni personali:

### <a name="person"></a>Persona
Riconoscere i nomi delle persone nel testo.

Linguaggi:
* Anteprima pubblica:`English`

| Nome sottotipo | Descrizione                                               | Disponibile a partire dalla versione del modello |
|--------------|-----------------------------------------------------------|----------------------------------------|
| N/D          | Nomi riconosciuti, `Bill Gates`ad esempio ,`Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Organization  

Riconoscere organizzazioni, società, agenzie, aziende, club e altri gruppi di persone.

Linguaggi: 

* Anteprima pubblica:`English`

| Nome sottotipo | Descrizione                                                                                       | Disponibile a partire dalla versione del modello|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| N/D          | organizzazioni, ad `Microsoft` `NASA`esempio , ,`National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Numero di telefono

Numeri di telefono (solo numeri di telefono USA). 

Linguaggi:

* Anteprima pubblica:`English`

| Nome sottotipo | Descrizione                                    | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------------|----------------------------------------|
| N/D          | I numeri di telefono degli Stati Uniti, ad esempio`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>Email

Indirizzo di posta elettronica. 

Linguaggi:

* Anteprima pubblica:`English`

| Nome sottotipo | Descrizione                                      | Disponibile a partire dalla versione del modello |
|--------------|--------------------------------------------------|----------------------------------------|
| N/D          | Indirizzo e-mail, ad esempio`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL

URL Internet.

Linguaggi:

* Anteprima pubblica:`English`

| Nome sottotipo | Descrizione                                          | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------------------|----------------------------------------|
| N/D          | URL a siti Web, ad esempio`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>Indirizzo IP

Indirizzo protocollo Internet

Linguaggi:

* Anteprima pubblica:`English`

| Nome sottotipo | Descrizione                              | Disponibile a partire dalla versione del modello |
|--------------|------------------------------------------|----------------------------------------|
| N/D          | Indirizzo di rete, ad esempio`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Quantità 

Quantità numeriche

Linguaggi:

* Anteprima pubblica:`English`

| Nome sottotipo | Descrizione                   | Disponibile a partire dalla versione del modello |
|--------------|-------------------------------|----------------------------------------|
| Age          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>Datetime

Entità di data e ora

Linguaggi:

* Anteprima pubblica:`English`

| Nome sottotipo | Descrizione                   | Disponibile a partire dalla versione del modello |
|--------------|-------------------------------|----------------------------------------|
| Data         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>Coordinate GPS EU

 coordinate GPS per le posizioni all'interno dell'Unione Europea. 

Linguaggi:

* Anteprima pubblica:`English`

| Nome sottotipo | Descrizione                               | Disponibile a partire dalla versione del modello |
|--------------|-------------------------------------------|----------------------------------------|
| N/D          | Coordinate GPS all'interno dell'Unione europea | `2019-10-01`                           |

### <a name="azure-information"></a>Informazioni di AzureAzure information

Informazioni identificabili di Azure, incluse le informazioni di autenticazione e le stringhe di connessione. 

* Disponibile a partire `2019-10-01`dalla versione del modello .

Linguaggi:

* Anteprima pubblica:`English`

| Nome sottotipo                          | Descrizione                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Chiave di autenticazione di Azure DocumentDB             | Chiave di autorizzazione per un server Azure DocumentDB.Authorization key for an Azure DocumentDB server.                           |
| Azure IAAS Database Connection String | Stringa di connessione per un database di Azure Infrastructure as a Service (IaaS). |
| Stringa di connessione SQL di AzureAzure SQL Connection String           | Stringa di connessione per un database SQL di Azure.Connection string for an Azure SQL database.                                |
| Stringa di connessione di Azure IoT           | Stringa di connessione per Azure Internet of Things(IoT).                        |
| Password delle impostazioni di pubblicazione di Azure        | Password per le impostazioni di pubblicazione di Azure.Password for Azure Publish settings.                                        |
| Stringa di connessione di Cache Redis di Azure   | Stringa di connessione per una cache di Azure per Redis.Connection string for an Azure Cache for Redis.                             |
| Azure SAS                             | Stringa di connessione per il software di Azure come servizio.Connection string for Azure Software as a Service(SAS).                     |
| Stringa di connessione del bus di servizio di Azure   | Stringa di connessione per un bus di servizio di Azure.Connection string for an Azure service bus.                                |
| Chiave dell'account di archiviazione di AzureAzure Storage Account Key             | Chiave dell'account per un account di archiviazione di Azure.Account key for an Azure storage account.                                   |
| Chiave dell'account di archiviazione di Azure (generico)   | Chiave dell'account generico per un account di archiviazione di Azure.Generic account key for an Azure storage account.                           |
| Stringa di connessione di SQL Server          | Stringa di connessione per un server SQL.                                         |

### <a name="identification"></a>Identificazione

* Disponibile a partire `2019-10-01`dalla versione del modello .

Linguaggi:

* Anteprima pubblica:`English`

#### <a name="financial-account-identification"></a>Identificazione del conto finanziario

| Nome sottotipo               | Descrizione                                                                |
|----------------------------|----------------------------------------------------------------------------|
| Numeri di instradamento ABA        | Numeri di instradamento di transito dell'American Banker Association(ABA).                  |
| Codice SWIFT                 | Codici SWIFT per le informazioni sulle istruzioni di pagamento.                           |
| Carta di credito                | Numeri di carta di credito.                                                       |
| Codice IBAN                  | Codici IBAN per le informazioni sulle istruzioni di pagamento.                            |

#### <a name="government-and-country-specific-identification"></a>Identificazione specifica del governo e del paese

Le entità riportate di seguito sono raggruppate ed elencate per paese:

Argentina
* Numero di identità nazionale (DNI)

Australia
* Numero del file fiscale 
* ID patente
* ID passaporto
* Numero di conto medico
* numeri di conto bancario (ad esempio assi correnti, conti di risparmio e di debito)

Belgio
* Numero nazionale

Brasile
* Numero persona giuridica (CNPJ)
* Numero CPF
* Carta d'identità nazionale (RG)

Canada
* ID passaporto
* ID patente
* Assicurazione malattia Numero
* Numero ID salute personale (PHIN)
* Numero di previdenza sociale
* numeri di conto bancario (ad esempio assi correnti, conti di risparmio e di debito)

Cile
* Numero di carta d'identità 

Cina
* Numero di carta d'identità
* Numero di carta d'identità residente (PRC)

Croazia
* Numero di carta d'identità
* Numero ID personale (OIB)

Repubblica ceca
* Numero di carta d'identità nazionale

Danimarca
* Numero di identificazione personale

Unione europea (UE)
* Numero di identificazione nazionale
* ID passaporto
* ID patente
* Numero di previdenza sociale (SSN) o ID equivalente
* Numero identificativo contribuente EU
* Numero di carta di debito UE

Finlandia
* Numero di identificazione nazionale
* ID passaporto

Francia
* Carta d'identità nazionale (CNI)
* Numero di previdenza sociale (INSEE)
* ID passaporto
* ID patente

Germania
* Numero carta d'identità
* ID passaporto
* ID patente

Grecia 
* Numero di carta d'identità nazionale

RAS di Hong Kong
* Numero di carta d'identità (HKID)

India
* Numero di conto permanente (PAN)
* Numero ID univoco (Aadhaar)

Indonesia
* Numero di carta d'identità (KTP)

Irlanda
* Numero ppS (Personal Public Service)

Israele
* Carta d'identità nazionale
* numeri di conto bancario (ad esempio assi correnti, conti di risparmio e di debito)

Italia
* ID patente

Giappone
* Numero di registrazione del residente
* Numero di carta di soggiorno
* ID patente
* Numero di previdenza sociale (SIN)
* ID passaporto
* numeri di conto bancario (ad esempio assi correnti, conti di risparmio e di debito)

Malaysia
* Numero di carta d'identità

Paesi Bassi
* Numero del servizio del cittadino (BSN)

Nuova Zelanda
* Numero del Ministero della Salute

Norvegia
* Numero di carta d'identità

Filippine
* Numero ID multiuso unificato

Polonia
* Numero carta d'identità
* ID nazionale (PESEL)
* ID passaporto

Portogallo 
* Numero di carta cittadino

Arabia Saudita
* Carta d'identità nazionale

Singapore
* Numero di carta d'identità di registrazione nazionale (NRIC)

Sud Africa
* Numero ID
* Numero di registrazione residente

Corea del Sud
* Numero di registrazione residente

Spagna 
* Codice fiscale

Svezia
* Carta d'identità nazionale
* ID passaporto

Taiwan 
* Carta d'identità nazionale
* Numero di certificato residente (ARC/TARC)
* ID passaporto

Thailandia
* Codice di identificazione della popolazione

Regno Unito
* ID passaporto
* ID patente
* Numero di assicurazione nazionale (NINO)
* Numero del Servizio Sanitario Nazionale (NHS)

Stati Uniti
* Codice fiscale
* ID patente
* ID passaporto
* Numero della lista elettorale
* Numero d'imposta individuale (ITIN)
* Numero DEA (Drug Enforcement Agency)
* numeri di conto bancario (ad esempio assi correnti, conti di risparmio e di debito)
