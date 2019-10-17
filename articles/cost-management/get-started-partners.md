---
title: Introduzione a gestione costi di Azure per i partner
description: Questo articolo illustra in che modo i partner usano le funzionalità di gestione dei costi di Azure e come permettono l'accesso alla gestione dei costi per i clienti.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 9d95e23cf92c7ee98291831d60088d610c3e5c52
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377694"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Introduzione a gestione costi di Azure per i partner

Gestione costi di Azure è disponibile in modo nativo per i partner che hanno caricato i clienti in un contratto per i clienti Microsoft. Questo articolo illustra in che modo i partner usano le funzionalità di [gestione dei costi di Azure](https://docs.microsoft.com/azure/cost-management/) . Viene inoltre descritto il modo in cui i partner abilitano l'accesso alla gestione dei costi per i clienti. I clienti CSP possono usare le funzionalità di gestione dei costi quando sono abilitate dal partner CSP.

I partner CSP utilizzano Gestione costi per:

- Comprendere i costi fatturati e associare i costi a clienti, sottoscrizioni, gruppi di risorse e servizi.
- Ottieni una visualizzazione intuitiva dei costi di Azure nell' [analisi dei costi](quick-acm-cost-analysis.md) con funzionalità per analizzare i costi per cliente, sottoscrizione, gruppo di risorse, risorse, misuratore, servizio e molte altre dimensioni.
- Visualizzare i costi delle risorse per i quali è stato applicato il credito guadagnato dal partner nell'analisi dei costi.
- Configurare notifiche e automazione usando [budget](tutorial-acm-create-budgets.md) e avvisi a livello di codice quando i costi superano i budget.
- Abilitare i criteri di Azure Resource Manager che forniscono l'accesso dei clienti ai dati di gestione dei costi. I clienti possono quindi visualizzare i dati sui costi di utilizzo per le proprie sottoscrizioni usando [tariffe con pagamento in base al](https://azure.microsoft.com/pricing/calculator/)consumo.

Tutte le funzionalità disponibili in gestione costi di Azure sono disponibili anche con le API REST. Usare le API per automatizzare le attività di gestione dei costi.

## <a name="prerequisites"></a>Prerequisiti

Gestione costi di Azure richiede l'accesso in lettura all'account di fatturazione o alla sottoscrizione. È possibile concedere l'accesso a qualsiasi livello al di sopra delle risorse, dall'account di fatturazione o da un gruppo di gestione a singoli gruppi di risorse in cui si gestiscono le app. Per consentire agli utenti della sottoscrizione di visualizzare i prezzi e i costi, è necessario abilitare l'accesso per visualizzare gli addebiti per l'account di fatturazione. Per altre informazioni sull'abilitazione e l'assegnazione dell'accesso a gestione costi di Azure, vedere [assegnare l'accesso ai dati](assign-access-acm-data.md). Per visualizzare un elenco completo dei tipi di account supportati, vedere [informazioni sui dati di gestione dei costi](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Modalità di utilizzo degli ambiti in gestione costi

Gli ambiti sono la posizione in cui si gestiscono i dati di fatturazione, i ruoli specifici dei pagamenti, la visualizzazione delle fatture e la gestione generale degli account. I ruoli di fatturazione e account vengono gestiti separatamente dagli ambiti usati per la gestione delle risorse, che usano RBAC. Per distinguere chiaramente lo scopo degli ambiti distinti, incluse le differenze di controllo degli accessi, sono denominati rispettivamente ambiti di fatturazione e ambiti RBAC.

Per informazioni sugli ambiti di fatturazione e sugli ambiti RBAC e sul funzionamento della gestione dei costi con gli ambiti, vedere [comprendere e usare gli ambiti](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Gestire i costi con gli ambiti di fatturazione del tenant partner

Dopo aver caricato i clienti in un contratto per i clienti Microsoft, nel tenant sono disponibili gli _ambiti di fatturazione_ seguenti. Usare gli ambiti per gestire i costi nella gestione dei costi.

### <a name="billing-account-scope"></a>Ambito dell'account di fatturazione

Usare l'ambito dell'account di fatturazione per visualizzare i costi pre-fiscali in tutti i clienti e i profili di fatturazione. È inoltre possibile visualizzare i costi delle fatture per i prodotti basati sul consumo per i clienti nel contratto del cliente Microsoft. I costi di fatturazione vengono visualizzati anche per i prodotti basati su acquistato per i clienti nel contratto del cliente Microsoft e l'offerta CSP. Attualmente, la valuta predefinita per visualizzare i costi nell'ambito è il dollaro statunitense. I budget impostati per l'ambito sono anche in USD.

Indipendentemente dalla valuta fatturata dai clienti, i partner usano l'ambito per impostare i budget e gestire i costi in USD tra i clienti, le sottoscrizioni, le risorse e i gruppi di risorse.

I partner filtrano inoltre i costi in una specifica valuta di fatturazione tra i clienti nella visualizzazione analisi dei costi. Selezionare l'elenco dei **costi effettivi** per visualizzare i costi nelle valute di fatturazione del cliente supportate.

![Esempio che mostra la selezione dei costi effettivi per le valute](./media/get-started-partners/actual-cost-selector.png)

Usare la [visualizzazione dei costi ammortizzati](quick-acm-cost-analysis.md#customize-cost-views) negli ambiti di fatturazione per visualizzare i costi ammortizzati dell'istanza riservata in un periodo di prenotazione.

### <a name="billing-profile-scope"></a>Ambito del profilo di fatturazione

Usare l'ambito del profilo di fatturazione per visualizzare i costi pre-imposta nella valuta di fatturazione per tutti i clienti per tutti i prodotti e le sottoscrizioni inclusi in una fattura. È possibile filtrare i costi in un profilo di fatturazione per una fattura specifica usando il filtro **InvoiceID** . Il filtro Mostra il consumo e i costi di acquisto del prodotto per una fattura specifica. È anche possibile filtrare i costi per un cliente specifico sulla fattura per visualizzare i costi pre-fiscali.

Dopo l'onboarding dei clienti a un contratto per i clienti Microsoft, si riceve una fattura del cliente che Mostra gli addebiti per i diritti e i prodotti acquistati, ad esempio SaaS, Azure Marketplace e le prenotazioni. Una volta fatturato nella stessa valuta di fatturazione, la fattura Mostra anche gli addebiti dei clienti che non sono inclusi nel nuovo contratto per i clienti Microsoft.

Per risolvere i costi rispetto alla fattura del cliente, l'ambito del profilo di fatturazione consente di visualizzare tutti i costi accumulati per una fattura per i clienti. Analogamente a quanto avviene per la fattura, l'ambito Mostra i costi di ogni cliente nel nuovo contratto di servizio Microsoft. L'ambito Mostra anche ogni addebito per i prodotti con diritti dei clienti ancora nell'offerta CSP corrente.

Gli ambiti del profilo di fatturazione e dell'account di fatturazione sono gli unici che visualizzano i costi per i prodotti basati su diritti e acquisti.

I profili di fatturazione definiscono le sottoscrizioni incluse in una fattura. I profili di fatturazione sono l'equivalente funzionale di una registrazione con contratto Enterprise Agreement. Una registrazione è l'ambito di generazione delle fatture. Analogamente, gli acquisti che non sono basati sull'utilizzo, ad esempio Azure Marketplace e le prenotazioni, sono disponibili solo nell'ambito del profilo di fatturazione.

Attualmente, la valuta di fatturazione del cliente è la valuta predefinita quando si visualizzano i costi nell'ambito del profilo di fatturazione. I budget impostati nell'ambito del profilo di fatturazione si trovano nella valuta di fatturazione.

I partner possono usare l'ambito per riconciliare le fatture. E usano l'ambito per impostare i budget nella valuta di fatturazione per un:

- Fattura filtrata specifica
- Customer
- Sottoscrizione
- Gruppo di risorse
- Gruppi
- Servizio di Azure
- Misuratore
- ResellerMPNID

### <a name="customer-scope"></a>Ambito del cliente

I partner usano l'ambito per gestire i costi associati ai clienti che vengono caricati nel contratto per i clienti Microsoft. L'ambito consente ai partner di visualizzare i costi pre-fiscali per un cliente specifico. È anche possibile filtrare i costi pre-fiscali per una sottoscrizione, un gruppo di risorse o una risorsa specifica.

L'ambito del cliente non include i clienti che si trovano nell'offerta CSP corrente. I costi per i diritti, non per l'utilizzo di Azure, per i clienti correnti dell'offerta CSP sono disponibili negli ambiti account di fatturazione e profilo di fatturazione quando si applica il filtro del cliente.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Accesso dei partner agli ambiti di fatturazione in gestione costi

Solo gli utenti con ruoli di **amministratore globale** e **amministratore** globale possono gestire e visualizzare i costi per gli account di fatturazione, i profili di fatturazione e i clienti direttamente nel tenant di Azure del partner. Per altre informazioni sui ruoli del centro per i partner, vedere [assegnare utenti ruoli e autorizzazioni](/partner-center/permissions-overview).

### <a name="enable-cost-management-in-the-customer-tenant"></a>Abilitare Gestione costi nel tenant del cliente

I partner possono consentire l'accesso a gestione costi dopo che i clienti sono stati caricati in un contratto per i clienti Microsoft. I partner possono quindi abilitare un criterio che consenta ai clienti di visualizzare i costi calcolati con tariffe al dettaglio con pagamento in base al consumo. I costi vengono visualizzati nella valuta di fatturazione del cliente per l'utilizzo utilizzato negli ambiti di sottoscrizione e gruppi di risorse RBAC.

Quando il criterio per la visibilità dei costi è abilitato dal partner, qualsiasi utente con accesso Azure Resource Manager alla sottoscrizione può gestire e analizzare i costi con tariffe a consumo. In realtà, i rivenditori e i clienti che dispongono dell'accesso RBAC appropriato alle sottoscrizioni di Azure possono visualizzare i costi.

Indipendentemente dai criteri, i partner possono anche visualizzare i costi se hanno accesso alla sottoscrizione e al gruppo di risorse.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Abilitare i criteri per visualizzare gli addebiti per l'utilizzo di Azure

I partner usano le informazioni seguenti per consentire ai criteri di visualizzare gli addebiti per l'utilizzo di Azure per i clienti.

Nella portale di Azure accedere al tenant partner e fare clic su **Gestione costi e fatturazione**. Selezionare un account di fatturazione e quindi fare clic su **clienti**. L'elenco dei clienti è associato all'account di fatturazione.

Nell'elenco dei clienti selezionare il cliente che si desidera consentire per visualizzare i costi.

![Selezionare i clienti in gestione costi](./media/get-started-partners/customer-list.png)

In **Impostazioni**fare clic su **criteri**.

I criteri di visibilità dei costi correnti vengono visualizzati per i costi di **utilizzo di Azure** associati alle sottoscrizioni per il cliente selezionato.
![Policy per consentire ai clienti di visualizzare gli addebiti con pagamento in base al consumo @ no__t-1

Quando il criterio è impostato su **No**, gestione costi di Azure non è disponibile per gli utenti della sottoscrizione associati al cliente. A meno che non sia abilitato da un partner, i criteri di visibilità dei costi sono disabilitati per impostazione predefinita per tutti gli utenti della sottoscrizione.

Quando il criterio di costo è impostato su **Sì**, gli utenti della sottoscrizione associati al tenant del cliente possono visualizzare i costi di utilizzo alle tariffe con pagamento in base al consumo.

Quando i criteri di visibilità dei costi sono abilitati, tutti i servizi con utilizzo delle sottoscrizioni mostrano i costi in base alle tariffe con pagamento in base al consumo. L'utilizzo della prenotazione viene visualizzato con costi zero per i costi effettivi e ammortizzati. Gli acquisti e i diritti non sono associati a una sottoscrizione specifica. Quindi, gli acquisti non vengono visualizzati nell'ambito della sottoscrizione.

Per visualizzare i costi per il tenant del cliente, aprire Gestione costi e fatturazione, quindi fare clic su account di fatturazione. Nell'elenco degli account di fatturazione fare clic su un account di fatturazione.

![Selezionare un account di fatturazione](./media/get-started-partners/select-billing-account.png)

In **fatturazione**fare clic su **sottoscrizioni Azure**, quindi fare clic su un cliente.

![Selezionare un cliente per la sottoscrizione di Azure](./media/get-started-partners/subscriptions-select-customer.png)

Fare clic su **analisi costi** e iniziare a esaminare i costi.
L'analisi dei costi, i budget e gli avvisi sono ora disponibili per gli ambiti di sottoscrizione e del gruppo di risorse RBAC con costi basati sulla velocità con pagamento in base al consumo.

![Visualizzazione dell'analisi dei costi come cliente ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Le visualizzazioni ammortizzate e i costi effettivi per le istanze riservate negli ambiti RBAC mostrano costi zero. I costi delle istanze riservate vengono visualizzati solo negli ambiti di fatturazione in cui sono stati effettuati gli acquisti.

## <a name="analyze-costs-in-cost-analysis"></a>Analizzare i costi nell'analisi dei costi

I partner possono esplorare e analizzare i costi nell'analisi dei costi tra i clienti per un cliente specifico o per una fattura. Le funzionalità di filtro e di raggruppamento consentono di analizzare i costi in base a più campi, tra cui:

| **Campo** | **Descrizione** | **Colonna equivalente nel centro per i partner** |
| --- | --- | --- |
| PartnerTenantID | Identificatore del tenant Azure Active Directory del partner | Partner Azure Active Directory TenantID chiamato ID partner. In formato GUID. |
| PartnerName | Nome del tenant Azure Active Directory partner | Nome del partner |
| CustomerTenantID | Identificatore del tenant Azure Active Directory della sottoscrizione del cliente | ID organizzazione del cliente. Ad esempio, Customer Azure Active Directory TenantID. |
| CustomerName | Nome del tenant Azure Active Directory contenente la sottoscrizione del cliente | Nome dell'organizzazione del cliente, come indicato nel centro per i partner. Importante per riconciliare la fattura con le informazioni sul sistema. |
| ResellerMPNID | MPNID per il rivenditore associato alla sottoscrizione | ID MPN del rivenditore del record per la sottoscrizione. Non disponibile per l'attività corrente. |
| ID sottoscrizione | Identificatore univoco generato da Microsoft per la sottoscrizione di Azure | N/D |
| subscriptionName | Nome della sottoscrizione di Azure | N/D |
| billingProfileID | Identificatore del profilo di fatturazione. Raggruppa i costi tra le fatture in una singola valuta di fatturazione tra i clienti. | ID del gruppo di fatturazione del partner MCAPI. Usato nelle richieste API, ma non incluso nelle risposte. |
| invoiceID | ID fattura per la fattura in cui viene visualizzata la transazione specifica | Numero di fattura in cui è presente la transazione specificata. |
| resourceGroup | Nome del gruppo di risorse di Azure. Usato per la gestione del ciclo di vita delle risorse. | Nome del gruppo di risorse. |
| partnerEarnedCreditRate | Tariffa di sconto applicata se è presente un credito guadagnato dal partner (PEC) in base all'accesso al collegamento dell'amministratore del partner. | Frequenza di credito guadagnato dal partner (PEC). Ad esempio 0% o 15%. |
| partnerEarnedCreditApplied | Indica se è stato applicato il credito guadagnato dal partner. | N/D |

Nella visualizzazione [analisi costi](quick-acm-cost-analysis.md) è inoltre possibile salvare le [visualizzazioni](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) ed esportare i dati in file [CSV e png](quick-acm-cost-analysis.md#automation-and-offline-analysis) .

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Visualizza i costi delle risorse per il credito guadagnato del partner (PEC)

In gestione costi di Azure, i partner possono usare l'analisi dei costi per visualizzare i costi che hanno ricevuto i vantaggi PEC.

Nella portale di Azure accedere al tenant partner e selezionare **Gestione costi e fatturazione**. In **Gestione costi**fare clic su **analisi dei costi**.

La visualizzazione analisi dei costi Mostra i costi dell'account di fatturazione per il partner. Selezionare l' **ambito** necessario per il partner, un cliente specifico o un profilo di fatturazione per riconciliare le fatture.

In un grafico ad anello fare clic sull'elenco a discesa e selezionare **PartnerEarnedCreditApplied** per esaminare i costi di PEC.

![Esempio che illustra come visualizzare il credito guadagnato dal partner](./media/get-started-partners/cost-analysis-pec1.png)

Quando la proprietà **PartnerEarnedCreditApplied** è _true_, il costo associato presenta il vantaggio del partner che ha ottenuto l'accesso amministrativo.

Quando la proprietà **PartnerEarnedCreditApplied** è _false_, il costo associato non ha soddisfatto l'idoneità necessaria per il credito. In alternativa, il servizio acquistato non è idoneo per il credito guadagnato dal partner.

I dati sull'utilizzo del servizio normalmente richiedono 8-24 ore per essere visualizzati in gestione costi. Per altre informazioni, vedere [frequenza di aggiornamento dei dati di utilizzo varia](understand-cost-mgt-data.md#usage-data-update-frequency-varies). I crediti PEC vengono visualizzati entro 48 ore dal momento dell'accesso in gestione costi di Azure.


È anche possibile raggruppare e filtrare in base alla proprietà **PartnerEarnedCreditApplied** usando le opzioni **Group by** . Usare le opzioni per esaminare i costi che hanno e non hanno PEC.

![Raggruppare o filtrare in base al credito guadagnato dal partner](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>API REST di gestione costi

I partner, i provider indiretti e i clienti possono usare le API di gestione dei costi descritte nelle sezioni seguenti per le attività più comuni.

### <a name="azure-cost-management-apis-for-partners"></a>API di gestione dei costi di Azure per i partner

I partner e gli utenti con accesso agli ambiti di fatturazione in un tenant partner possono usare le API seguenti.

#### <a name="to-get-a-list-of-billing-accounts"></a>Per ottenere un elenco di account di fatturazione

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Per ottenere un elenco dei clienti

```
armclient get "providers/Microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>Per ottenere un elenco di sottoscrizioni

```
armclient get "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-create-new-subscription"></a>Per creare una nuova sottoscrizione

```
armclient post "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub.json -verbose
```

#### <a name="to-get-or-download-usage-for-azure-services"></a>Per ottenere o scaricare l'utilizzo per i servizi di Azure

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-get-a-list-of-billing-profiles"></a>Per ottenere un elenco dei profili di fatturazione

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/billingProfiles?api-version=2019-10-01-preview
```

#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Per ottenere o scaricare l'elenco prezzi per i servizi di Azure utilizzati

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/BillingProfiles/JUT6-EU3Q-BG7-TGB/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>Per ottenere i costi del cliente per gli ultimi due mesi, ordinati in base al mese

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>Per ottenere i costi della sottoscrizione di Azure per gli ultimi due mesi, ordinati per mese

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>Per ottenere i costi giornalieri per il mese corrente

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Per ottenere i criteri per i clienti per visualizzare i costi

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Per impostare i criteri per i clienti per visualizzare i costi

```
armclient put "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview" @policy.json
```

### <a name="azure-cost-management-apis-for-indirect-providers"></a>API di gestione dei costi di Azure per provider indiretti

I provider indiretti con accesso agli ambiti RBAC in un tenant del cliente possono usare le API seguenti. Per iniziare, accedere come utente o con un'entità servizio.

#### <a name="to-get-the-billing-account-information"></a>Per ottenere le informazioni sull'account di fatturazione

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Per ottenere un elenco dei clienti

```
armclient get "providers/Microsoft.billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-resellers-associated-with-the-customer"></a>Per ottenere un elenco dei rivenditori associati al cliente

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db?api-version=2019-10-01-preview&$expand=resellers
```

#### <a name="to-get-a-list-of-subscriptions-with-reseller-information"></a>Per ottenere un elenco di sottoscrizioni con le informazioni sul rivenditore

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-create-a-subscription"></a>Per creare una sottoscrizione

```
armclient post "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub_reseller.json
```

### <a name="azure-cost-management-apis-for-customers"></a>API di gestione dei costi di Azure per i clienti

Per accedere alle API, i clienti usano le informazioni seguenti. Per iniziare, accedere come utente.

#### <a name="to-get-or-download-azure-consumption-usage-information-with-retail-rates"></a>Per ottenere o scaricare le informazioni sull'utilizzo del consumo di Azure con tariffe al dettaglio

```
armclient post /subscriptions/66bada28-271e-4b7a-aaf5-c0ead63923d7/providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

## <a name="next-steps"></a>Passaggi successivi
- [Inizia ad analizzare i costi](quick-acm-cost-analysis.md) in gestione costi
- [Creare e gestire i budget](tutorial-acm-create-budgets.md) in gestione costi
