---
title: API per clienti Enterprise per la fatturazione di Azure | Microsoft Docs
description: Informazioni sulle API di creazione di report che consentono ai clienti Enterprise di Azure di estrarre i dati sull'uso a livello di codice.
services: ''
documentationcenter: ''
author: mumami
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: erikre
ms.openlocfilehash: 52612419599ef69e7476c660b52f9e6e36946825
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615564"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>Panoramica delle API di creazione di report per i clienti Enterprise
Le API di creazione di report consentono ai clienti Enterprise di Azure di estrarre i dati di fatturazione e sull'uso a livello di codice per inserirli negli strumenti di analisi preferiti. I clienti Enterprise hanno sottoscritto un [Contratto Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) con Azure per concludere impegni monetari negoziati e ottenere l'accesso alla determinazione dei prezzi personalizzata per le risorse di Azure.

## <a name="enabling-data-access-to-the-api"></a>Abilitazione dell'API per l'accesso ai dati
* **Generare o recuperare la chiave API**: accedere a Enterprise Portal e passare a Report > Scarica utilizzo > API Access Key (Chiave di accesso API) per generare o recuperare la chiave API.
* **Passare le chiavi nell'API** - La chiave API deve essere passata per ogni chiamata per l'autenticazione e l'autorizzazione. La proprietà seguente deve essere passata alle intestazioni HTTP:

|Chiave intestazione necessaria | Value|
|-|-|
|Authorization| Specificare il valore nel formato: **bearer {API_KEY}** <br/> Esempio: bearer eyr....09| 

## <a name="consumption-apis"></a>API per l'uso
Per le API descritte di seguito, [qui](https://consumption.azure.com/swagger/ui/index) è disponibile un endpoint Swagger che deve consentire una facile analisi dell'API e la possibilità di generare SDK client tramite [AutoRest](https://github.com/Azure/AutoRest) o [Swagger CodeGen](https://swagger.io/swagger-codegen/). I dati a partire dal 1° maggio 2014 sono disponibili tramite questa API. 

* **Saldi e riepilogo** - L'[API per saldi e riepilogo](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) offre un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per il servizio Azure Marketplace e spese per modifiche e da pagare in eccedenza.

* **Dettagli sull'uso** - L'[API per dettagli sull'uso](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) offre un'analisi giornaliera dettagliata delle quantità usate e delle spese stimate in relazione a una registrazione. Il risultato include anche informazioni su istanze, contatori e reparti. Le query sull'API possono essere eseguite in base al periodo di fatturazione oppure in base a un intervallo definito da date di inizio e di fine specificate. 

* **Spese per Marketplace Store** - L'[API per le spese in Marketplace Store](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) restituisce le spese giornaliere dettagliate in base all'uso correlate al Marketplace per il periodo di fatturazione specificato o per le date di inizio e fine indicate (le spese una tantum non sono incluse).

* **Elenco prezzi** - L'[API elenco prezzi](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) offre la tariffa applicabile per ogni contatore per la registrazione e il periodo di fatturazione specificati. 

## <a name="data-freshness"></a>Aggiornamento dei dati
Vengono restituiti ETag in risposta a tutte le API indicate in precedenza. Una modifica di ETag indica che i dati sono stati aggiornati.  Nelle chiamate successive alla stessa API tramite gli stessi parametri passare l'ETag acquisito con la chiave "If-None-Match" nell'intestazione della richiesta http. Il codice di stato della risposta sarà "NotModified" se i dati non sono stati aggiornati ulteriormente e non verrà restituito alcun dato. L'API restituirà il set di dati completo per il periodo richiesto a ogni modifica di ETag.

## <a name="helper-apis"></a>API di supporto
 **Elenco periodi di fatturazione** - L'[API per periodi di fatturazione](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) restituisce un elenco di periodi di fatturazione contenente i dati sull'uso per la registrazione specificata in ordine cronologico inverso. Ogni periodo contiene una proprietà che punta alla route API per i quattro set di dati, ovvero BalanceSummary, UsageDetails, MarketplaceCharges e PriceSheet.


## <a name="api-response-codes"></a>Codici di risposta dell'API   
|Codice di stato della risposta|Message|DESCRIZIONE|
|-|-|-|
|200| OK|Nessun errore|
|401| Non autorizzata| Chiave API non trovata, non valida, scaduta e così via|
|404| Non disponibile| Endpoint del report non trovato|
|400| Bad Request| Parametri non validi (intervalli di date, numeri EA e così via)|
|500| Errore del server| Errore imprevisto nell'elaborazione della richiesta| 









