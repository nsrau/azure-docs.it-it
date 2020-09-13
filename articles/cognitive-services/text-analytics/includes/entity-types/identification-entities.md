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
ms.openlocfilehash: 8e0798f75aaa79031ca7cc03814282daa049fbfe
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662935"
---
Questa categoria di entità include informazioni finanziarie e forme ufficiali di identificazione. Disponibile a partire dalla versione del modello `2019-10-01` . I sottotipi sono elencati di seguito. 

### <a name="financial-account-identification"></a>Identificazione account finanziario

| Nome sottotipo               | Descrizione                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA Routing Number (Codice ABA)        | Numeri di routing di transito American Banker Association (ABA).                  |
| Codice SWIFT                 | Codici SWIFT per informazioni sulle istruzioni di pagamento.                           |
| Carta di credito                | Numeri di carta di credito.                                                       |
| International Banking Account Number (IBAN)                  | Codici IBAN per informazioni sulle istruzioni di pagamento.                            |


### <a name="government-and-countryregion-specific-identification"></a>Identificazione per enti pubblici e paesi/aree geografiche

> [!NOTE]
> Le entità finanziarie e specifiche del paese seguenti non vengono restituite con il `domain=phi` parametro:
> * Numeri di passaporto
> * ID imposta

Le entità seguenti sono raggruppate ed elencate in base al paese:

Argentina
* Numero di identità nazionale Argentina (DNI)

Austria
* Scheda identità austriaca
* Numero di identificazione fiscale austriaco
* Numero di imposte IVA aggiunto al valore austriaco

Australia
* Numero di conto bancario in Australia
* Numero aziendale australiano
* Numero società australiana
* Numero di licenza del driver Australia
* Numero di account medico dell'Australia
* Numero Passport Australia
* Numero di file fiscali Australia

Belgio
* Numero nazionale Belgio
* Numero di imposte aggiunto al valore Belgium

Brasile 
* Numero di entità legali in Brasile (CNPJ)
* Numero CPF Brasile
* Scheda ID nazionale (RG) del Brasile

Bulgaria
* Numero civile uniforme bulgaro

Canada
* Numero conto bancario del Canada
* Numero di licenza del driver Canada
* Numero Servizio integrità Canada
* Numero passaporto Canada
* Numero di identificazione dell'integrità personale del Canada (PHIN)
* Numero di assicurazioni sociali Canada

Cile
* Numero di carta di identità 

Cina
* Numero di carta di identità residente Cina (PRC)

Croazia
* Numero di carta identità Croazia
* Numero di carta ID nazionale Croazia
* Numero di identificazione personale della Croazia (OIB)

Cipro
* Numero di carta identità Cipro
* Numero di identificazione fiscale Cipro

Repubblica Ceca
* Numero di identità personale ceco

Danimarca
* Numero di identificazione personale Danimarca

Estonia
* Codice di identificazione personale Estonia

Unione europea (UE)
* Numero di carta di debito UE
* Numero di patente di guida EU
* Numero documento di identità EU
* Numero di passaporto EU
* Codice fiscale (CF) EU o ID equivalente
* Numero identificativo contribuente EU

Finlandia
* Numero di assicurazioni sull'integrità europea finlandese
* ID nazionale Finlandia
* Numero di passaporto Finlandia

Francia
* Numero di licenza del driver Francia
* Numero di assicurazioni di integrità Francia
* Scheda ID nazionale Francia (CNI)
* Numero di passaporto Francia
* Numero di previdenza sociale in Francia (INSEE)
* Numero di identificazione fiscale Francia (numéro SPI)
* Numero di imposta aggiunto al valore Francia

Germania
* Numero di licenza del driver tedesco
* Numero di carta di identità Germania
* Numero passaporto tedesco
* Numero di identificazione fiscale Germania
* Numero di imposta in Germania aggiunto al valore

Grecia 
* Numero di carta ID nazionale Grecia
* Numero di identificazione fiscale Grecia

