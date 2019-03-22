---
title: Eseguire la migrazione dal contratto Enterprise a Microsoft dal cliente contratto API - Azure | Microsoft Docs
description: Questo articolo aiuta a comprendere le conseguenze della migrazione di un Microsoft Enterprise Agreement (EA) per un contratto di clienti Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 9f5ea30c458954a3e87b9575b5632fbfb0df97b8
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318600"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Eseguire la migrazione dal contratto Enterprise a API di contratto dei clienti di Microsoft

Questo articolo aiuta a comprendere la struttura di data, API e altre differenze di integrazione system tra account Enterprise Agreement (EA) e Microsoft Customer contratto MCA (). Gestione costi di Azure supporta le API per entrambi i tipi di account. Rivedere le [configurare account di fatturazione per](../billing/billing-mca-setup-account.md) articolo Microsoft Customer contratto prima di continuare.

Le organizzazioni con un account con contratto Enterprise esistente devono leggere l'articolo in combinazione con configurazione di un account MCA. Rinnovo di un account EA richiedeva in precedenza, alcune operazioni minime per passare da una registrazione precedente a uno nuovo. Tuttavia, la migrazione a un account MCA richiede un impegno maggiore. Impegno aggiuntivo è a causa delle modifiche nel sottosistema di fatturazione sottostante, che influiscono su tutte le API correlate ai costi e le offerte di servizio.

## <a name="mca-apis-and-integration"></a>API MCA e integrazione

Le API MCA e integrazione con new consentono di:

- Avere disponibilità API completo tramite API di Azure native.
- Configurare più fatture in un singolo account di fatturazione.
- Accedere a un'API combinata con utilizzo del servizio di Azure, sull'utilizzo di Marketplace di terze parti e acquisti del Marketplace.
- Visualizzare i costi attraverso fatturazione profili (lo stesso nome enrollments) tramite Gestione costi di Azure.
- Accedere a nuove API per mostrare i costi, ricevere una notifica quando i costi superano le soglie predefinite ed esportare i dati non elaborati automaticamente.

## <a name="migration-checklist"></a>Elenco di controllo per migrazione

Gli elementi della Guida seguenti che eseguire la transizione alle API MCA.

