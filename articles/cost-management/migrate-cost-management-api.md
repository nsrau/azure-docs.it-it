---
title: Eseguire la migrazione da Enterprise Agreement alle API del contratto clienti Microsoft-Azure | Microsoft Docs
description: Questo articolo consente di comprendere le conseguenze della migrazione di un Enterprise Agreement Microsoft (EA) a un contratto per i clienti Microsoft.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 5c2041984ffa2c455ea4d60a756fcb4142219d91
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981449"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>Eseguire la migrazione da Enterprise Agreement alle API del contratto clienti Microsoft

Questo articolo consente di comprendere la struttura dei dati, l'API e altre differenze nell'integrazione del sistema tra Enterprise Agreement (EA) e gli account Microsoft Customer Agreement (MCA). Gestione costi di Azure supporta le API per entrambi i tipi di account. Prima di continuare, vedere l'articolo [configurare l'account di fatturazione per](../billing/billing-mca-setup-account.md) il contratto clienti Microsoft.

Le organizzazioni con un account EA esistente dovrebbero rivedere questo articolo insieme alla configurazione di un account MCA. In precedenza, il rinnovo di un account EA richiedeva un lavoro minimo per passare da una registrazione precedente a una nuova. Tuttavia, la migrazione a un account MCA richiede un ulteriore sforzo. Ulteriori sforzi sono dovuti a modifiche nel sottosistema di fatturazione sottostante, che interessano tutte le API correlate ai costi e le offerte di servizio.

## <a name="mca-apis-and-integration"></a>API e integrazione di MCA

Le API MCA e la nuova integrazione consentono di:

- Disponibilità completa dell'API tramite le API native di Azure.
- Configurare più fatture in un unico account di fatturazione.
- Accedi a un'API combinata con l'utilizzo dei servizi di Azure, l'utilizzo del Marketplace di terze parti e gli acquisti nel Marketplace.
- Visualizzare i costi nei profili di fatturazione (le stesse registrazioni) usando Gestione costi di Azure.
- Accedi alle nuove API per visualizzare i costi, ricevi notifiche quando i costi superano le soglie predefinite ed Esporta automaticamente i dati non elaborati.

## <a name="migration-checklist"></a>Elenco di controllo migrazione

Gli elementi seguenti consentono di eseguire la transizione alle API MCA.

