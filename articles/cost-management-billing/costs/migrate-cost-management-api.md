---
title: Eseguire la migrazione delle API dal Contratto Enterprise al Contratto del cliente Microsoft - Azure
description: Questo articolo illustra le conseguenze della migrazione di un Contratto Enterprise (EA) a un Contratto del cliente Microsoft.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.openlocfilehash: 397e0a21b1ba11b3bdd74c2030ff358c1ce159d8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77201034"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Eseguire la migrazione delle API dal Contratto Enterprise al Contratto del cliente Microsoft

Questo articolo illustra le differenze della struttura dei dati, delle API e di altre integrazioni di sistema tra gli account Contratto Enterprise (EA) e Contratto del cliente Microsoft. Gestione costi di Azure supporta le API per entrambi i tipi di account. Prima di continuare, vedere l'articolo [Configurare l'account di fatturazione per un Contratto del cliente Microsoft](../manage/mca-setup-account.md).

È consigliabile che le organizzazioni con un account EA esistente leggano questo articolo durante la configurazione di un account MCA. In precedenza, il rinnovo di un account EA richiedeva un minimo intervento per passare da una registrazione a una nuova. La migrazione a un account MCA, invece, richiede un intervento aggiuntivo. Il motivo è dovuto alle modifiche del sottosistema di fatturazione sottostante, che influiscono su tutte le API correlate ai costi e sulle offerte di servizi.

## <a name="mca-apis-and-integration"></a>API MCA e integrazione

Le API MCA e la nuova integrazione consentono di:

- Avere disponibilità completa di API tramite le API native di Azure.
- Configurare più fatture in un singolo account di fatturazione.
- Accedere a un'API combinata con l'utilizzo di servizi di Azure, l'utilizzo del Marketplace di terze parti e gli acquisti nel Marketplace.
- Visualizzare i costi in vari profili di fatturazione (come per le registrazioni) tramite Gestione costi di Azure.
- Accedere alle nuove API per visualizzare i costi, ricevere notifiche quando i costi superano le soglie predefinite ed esportare automaticamente i dati non elaborati.

## <a name="migration-checklist"></a>Elenco di controllo per la migrazione

Gli elementi seguenti facilitano la transizione alle API MCA.

