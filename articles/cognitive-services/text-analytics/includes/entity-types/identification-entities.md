---
title: Entità di identificazione
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f07b71bf8996612798b87d32a21a15ec72db0b32
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140931"
---
Questa categoria di entità include informazioni finanziarie e forme ufficiali di identificazione. Disponibile a partire dalla versione del modello `2019-10-01` . I sottotipi sono elencati di seguito. 

### <a name="financial-account-identification"></a>Identificazione account finanziario

| Nome sottotipo               | Descrizione                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA Routing Number (Codice ABA)        | Numeri di routing di transito American Banker Association (ABA).                  |
| Codice SWIFT                 | Codici SWIFT per informazioni sulle istruzioni di pagamento.                           |
| Carta di credito                | Numeri di carta di credito.                                                       |
| International Banking Account Number (IBAN)                  | Codici IBAN per informazioni sulle istruzioni di pagamento.                            |


### <a name="government-and-countryregion-specific-identification"></a>Identificazione specifica per enti pubblici e paesi/aree geografiche

> [!NOTE]
> Le entità finanziarie e specifiche del paese seguenti non vengono restituite con il `domain=phi` parametro:
> * Numeri di passaporto
> * ID imposta

Le entità seguenti sono raggruppate ed elencate in base al paese:

Argentina
* Numero di identità nazionale Argentina (DNI)

Australia
* Numero Passport Australia
* Numero di file fiscali Australia
* Numero di licenza del driver Australia
* Numero di account medico dell'Australia
* Numero di conto bancario in Australia

Belgio
* Numero nazionale Belgio

Brasile 
* Numero di entità legali in Brasile (CNPJ)
* Numero CPF Brasile
* Scheda ID nazionale (RG) del Brasile

Canada
* Numero di assicurazioni sociali Canada
* Numero di licenza del driver Canada
* Numero conto bancario del Canada
* Numero passaporto Canada
* Numero di identificazione dell'integrità personale del Canada (PHIN)
* Numero Servizio integrità Canada

Cile
* Numero di carta di identità 

Cina
* Numero di carta di identità residente Cina (PRC)

Croazia
* Numero di carta identità Croazia
* Numero di identificazione personale della Croazia (OIB)

Repubblica Ceca
* Numero di identità personale ceco

Danimarca
* Numero di identificazione personale Danimarca

Unione europea (UE)
* Numero documento di identità EU
* Numero di passaporto EU
* Numero di patente di guida EU
* Codice fiscale (CF) EU o ID equivalente
* Numero identificativo contribuente EU
* Numero di carta di debito UE

Finlandia
* ID nazionale Finlandia
* Numero di passaporto Finlandia

Francia
* Scheda ID nazionale Francia (CNI)
* Numero di previdenza sociale in Francia (INSEE)
* Numero di passaporto Francia
* Numero di licenza del driver Francia

Germania
* Numero di carta di identità Germania
* Numero passaporto tedesco
* Numero di licenza del driver tedesco

Grecia 
* Numero di carta ID nazionale Grecia

RAS di Hong Kong
* Numero della carta di identità di Hong Kong (HKID)

India
* Numero di account permanente India (PAN)
* Numero di identificazione univoca India (Aadhaar)

Indonesia
* Numero KTP (Indonesia Identity Card)

Irlanda
* Numero di servizio pubblico personale (PPS) dell'Irlanda

Israele
* ID nazionale Israel
* Numero conto di Israele Bank

Italia
* ID licenza del driver Italia

Giappone
* Numero di registrazione residente in Giappone
* Numero di tessera residenza giapponese
* Numero di licenza del driver giapponese
* Numero di assicurazioni sociali (SIN)
* Numero passaporto Giappone
* Numero del conto bancario in Giappone

Malaysia
* Numero di carta di identità Malaysia

Paesi Bassi
* Numero del servizio Citizen (del BSN) dei Paesi Bassi

Nuova Zelanda
* Ministero della Nuova Zelanda per il numero di integrità

Norvegia
* Numero di identità norvegese

Filippine
* Numero di ID multifunzione unificato per le Filippine

Polonia
* Scheda identità della Polonia
* ID nazionale Polonia (PESEL)
* Passaporto Polonia

Portogallo 
* Numero di carta Citizen del Portogallo

Arabia Saudita
* ID nazionale Arabia Saudita

Singapore
* Numero NRIC (National Registration ID card) di Singapore

Sudafrica
* Numero di identificazione sudafricano

Corea del Sud
* Numero di registrazione residente Corea del sud

Spagna 
* Codice fiscale Spagna (SSN)

Svezia
* ID nazionale svedese
* Numero passaporto Svezia

Taiwan 
* ID nazionale taiwanese
* Certificato residente Taiwan (ARC/TARC Tax)
* Numero passaporto Taiwan

Thailandia
* Codice di identificazione della popolazione tailandese

Regno Unito
* ID Passport
* Regno Unito. Numero di patente del driver
* Regno Unito. Numero assicurativo nazionale (NINO)
* Regno Unito. Numero Servizio integrità nazionale
* Regno Unito. Numero di rollup elettorale
* STATI UNITI/REGNO UNITO Numero passaporto

Stati Uniti
* Numero di previdenza sociale (SSN) degli Stati Uniti
* Numero di licenza del driver U.S.
* STATI UNITI/REGNO UNITO Numero passaporto
* Numero di identificazione dei singoli contribuenti statunitensi
* Numero DEA (Drug Enforcement Agency)
* Numero di conto bancario statunitense