- Acquisire familiarità con le nuove [contratto di clienti Microsoft account di fatturazione](../billing/billing-mca-overview.md).
- Determinare quali API è utilizzare e visualizzare quelle che vengono sostituite nella sezione seguente.
- Acquisire familiarità con [API REST di Azure Resource Manager](/rest/api/azure).
- Se l'API di Azure Resource Manager, non è già in uso [registrare l'app client con Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Aggiornare il codice di programmazione [usare l'autenticazione AD Azure](/rest/api/azure/#create-the-request).
- Aggiornare il codice di programmazione per sostituire le chiamate API con contratto Enterprise Agreement con chiamate all'API MCA.
- Aggiornare per usare nuovi codici di errore di gestione degli errori.
- Esaminare le offerte di integrazione aggiuntiva, ad esempio azione necessari di Cloudyn e Power BI, per altri.

## <a name="ea-apis-replaced-with-mca-apis"></a>API di EA sostituiti con le API MCA

API di EA usano una chiave API per l'autenticazione e autorizzazione. MCA API usano l'autenticazione di Azure AD.

| Scopo | API DI EA | MCA API |
| --- | --- | --- |
| Il saldo e crediti | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Utilizzo (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilizzo (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace Usage (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Periodi di fatturazione | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Elenco prezzi | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… formato /pricesheet/default/download = json|CSV Microsoft.Billing/billingAccounts/... / billingProfiles /... /Providers/Microsoft.consumption/pricesheets/download  |
| Acquisto di istanze riservate | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Consigli sulle prenotazioni | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Utilizzo della prenotazione | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-summary) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> il servizio di azure e l'utilizzo di Marketplace di terze parti sono disponibili con il [API per dettagli sull'uso](/rest/api/consumption/usagedetails).

Le API seguenti sono disponibili per gli account di fatturazione MCA:

| Scopo | API del contratto (MCA) dei clienti di Microsoft |
| --- | --- |
| Gli account di fatturazione<sup>2</sup> | Microsoft.Billing/billingAccounts |
| I profili di fatturazione<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Le sezioni della fattura<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Fatture | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| La fatturazione sottoscrizioni | {scope}/billingSubscriptions |

<sup>2</sup> API restituiscono elenchi di oggetti che sono gli ambiti, dove le esperienze di gestione dei costi nel portale di Azure e API di funzionano. Per altre informazioni sugli ambiti di gestione dei costi, vedere [comprendere e usare gli ambiti](understand-work-scopes.md).

Se si usa qualsiasi API con contratto Enterprise esistente, è necessario aggiornarli in modo da supportare gli account di fatturazione MCA. La tabella seguente illustra le altre modifiche di integrazione:

| Scopo | Offerta precedente | Nuova offerta |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Gestione costi di Azure](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) pack e il connettore del contenuto | [App Power BI Insights il consumo di Microsoft Azure](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) e [ connettore di Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API per saldi e crediti

Il [ottenere riepilogo bilanciamento](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API ti offre un riepilogo mensile delle:

- Saldi
- Nuovi acquisti
- Addebiti per servizi di Azure Marketplace
- Rettifiche
- Addebiti per eccedenza del servizio

Tutte le API di utilizzo vengono sostituite dalle API nativa di Azure che usano Azure AD per l'autenticazione e autorizzazione. Per altre informazioni sulla chiamata API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request).

L'API di ottenere bilanciare il riepilogo viene sostituita dall'API Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Per ottenere i saldi disponibili con l'API di saldo disponibili:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API per ottenere utilizzo e costi

Ottenere un'analisi giornaliera dettagliata dei costi di utilizzo del servizio di Azure, sull'utilizzo di Marketplace di terze parti e gli altri acquisti del Marketplace con le API seguenti. Sono state unite le seguenti API separate per servizi di Azure e l'uso di Marketplace di terze parti. Le API precedente vengono sostituite dal [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API. Aggiunge gli acquisti su Marketplace, che sono state in precedenza solo illustrate nel saldo riepilogo alla data.

- [Ottieni dettagli di utilizzo/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Ottenere dettagli/invio dell'utilizzo](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Ottieni dettagli di utilizzo/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Ottieni dettagli di utilizzo/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Ottenere marketplace store addebito/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Ottenere marketplace store addebito/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Tutte le API di utilizzo vengono sostituite dalle API nativa di Azure che usano Azure AD per l'autenticazione e autorizzazione. Per altre informazioni sulla chiamata API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request).

Tutte le API precedenti vengono sostituite dall'API di dettagli di utilizzo/consumo.

Per ottenere i dettagli di utilizzo con l'API di dettagli di utilizzo:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

L'API di dettagli di utilizzo, come con tutte le API di gestione dei costi, è disponibile in più ambiti. Per i costi fatturati, come in genere si riceverebbe un livello di registrazione, usare l'ambito del profilo di fatturazione.  Per altre informazioni sugli ambiti di gestione dei costi, vedere [comprendere e usare gli ambiti](understand-work-scopes.md).

| Type | Formato dell'ID |
| --- | --- |
| Account di fatturazione | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Profilo di fatturazione | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Sottoscrizione | `/subscriptions/{subscriptionId}` |
| Gruppo di risorse | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Usare i parametri di stringa di query seguenti per aggiornare il codice di programmazione.

| Parametri precedenti | Nuovi parametri |
| --- | --- |
| `billingPeriod={billingPeriod}` | Non supportate |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Modificata anche il corpo della risposta.

Corpo della risposta precedente:

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

Nuovo corpo della risposta:

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

Modificare il nome di proprietà che contiene la matrice di record di utilizzo dai dati per _valori_. Ogni record usati per ottenere un elenco completo di proprietà dettagliate. Tuttavia, ogni record ora tutti i dettagli sono ora disponibili in una proprietà annidata denominata _proprietà_, ad eccezione di tag. La nuova struttura è coerenza con altre API di Azure. Sono stati modificati alcuni nomi di proprietà. La tabella seguente illustra le proprietà corrispondenti.

| Proprietà precedente | Nuova proprietà | Note |
| --- | --- | --- |
| ID account | N/D | Il creatore della sottoscrizione non viene tenuta traccia. Usare invoiceSectionId (uguale a departmentId). |
| AccountNameAccountOwnerId e AccountOwnerEmail | N/D | Il creatore della sottoscrizione non viene tenuta traccia. Usare invoiceSectionName (uguale a departmentName). |
| Informazioni aggiuntive | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Si noti che queste proprietà sono opposti. Se è true isAzureCreditEnabled, ChargesBilledSeparately sarà false. |
| ConsumedQuantity | quantity | &nbsp; |
| Servizio utilizzato | consumedService | Valori stringa esatta potrebbero differire. |
| ID del servizio utilizzato | Nessuna | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Data e usageStartDate | date | &nbsp;  |
| Giorno | Nessuna | Analizza giorno dalla data. |
| ID del reparto | invoiceSectionId | Differenze tra valori esatti. |
| DepartmentName | invoiceSectionName | Valori stringa esatta potrebbero differire. Configurare le sezioni della fattura in modo da corrispondere a reparti, se necessario. |
| ExtendedCost e i costi | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| È un addebito ricorrente | Nessuna | &nbsp;  |
| Località | location | &nbsp;  |
| Categoria del contatore | meterCategory | Valori stringa esatta potrebbero differire. |
| ID contatore | meterId | Differenze tra valori stringa esatta. |
| Nome del contatore | meterName | Valori stringa esatta potrebbero differire. |
| Area del contatore | meterRegion | Valori stringa esatta potrebbero differire. |
| Sottocategoria del contatore | meterSubCategory | Valori stringa esatta potrebbero differire. |
| Mese | Nessuna | Analizza mese della data. |
| Nome offerta | Nessuna | Usare publisherName e productOrderName. |
| OfferId | Nessuna | &nbsp;  |
| Numero di ordine | Nessuna | &nbsp;  |
| PartNumber | Nessuna | Usare meterId e productOrderName per identificare in modo univoco i prezzi. |
| Nome piano | productOrderName | &nbsp;  |
| Prodotto | Prodotto |   |
| ProductId | productId | Differenze tra valori stringa esatta. |
| Nome entità di pubblicazione | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | Differenze tra valori stringa esatta. |
| Posizione della risorsa | resourceLocation | &nbsp;  |
| ID della posizione della risorsa | Nessuna | &nbsp;  |
| Tariffa per la risorsa | effectivePrice | &nbsp;  |
| ID dell'amministratore del servizio | N/D | &nbsp;  |
| Informazioni sul servizio 1 | serviceInfo1 | &nbsp;  |
| Informazioni sul servizio 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | Valori stringa esatta potrebbero differire. |
| Livello di servizio | meterSubCategory | Valori stringa esatta potrebbero differire. |
| Identificatore del servizio Store | N/D | &nbsp;  |
| GUID della sottoscrizione | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Tag | tags | La proprietà tags applicata all'oggetto radice, non per la proprietà di proprietà annidate. |
| Unità di misura | unitOfMeasure | Differenze tra valori stringa esatta. |
| usageEndDate | date | &nbsp;  |
| Year | Nessuna | Analizza l'anno della data. |
| (nuovo) | billingCurrency | Valuta usata per l'addebito. |
| (nuovo) | billingProfileId | ID univoco per il profilo di fatturazione (uguale a registrazione). |
| (nuovo) | billingProfileName | Nome del profilo di fatturazione (uguale a registrazione). |
| (nuovo) | chargeType | Consente di differenziare l'utilizzo di servizi di Azure, Marketplace e acquisti. |
| (nuovo) | invoiceId | ID univoco della fattura. Elenco vuoto per il mese corrente, aprire. |
| (nuovo) | publisherType | Tipo del server di pubblicazione per gli acquisti. Elenco vuoto per l'utilizzo. |
| (nuovo) | serviceFamily | Tipo di acquisto. Elenco vuoto per l'utilizzo. |
| (nuovo) | servicePeriodEndDate | Data di fine per il servizio acquistato. |
| (nuovo) | servicePeriodStartDate | Data di inizio per il servizio acquistato. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>Sostituito dall'API di fatture API per periodi di fatturazione

Gli account di fatturazione MCA non usano i periodi di fatturazione. Invece le fatture per i costi di ambito per i periodi di fatturazione specifici. Il [API per periodi di fatturazione](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) viene sostituito dall'API di fatture. Tutte le API di utilizzo vengono sostituite dalle API nativa di Azure che usano Azure AD per l'autenticazione e autorizzazione. Per altre informazioni sulla chiamata API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request).

Per ottenere le fatture l'API di fatture:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>API elenco prezzi

In questa sezione vengono illustrate le API di foglio di prezzo esistenti e offre suggerimenti per spostare l'API elenco prezzi per i contratti dei clienti Microsoft. Inoltre, descrive l'API elenco prezzi per i contratti dei clienti Microsoft e vengono illustrati i campi nei fogli di prezzo. Il [Enterprise Ottieni elenco prezzi](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) e [Enterprise Ottieni i periodi di fatturazione](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API vengono sostituite dall'API elenco prezzi per i contratti dei clienti Microsoft (Microsoft.Billing/billingAccounts/billingProfiles / elenco prezzi). La nuova API supporta formati JSON e CSV, in formati REST asincroni. Tutte le API di utilizzo vengono sostituite dalle API nativa di Azure che usano Azure AD per l'autenticazione e autorizzazione. Per altre informazioni sulla chiamata API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>API di fatturazione Enterprise

API di fatturazione Enterprise è utilizzato con le registrazioni Enterprise per ottenere informazioni sul periodi di fatturazione e prezzi. Autenticazione e autorizzazione usato i token web di Azure Active Directory.

Per ottenere prezzi applicabili per l'iscrizione Enterprise specificato con l'elenco prezzi e le API di periodo di fatturazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API elenco prezzi per i contratti dei clienti di Microsoft

Usare l'API elenco prezzi per i contratti dei clienti Microsoft per visualizzare i prezzi per tutti i servizi di consumo sul consumo di Azure e Marketplace. I prezzi indicati per il profilo di fatturazione si applicano a tutte le sottoscrizioni che appartengono al profilo di fatturazione.

Usare l'API elenco prezzi per visualizzare tutti i dati di elenco prezzi di servizi sul consumo di Azure in formato CSV:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Usare l'API elenco prezzi per visualizzare tutti i dati di elenco prezzi di servizi sul consumo di Azure in formato JSON:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

Tramite l'API restituisce l'elenco prezzi per l'intero account. Tuttavia, è anche possibile ottenere una versione ridotta dell'elenco prezzi in formato PDF. Il riepilogo include servizi di consumo sul consumo di Azure e del Marketplace che vengono fatturati una fattura specifica. La fattura è identificata da {invoiceId}, ovvero lo stesso come il **numero di fattura** illustrati nei file PDF di riepilogo della fattura. Di seguito è riportato un esempio.

![Immagine di esempio che mostra il numero di fattura che corrisponde alla InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Per visualizzare le informazioni di fatturazione con l'API elenco prezzi in formato CSV:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Per visualizzare le informazioni di fatturazione con l'API elenco prezzi in formato JSON:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Inoltre, è possibile visualizzare i prezzi stimati per qualsiasi servizio di utilizzo sul consumo di Azure o in Marketplace nel ciclo di fatturazione corrente aperto o punto di servizio.

Per visualizzare i prezzi stimati per i servizi al consumo con l'API elenco prezzi in formato CSV:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Per visualizzare i prezzi stimati per i servizi al consumo con l'API elenco prezzi in formato JSON:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Le API di Microsoft dal cliente contratto prezzo foglio sono *API REST asincrone*. Le risposte per le API modificate dalle precedenti API sincrone. Modificata anche il corpo della risposta API.

#### <a name="old-response-body"></a>Corpo della risposta precedente

Di seguito è riportato un esempio di risposta sincrona l'API REST:

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>Nuovo corpo della risposta

Le API supportano il [Azure REST asincrone](../azure-resource-manager/resource-manager-async-operations.md) formato. Chiamare l'API di uso di GET e si riceve la risposta seguente:

```
No Response Body

HTTP Status 202 Accepted
```

Le intestazioni seguenti vengono inviate con la posizione dell'output:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Eseguire GET un'altra chiamata al percorso. La risposta alla chiamata GET è lo stesso fino a quando l'operazione raggiunge uno stato di completamento o l'errore. Al termine, la risposta nel percorso di chiamata GET restituisce l'URL di download. Proprio come se l'operazione è stata eseguita nello stesso momento. Ad esempio:

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

Il client può anche inviare una chiamata GET il `Azure-AsyncOperation`. L'endpoint restituisce lo stato per l'operazione.

La tabella seguente illustra i campi nella precedente API di elenco prezzi Enterprise ottenere. Include campi corrispondenti nell'elenco nuovi prezzi per i contratti dei clienti Microsoft:

| Proprietà precedente | Nuova proprietà | Note |
| --- | --- | --- |
| billingPeriodId  | _Non applicabile_ | Non applicabile Per i contratti dei clienti Microsoft, la fattura e l'elenco prezzi associati sostituito il concetto di billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | Valori stringa esatta potrebbero differire. |
| includedQuantity  | includedQuantity | Non applicabile per i servizi in contratti dei clienti Microsoft. |
| partNumber  | _Non applicabile_ | In alternativa, usare una combinazione di productOrderName (uguale a IDOfferta) e meterid. |
| unitPrice  | unitPrice | Prezzo unitario è applicabile ai servizi utilizzati in contratti dei clienti Microsoft. |
| currencyCode  | pricingCurrency | Contratti dei clienti Microsoft hanno rappresentazioni prezzi nella valuta dei prezzi e fatturazione. Il currencyCode corrisponde a pricingCurrency negli accordi dei clienti Microsoft. |
| offerId | productOrderName | Anziché IDOfferta, è possibile usare productOrderName ma non è la stessa IDOfferta. Tuttavia, productOrderName e misuratore determinare prezzo in contratti dei clienti Microsoft correlati a meterId e Offerid in registrazioni legacy. |

## <a name="consumption-price-sheet-api-operations"></a>Operazioni API elenco prezzi al consumo

Per i contratti Enterprise, è utilizzata l'API elenco prezzi al consumo [ottenere](/rest/api/consumption/pricesheet/get) e [ottenere dal periodo di fatturazione](/rest/api/consumption/pricesheet/getbybillingperiod) operazioni per un ambito in base all'ID sottoscrizione o un periodo di fatturazione. L'API Usa l'autenticazione di Azure Resource Manager.

Per ottenere le informazioni dell'elenco prezzi per un ambito con l'API elenco prezzi:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Per ottenere informazioni elenco prezzi, fatturazione periodo con l'API elenco prezzi:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Anziché gli endpoint API precedenti, usare i seguenti per i contratti dei clienti Microsoft:

**API elenco prezzi per i contratti dei clienti Microsoft (API REST asincrone)**

Questa API è per i contratti dei clienti Microsoft e fornisce gli attributi aggiuntivi.

**Elenco prezzi per un ambito del profilo di fatturazione in un Account di fatturazione**

Questa API è l'API esistente. È stato aggiornato per fornire l'elenco prezzi per un profilo di fatturazione in un account di fatturazione.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Elenco prezzi per un ambito dall'account di fatturazione

L'autenticazione di Azure Resource Manager viene usato quando si recupera l'elenco prezzi nell'ambito della registrazione in un account di fatturazione.

Per ottenere l'elenco prezzi nell'account di registrazione in un account di fatturazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Per un contratto di clienti Microsoft, usare le informazioni nella sezione seguente. Fornisce le proprietà di campo utilizzate per i contratti Microsoft Customer.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Elenco prezzi per un ambito profilo di fatturazione in un account di fatturazione

L'elenco prezzi aggiornati dall'account di fatturazione API Ottiene l'elenco prezzi in formato CSV. Per ottenere l'elenco prezzi nell'ambito del profilo di fatturazione per un MCA:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Nell'ambito di registrazione del contratto Enterprise, la risposta dell'API e le proprietà sono identiche. Le proprietà corrispondono alle proprietà MCA stesso.

Le proprietà precedenti [API di Azure Resource Manager prezzo foglio](/rest/api/consumption/pricesheet) e sono le stesse proprietà di nuovo nella tabella seguente.

| Proprietà API precedente Azure Resource Manager prezzo foglio  | Nuova proprietà API elenco prezzi di Microsoft dal cliente contratto   | DESCRIZIONE |
| --- | --- | --- |
| ID misuratore | _meterId_ | Identificatore univoco per il contatore. Uguale a meterId. |
| Nome del contatore | meterName | Nome del contatore. Misurazione rappresenta la risorsa da distribuire il servizio di Azure. |
| Categoria del contatore  | service | Nome della categoria di classificazione per il contatore. Uguale a servizio nell'elenco prezzi contratto dei clienti Microsoft. Differenze tra valori stringa esatta. |
| Sottocategoria di contatore | meterSubCategory | Nome della categoria sottoclassificazione del misuratore. Basata sulla classificazione di differenziazione di set di funzionalità generale nel servizio. Ad esempio, database SQL Basic Visual Studio database SQL Standard. |
| Area del contatore | meterRegion | &nbsp;  |
| Unità | _Non applicabile_ | Può essere analizzato da unitOfMeasure. |
| Unità di misura | unitOfMeasure | &nbsp;  |
| Codice | _Non applicabile_ | Anziché operativo{0, usare productOrderName e ID contatore per identificare in modo univoco il prezzo per un profilo di fatturazione. I campi vengono elencati nella fattura MCA anziché operativo{0 MCA fatture. |
| Prezzo unitario | unitPrice | Prezzo unitario di contratto di Microsoft dal cliente. |
| Codice valuta | pricingCurrency | Microsoft Customer contratti rappresentano i prezzi nella valuta di prezzi e fatturazione valuta. Codice valuta equivale pricingCurrency negli accordi dei clienti Microsoft. |
| Quantità inclusa | includedQuantity | Non è applicabile ai servizi in contratti dei clienti Microsoft. Visualizzare con i valori pari a zero. |
|  ID offerta  | productOrderName | Anziché IDOfferta, usare productOrderName. Non è identico IDOfferta, tuttavia il productOrderName e contatore determinano prezzo in contratti dei clienti Microsoft. Correlato a meterId e Offerid in registrazioni legacy. |

Il prezzo per i contratti Microsoft dal cliente è definito in modo diverso rispetto a contratti Enterprise. Il prezzo per i servizi nell'iscrizione Enterprise è univoco per prodotto, operativo{0, contatore e l'offerta. L'operativo{0 non vengono usati in contratti dei clienti Microsoft.

Il prezzo del servizio sul consumo di Azure che fa parte di un contratto di Microsoft dei clienti è univoco per productOrderName e meterId. Rappresentano il contatore servizio e il piano del prodotto.

Per risolvere le differenze tra l'elenco prezzi e l'utilizzo dell'API di dettagli di utilizzo, è possibile utilizzare il productOrderName e meterId.

Gli utenti che dispongono di fatturazione del profilo proprietario, collaboratore, lettore e fatturare i diritti di gestione possono scaricare l'elenco prezzi.

L'elenco prezzi includa i prezzi per i servizi il cui prezzo è basato sull'utilizzo. I servizi includono l'utilizzo di Azure e uso di Marketplace. I prezzi più recenti alla fine di ogni punto di servizio sono bloccato e applicati all'utilizzo in un periodo di singolo servizio. Per i servizi di consumo di Azure, il periodo del servizio è in genere un mese di calendario.

### <a name="retired-price-sheet-api-fields"></a>Ritirato campi API elenco prezzi

I campi seguenti non sono disponibili nelle API di Microsoft dal cliente contratto prezzo foglio o hanno gli stessi campi.

|Campo ritirato| DESCRIZIONE|
|---|---|
| billingPeriodId | Non applicabile. Corrisponde a InvoiceId per MCA. |
| offerId | Non applicabile Corrisponde a productOrderName in MCA. |
| meterCategory  | Non applicabile Corrisponde al servizio in MCA. |
| unit | Non applicabile Può essere analizzato da unitOfMeasure. |
| currencyCode | Stesso come il pricingCurrency in MCA. |
| meterLocation | Uguale a meterRegion in MCA. |
| Operativo{0 operativo{0 | Non applicabile perché il numero di parte non è elencato nella MCA fatture. Anziché operativo{0, usare la combinazione di ID contatore e productOrderName per identificare in modo univoco i prezzi. |
| totalIncludedQuantity | Non applicabile |
| pretaxStandardRate  | Non applicabile |

## <a name="reservation-instance-charge-api-replaced"></a>API per spese istanza prenotazione sostituito

È possibile ottenere fatturazione transazioni per l'acquisto di istanze riservate con il [API per spese istanza riservata](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). La nuova API include tutti gli acquisti, tra cui le offerte di Marketplace di terze parti. Tutte le API di utilizzo vengono sostituite dalle API nativa di Azure che usano Azure AD per l'autenticazione e autorizzazione. Per altre informazioni sulla chiamata API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request). L'API per spese istanza riservata viene sostituito dall'API di transazioni.

Per ottenere le transazioni di acquisto con l'API di transazioni prenotazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>Recommendations API sostituite

Le API Recommendations di acquisto istanza riservata forniscono l'utilizzo della macchina virtuale negli ultimi 7, 30 o 60 giorni. API forniscono anche consigli per gli acquisti prenotazione. tra cui:

- [Condiviso API dell'istanza riservata di raccomandazione](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API consigli singola istanza riservata](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Tutte le API di utilizzo vengono sostituite dalle API nativa di Azure che usano Azure AD per l'autenticazione e autorizzazione. Per altre informazioni sulla chiamata API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request). Le raccomandazioni di prenotazione le API elencate in precedenza vengono sostituite dal [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API.

Per ottenere le raccomandazioni di prenotazione con l'API Recommendations di prenotazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>API di utilizzo di prenotazione sostituito

È possibile ottenere l'utilizzo della prenotazione in una registrazione con l'API di utilizzo di istanza riservata. Se è presente più di un'istanza riservata in una registrazione, è anche possibile ottenere l'utilizzo di tutte le istanze riservate acquisti con questa API.

tra cui:

- [Dettagli utilizzo istanze riservate](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [Riepilogo utilizzo istanze riservate](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-summary)

Tutte le API di utilizzo vengono sostituite dalle API nativa di Azure che usano Azure AD per l'autenticazione e autorizzazione. Per altre informazioni sulla chiamata API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request). Le raccomandazioni di prenotazione le API elencate in precedenza vengono sostituite dal [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) e [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API.

Per ottenere i dettagli della prenotazione con l'API di dettagli prenotazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Per ottenere i riepiloghi di prenotazione con l'API di riepiloghi di prenotazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Passare da Cloudyn a gestione costi

Le organizzazioni che usano [Cloudyn](https://cloudyn.com) devono iniziare a usare [Azure Cost Management](https://azure.microsoft.com/services/cost-management/) per eventuali esigenze di gestione costi. Gestione costi è disponibile nel portale di Azure con alcun processo di onboarding e una latenza di otto ore. Per altre informazioni, vedere la [documentazione di gestione dei costi](index.yml).

Con gestione costi di Azure, è possibile:

- Visualizzare i costi nel tempo con un budget predefinito. Analizzare ogni giorno i modelli di costo per identificare e arrestare le anomalie di spesa. Suddividere i costi per tag, gruppo di risorse, servizi e località.
- Creare i budget per impostare i limiti sull'utilizzo e costi e ricevere una notifica quando vengono hanno quasi raggiunto le soglie di importante. Configurare automazione con i gruppi di azioni per attivare gli eventi personalizzati e applicare i limiti rigidi in base alle proprie esigenze.
- Ottimizzare i costi e all'utilizzo con raccomandazioni di Azure Advisor. Individuare le ottimizzazioni di acquisto con le prenotazioni, ridimensionare le macchine virtuali sottoutilizzate ed eliminare le risorse inutilizzate di rimanere entro i budget.
- Pianificare un'esportazione dei dati di utilizzo e costi per la pubblicazione di un file CSV per l'account di archiviazione ogni giorno. Automatizzare l'integrazione con sistemi esterni per mantenere i dati di fatturazione sincronizzati e aggiornati.

## <a name="power-bi-integration"></a>Integrazione con Power BI

Se si usa Power BI per i report dei costi, è necessario per eseguire la transizione al seguente:

- Microsoft Azure consumo Insights app Power BI
- Connettore desktop Azure Consumption Insights


Il connettore è consigliato per le organizzazioni che desiderano la massima flessibilità. Tuttavia, l'app Power BI è anche disponibile per installazione rapida.

- Installare il [Microsoft Azure Consumption Insights di Power BI app](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Connettersi con il connettore Azure Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights)

Il pacchetto di contenuto Consumption Insights e il connettore precedente ha lavorato a un livello di registrazione. È necessario l'accesso in lettura ad almeno. La nuova app di Power BI Insights di consumo e il nuovo connettore di Azure Consumption Insights sono disponibili per gli utenti del profilo di fatturazione. I team che necessitano di opzioni aggiuntive per rivedere i costi o per visualizzare i costi tra i profili di fatturazione è necessario utilizzare nel [analisi dei costi](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) il portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Leggere il [documentazione di gestione dei costi](index.yml) per imparare a monitorare e controllare la spesa di Azure. In alternativa, se si desidera ottimizzare l'utilizzo delle risorse con gestione costi.