RAS di Hong Kong
* Numero della carta di identità di Hong Kong (HKID)

Ungheria
* Numero di identificazione nazionale Ungheria
* Numero di identificazione fiscale Ungheria
* Numero di imposta aggiunto al valore Hungary

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
* Codice fiscale Italia
* Numero di imposta aggiunto al valore Italia

Giappone
* Numero del conto bancario in Giappone
* Numero di licenza del driver giapponese
* Personale del mio numero giapponese
* Il mio numero giapponese aziendale
* Numero di registrazione residente in Giappone
* Numero di tessera residenza giapponese
* Numero di assicurazioni sociali Giappone (SIN)
* Numero passaporto Giappone

Lettonia
* Codice personale della Lettonia

Lituania
* Codice personale della Lituania

Lussemburgo
* Numero di identificazione nazionale lussemburghese (persone naturali)
* Numero di identificazione nazionale lussemburghese (persone non naturali)

Malaysia
* Numero di carta di identità Malaysia

Malta
* Numero di carta di identità Malta
* Numero di identificazione fiscale Malta

Paesi Bassi
* Numero del servizio Citizen (del BSN) dei Paesi Bassi
* Numero di identificazione fiscale Paesi Bassi
* Numero di imposta aggiunto al valore olandese

Nuova Zelanda
* Numero di conto bancario Nuova Zelanda
* Numero di patente del driver neozelandese
* Numero di ricavi Inland Nuova Zelanda
* Ministero della Nuova Zelanda per il numero di integrità
* Numero di previdenza sociale in Nuova Zelanda

Norvegia
* Numero di identità norvegese

Filippine
* Numero di ID multifunzione unificato per le Filippine

Polonia
* Scheda identità della Polonia
* ID nazionale Polonia (PESEL)
* Numero passaporto Polonia
* Numero di Polonia REGOn
* Numero di identificazione fiscale Polonia

Portogallo 
* Numero di carta Citizen del Portogallo
* Numero di identificazione fiscale Portugal

Romania
* Codice numerico personale della Romania (CNP)

Russia
* Numero di passaporto russo (nazionale)
* Numero di passaporto russo (internazionale)

Arabia Saudita
* ID nazionale Arabia Saudita

Singapore
* Numero NRIC (National Registration ID card) di Singapore

Slovacchia 
* Numero personale Slovakia

Slovenia
* Numero di identificazione fiscale Slovenia
* Numero di Citizen Master univoco Slovenia

Sudafrica
* Numero di identificazione sudafricano

Corea del Sud
* Numero di registrazione residente Corea del sud

Spagna 
* DNI Spagna
* Codice fiscale Spagna (SSN)
* Numero di identificazione fiscale Spagna

Svezia
* ID nazionale svedese
* Numero passaporto Svezia
* Numero di identificazione fiscale Sweden

Svizzera
* Numero di previdenza sociale Svizzera AVS

Taiwan 
* ID nazionale taiwanese
* Certificato residente Taiwan (ARC/TARC Tax)
* Numero passaporto Taiwan

Thailandia
* Codice di identificazione della popolazione tailandese

Turchia
* Numero di identificazione nazionale turca

Ucraina
* Numero passaporto ucraino (nazionale)
* Numero passaporto ucraino (internazionale)

Regno Unito
* Regno Unito. Numero di patente del driver
* Regno Unito. Numero di rollup elettorale
* Regno Unito. Numero di Servizio integrità nazionale (NHS)
* Regno Unito. Numero assicurativo nazionale (NINO)
* Regno Unito. Numero passaporto
* Regno Unito. Numero di riferimento del contribuente univoco

Stati Uniti
* Numero di previdenza sociale (SSN) degli Stati Uniti
* Numero di licenza del driver U.S.
* Numero passaporto statunitense
* Numero di identificazione dei singoli contribuenti statunitensi
* Numero DEA (Drug Enforcement Agency) degli Stati Uniti
* Numero di conto bancario statunitense
