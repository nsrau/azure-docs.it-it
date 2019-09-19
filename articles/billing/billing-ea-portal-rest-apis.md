---
title: API REST Azure Enterprise
description: Questo articolo descrive le API REST da usare con la registrazione EA di Azure.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 4b56ef09525473920c5f5925485aae9c793ecc04
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900863"
---
# <a name="azure-enterprise-rest-apis"></a>API REST Azure Enterprise

Questo articolo descrive le API REST da usare con la registrazione EA di Azure. Spiega anche come risolvere i problemi comuni con le API REST.

## <a name="consumption-and-usage-apis"></a>API di utilizzo e consumo

I clienti di Microsoft Azure Enterprise possono ottenere informazioni sull'utilizzo e la fatturazione tramite le API REST. Il proprietario del ruolo (amministratore dell'organizzazione, amministratore del reparto, proprietario dell'account) deve abilitare l'accesso all'API generando una chiave da Azure EA Portal. Quindi, chiunque abbia il numero e la chiave di registrazione può accedere ai dati tramite l'API.

### <a name="available-apis"></a>API disponibili

**Saldo e riepilogo**: l'[API Saldo e riepilogo](billing-enterprise-api-balance-summary.md) offre un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per il servizio Azure Marketplace, rettifiche e addebiti per eccedenza. Per altre informazioni, vedere [API di creazione report per clienti Enterprise - Saldo e riepilogo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary).

**Dettagli utilizzo**: l'[API Dettagli utilizzo](billing-enterprise-api-usage-detail.md) offre un'analisi giornaliera dettagliata delle quantità utilizzate e degli addebiti stimati in relazione a una registrazione. Il risultato include anche informazioni su istanze, contatori e reparti. È possibile eseguire query sull'API in base al periodo di fatturazione o a una data di inizio e di fine specificata. Per altre informazioni, vedere [API di creazione di report per i clienti Enterprise - Dettagli utilizzo](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

**Addebito di Marketplace Store**: l'[API Addebito di Marketplace Store](billing-enterprise-api-marketplace-storecharge.md) restituisce gli addebiti giornalieri dettagliati in base all'utilizzo per il Marketplace per il periodo di fatturazione specificato o per le date di inizio e fine indicate. Per altre informazioni, vedere [API di creazione report per clienti Enterprise - Addebito di Marketplace Store](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge).

**Elenco prezzi**: l'[API Elenco prezzi](/billing-enterprise-api-pricesheet.md) fornisce la tariffa applicabile per ogni contatore per un periodo di registrazione e fatturazione. Per altre informazioni, vedere [API di creazione di report per clienti Enterprise - Elenco prezzi](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet).

**Periodi di fatturazione**: l'[API Periodi di fatturazione](billing-enterprise-api-billing-periods.md) restituisce un elenco di periodi di fatturazione contenenti i dati di consumo per una registrazione in ordine cronologico inverso. Ogni periodo contiene una proprietà che punta alla route API per i quattro set di dati, ovvero BalanceSummary, UsageDetails, MarketplaceCharges e PriceSheet. Per altre informazioni, vedere [API di creazione report per clienti Enterprise - Periodi di fatturazione](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods).

### <a name="enable-api-data-access"></a>Abilitare l'accesso ai dati API

I proprietari del ruolo possono seguire queste procedure in Azure EA Portal. Passare a **Report** > **Scarica utilizzo** > **API Access Key** (Chiave di accesso API). È quindi possibile:

- Generare chiavi di accesso primarie e secondarie.
- Disabilitare le chiavi di accesso.
- Visualizzare le date di inizio e di fine delle chiavi di accesso.

### <a name="generate-or-retrieve-the-api-key"></a>Generare o recuperare la chiave API

1. Accedere come amministratore dell'organizzazione.
2. Fare clic su **Report** nella finestra di spostamento a sinistra e quindi fare clic sulla scheda **Scarica utilizzo**.
3. Fare clic su **API Access Key** (Chiave di accesso API).
4. In **Enrollment Access Keys** (Chiavi di accesso registrazione) selezionare il simbolo di generazione chiave per generare una chiave primaria o secondaria.
5. Selezionare **Expand Key** (Espandi chiave) per visualizzare l'intera chiave di accesso API generata.
6. Selezionare **Copia** per ottenere la chiave di accesso API per l'uso immediato.

