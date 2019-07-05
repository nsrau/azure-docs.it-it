---
title: Comprendere i termini nel foglio di prezzo per un contratto del cliente di Microsoft - Azure
description: Informazioni su come leggere e comprendere l'utilizzo e la fattura per un contratto di clienti Microsoft.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4d83228fbec395d604e5ce3f988d2a6157f21eed
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490654"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Condizioni per il foglio di prezzo di contratto di Microsoft dal cliente

Questo articolo si applica a un account di fatturazione di Azure per un contratto di clienti Microsoft. [Controllare se si ha accesso a un contratto di Microsoft dal cliente](#check-access-to-a-microsoft-customer-agreement).

Se sei un profilo di fatturazione proprietario, collaboratore, lettore o fattura Manager è possibile scaricare l'elenco prezzi dell'organizzazione dal portale di Azure. Visualizzare [visualizzazione e download dei prezzi di organizzazione del](billing-ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Termini e le descrizioni nell'elenco prezzi

La sezione seguente descrive i termini più importanti presenti nel foglio di prezzo di contratto di Microsoft dal cliente.

| **Nome campo**   | **Descrizione**   |
| --- | --- |
| billingAccountId  | Identificatore univoco per l'account di fatturazione.   |
| billingAccountName  | Nome dell'account di fatturazione.  |
| billingProfileId  | Identificatore univoco per il profilo di fatturazione.   |
| billingProfileName  | Nome del profilo di fatturazione è configurato per ricevere le fatture. I prezzi riportati nell'elenco prezzi sono associati a questo profilo di fatturazione. |
| productOrderName  | Nome del piano di un prodotto acquistato. |
| serviceFamily  | Tipo di servizio di Azure. Ad esempio: Sicurezza delle risorse di calcolo, Analitica, |
| Prodotto  | Nome del prodotto i costi maturati. Ad esempio: Visual Studio database SQL Basic Standard SQL DB  |
| productId  | Identificatore univoco per il prodotto viene utilizzata la cui misurazione. |
| unitOfMeasure  | Identifica le unità di misura per la fatturazione per il servizio. Ad esempio, i servizi di calcolo vengono fatturati per ora. |
| meterId  | Identificatore univoco per il contatore. |
| meterName  | Nome del contatore. La misurazione rappresenta la risorsa distribuibile di un servizio di Azure. |
| meterCategory  | Nome della categoria di classificazione per il contatore. Ad esempio, _servizi Cloud_, _Networking_e così via. |
| meterType  |  Nome del tipo di misuratore. |
| meterSubCategory  | Nome della categoria sottoclassificazione del misuratore.  |
| meterRegion  | Nome dell'area in cui il contatore per il servizio è disponibile. Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa.    |
| tierId  | Identifica il piano tariffario quando applicabile. Questo avviene insieme con tierMinimumUnits per l'impostazione dei prezzi a livelli quando i prezzi variano in base al numero di unità usate.    |
| tierMinimumUnits  | Definisce il limite inferiore dell'intervallo di livello per il quale sono definiti i prezzi. Ad esempio, se l'intervallo è 0 e 100, tierMinimumUnits sarà 0.  |
| effectiveStartDate  | Avviare data in cui il prezzo diventa effettivo. |
| effectiveEndDate  | Data di fine del prezzo effettivo. |
| unitPrice  | Prezzo per unità al momento della fatturazione (non il combinata prezzo effettivo) più preciso per un misuratore e ordine nome di prodotto.  Note: Il prezzo unitario non è lo stesso mentre il prezzo effettivo nei dettagli utilizzo scaricato nel caso dei servizi con i prezzi di backup differenziali tra i livelli.  Nel caso dei servizi a più livelli piano tariffario, il prezzo effettivo prevede una tariffa combinata tra i livelli e non viene visualizzato un prezzo unitario livello specifico. Il prezzo combinata o prezzo effettivo è il prezzo netto per la quantità consumata che si estende ai diversi livelli (in cui ogni livello ha un prezzo unitario specifico). |
| basePrice  | Il prezzo di mercato al momento il cliente si disconnette o il prezzo di mercato al momento il misuratore del servizio viene avviato se è dopo sign-on.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un contratto di Microsoft dal cliente
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare e scaricare i prezzi dell'organizzazione](billing-ea-pricing.md)