- Familiarizzare con il nuovo [account di fatturazione del contratto clienti Microsoft](../billing/billing-mca-overview.md).
- Determinare le API usate e vedere quali sono state sostituite nella sezione seguente.
- Acquisire familiarità con le [API REST di Azure Resource Manager](/rest/api/azure).
- Se non si usano già Azure Resource Manager API, [registrare l'app client con Azure ad](/rest/api/azure/#register-your-client-application-with-azure-ad).
- Aggiornare il codice di programmazione per [usare l'autenticazione Azure ad](/rest/api/azure/#create-the-request).
- Aggiornare qualsiasi codice di programmazione per sostituire le chiamate API EA con le chiamate all'API MCA.
- Aggiornamento della gestione degli errori per l'utilizzo di nuovi codici di errore.
- Per altre azioni necessarie, esaminare le offerte di integrazione aggiuntive, ad esempio Cloudyn e Power BI.

## <a name="ea-apis-replaced-with-mca-apis"></a>API EA sostituite con le API MCA

Le API EA usano una chiave API per l'autenticazione e l'autorizzazione. Le API MCA usano l'autenticazione Azure AD.

| Scopo | API EA | API MCA |
| --- | --- | --- |
| Saldo e crediti | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| Utilizzo (JSON) | [/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) [/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilizzo (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Utilizzo del Marketplace (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) [/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Periodi di fatturazione | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Elenco prezzi | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft. Billing/billingAccounts/billingProfiles/pricesheets/default/download format = JSON|CSV Microsoft. Billing/billingAccounts/.../billingProfiles/.../fatture/... formato/Pricesheet/default/download = JSON|CSV Microsoft. Billing/billingAccounts/.. /billingProfiles/.. /providers/Microsoft.Consumption/pricesheets/download  |
| Acquisti prenotazione | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| Consigli sulle prenotazioni | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft. consumer/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| Utilizzo prenotazione | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details) [/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft. consumer/reservationDetails](/rest/api/consumption/reservationsdetails) [Microsoft. consumer/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> il servizio di Azure e l'utilizzo del Marketplace di terze parti sono disponibili con l' [API dettagli utilizzo](/rest/api/consumption/usagedetails).

Per gli account di fatturazione MCA sono disponibili le API seguenti:

| Scopo | API Microsoft Customer Agreement (MCA) |
| --- | --- |
| Account di fatturazione<sup>2</sup> | Microsoft.Billing/billingAccounts |
| Profili di fatturazione<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| Sezioni fattura<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| Fatture | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| Sottoscrizioni di fatturazione | {Scope}/billingSubscriptions |

<sup>2</sup> le API restituiscono elenchi di oggetti, ovvero ambiti, in cui le esperienze di gestione dei costi nel portale di Azure e nelle API operano. Per ulteriori informazioni sugli ambiti di gestione dei costi, vedere [comprendere e utilizzare](understand-work-scopes.md)gli ambiti.

Se si usano le API EA esistenti, è necessario aggiornarle per supportare gli account di fatturazione MCA. Nella tabella seguente vengono illustrate le altre modifiche di integrazione:

| Scopo | Offerta precedente | Nuova offerta |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Gestione costi di Azure](https://azure.microsoft.com/services/cost-management/) |
| Power BI | Pacchetto di contenuto e connettore [Microsoft consume Insights](/power-bi/desktop-connect-azure-consumption-insights) | [Informazioni dettagliate sul consumo di Microsoft Azure Power bi app](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview) e [informazioni dettagliate sul consumo di Azure Connector](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>API per ottenere bilanciamento e crediti

L'API [Get Balance Summary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) fornisce un riepilogo mensile di:

- Saldi
- Nuovi acquisti
- Addebiti per il servizio Azure Marketplace
- Modifiche
- Addebiti per eccedenza del servizio

Tutte le API a consumo sono sostituite dalle API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulla chiamata delle API REST di Azure, vedere [Introduzione a Rest](/rest/api/azure/#create-the-request).

L'API di riepilogo Get Balance viene sostituita dall'API Microsoft. Billing/billingAccounts/billingProfiles/availableBalance.

Per ottenere i saldi disponibili con l'API Balance disponibile:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>API per ottenere i costi e l'utilizzo

Ottieni una ripartizione giornaliera dei costi di utilizzo del servizio di Azure, dell'utilizzo del Marketplace di terze parti e di altri acquisti del Marketplace con le API seguenti. Le API separate seguenti sono state unite per i servizi di Azure e l'utilizzo del Marketplace di terze parti. Le API precedenti vengono sostituite dall'API [Microsoft. consumer/usageDetails](/rest/api/consumption/usagedetails) . Aggiunge gli acquisti del Marketplace, che in precedenza venivano visualizzati solo nel riepilogo del saldo fino alla data.

- [Ottenere dettagli sull'utilizzo/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Ottieni dettagli sull'utilizzo/Invia](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [Ottenere i dettagli di utilizzo/UsageDetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Ottenere i dettagli di utilizzo/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [Ottieni addebito per l'archivio del Marketplace/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [Ottieni addebito per l'archivio del Marketplace/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

Tutte le API a consumo sono sostituite dalle API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulla chiamata delle API REST di Azure, vedere [Introduzione a Rest](/rest/api/azure/#create-the-request).

Tutte le API precedenti vengono sostituite dall'API consumo/dettagli utilizzo.

Per ottenere i dettagli di utilizzo con l'API dettagli di utilizzo:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

L'API dettagli di utilizzo, come per tutte le API di gestione costi, è disponibile in più ambiti. Per i costi fatturati, come si riceve tradizionalmente a livello di registrazione, usare l'ambito del profilo di fatturazione.  Per ulteriori informazioni sugli ambiti di gestione dei costi, vedere [comprendere e utilizzare](understand-work-scopes.md)gli ambiti.

| Type | Formato ID |
| --- | --- |
| Account di fatturazione | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| Profilo di fatturazione | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| Sottoscrizione | `/subscriptions/{subscriptionId}` |
| Gruppo di risorse | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

Usare i seguenti parametri QueryString per aggiornare qualsiasi codice di programmazione.

| Parametri obsoleti | Nuovi parametri |
| --- | --- |
| `billingPeriod={billingPeriod}` | Non supportate |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

Anche il corpo della risposta è stato modificato.

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

Il nome della proprietà contenente la matrice di record di utilizzo è stato modificato dai dati ai _valori_. Ogni record usato per avere un elenco semplice di proprietà dettagliate. Tuttavia, ogni record ora tutti i dettagli sono ora in una proprietà annidata denominata _Properties_, ad eccezione dei tag. La nuova struttura è coerente con altre API di Azure. Alcuni nomi di proprietà sono stati modificati. Nella tabella seguente vengono illustrate le proprietà corrispondenti.

| Proprietà precedente | Nuova proprietà | Note |
| --- | --- | --- |
| AccountId | N/D | L'autore della sottoscrizione non viene registrato. Usare invoiceSectionId (uguale a departmentId). |
| AccountNameAccountOwnerId e indirizzo | N/D | L'autore della sottoscrizione non viene registrato. Usare invoiceSectionName (uguale a DepartmentName). |
| Informazioni aggiuntive | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | Si noti che queste proprietà sono opposte. Se isAzureCreditEnabled è true, ChargesBilledSeparately sarà false. |
| Quantità utilizzata | quantity | &nbsp; |
| ConsumedService | consumedService | I valori stringa esatti potrebbero essere diversi. |
| ConsumedServiceId | Nessuna | &nbsp; |
| Centro di costo | costCenter | &nbsp; |
| Data e usageStartDate | date | &nbsp;  |
| Giorno | Nessuna | Analizza il giorno dalla data. |
| DepartmentId | invoiceSectionId | I valori esatti sono diversi. |
| Nome del reparto | invoiceSectionName | I valori stringa esatti potrebbero essere diversi. Configurare le sezioni della fattura in modo che corrispondano ai reparti, se necessario. |
| Costo esteso e costo | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| È un addebito ricorrente | Nessuna | &nbsp;  |
| Location | location | &nbsp;  |
| Categoria del contatore | meterCategory | I valori stringa esatti potrebbero essere diversi. |
| ID del contatore | meterId | I valori stringa esatti sono diversi. |
| Nome del contatore | meterName | I valori stringa esatti potrebbero essere diversi. |
| Area del contatore | meterRegion | I valori stringa esatti potrebbero essere diversi. |
| Sottocategoria del contatore | meterSubCategory | I valori stringa esatti potrebbero essere diversi. |
| Mese | Nessuna | Analizza il mese da data. |
| Nome offerta | Nessuna | Usare PublisherName e productOrderName. |
| OfferId | Nessuna | &nbsp;  |
| Numero di ordine | Nessuna | &nbsp;  |
| PartNumber | Nessuna | Usare ID contatore e productOrderName per identificare in modo univoco i prezzi. |
| Nome piano | productOrderName | &nbsp;  |
| Prodotto | Prodotto |   |
| ID prodotto | productId | I valori stringa esatti sono diversi. |
| Nome autore | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | I valori stringa esatti sono diversi. |
| Posizione della risorsa | resourceLocation | &nbsp;  |
| ResourceLocationId | Nessuna | &nbsp;  |
| Tariffa per la risorsa | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N/D | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| Informazioni sul servizio 2 | serviceInfo2 | &nbsp;  |
| Nome del servizio | meterCategory | I valori stringa esatti potrebbero essere diversi. |
| ServiceTier | meterSubCategory | I valori stringa esatti potrebbero essere diversi. |
| StoreServiceIdentifier | N/D | &nbsp;  |
| GUID della sottoscrizione | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| Nome della sottoscrizione | subscriptionName | &nbsp;  |
| Tag | tags | La proprietà Tags si applica all'oggetto radice, non alla proprietà nidificata Properties. |
| Unità di misura | unitOfMeasure | I valori stringa esatti sono diversi. |
| usageEndDate | date | &nbsp;  |
| Year | Nessuna | Analizza l'anno dalla data. |
| nuovo | billingCurrency | Valuta utilizzata per l'addebito. |
| nuovo | billingProfileId | ID univoco per il profilo di fatturazione (corrispondente alla registrazione). |
| nuovo | billingProfileName | Nome del profilo di fatturazione (corrispondente alla registrazione). |
| nuovo | chargeType | Usare per distinguere l'utilizzo del servizio di Azure, l'utilizzo del Marketplace e gli acquisti. |
| nuovo | invoiceId | ID univoco per la fattura. Empty per il mese aperto corrente. |
| nuovo | publisherType | Tipo di server di pubblicazione per gli acquisti. Vuoto per l'utilizzo. |
| nuovo | serviceFamily | Tipo di acquisto. Vuoto per l'utilizzo. |
| nuovo | servicePeriodEndDate | Data di fine per il servizio acquistato. |
| nuovo | servicePeriodStartDate | Data di inizio per il servizio acquistato. |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>API periodi di fatturazione sostituita dall'API fatture

Gli account di fatturazione MCA non usano i periodi di fatturazione. Usano invece le fatture per limitare l'ambito dei costi a periodi di fatturazione specifici. L' [API periodi di fatturazione](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) viene sostituita dall'API fatture. Tutte le API a consumo sono sostituite dalle API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulla chiamata delle API REST di Azure, vedere [Introduzione a Rest](/rest/api/azure/#create-the-request).

Per ottenere le fatture con l'API fatture:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>API elenco prezzi

In questa sezione vengono descritte le API degli elenco prezzi esistenti e vengono fornite indicazioni per passare all'API Listino prezzi per i contratti con i clienti Microsoft. Viene inoltre illustrata l'API elenco prezzi per i contratti con i clienti Microsoft e vengono illustrati i campi negli elenchi prezzi. Le API per l' [elenco dei prezzi](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) delle aziende Get e i [periodi di fatturazione Enterprise Get](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) sono sostituite dall'API dell'elenco prezzi per i contratti dei clienti Microsoft (Microsoft. Billing/billingAccounts/billingProfiles/pricesheets). La nuova API supporta i formati JSON e CSV, in formati REST asincroni. Tutte le API a consumo sono sostituite dalle API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulla chiamata delle API REST di Azure, vedere [Introduzione a Rest](/rest/api/azure/#create-the-request).

### <a name="billing-enterprise-apis"></a>API Enterprise per la fatturazione

Sono state usate le API Enterprise per la fatturazione con le registrazioni Enterprise per ottenere informazioni sui prezzi e sul periodo di fatturazione. Autenticazione e autorizzazione usati Azure Active Directory token Web.

Per ottenere i prezzi applicabili per la registrazione Enterprise specificata con l'elenco prezzi e le API del periodo di fatturazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>API elenco prezzi per contratti con i clienti Microsoft

Usare l'API elenco prezzi per i contratti con i clienti Microsoft per visualizzare i prezzi per tutti i servizi di consumo di Azure e di consumo del Marketplace. I prezzi indicati per il profilo di fatturazione si applicano a tutte le sottoscrizioni appartenenti al profilo di fatturazione.

Usare l'API elenco prezzi per visualizzare tutti i dati dell'elenco prezzi dei servizi di consumo di Azure in formato CSV:

| Metodo | URI della richiesta |
| --- | --- |
| INSERISCI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

Usare l'API elenco prezzi per visualizzare tutti i dati dell'elenco prezzi dei servizi di consumo di Azure in formato JSON:

| Metodo | URI della richiesta |
| --- | --- |
| INSERISCI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

L'uso dell'API restituisce l'elenco prezzi per l'intero account. Tuttavia, è anche possibile ottenere una versione condensata dell'elenco prezzi in formato PDF. Il riepilogo include il consumo di Azure e i servizi di consumo del Marketplace fatturati per una fattura specifica. La fattura è identificata da {invoiceId}, che corrisponde al **numero di fattura** mostrato nei file PDF di riepilogo della fattura. Ecco un esempio.

![Immagine di esempio che mostra il numero di fattura corrispondente a InvoiceId](./media/migrate-cost-management-api/invoicesummary.png)

Per visualizzare le informazioni sulla fattura con l'API elenco prezzi in formato CSV:

| Metodo | URI della richiesta |
| --- | --- |
| INSERISCI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Per visualizzare le informazioni sulla fattura con l'API elenco prezzi in formato JSON:

| Metodo | URI della richiesta |
| --- | --- |
| INSERISCI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

È anche possibile visualizzare i prezzi stimati per qualsiasi servizio di consumo di Azure o di consumo del Marketplace nel ciclo di fatturazione aperto o nel periodo di servizio corrente.

Per visualizzare i prezzi stimati per i servizi di consumo con l'API elenco prezzi in formato CSV:

| Metodo | URI della richiesta |
| --- | --- |
| INSERISCI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

Per visualizzare i prezzi stimati per i servizi di consumo con l'API elenco prezzi in formato JSON:

| Metodo | URI della richiesta |
| --- | --- |
| INSERISCI | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Le API di elenco prezzi del contratto clienti Microsoft sono *API REST asincrone*. Le risposte per le API sono state modificate rispetto alle API sincrone precedenti. È stato modificato anche il corpo della risposta API.

#### <a name="old-response-body"></a>Corpo della risposta precedente

Di seguito è riportato un esempio della risposta all'API REST sincrona:

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

Le API supportano il formato [asincrono REST di Azure](../azure-resource-manager/resource-manager-async-operations.md) . Chiamare l'API usando GET e si riceve la risposta seguente:

```
No Response Body

HTTP Status 202 Accepted
```

Le intestazioni seguenti vengono inviate con il percorso dell'output:

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

Eseguire un'altra chiamata GET al percorso. La risposta alla chiamata GET è identica fino a quando l'operazione non raggiunge uno stato di completamento o di errore. Al termine, la risposta al percorso GET Call restituisce l'URL di download. Come se l'operazione venisse eseguita nello stesso momento. Di seguito è riportato un esempio:

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

Il client può anche effettuare una chiamata GET per l' `Azure-AsyncOperation`oggetto. L'endpoint restituisce lo stato dell'operazione.

La tabella seguente illustra i campi nell'API per la visualizzazione dell'elenco di prezzi Enterprise Get precedente. Include i campi corrispondenti nel nuovo elenco prezzi per i contratti con i clienti Microsoft:

| Proprietà precedente | Nuova proprietà | Note |
| --- | --- | --- |
| billingPeriodId  | _Non applicabile_ | Non applicabile. Per i contratti con i clienti Microsoft, la fattura e l'elenco prezzi associato hanno sostituito il concetto di billingPeriodId. |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | I valori stringa esatti potrebbero essere diversi. |
| includedQuantity  | includedQuantity | Non applicabile per i servizi nei contratti con i clienti Microsoft. |
| partNumber  | _Non applicabile_ | Usare invece una combinazione di productOrderName (uguale a IDOfferta) e ID contatore. |
| unitPrice  | unitPrice | Il prezzo unitario è applicabile ai servizi utilizzati nei contratti con i clienti Microsoft. |
| currencyCode  | pricingCurrency | I contratti dei clienti Microsoft dispongono di rappresentazioni dei prezzi per la valuta e la valuta di fatturazione. Il currencyCode corrisponde a pricingCurrency nei contratti con i clienti Microsoft. |
| offerId | productOrderName | Anziché IDOfferta, è possibile usare productOrderName ma non è uguale a IDOfferta. Tuttavia, productOrderName e Meter determinano i prezzi nei contratti con i clienti Microsoft correlati a ID contatore e IDOfferta nelle registrazioni legacy. |

## <a name="consumption-price-sheet-api-operations"></a>Operazioni dell'API elenco prezzi a consumo

Per i contratti Enterprise, è stata usata l'API elenco prezzi consumo [Get](/rest/api/consumption/pricesheet/get) e [Get per periodo di fatturazione](/rest/api/consumption/pricesheet/getbybillingperiod) per un ambito SubscriptionId o un periodo di fatturazione. L'API usa l'autenticazione di Azure Resource Manager.

Per ottenere le informazioni dell'elenco prezzi per un ambito con l'API dell'elenco prezzi:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Per ottenere informazioni sugli elenco prezzi per periodo di fatturazione con l'API elenco prezzi:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

Anziché gli endpoint API precedenti, usare i seguenti per i contratti con i clienti Microsoft:

**API elenco prezzi per i contratti dei clienti Microsoft (API REST asincrona)**

Questa API è destinata ai contratti con i clienti Microsoft e fornisce attributi aggiuntivi.

**Elenco prezzi per un ambito del profilo di fatturazione in un account di fatturazione**

Questa API è l'API esistente. È stato aggiornato per fornire l'elenco prezzi per un profilo di fatturazione in un account di fatturazione.

## <a name="price-sheet-for-a-scope-by-billing-account"></a>Elenco prezzi per un ambito per account di fatturazione

Azure Resource Manager autenticazione viene usata quando si ottiene l'elenco prezzi nell'ambito della registrazione in un account di fatturazione.

Per ottenere l'elenco prezzi dell'account di registrazione in un account di fatturazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Per un contratto per i clienti Microsoft, usare le informazioni nella sezione seguente. Fornisce le proprietà dei campi utilizzate per i contratti con i clienti Microsoft.

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>Elenco prezzi per un ambito del profilo di fatturazione in un account di fatturazione

L'elenco prezzi aggiornato tramite l'API dell'account di fatturazione Ottiene l'elenco prezzi in formato CSV. Per ottenere l'elenco prezzi nell'ambito del profilo di fatturazione per un MCA:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Nell'ambito della registrazione di EA, la risposta e le proprietà dell'API sono identiche. Le proprietà corrispondono alle stesse proprietà MCA.

Nella tabella seguente sono riportate le proprietà precedenti per le [API dell'elenco Azure Resource Manager prezzi](/rest/api/consumption/pricesheet) e le stesse nuove proprietà.

| Vecchia proprietà API elenco prezzi Azure Resource Manager  | Nuova proprietà API elenco prezzi contratto cliente Microsoft   | Descrizione |
| --- | --- | --- |
| ID contatore | _meterId_ | Identificatore univoco del contatore. Uguale a ID contatore. |
| Nome del contatore | meterName | Nome del contatore. Il contatore rappresenta la risorsa distribuibile del servizio di Azure. |
| Categoria del contatore  | servizio | Nome della categoria di classificazione per il contatore. Come il servizio nell'elenco prezzi del contratto clienti Microsoft. I valori stringa esatti sono diversi. |
| Sottocategoria di contatore | meterSubCategory | Nome della categoria di sottoclassificazione del contatore. In base alla classificazione della differenziazione del set di funzionalità di alto livello nel servizio. Ad esempio, il database SQL di base e il database SQL standard. |
| Area del contatore | meterRegion | &nbsp;  |
| Unità | _Non applicabile_ | Può essere analizzato da misura. |
| Unità di misura | unitOfMeasure | &nbsp;  |
| Codice | _Non applicabile_ | Anziché NumeroArticolo, utilizzare productOrderName e ID contatore per identificare in modo univoco il prezzo di un profilo di fatturazione. I campi vengono elencati nella fattura MCA invece che in NumeroArticolo nelle fatture MCA. |
| Prezzo unitario | unitPrice | Prezzo unitario del contratto clienti Microsoft. |
| Codice valuta | pricingCurrency | I contratti con i clienti Microsoft rappresentano i prezzi nella valuta dei prezzi e della fatturazione. Il codice di valuta è identico a quello di pricingCurrency nei contratti con i clienti Microsoft. |
| Quantità inclusa | includedQuantity | Non applicabile ai servizi nei contratti con i clienti Microsoft. Mostra con valori pari a zero. |
|  ID offerta  | productOrderName | Anziché IDOfferta, utilizzare productOrderName. Non è uguale a IDOfferta, tuttavia il productOrderName e il contatore determinano i prezzi nei contratti dei clienti Microsoft. Correlato a ID contatore e IDOfferta nelle registrazioni legacy. |

Il prezzo per i contratti con i clienti Microsoft è definito in modo diverso rispetto ai contratti Enterprise. Il prezzo per i servizi nell'iscrizione Enterprise è univoco per il prodotto, il NumeroArticolo, il contatore e l'offerta. Il NumeroArticolo non viene utilizzato nei contratti con i clienti Microsoft.

Il prezzo del servizio di consumo di Azure che fa parte di un contratto cliente Microsoft è univoco per productOrderName e ID contatore. Rappresentano il contatore del servizio e il piano del prodotto.

Per risolvere le differenze tra l'elenco prezzi e l'utilizzo nell'API dei dettagli di utilizzo, è possibile usare productOrderName e ID contatore.

Gli utenti che hanno diritti di proprietario, collaboratore, lettore e responsabile del profilo di fatturazione possono scaricare l'elenco prezzi.

L'elenco prezzi include i prezzi per i servizi il cui prezzo è basato sull'utilizzo. I servizi includono il consumo di Azure e il consumo del Marketplace. Il prezzo più recente alla fine di ogni periodo di servizio viene bloccato e applicato all'utilizzo in un singolo periodo di servizio. Per i servizi di consumo di Azure, il periodo di servizio è in genere un mese di calendario.

### <a name="retired-price-sheet-api-fields"></a>Campi API elenco prezzi ritirati

I campi seguenti non sono disponibili nelle API degli elenco prezzi del contratto clienti Microsoft o hanno gli stessi campi.

|Campo ritirato| Descrizione|
|---|---|
| billingPeriodId | Nessun applicabile. Corrisponde a InvoiceId per MCA. |
| offerId | Non applicabile. Corrisponde a productOrderName in MCA. |
| meterCategory  | Non applicabile. Corrisponde al servizio in MCA. |
| unit | Non applicabile. Può essere analizzato da misura. |
| currencyCode | Uguale a pricingCurrency in MCA. |
| meterLocation | Uguale a meterRegion in MCA. |
| NumeroArticolo NumeroArticolo | Non applicabile perché il numero di parte non è elencato in fatture MCA. Anziché NumeroArticolo, usare la combinazione ID contatore e productOrderName per identificare in modo univoco i prezzi. |
| totalIncludedQuantity | Non applicabile. |
| pretaxStandardRate  | Non applicabile. |

## <a name="reservation-instance-charge-api-replaced"></a>API di addebito istanza prenotazione sostituita

È possibile ottenere transazioni di fatturazione per gli acquisti di prenotazione con l' [API riservata](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)di addebito per le istanze. La nuova API include tutti gli acquisti, incluse le offerte di Marketplace di terze parti. Tutte le API a consumo sono sostituite dalle API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulla chiamata delle API REST di Azure, vedere [Introduzione a Rest](/rest/api/azure/#create-the-request). L'API di addebito per le istanze riservate viene sostituita dall'API Transactions.

Per ottenere le transazioni di acquisto della prenotazione con l'API Transactions:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>API raccomandazioni sostituite

Le API raccomandazioni per l'acquisto di istanze riservate forniscono l'utilizzo delle macchine virtuali negli ultimi 7, 30 o 60 giorni. Le API forniscono anche consigli per l'acquisto di prenotazioni. e comprendono:

- [API di raccomandazione per le istanze riservate condivise](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [API consigli per singole istanze riservate](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

Tutte le API a consumo sono sostituite dalle API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulla chiamata delle API REST di Azure, vedere [Introduzione a Rest](/rest/api/azure/#create-the-request). Le API raccomandazioni di prenotazione elencate in precedenza vengono sostituite dall'API [Microsoft. consumer/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) .

Per ottenere raccomandazioni di prenotazione con l'API raccomandazioni per la prenotazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>API utilizzo prenotazione sostituite

È possibile ottenere l'utilizzo della prenotazione in una registrazione con l'API Utilizzo istanze riservate. Se è presente più di un'istanza riservata in una registrazione, è anche possibile usare questa API per ottenere l'utilizzo di tutti gli acquisti di istanze riservate.

e comprendono:

- [Dettagli Utilizzo istanze riservate](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [Riepilogo Utilizzo istanze riservate](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

Tutte le API a consumo sono sostituite dalle API native di Azure che usano Azure AD per l'autenticazione e l'autorizzazione. Per altre informazioni sulla chiamata delle API REST di Azure, vedere [Introduzione a Rest](/rest/api/azure/#create-the-request). Le API raccomandazioni di prenotazione elencate in precedenza vengono sostituite dalle API [Microsoft. consumer/reservationDetails](/rest/api/consumption/reservationsdetails) e [Microsoft. consumer/reservationSummaries](/rest/api/consumption/reservationssummaries) .

Per ottenere i dettagli della prenotazione con l'API dei dettagli della prenotazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

Per ottenere riepiloghi di prenotazione con l'API riepiloghi prenotazione:

| Metodo | URI della richiesta |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>Passare da Cloudyn a gestione costi

Le organizzazioni che usano [Cloudyn](https://cloudyn.com) devono iniziare a usare [Gestione costi di Azure](https://azure.microsoft.com/services/cost-management/) per le esigenze di gestione dei costi. Gestione costi è disponibile nel portale di Azure senza onboarding e una latenza di otto ore. Per ulteriori informazioni, vedere la [documentazione di gestione dei costi](index.yml).

Con gestione costi di Azure è possibile:

- Visualizza i costi nel tempo rispetto a un budget predefinito. Analizzare i modelli di costo giornalieri per identificare e arrestare le anomalie di spesa. Suddividere i costi in base a tag, gruppo di risorse, servizio e località.
- È possibile creare budget per impostare i limiti relativi all'utilizzo e ai costi e ricevere notifiche quando vengono raggiunte le soglie importanti. Configurare l'automazione con i gruppi di azioni per attivare eventi personalizzati e applicare limiti rigidi in base alle proprie esigenze.
- Ottimizzare i costi e l'utilizzo con le raccomandazioni di Azure Advisor. Scopri le ottimizzazioni di acquisto con prenotazioni, ridimensiona le macchine virtuali sottoutilizzate ed Elimina le risorse inutilizzate per rimanere entro i budget.
- Pianificare un'esportazione dei dati relativi a costi e utilizzo per pubblicare un file CSV nell'account di archiviazione ogni giorno. Automatizzare l'integrazione con sistemi esterni per rendere sincronizzati i dati di fatturazione e aggiornati.

## <a name="power-bi-integration"></a>Integrazione con Power BI

Se si usa Power BI per la creazione di report sui costi, è necessario eseguire la transizione a quanto segue:

- App Power BI Informazioni dettagliate sul consumo di Microsoft Azure
- Connettore Informazioni dettagliate sul consumo di Azure desktop


Il connettore è consigliato per le organizzazioni che vogliono avere la massima flessibilità. Tuttavia, l'app Power BI è disponibile anche per la configurazione rapida.

- Installare l' [app Power BI informazioni dettagliate sul consumo di Microsoft Azure](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [Connettersi con il connettore Informazioni dettagliate sul consumo di Azure](/power-bi/desktop-connect-azure-consumption-insights)

Il pacchetto di contenuto e il connettore per le informazioni sul consumo precedenti hanno funzionato a livello di registrazione. È necessario almeno l'accesso in lettura. Per gli utenti del profilo di fatturazione sono disponibili le nuove app Power BI di consumo e il nuovo Informazioni dettagliate sul consumo di Azure connettore. I team che necessitano di opzioni aggiuntive per esaminare i costi o per visualizzare i costi nei profili di fatturazione devono usare nell' [analisi dei costi](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis) l'portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Leggere la [documentazione di gestione dei costi](index.yml) per informazioni su come monitorare e controllare la spesa di Azure. In alternativa, se si vuole ottimizzare l'uso delle risorse con gestione costi.