![Esempio della pagina della chiave di accesso API](./media/billing-ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

Se si vuole assegnare le chiavi di accesso API a utenti che non sono amministratori dell'organizzazione nella registrazione, seguire questa procedura:

1. Nella finestra di spostamento sinistra fare clic su **Gestisci**.
2. Fare clic sul simbolo della matita accanto a **DA view charges** (Visualizzazione addebiti per amministratori di reparto).
3. Selezionare **Abilita** e fare clic su **Salva**.
4. Fare clic sul simbolo della matita accanto a **AO view charges** (Visualizzazione addebiti per proprietari dell'account).
5. Selezionare **Abilita** e fare clic su **Salva**.

![Esempio che mostra la visualizzazione addebiti abilitata per amministratori di reparto e proprietari dell'account](./media/billing-ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) Le procedure descritte sopra consentono ai titolari di chiavi di accesso API di accedere alle informazioni sui costi e sui prezzi nei report sull'utilizzo.

### <a name="pass-keys-in-the-api"></a>Passare le chiavi nell'API

Passare la chiave API per ogni chiamata per l'autenticazione e l'autorizzazione. Passare la proprietà seguente alle intestazioni HTTP:

| Chiave intestazione necessaria | Valore |
| --- | --- |
| Authorization | Specificare il valore nel formato: **bearer {API\_KEY}**
Esempio: bearer \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

Un endpoint Swagger è disponibile alla pagina [Enterprise Reporting v3 APIs](https://consumption.azure.com/swagger/ui/index) (API di creazione di report per clienti Enterprise v3) per le API seguenti. Swagger consente di esaminare l'API più facilmente. Usare Swagger per generare SDK client usando [AutoRest](https://github.com/Azure/AutoRest) o [Swagger CodeGen](http://swagger.io/swagger-codegen/). I dati disponibili dopo il 1° maggio 2014 sono disponibili tramite l'API.

### <a name="api-response-codes"></a>Codici di risposta API

Quando si usa un'API, vengono visualizzati i codici di stato della risposta. La tabella seguente descrive questi codici.

| Codice di stato della risposta | Message | DESCRIZIONE |
| --- | --- | --- |
| 200 | OK | Nessun errore |
| 401 | Non autorizzata | Chiave API non trovata, non valida, scaduta e così via |
| 404 | Non disponibile | Endpoint del report non trovato |
| 400 | Bad Request | Parametri non validi (intervalli di date, numeri EA e così via) |
| 500 | Errore del server | Errore imprevisto nell'elaborazione della richiesta |

### <a name="usage-and-billing-data-update-frequency"></a>Frequenza di aggiornamento dei dati di utilizzo e fatturazione

I file dei dati di utilizzo e di fatturazione vengono aggiornati ogni 24 ore per il mese di fatturazione corrente. Può tuttavia verificarsi una latenza dei dati fino a un massimo di tre giorni. Se, ad esempio, l'utilizzo ha avuto luogo il lunedì, i dati potrebbero non essere visualizzati nel file di dati fino a giovedì.

### <a name="test-enrollment-for-development"></a>Testare la registrazione per lo sviluppo

Se si è un partner o uno sviluppatore senza una registrazione EA di Azure e si vuole accedere all'API, è possibile usare la registrazione di test. Il nome della registrazione è _EnrollmentNumber 100_. È quindi possibile [eseguire il test con la chiave di accesso della registrazione](https://automaticbillingspec.blob.core.windows.net/spec/TestEnrollmentUsageApiKey.txt) per chiamare l'API e visualizzare i dati di esempio.

### <a name="azure-service-catalog"></a>Catalogo dei servizi di Azure

Tutti i servizi di Azure vengono pubblicati in un catalogo in formato CSV in un BLOB di Archiviazione di Azure. Il catalogo è utile se occorre creare un catalogo curato di tutti i servizi di Azure per il sistema. Il catalogo corrente è disponibile all'indirizzo [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv).

### <a name="csv-data-file-details"></a>Dettagli del file di dati CSV

Le informazioni seguenti descrivono le proprietà dei report sulle API.

#### <a name="usage-summary"></a>Riepilogo utilizzo

Il formato JSON viene generato dal report CSV. Di conseguenza, il formato è uguale al formato CSV di riepilogo. Il nome della colonna viene maneggiato, quindi è consigliabile eseguire la deserializzazione in una tabella dati quando si utilizzano i dati di riepilogo JSON.

| Nome colonna CSV | Nome colonna JSON | Nuova colonna JSON | Comment |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| Nome account | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| Nome sottoscrizione | SubscriptionName | SubscriptionName |   |
| Data | Data | Data | Indica la data di esecuzione del report del catalogo di servizi. Il formato è una stringa di data senza timestamp. |
| Mese | Mese | Mese |   |
| Giorno | Giorno | Giorno |   |
| Year | Year | Year |   |
| Prodotto | BillableItemName | Prodotto |   |
| ID misuratore | ResourceGUID | ID contatore |   |
| Categoria misuratore | Service | MeterCategory | È utile per trovare i servizi. Pertinente per i servizi con più ServiceType. Esempio: Macchine virtuali. |
| Sottocategoria misuratore | ServiceType | MeterSubCategory | Fornisce un secondo livello di dettagli per un servizio. Esempio: VM A1 (non Windows).  |
| Area misuratore | ServiceRegion | MeterRegion | Terzo livello di dettaglio richiesto per un servizio. Utile per trovare il contesto dell'area di ResourceGUID. |
| Nome misuratore | ServiceResource | MeterName | Il nome del servizio. |
| Quantità consumata | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| Percorso della risorsa | ServiceSubRegion | ResourceLocation |   |
| Servizio utilizzato | ServiceInfo | ConsumedService |   |
| ID istanza | Componente | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Tag | Tag | Tag |   |
| Identificatore del servizio di archiviazione   | OrderNumber | StoreServiceIdentifier   |   |
| Department Name | DepartmentName | DepartmentName |   |
| Cost Center | CostCenter | CostCenter |   |
| Unità di misura | UnitOfMeasure | UnitOfMeasure | Valori di esempio: Hours, GB, Events, Pushes, Unit, Unit Hours, MB, Daily Units |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Report di Azure Marketplace

| Nome colonna CSV | Nome colonna JSON | Nuova colonna JSON |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| Nome account | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| Nome sottoscrizione | SubscriptionName |  SubscriptionName |
| Data | BillingCycle |  Date (solo stringa di data, senza timestamp)
| Mese | Mese |  Mese |
| Giorno | Giorno |  Giorno |
| Year | Year |  Year |
| ID misuratore | MeterResourceId |  ID contatore |
| Nome entità di pubblicazione | PublisherFriendlyName |  PublisherName |
| Nome offerta | OfferFriendlyName |  OfferName |
| Plan Name | PlanFriendlyName |  PlanName |
| Quantità consumata | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| Unità di misura | UnitOfMeasure | UnitOfMeasure |
| ID istanza | InstanceId | InstanceId |
| Informazioni aggiuntive | AdditionalInfo | AdditionalInfo |
| Tag | Tag | Tag |
| Order Number | OrderNumber | OrderNumber |
| Department Name | DepartmentNames | DepartmentName |
| Cost Center | CostCenters |  CostCenter |
| Gruppo di risorse | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>Elenco prezzi

| Nome colonna CSV | Nome colonna JSON | Comment |
| --- | --- | --- |
| Service | Service |  Nessuna modifica al prezzo |
| Unità di misura | UnitOfMeasure |   |
| Overage Part Number | ConsumptionPartNumber |   |
| Overage Unit Price | ConsumptionPrice |   |
| Codice valuta | CurrencyCode |     |

### <a name="common-api-issues"></a>Problemi comuni delle API

Durante l'uso delle API REST Azure Enterprise si potrebbe riscontrare uno dei problemi comuni seguenti.

È possibile che si stia cercando di usare una chiave API che non ha il tipo di autorizzazione corretto. Le chiavi API vengono generate da:

- Amministratore dell'organizzazione
- Amministratore del reparto
- Proprietario dell'account

Una chiave generata dall'amministratore EA consente l'accesso a tutte le informazioni relative alla registrazione corrispondente. Un amministratore EA in sola lettura non può generare una chiave API.

Una chiave generata da un amministratore del reparto o un proprietario dell'account non fornisce l'accesso alle informazioni relative a saldo, addebito ed elenco prezzi.

Una chiave API scade ogni sei mesi. Una volta scaduta, è necessario rigenerarla.

Se si riceve un errore di timeout, è possibile risolverlo aumentando il limite di soglia di timeout.

È possibile che venga visualizzato un errore di scadenza 401 (non autorizzato). L'errore è in genere causato da una chiave scaduta. Se la chiave è scaduta, è possibile rigenerarla.

Se non sono disponibili dati correnti per l'intervallo di date selezionato, si potrebbero ricevere gli errori 400 e 404 (non disponibile) restituiti da una chiamata API. Ad esempio, questo errore potrebbe verificarsi perché un trasferimento di registrazione è stato avviato di recente. I dati a partire da una data specifica si trovano ora in una nuova registrazione. L'errore potrebbe altrimenti verificarsi se si usa un nuovo numero di registrazione per recuperare le informazioni che si trovano in una registrazione precedente.

## <a name="next-steps"></a>Passaggi successivi

- È consigliabile che gli amministratori di Azure EA Portal leggano [Amministrazione di Azure EA Portal](billing-ea-portal-administration.md) per informazioni sulle comuni attività amministrative.
- Se serve assistenza per la risoluzione dei problemi relativi ad Azure EA Portal, vedere [Risolvere i problemi di accesso ad Azure EA Portal](billing-ea-portal-troubleshoot.md).