- Acquisire familiarità con il nuovo [account di fatturazione del Contratto del cliente Microsoft](../understand/mca-overview.md).
- Identificare le API in uso e verificare quali vengono sostituite nella sezione seguente.
- Acquisire familiarità con le [API REST di Azure Resource Manager](/rest/api/azure).
- Se non si usano ancora le API di Azure Resource Manager, [registrare l'app client con Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Aggiornare il codice di programmazione per l'[uso dell'autenticazione di Azure AD](/rest/api/azure/#create-the-request).
- Aggiornare il codice di programmazione per sostituire le chiamate alle API EA con le chiamate alle API MCA.
- Aggiornare la gestione degli errori per l'uso di nuovi codici errore.
- Per altre azioni necessarie, esaminare le offerte di integrazione aggiuntive, ad esempio Cloudyn e Power BI.

## <a name="ea-apis-replaced-with-mca-apis"></a>API EA sostituite con API MCA

Le API EA usano una chiave API per l'autenticazione e l'autorizzazione. Le API MCA usano l'autenticazione di Azure AD.

| Scopo | API EA | API MCA |
| --- | --- | --- |
| Saldo e crediti | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Utilizzo (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilizzo (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilizzo del Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Periodi di fatturazione | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Elenco prezzi | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| Acquisti di prenotazioni | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Consigli sulle prenotazioni | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Utilizzo delle prenotazioni | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> I dati sull'utilizzo dei servizi di Azure e del Marketplace di terze parti sono disponibili con l'[API Dettagli utilizzo](/rest/api/consumption/usagedetails).

Per gli account di fatturazione MCA sono disponibili le API seguenti:

| Scopo | API Contratto del cliente Microsoft (MCA) |
| --- | --- |
| Account di fatturazione<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Profili di fatturazione<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Sezioni della fattura<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Fatture | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Sottoscrizioni di fatturazione | {scope}/billingSubscriptions |

<sup>2</sup> Le API restituiscono gli elenchi di oggetti, ovvero gli ambiti, in cui operano le esperienze di Gestione costi dei costi nel portale di Azure e le API. Per altre informazioni sugli ambiti di Gestione costi, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

Se si usano API EA esistenti, è necessario aggiornarle per supportare gli account di fatturazione MCA. La tabella seguente illustra altre modifiche di integrazione:

| Scopo | Vecchia offerta | Nuova offerta |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Gestione costi di Azure](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Pacchetto di contenuto e connettore [Informazioni cognitive dettagliate sul consumo di Microsoft](/power-bi/desktop-connect-azure-consumption-insights) |  [Connettore Informazioni dettagliate sul consumo di Azure](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API per ottenere saldo e crediti

L'API [Get Balance Summary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) fornisce un riepilogo mensile di:

- Saldi
- Nuovi acquisti
- Addebiti per il servizio Azure Marketplace
- Rettifiche
- Addebiti per eccedenza del servizio

Tutte le API sul consumo vengono sostituite da API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulle chiamate alle API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request).

L'API Get Balance Summary viene sostituita dall'API Microsoft.Billing/billingAccounts/billingProfiles/availableBalance.

Per ottenere i saldi disponibili con l'API Available Balance:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API per ottenere dati su costi e utilizzo

Le API seguenti consentono di ottenere una scomposizione quotidiana dei costi derivanti dall'utilizzo di servizi di Azure, dall'utilizzo del Marketplace di terze parti e da altri acquisti nel Marketplace. Le seguenti API distinte sono state unite per i servizi di Azure e per l'utilizzo del Marketplace di terze parti. Le API precedenti vengono sostituite dall'API [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails). Questa API include anche gli acquisti nel Marketplace, che in precedenza erano mostrati solo nel riepilogo del saldo alla data corrente.

- [Get usage detail/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Get usage detail/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Get usage detail/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Get usage detail/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Get marketplace store charge/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Get marketplace store charge/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Tutte le API sul consumo vengono sostituite da API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulle chiamate alle API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request).

Tutte le API precedenti vengono sostituite dall'API Consumption/Usage Details.

Per ottenere dettagli sull'utilizzo con l'API Usage Details:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

L'API Usage Details, come tutte le API di Gestione costi, è disponibile in più ambiti. Per i costi fatturati, che in genere si ricevono a livello di registrazione, usare l'ambito del profilo di fatturazione.  Per altre informazioni sugli ambiti di Gestione costi, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

| Type | Formato ID |
| --- | --- |
| Account di fatturazione | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Profilo di fatturazione | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Subscription | `/subscriptions/{subscriptionId}` |
| Resource group | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Usare i parametri querystring seguenti per aggiornare il codice di programmazione.

| Vecchi parametri | Nuovi parametri |
| --- | --- |
| `billingPeriod={billingPeriod}` | Non supportate |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Anche il corpo della risposta è cambiato.

Vecchio corpo della risposta:

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

Il nome della proprietà che contiene la matrice di record di utilizzo è cambiato da dati a _valori_. Ogni record includeva un elenco semplice di proprietà dettagliate. Ora invece ogni record include tutti i dettagli in una proprietà annidata denominata _properties_, ad eccezione dei tag. La nuova struttura è coerente con altre API di Azure. Alcuni nomi di proprietà sono cambiati. La tabella seguente mostra le proprietà corrispondenti.

| Vecchia proprietà | Nuova proprietà | Note |
| --- | --- | --- |
| AccountId | N/D | L'autore della sottoscrizione non viene registrato. Usare invoiceSectionId (uguale a departmentId). |
| AccountNameAccountOwnerId e AccountOwnerEmail | N/D | L'autore della sottoscrizione non viene registrato. Usare invoiceSectionName (uguale a departmentName). |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Si noti che queste proprietà sono opposte. Se isAzureCreditEnabled è true, ChargesBilledSeparately sarà false. |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | I valori di stringa esatti potrebbero essere diversi. |
| ConsumedServiceId | nessuno | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date e usageStartDate | Data | &nbsp;  |
| Giorno | nessuno | Analizza il giorno dalla data. |
| DepartmentId | invoiceSectionId | I valori esatti sono diversi. |
| DepartmentName | invoiceSectionName | I valori di stringa esatti potrebbero essere diversi. Configurare le sezioni della fattura in base ai reparti, se necessario. |
| ExtendedCost e Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| Is Recurring Charge | nessuno | &nbsp;  |
| Location | posizione | &nbsp;  |
| MeterCategory | meterCategory | I valori di stringa esatti potrebbero essere diversi. |
| ID contatore | meterId | I valori di stringa esatti sono diversi. |
| MeterName | meterName | I valori di stringa esatti potrebbero essere diversi. |
| MeterRegion | meterRegion | I valori di stringa esatti potrebbero essere diversi. |
| MeterSubCategory | meterSubCategory | I valori di stringa esatti potrebbero essere diversi. |
| Month | nessuno | Analizza il mese dalla data. |
| Nome offerta | nessuno | Usare publisherName e productOrderName. |
| OfferID | nessuno | &nbsp;  |
| Order Number | nessuno | &nbsp;  |
| PartNumber | nessuno | Usare meterId e productOrderName per identificare in modo univoco i prezzi. |
| Plan Name | productOrderName | &nbsp;  |
| Prodotto | Prodotto |   |
| ProductId | productId | I valori di stringa esatti sono diversi. |
| Nome entità di pubblicazione | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | I valori di stringa esatti sono diversi. |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | nessuno | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N/D | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | I valori di stringa esatti potrebbero essere diversi. |
| ServiceTier | meterSubCategory | I valori di stringa esatti potrebbero essere diversi. |
| StoreServiceIdentifier | N/D | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Tag | tags | La proprietà tags si applica all'oggetto radice, non alla proprietà properties annidata. |
| UnitOfMeasure | unitOfMeasure | I valori di stringa esatti sono diversi. |
| usageEndDate | Data | &nbsp;  |
| Year | nessuno | Analizza l'anno dalla data. |
| (nuova) | billingCurrency | Valuta usata per l'addebito. |
| (nuova) | billingProfileId | ID univoco per il profilo di fatturazione (uguale alla registrazione). |
| (nuova) | billingProfileName | Nome del profilo di fatturazione (uguale alla registrazione). |
| (nuova) | chargeType | Da usare per differenziare l'utilizzo dei servizi di Azure, l'utilizzo del Marketplace e gli acquisti. |
| (nuova) | invoiceId | ID univoco per la fattura. Vuota per il mese corrente aperto. |
| (nuova) | publisherType | Tipo di server di pubblicazione per gli acquisti. Vuota per l'utilizzo. |
| (nuova) | serviceFamily | Tipo di acquisto. Vuota per l'utilizzo. |
| (nuova) | servicePeriodStartDate | Data di fine per il servizio acquistato. |
| (nuova) | servicePeriodStartDate | Data di inizio per il servizio acquistato. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API Billing Periods sostituite da API Invoices

Gli account di fatturazione MCA non usano i periodi di fatturazione. Usano invece le fatture per definire l'ambito dei costi in specifici periodi di fatturazione. L'[API Billing Periods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) viene sostituita dall'API Invoices. Tutte le API sul consumo vengono sostituite da API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulle chiamate alle API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request).

Per ottenere le fatture con l'API Invoices:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>API Price Sheet

Questa sezione descrive le attuali API Price Sheet e fornisce raccomandazioni per il passaggio all'API Price Sheet per i Contratti del cliente Microsoft. Descrive inoltre l'API Price Sheet per i Contratti del cliente Microsoft e i campi inclusi negli elenchi prezzi. Le API [Enterprise Get price sheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) e [Enterprise Get billing periods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) vengono sostituite dall'API Price Sheet per i Contratti del cliente Microsoft (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet). La nuova API supporta sia i formati JSON che CSV, nei formati REST asincroni. Tutte le API sul consumo vengono sostituite da API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulle chiamate alle API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>API Billing Enterprise

Le API Billing Enterprise si usano con le registrazioni Enterprise per ottenere informazioni su prezzi e periodi di fatturazione. L'autenticazione e l'autorizzazione vengono gestite tramite token Web di Azure Active Directory.

Per ottenere i prezzi applicabili per la registrazione Enterprise specificata con le API Price Sheet e Billing Period:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API Price Sheet per i Contratti del cliente Microsoft

Usare l'API Price Sheet per i Contratti del cliente Microsoft per visualizzare i prezzi per tutti i servizi a consumo di Azure e del Marketplace. I prezzi mostrati per il profilo di fatturazione si applicano a tutte le sottoscrizioni che include.

Usare l'API Price Sheet per visualizzare i dati sugli elenchi prezzi dei servizi a consumo di Azure in formato CSV:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Usare l'API Price Sheet per visualizzare i dati sugli elenchi prezzi dei servizi a consumo di Azure in formato JSON:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

L'uso dell'API restituisce l'elenco prezzi per l'intero account. Tuttavia, è anche possibile ottenere una versione ridotta dell'elenco prezzi in formato PDF. Il riepilogo include i servizi a consumo di Azure e del Marketplace fatturato per una specifica fattura. La fattura è identificata tramite {invoiceId}, che corrisponde esattamente al **numero di fattura** riportato nei file PDF Riepilogo fattura. Di seguito è riportato un esempio.

![Immagine di esempio che mostra il numero di fattura corrispondente a InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Per visualizzare informazioni sulla fattura con l'API Price Sheet in formato CSV:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Per visualizzare informazioni sulla fattura con l'API Price Sheet in formato JSON:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

È anche possibile visualizzare i prezzi stimati per qualsiasi servizio a consumo di Azure o del Marketplace nel ciclo di fatturazione aperto corrente o nel periodo del servizio.

Per visualizzare i prezzi stimati per i servizi a consumo con l'API Price Sheet in formato CSV:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Per visualizzare i prezzi stimati per i servizi a consumo con l'API Price Sheet in formato JSON:

| Metodo | URI della richiesta |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Le API Price Sheet per i Contratti del cliente Microsoft sono *API REST asincrone*. Le risposte per le API sono diverse rispetto alle API sincrone precedenti. Anche il corpo della risposta dell'API è cambiato.

#### <a name="old-response-body"></a>Vecchio corpo della risposta

Ecco un esempio di risposta dell'API REST sincrona:

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

Le API supportano il formato [Azure REST asincrono](../../azure-resource-manager/management/async-operations.md). Chiamare l'API usando GET per ricevere la risposta seguente:

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

Effettuare un'altra chiamata GET per la posizione. La risposta alla chiamata GET è la stessa finché l'operazione non raggiunge uno stato di completamento o di errore. Al termine, la risposta alla chiamata GET per la posizione restituisce l'URL di download, come se l'operazione venisse eseguita contemporaneamente. Ad esempio:

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

Anche il client può effettuare una chiamata GET per `Azure-AsyncOperation`. L'endpoint restituisce lo stato dell'operazione.

La tabella seguente illustra i campi dell'API Enterprise Get price sheet precedente. Include i campi corrispondenti del nuovo elenco prezzi per i Contratti del cliente Microsoft:

| Vecchia proprietà | Nuova proprietà | Note |
| --- | --- | --- |
| billingPeriodId  | _Non applicabile_ | Non applicabile. Per i Contratti del cliente Microsoft, la fattura e l'elenco prezzi associato sostituiscono il concetto di billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | I valori di stringa esatti potrebbero essere diversi. |
| includedQuantity  | includedQuantity | Non applicabile ai servizi nei Contratti del cliente Microsoft. |
| partNumber  | _Non applicabile_ | Usare invece una combinazione di productOrderName (uguale a offerID) e meterID. |
| unitPrice  | unitPrice | Il prezzo unitario è applicabile per i servizi usati nei Contratti del cliente Microsoft. |
| currencyCode  | pricingCurrency | I Contratti del cliente Microsoft rappresentano i prezzi in valuta prezzi e valuta di fatturazione. currencyCode corrisponde a pricingCurrency nei Contratti del cliente Microsoft. |
| offerID | productOrderName | Invece di OfferID, è possibile usare productOrderName ma non è uguale a OfferID. Tuttavia, productOrderName e meter determinano i prezzi nei Contratti del cliente Microsoft correlati a meterId e OfferID nelle registrazioni legacy. |

## <a name="consumption-price-sheet-api-operations"></a>Operazioni dell'API Consumption Price Sheet

Per i Contratti Enterprise si usano le operazioni [Get](/rest/api/consumption/pricesheet/get) e [Get By Billing Period](/rest/api/consumption/pricesheet/getbybillingperiod) dell'API Consumption Price Sheet per un ambito in base a subscriptionId o a periodo di fatturazione. L'API usa l'autenticazione di Azure Resource Management.

Per ottenere informazioni sull'elenco prezzi per un ambito con l'API Price Sheet:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Per ottenere informazioni sull'elenco prezzi in base a periodo di fatturazione con l'API Price Sheet:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Invece degli endpoint API precedenti, usare quelli seguenti per i Contratti del cliente Microsoft:

**API Price Sheet per i Contratti del cliente Microsoft (API REST asincrone)**

Questa API è destinata ai Contratti del cliente Microsoft e prevede attributi aggiuntivi.

**Elenco prezzi per un ambito del profilo di fatturazione in un account di fatturazione**

Questa è l'API esistente. È stata aggiornata per fornire l'elenco prezzi per un profilo di fatturazione in un account di fatturazione.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Elenco prezzi per un ambito in base ad account di fatturazione

L'autenticazione di Azure Resource Manager si usa quando si ottiene l'elenco prezzi nell'ambito di registrazione in un account di fatturazione.

Per ottenere l'elenco prezzi a livello di account di registrazione in un account di fatturazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Per un Contratto del cliente Microsoft, usare le informazioni della sezione seguente. Fornisce le proprietà dei campi usati per i Contratti del cliente Microsoft.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Elenco prezzi per un ambito del profilo di fatturazione in un account di fatturazione

L'API aggiornata in base ad account di fatturazione ottiene l'elenco prezzi in formato CSV. Per ottenere l'elenco prezzi nell'ambito del profilo di fatturazione per un contratto MCA:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Nell'ambito di registrazione del contratto EA, la risposta e le proprietà dell'API sono identiche. Le proprietà corrispondono alle stesse proprietà di MCA.

Le proprietà precedenti per le [API Price Sheet di Azure Resource Manager ](/rest/api/consumption/pricesheet) e le nuove proprietà corrispondenti sono indicate nella tabella seguente.

| Proprietà della vecchia API Price Sheet di Azure Resource Manager  | Proprietà della nuova API Price Sheet del Contratto del cliente Microsoft   | Descrizione |
| --- | --- | --- |
| ID misuratore | _meterId_ | Identificatore univoco del contatore. Uguale a meterID. |
| Nome contatore | meterName | Nome del contatore. Il contatore rappresenta la risorsa distribuibile di un servizio di Azure. |
| Categoria del contatore  | service | Nome della categoria di classificazione per il contatore. Uguale al servizio nell'elenco prezzi del Contratto del cliente Microsoft. I valori di stringa esatti sono diversi. |
| Sottocategoria di contatore | meterSubCategory | Nome della categoria di sottoclassificazione del contatore. In base alla classificazione di differenziazione dei set di funzionalità di alto livello nel servizio. Ad esempio, database SQL di base rispetto a database SQL standard. |
| Area contatore | meterRegion | &nbsp;  |
| Unità | _Non applicabile_ | Può essere analizzato da unitOfMeasure. |
| Unità di misura | unitOfMeasure | &nbsp;  |
| Numero parte | _Non applicabile_ | Invece del numero parte, usare productOrderName e MeterID per identificare in modo univoco il prezzo per un profilo di fatturazione. Nella fattura MCA sono riportati i campi invece del numero parte. |
| Prezzo unitario | unitPrice | Prezzo unitario del Contratto del cliente Microsoft. |
| Codice valuta | pricingCurrency | I Contratti del cliente Microsoft rappresentano i prezzi in valuta prezzi e valuta di fatturazione. Il codice valuta è uguale a pricingCurrency nei Contratti del cliente Microsoft. |
| Quantità inclusa | includedQuantity | Non applicabile ai servizi nei Contratti del cliente Microsoft. Visualizza valori zero. |
|  Offer ID (ID offerta)  | productOrderName | Invece di OfferID, usare productOrderName. Non è uguale a OfferID, ma productOrderName e meter determinano i prezzi nei Contratti dei clienti Microsoft. È correlata a meterId e OfferID nelle registrazioni legacy. |

Il prezzo per i Contratti con i clienti Microsoft è definito in modo diverso rispetto ai Contratti Enterprise. Il prezzo per i servizi nella registrazione Enterprise è univoco per prodotto, numero parte, contatore e offerta. Il numero parte non viene usato nei Contratti con i clienti Microsoft.

Il prezzo dei servizi a consumo di Azure che fanno parte di un Contratto del cliente Microsoft è univoco per productOrderName e meterID, che rappresentano il contatore del servizio e il piano del prodotto.

Per riconciliare tra l'elenco prezzi e l'utilizzo nell'API Usage Details, è possibile usare productOrderName e meterID.

Gli utenti con diritti di proprietario, collaboratore, lettore e gestione fatture nel profilo di fatturazione possono scaricare l'elenco prezzi.

L'elenco prezzi riguarda i servizi il cui prezzo è basato sull'utilizzo. I servizi includono quelli a consumo di Azure e a consumo del Marketplace. L'ultimo prezzo alla fine di ogni periodo di servizio è bloccato e viene applicato all'utilizzo in un singolo periodo di servizio. Per i servizi a consumo di Azure, il periodo di servizio è in genere un mese di calendario.

### <a name="retired-price-sheet-api-fields"></a>Campi ritirati dell'API Price Sheet

I campi seguenti non sono disponibili nelle API Price Sheet del Contratto del cliente Microsoft oppure sono uguali.

|Campo ritirato| Descrizione|
|---|---|
| billingPeriodId | Non applicabile. Corrisponde a InvoiceId per MCA. |
| offerID | Non applicabile. Corrisponde a productOrderName in MCA. |
| meterCategory  | Non applicabile. Corrisponde a Service per MCA. |
| unit | Non applicabile. Può essere analizzato da unitOfMeasure. |
| currencyCode | Uguale a pricingCurrency in MCA. |
| meterLocation | Uguale a meterRegion in MCA. |
| partNumber partnumber | Non applicabile perché il numero parte non è incluso nelle fatture MCA. Invece del numero parte, usare la combinazione meterId e productOrderName per identificare in modo univoco i prezzi. |
| totalIncludedQuantity | Non applicabile. |
| pretaxStandardRate  | Non applicabile. |

## <a name="reservation-instance-charge-api-replaced"></a>API Reservation Instance Charge sostituita

Con l'[API Reserved Instance Charge](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges), è possibile ottenere le transazioni di fatturazione per gli acquisti di prenotazioni. La nuova API include tutti gli acquisti, incluse le offerte del Marketplace di terze parti. Tutte le API sul consumo vengono sostituite da API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulle chiamate alle API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request). L'API Reserved Instance Charge viene sostituita dall'API Transaction.

Per ottenere le transazioni di acquisto di prenotazioni con l'API Transactions:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>API Recommendations sostituita

Le API Reserved Instance Purchase Recommendations forniscono dati di utilizzo delle macchine virtuali relativi agli ultimi 7, 30 o 60 giorni. Le API forniscono anche raccomandazioni per l'acquisto di prenotazioni. Tali impostazioni includono:

- [API Shared Reserved Instance Recommendation](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API Single Reserved Instance Recommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Tutte le API sul consumo vengono sostituite da API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulle chiamate alle API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request). Le API di raccomandazioni sulle prenotazioni elencate sopra vengono sostituite dall'API [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list).

Per ottenere raccomandazioni sulle prenotazioni con l'API Reservation Recommendations:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>API Reservation Usage sostituite

Con l'API Reserved Instance Usage, è possibile ottenere dati di utilizzo delle prenotazioni in una registrazione. Se una registrazione include più di un'istanza riservata, con questa API è anche possibile ottenere dati di utilizzo di tutte le istanze riservate acquistate.

Tali impostazioni includono:

- [Dettagli sull'utilizzo di istanze riservate](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Riepilogo dell'utilizzo di istanze riservate](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Tutte le API sul consumo vengono sostituite da API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulle chiamate alle API REST di Azure, vedere [Introduzione a REST](/rest/api/azure/#create-the-request). Le API di raccomandazioni sulle prenotazioni elencate sopra vengono sostituite dalle API [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) e [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries).

Per ottenere i dettagli delle prenotazioni con l'API Reservation Details:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Per ottenere i dettagli delle prenotazioni con l'API Reservation Summaries:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Passaggio da Cloudyn a Gestione costi

Le organizzazioni che usano [Cloudyn](https://cloudyn.com) devono iniziare a usare [Gestione costi di Azure](https://azure.microsoft.com/services/cost-management/) per tutte le esigenze di gestione dei costi. Gestione costi è disponibile nel portale di Azure senza onboarding e con una latenza di otto ore. Per altre informazioni, vedere [Documentazione di Gestione costi](../index.yml).

Con Gestione costi di Azure, è possibile:

- Visualizzare i costi nel corso del tempo rispetto a un budget predefinito. Analizzare i modelli di costo giornalieri per identificare e interrompere anomalie di spesa. Suddividere i costi in base a tag, gruppo di risorse, servizio e località.
- Creare budget per impostare i limiti relativi all'utilizzo e ai costi e ricevere notifiche quando vengono raggiunte le soglie importanti. Configurare l'automazione con i gruppi di azioni per attivare eventi personalizzati e applicare limiti massimi in base a specifiche esigenze.
- Ottimizzare i costi e l'utilizzo con le raccomandazioni di Azure Advisor. Individuare le ottimizzazioni di acquisto con le prenotazioni, ridurre le dimensioni delle macchine virtuali sottoutilizzate ed eliminare le risorse inutilizzate per rientrare nei budget.
- Pianificare un'esportazione dei dati relativi a costi e utilizzo per pubblicare ogni giorno un file CSV nell'account di archiviazione. Automatizzare l'integrazione con sistemi esterni per mantenere i dati di fatturazione sincronizzati e aggiornati.

## <a name="power-bi-integration"></a>Integrazione con Power BI

Per la creazione di report sui costi, è anche possibile usare Power BI. Il [connettore Gestione costi di Azure](/power-bi/desktop-connect-azure-cost-management) per Power BI Desktop può essere usato per creare report avanzati e personalizzati che consentono di comprendere meglio la spesa di Azure. Il connettore Gestione costi di Azure attualmente supporta i clienti con Contratto del cliente Microsoft o Contratto Enterprise.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come monitorare e controllare la spesa di Azure, vedere [Documentazione di Gestione costi](../index.yml). Oppure se si vuole ottimizzare l'uso delle risorse con gestione costi.
