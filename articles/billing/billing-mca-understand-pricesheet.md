---
title: Informazioni sui termini nell'elenco prezzi per un Contratto del cliente Microsoft - Azure
description: Informazioni su come leggere e comprendere l'utilizzo e la fattura per un Contratto del cliente Microsoft.
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
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490654"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Termini presenti nell'elenco prezzi del Contratto del cliente Microsoft

Questo articolo si applica a un account di fatturazione di Azure per un Contratto del cliente Microsoft. [Verificare di avere accesso a un Contratto del cliente Microsoft](#check-access-to-a-microsoft-customer-agreement).

Se si ha il ruolo di proprietario, collaboratore, lettore o gestione fatture del profilo di fatturazione, è possibile scaricare l'elenco prezzi dell'organizzazione dal portale di Azure. Vedere [Visualizzare e scaricare i prezzi dell'organizzazione](billing-ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Termini e descrizioni nell'elenco prezzi

La sezione seguente descrive i termini importanti visualizzati nell'elenco prezzi del Contratto del cliente Microsoft.

| **Nome campo**   | **Descrizione**   |
| --- | --- |
| billingAccountId  | Identificatore univoco per l'account di fatturazione.   |
| billingAccountName  | Nome dell'account di fatturazione.  |
| billingProfileId  | Identificatore univoco per il profilo di fatturazione.   |
| billingProfileName  | Nome del profilo di fatturazione configurato per la ricezione delle fatture. I prezzi nell'elenco prezzi sono associati a questo profilo di fatturazione. |
| productOrderName  | Nome del piano del prodotto acquistato. |
| serviceFamily  | Tipo di servizio di Azure. Ad esempio: calcolo, analisi, sicurezza |
| Prodotto  | Nome del prodotto che determina un incremento dei costi. Ad esempio: database SQL di base e database SQL standard  |
| productId  | Identificatore univoco per il prodotto di cui viene utilizzato il contatore. |
| unitOfMeasure  | Identifica l'unità di misura per la fatturazione del servizio. I servizi di calcolo, ad esempio, vengono fatturati all'ora. |
| meterId  | Identificatore univoco del contatore. |
| meterName  | Nome del contatore. Il contatore rappresenta la risorsa distribuibile di un servizio di Azure. |
| meterCategory  | Nome della categoria di classificazione per il contatore. Ad esempio, _Servizi cloud_, _Rete_, ecc. |
| meterType  |  Nome del tipo di contatore. |
| meterSubCategory  | Nome della categoria di sottoclassificazione del contatore.  |
| meterRegion  | Nome dell'area in cui è disponibile il contatore per il servizio. Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa.    |
| tierId  | Identifica il piano tariffario quando applicabile. Questo valore viene usato insieme a tierMinimumUnits per l'impostazione dei prezzi a livelli quando i prezzi variano in base al numero di unità utilizzate.    |
| tierMinimumUnits  | Definisce il limite minimo dell'intervallo di livelli per il quale vengono definiti i prezzi. Se, ad esempio, l'intervallo è compreso tra 0 e 100, tierMinimumUnits sarà 0.  |
| effectiveStartDate  | Data di inizio in cui il prezzo diventa effettivo. |
| effectiveEndDate  | Data di fine del prezzo effettivo. |
| unitPrice  | Prezzo per unità al momento della fatturazione (non il prezzo effettivo combinato) in base a un contatore e un nome dell'ordine del prodotto.  Note: Il prezzo unitario non è uguale al prezzo effettivo nei download dei dettagli di utilizzo in caso di servizi con prezzi differenziati tra i livelli.  Nel caso di servizi con prezzi per più livelli, il prezzo effettivo è una tariffa combinata tra i livelli e non mostra un prezzo unitario specifico del livello. Il prezzo combinato o il prezzo effettivo è il prezzo netto per la quantità utilizzata che si estende tra più livelli (dove ogni livello ha un prezzo unitario specifico). |
| basePrice  | Prezzo di mercato al momento dell'accesso del cliente o prezzo del mercato al momento dell'avvio del contatore di servizi, se è dopo l'accesso.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificare l'accesso a un Contratto del cliente Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare e scaricare i prezzi dell'organizzazione](billing-ea-pricing.md)
