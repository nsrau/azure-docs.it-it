---
title: Introduzione a gestione costi di Azure per i partner
description: Questo articolo illustra in che modo i partner usano le funzionalità di gestione dei costi di Azure e come permettono l'accesso alla gestione dei costi per i clienti.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/15/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: b7ae388488de32bb106ae29f975302953cfcb2e9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123020"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Introduzione a gestione costi di Azure per i partner

Gestione costi di Azure è disponibile in modo nativo per i partner che hanno caricato i clienti in un contratto per i clienti Microsoft e hanno [acquistato un piano Azure](/partner-center/purchase-azure-plan). Questo articolo illustra in che modo i partner usano le funzionalità di [gestione dei costi di Azure](index.yml) per visualizzare i costi per le sottoscrizioni nel piano di Azure. Viene inoltre descritto il modo in cui i partner abilitano l'accesso alla gestione dei costi per i clienti. I clienti possono usare le funzionalità di gestione dei costi quando sono abilitate dal partner CSP.

I partner CSP utilizzano Gestione costi per:

- Comprendere i costi fatturati e associare i costi a clienti, sottoscrizioni, gruppi di risorse e servizi.
- Ottieni una visualizzazione intuitiva dei costi di Azure nell' [analisi dei costi](quick-acm-cost-analysis.md) con funzionalità per analizzare i costi per cliente, sottoscrizione, gruppo di risorse, risorse, misuratore, servizio e molte altre dimensioni.
- Visualizzare i costi delle risorse per i quali è stato applicato il credito guadagnato dal partner nell'analisi dei costi.
- Configurare notifiche e automazione usando [budget](tutorial-acm-create-budgets.md) e avvisi a livello di codice quando i costi superano i budget.
- Abilitare i criteri di Azure Resource Manager che forniscono l'accesso dei clienti ai dati di gestione dei costi. I clienti possono quindi visualizzare i dati sui costi di utilizzo per le proprie sottoscrizioni usando [tariffe con pagamento in base al](https://azure.microsoft.com/pricing/calculator/)consumo.

Ecco un esempio che mostra i costi per tutti i clienti.
![esempio che mostra i costi per tutti i clienti](./media/get-started-partners/customer-costs1.png)

Ecco un esempio che mostra i costi per un singolo cliente.
![esempio che mostra i costi per un singolo cliente](./media/get-started-partners/customer-costs2.png)

Tutte le funzionalità disponibili in gestione costi di Azure sono disponibili anche con le API REST. Usare le API per automatizzare le attività di gestione dei costi.

## <a name="prerequisites"></a>prerequisiti

Gestione costi di Azure richiede l'accesso in lettura all'account di fatturazione o alla sottoscrizione. È possibile concedere l'accesso a qualsiasi livello al di sopra delle risorse, dall'account di fatturazione o da un gruppo di gestione a singoli gruppi di risorse in cui si gestiscono le app. Per altre informazioni sull'abilitazione e l'assegnazione dell'accesso a gestione costi di Azure per un account di fatturazione, vedere [assegnare utenti a ruoli e autorizzazioni](/partner-center/permissions-overview). I ruoli **amministratore globale** e **agente di amministrazione** possono gestire i costi per un account di fatturazione.

Per visualizzare un elenco completo dei tipi di account supportati, vedere [informazioni sui dati di gestione dei costi](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Modalità di utilizzo degli ambiti in gestione costi

Gli ambiti sono la posizione in cui si gestiscono i dati di fatturazione, i ruoli specifici dei pagamenti, la visualizzazione delle fatture e la gestione generale degli account. I ruoli di fatturazione e account vengono gestiti separatamente dagli ambiti usati per la gestione delle risorse, che usano RBAC. Per distinguere chiaramente lo scopo degli ambiti distinti, incluse le differenze di controllo degli accessi, sono denominati rispettivamente ambiti di fatturazione e ambiti RBAC.

Per informazioni sugli ambiti di fatturazione e sugli ambiti RBAC e sul funzionamento della gestione dei costi con gli ambiti, vedere [comprendere e usare gli ambiti](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Gestire i costi con gli ambiti di fatturazione del tenant partner

Dopo aver caricato i clienti in un contratto per i clienti Microsoft, nel tenant sono disponibili gli _ambiti di fatturazione_ seguenti. Usare gli ambiti per gestire i costi nella gestione dei costi.

### <a name="billing-account-scope"></a>Ambito dell'account di fatturazione

Usare l'ambito dell'account di fatturazione per visualizzare i costi pre-fiscali in tutti i clienti e i profili di fatturazione. I costi di fatturazione vengono visualizzati solo per i prodotti basati sul consumo del cliente nel contratto per i clienti Microsoft. Tuttavia, i costi di fatturazione vengono visualizzati per i prodotti basati su acquistato per i clienti sia nel contratto del cliente Microsoft che nell'offerta CSP. Attualmente, la valuta predefinita per visualizzare i costi nell'ambito è il dollaro statunitense. I budget impostati per l'ambito sono anche in USD.

Indipendentemente dalle diverse valute fatturate dai clienti, i partner usano l'ambito dell'account di fatturazione per impostare i budget e gestire i costi in USD tra i clienti, le sottoscrizioni, le risorse e i gruppi di risorse.

I partner filtrano inoltre i costi in una specifica valuta di fatturazione tra i clienti nella visualizzazione analisi dei costi. Selezionare l'elenco dei **costi effettivi** per visualizzare i costi nelle valute di fatturazione del cliente supportate.

![Esempio che mostra la selezione dei costi effettivi per le valute](./media/get-started-partners/actual-cost-selector.png)

Usare la [visualizzazione dei costi ammortizzati](quick-acm-cost-analysis.md#customize-cost-views) negli ambiti di fatturazione per visualizzare i costi ammortizzati dell'istanza riservata in un periodo di prenotazione.

### <a name="billing-profile-scope"></a>Ambito del profilo di fatturazione

Usare l'ambito del profilo di fatturazione per visualizzare i costi pre-imposta nella valuta di fatturazione per tutti i clienti per tutti i prodotti e le sottoscrizioni inclusi in una fattura. È possibile filtrare i costi in un profilo di fatturazione per una fattura specifica usando il filtro **InvoiceID** . Il filtro Mostra il consumo e i costi di acquisto del prodotto per una fattura specifica. È anche possibile filtrare i costi per un cliente specifico sulla fattura per visualizzare i costi pre-fiscali.

Dopo l'onboarding dei clienti a un contratto per i clienti Microsoft, si riceve una fattura che include tutti gli addebiti per tutti i prodotti (consumo, acquisti e diritti) per questi clienti nel contratto del cliente Microsoft. Una volta fatturato nella stessa valuta, queste fatture includono anche gli addebiti per i diritti e i prodotti acquistati, ad esempio SaaS, Azure Marketplace e le prenotazioni per i clienti che si trovano ancora nell'offerta CSP.

Per risolvere i costi rispetto alla fattura del cliente, l'ambito del profilo di fatturazione consente di visualizzare tutti i costi accumulati per una fattura per i clienti. Analogamente a quanto avviene per la fattura, l'ambito Mostra i costi di ogni cliente nel nuovo contratto di servizio Microsoft. L'ambito Mostra anche ogni addebito per i prodotti con diritti dei clienti ancora nell'offerta CSP corrente.

Gli ambiti del profilo di fatturazione e dell'account di fatturazione sono gli unici ambiti applicabili che visualizzano i costi per i prodotti basati su diritti e acquisti come Azure Marketplace e gli acquisti di prenotazione.

I profili di fatturazione definiscono le sottoscrizioni incluse in una fattura. I profili di fatturazione sono l'equivalente funzionale di una registrazione con contratto Enterprise Agreement. Un profilo di fatturazione è l'ambito in cui vengono generate le fatture.

Attualmente, la valuta di fatturazione del cliente è la valuta predefinita quando si visualizzano i costi nell'ambito del profilo di fatturazione. I budget impostati nell'ambito del profilo di fatturazione si trovano nella valuta di fatturazione.

I partner possono usare l'ambito per riconciliare le fatture. E usano l'ambito per impostare i budget nella valuta di fatturazione per gli elementi seguenti:

- Fattura filtrata specifica
- Customer
- sottoscrizione
- Resource group
- Risorsa
- Servizio di Azure
- Contatore
- ResellerMPNID

### <a name="customer-scope"></a>Ambito del cliente

I partner usano l'ambito per gestire i costi associati ai clienti che vengono caricati nel contratto per i clienti Microsoft. L'ambito consente ai partner di visualizzare i costi pre-fiscali per un cliente specifico. È anche possibile filtrare i costi pre-fiscali per una sottoscrizione, un gruppo di risorse o una risorsa specifica.

L'ambito del cliente non include i clienti che si trovano nell'offerta CSP corrente. L'ambito include solo i clienti che dispongono di un contratto per i clienti Microsoft. I costi per i diritti, non per l'utilizzo di Azure, per i clienti correnti dell'offerta CSP sono disponibili negli ambiti account di fatturazione e profilo di fatturazione quando si applica il filtro del cliente.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Accesso dei partner agli ambiti di fatturazione in gestione costi

Solo gli utenti con ruoli di **amministratore globale** e **amministratore** globale possono gestire e visualizzare i costi per gli account di fatturazione, i profili di fatturazione e i clienti direttamente nel tenant di Azure del partner. Per altre informazioni sui ruoli del centro per i partner, vedere [assegnare utenti ruoli e autorizzazioni](/partner-center/permissions-overview).

## <a name="enable-cost-management-in-the-customer-tenant"></a>Abilitare Gestione costi nel tenant del cliente

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
![criteri per consentire ai clienti di visualizzare gli addebiti con pagamento in base al consumo](./media/get-started-partners/cost-management-billing-policies.png)

Quando il criterio è impostato su **No**, gestione costi di Azure non è disponibile per gli utenti della sottoscrizione associati al cliente. A meno che non sia abilitato da un partner, i criteri di visibilità dei costi sono disabilitati per impostazione predefinita per tutti gli utenti della sottoscrizione.

Quando il criterio di costo è impostato su **Sì**, gli utenti della sottoscrizione associati al tenant del cliente possono visualizzare i costi di utilizzo alle tariffe con pagamento in base al consumo.

Quando i criteri di visibilità dei costi sono abilitati, tutti i servizi con utilizzo delle sottoscrizioni mostrano i costi in base alle tariffe con pagamento in base al consumo. L'utilizzo della prenotazione viene visualizzato con costi zero per i costi effettivi e ammortizzati. Gli acquisti e i diritti non sono associati a una sottoscrizione specifica. Quindi, gli acquisti non vengono visualizzati nell'ambito della sottoscrizione.

Per visualizzare i costi per il tenant del cliente, aprire Gestione costi e fatturazione, quindi fare clic su account di fatturazione. Nell'elenco degli account di fatturazione fare clic su un account di fatturazione.

![Selezionare un account di fatturazione](./media/get-started-partners/select-billing-account.png)

In **fatturazione**fare clic su **sottoscrizioni Azure**, quindi fare clic su un cliente.

![Selezionare un cliente per la sottoscrizione di Azure](./media/get-started-partners/subscriptions-select-customer.png)

Fare clic su **analisi costi** e iniziare a esaminare i costi.
L'analisi dei costi, i budget e gli avvisi sono disponibili per gli ambiti di sottoscrizione e del gruppo di risorse RBAC con costi basati sulla velocità con pagamento in base al consumo.

![Visualizzazione dell'analisi dei costi come cliente ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Le visualizzazioni ammortizzate e i costi effettivi per le istanze riservate negli ambiti RBAC mostrano costi zero. I costi delle istanze riservate vengono visualizzati solo negli ambiti di fatturazione in cui sono stati effettuati gli acquisti.

## <a name="analyze-costs-in-cost-analysis"></a>Analizzare i costi nell'analisi dei costi

I partner possono esplorare e analizzare i costi nell'analisi dei costi tra i clienti per un cliente specifico o per una fattura. Nella visualizzazione [analisi costi](quick-acm-cost-analysis.md) è inoltre possibile salvare le [visualizzazioni](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) ed esportare i dati in [file CSV e png](quick-acm-cost-analysis.md#automation-and-offline-analysis).

Per analizzare i costi in base a più campi, è possibile utilizzare le funzionalità di filtro e di raggruppamento in base all'analisi dei costi. Nella sezione successiva vengono visualizzati i campi specifici del partner.

## <a name="data-fields"></a>Campi dati

I campi dati seguenti si trovano nei file di dettagli sull'utilizzo e nelle API di gestione dei costi. Se disponibile, vengono visualizzate le informazioni equivalenti del centro per i partner. Per i campi in grassetto seguenti, i partner possono usare le funzionalità di filtro e di raggruppamento in base all'analisi dei costi per analizzare i costi in base a più campi. I campi in grassetto si applicano solo ai contratti dei clienti Microsoft supportati dai partner.

| **Nome campo** | **Descrizione** | **Equivalente del centro per i partner** |
| --- | --- | --- |
| invoiceId | ID fattura visualizzato nella fattura per la transazione specifica. | Numero di fattura in cui viene mostrata la transazione. |
| previousInvoiceID | Riferimento a una fattura originale. si tratta di un rimborso (costo negativo). Popolato solo quando si verifica un rimborso. | N/D |
| billingAccountName | Nome dell'account di fatturazione che rappresenta il partner. Accumula tutti i costi tra i clienti che hanno eseguito l'onboarding a un contratto di cliente Microsoft e i clienti CSP che hanno effettuato acquisti di diritti come SaaS, Azure Marketplace e prenotazioni. | N/D |
| billingAccountID | Identificatore dell'account di fatturazione che rappresenta il partner. | ID radice di Commerce partner MCAPI. Usato in una richiesta, ma non incluso in una risposta.|
| billingProfileID | Identificatore del profilo di fatturazione che raggruppa i costi tra le fatture in una singola valuta di fatturazione tra i clienti che hanno eseguito l'onboarding a un contratto di cliente Microsoft e i clienti CSP che hanno effettuato acquisti di diritti come SaaS, Azure Marketplace e prenotazioni. | ID del gruppo di fatturazione del partner MCAPI. Usato in una richiesta, ma non incluso in una risposta. |
| billingProfileName | Nome del profilo di fatturazione che raggruppa i costi tra le fatture in una singola valuta di fatturazione tra i clienti che hanno eseguito l'onboarding a un contratto di cliente Microsoft e i clienti CSP che hanno effettuato acquisti di diritti come SaaS, Azure Marketplace e prenotazioni. | N/D |
| invoiceSectionName | Nome del progetto che viene addebitato nella fattura. Non applicabile per i contratti con i clienti Microsoft caricati dai partner. | N/D |
| invoiceSectionID | Identificatore del progetto che viene addebitato nella fattura. Non applicabile per i contratti con i clienti Microsoft caricati dai partner. | N/D |
| **CustomerTenantID** | Identificatore del tenant Azure Active Directory della sottoscrizione del cliente. | ID organizzazione del cliente: il Azure Active Directory del cliente TenantID. |
| **CustomerName** | Nome del tenant Azure Active Directory per la sottoscrizione del cliente. | Nome dell'organizzazione del cliente, come illustrato nel centro per i partner. Importante per riconciliare la fattura con le informazioni sul sistema. |
| **CustomerTenantDomainName** | Nome di dominio per il tenant Azure Active Directory della sottoscrizione del cliente. | Il cliente Azure Active Directory dominio tenant. |
| **PartnerTenantID** | Identificatore del tenant Azure Active Directory del partner. | Partner Azure Active Directory ID tenant denominato ID partner, in formato GUID. |
| **PartnerName** | Nome del tenant del Azure Active Directory partner. | Nome partner. |
| **ResellerMPNID** | MPNID per il rivenditore associato alla sottoscrizione. | ID MPN del rivenditore registrato per la sottoscrizione. Non disponibile per l'attività corrente. |
| costCenter | Centro di costo associato alla sottoscrizione. | N/D |
| billingPeriodStartDate | Data di inizio del periodo di fatturazione, come illustrato nella fattura. | N/D |
| billingPeriodEndDate | Data di fine del periodo di fatturazione, come illustrato nella fattura. | N/D |
| servicePeriodStartDate | Data di inizio del periodo di valutazione in cui l'utilizzo del servizio è stato valutato per gli addebiti. I prezzi per i servizi di Azure sono determinati per il periodo di valutazione. | ChargeStartDate nel centro per i partner. Data di inizio del ciclo di fatturazione, tranne quando vengono presentate date di dati di utilizzo latenti precedentemente non addebitati da un ciclo di fatturazione precedente. L'ora è sempre l'inizio della giornata, 0:00. |
| servicePeriodStartDate | Data di fine del periodo in cui l'utilizzo del servizio è stato valutato per gli addebiti. I prezzi per i servizi di Azure sono determinati in base al periodo di valutazione. | N/D |
| date | Per i dati sul consumo di Azure, Visualizza la data di utilizzo nominale. Per l'istanza riservata, viene visualizzata la data di acquisto. Per gli addebiti ricorrenti e i costi monouso, ad esempio Marketplace e supporto, viene visualizzata la data di acquisto. | N/D |
| productID | Identificatore per il prodotto che ha accumulato addebiti per consumo o acquisto. Si tratta della chiave concatenata di productID e SKuID, come illustrato nel centro per i partner. | ID del prodotto. |
| product | Nome del prodotto che ha accumulato addebiti per consumo o acquisto, come illustrato nella fattura. | Nome del prodotto nel catalogo. |
| serviceFamily | Mostra la famiglia di servizi per il prodotto acquistato o addebitato. Ad esempio, archiviazione o calcolo. | N/D |
| productOrderID | Identificatore dell'asset o del nome del piano Azure a cui appartiene la sottoscrizione. Ad esempio, piano di Azure. | N/D |
| productOrderName | Nome del piano Azure a cui appartiene la sottoscrizione. Ad esempio, piano di Azure. | N/D|
| consumedService | Servizio utilizzato (tassonomia legacy) usato nei dettagli di utilizzo di EA legacy. | Servizio visualizzato nel centro per i partner. Ad esempio, Microsoft. storage, Microsoft. COMPUTE e Microsoft. operationalinsights. |
| meterID | Identificatore a consumo per l'utilizzo misurato. | ID del contatore utilizzato. |
| meterName | Identifica il nome del contatore per l'utilizzo misurato. | Nome del contatore utilizzato. |
| meterCategory | Identifica il servizio di primo livello per l'utilizzo. | Servizio di primo livello per l'utilizzo. |
| meterSubCategory | Definisce il tipo o la sottocategoria del servizio di Azure che può influire sulla frequenza. | Tipo di servizio di Azure che può influire sulla frequenza.|
| meterRegion | Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa. | Posizione regionale di un data center per i servizi, laddove applicabile e popolato. |
| ID sottoscrizione | Identificatore univoco generato da Microsoft per la sottoscrizione di Azure. | N/D |
| subscriptionName | Nome della sottoscrizione di Azure. | N/D |
| Termine | Visualizza il termine della validità dell'offerta. Le istanze riservate, ad esempio, mostrano 12 mesi di un termine annuale dell'istanza riservata. Per gli acquisti monouso o gli acquisti ricorrenti, il termine Visualizza un mese per SaaS, Azure Marketplace e il supporto. Non applicabile per il consumo di Azure. | N/D |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Tipo di server di pubblicazione che identifica il server di pubblicazione come prima parte, rivenditore di terze parti o agenzia di terze parti. | N/D |
| partNumber | Numero di parte per l'istanza riservata non usata e per i servizi di Azure Marketplace. | N/D |
| publisherName | Nome dell'autore del servizio che include autori Microsoft o di terze parti. | Nome del server di pubblicazione del prodotto.|
| reservationId | Identificatore per l'acquisto di istanze riservate. | N/D |
| reservationName | Nome dell'istanza riservata. | N/D |
| reservationOrderId | OrderID per l'istanza riservata. | N/D |
| frequency | Frequenza di pagamento per un'istanza riservata. | N/D |
| resourceGroup | Nome del gruppo di risorse di Azure usato per la gestione delle risorse del ciclo di vita. | Nome del gruppo di risorse. |
| instanceID (o) ResourceID | Identificatore dell'istanza di risorsa. | Visualizzato come ResourceURI che include le proprietà complete delle risorse. |
| resourceLocation | Nome della posizione della risorsa. | Posizione della risorsa. |
| Location | Posizione normalizzata della risorsa. | N/D |
| effectivePrice | Prezzo unitario effettivo del servizio, in valuta prezzo. Univoco per un prodotto, una famiglia di servizi, un contatore e un'offerta. Usato con prezzi nell'elenco prezzi per l'account di fatturazione. Quando sono presenti prezzi a livelli o una quantità inclusa, viene visualizzato il prezzo misto per l'utilizzo. | Il prezzo unitario dopo le modifiche apportate. |
| Quantità | Quantità misurata acquistata o utilizzata. Quantità del contatore utilizzata durante il periodo di fatturazione. | Numero di unità. Assicurarsi che corrisponda alle informazioni del sistema di fatturazione durante la riconciliazione. |
| unitOfMeasure | Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB e ore. | Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB, ore e 10 migliaia. |
| pricingCurrency | Valuta che definisce il prezzo unitario. | Valuta nel listino prezzi.|
| billingCurrency | Valuta che definisce il costo fatturato. | Valuta dell'area geografica del cliente. |
| chargeType | Definisce il tipo di addebito rappresentato dal costo in gestione costi di Azure, ad esempio acquisto e rimborso. | Tipo di addebito o regolazione. Non disponibile per l'attività corrente. |
| costinBillingCurrency | Costo costo esteso o blended prima delle imposte nella valuta fatturata. | N/D |
| costinPricingCurrency | Costo di costo esteso o blended prima dell'imposta sulla valuta dei prezzi per la correlazione con i prezzi. | N/D |
| **costinUSD** | Costo stimato di costo esteso o blended prima dell'imposizione in USD. | N/D |
| **paygCostInBillingCurrency** | Mostra i costi se i prezzi sono nei prezzi di vendita al dettaglio. Mostra i prezzi con pagamento in base al consumo nella valuta di fatturazione. Disponibile solo in ambito RBAC. | N/D |
| **paygCostInUSD** | Mostra i costi se i prezzi sono nei prezzi di vendita al dettaglio. Mostra i prezzi con pagamento in base al consumo in USD. Disponibile solo in ambito RBAC. | N/D |
| exchangeRate | Tasso di cambio usato per la conversione dalla valuta dei prezzi alla valuta di fatturazione. | Denominato PCToBCExchangeRate nel centro per i partner. Valuta dei prezzi per il tasso di cambio di valuta.|
| exchangeRateDate | Data per il tasso di cambio usato per eseguire la conversione dalla valuta dei prezzi alla valuta di fatturazione. | Denominato PCToBCExchangeRateDat nel centro per i partner. Valuta dei prezzi per la fatturazione della data del tasso di cambio valuta.|
| isAzureCreditEligible | Indica se il costo è idoneo per il pagamento da parte di crediti di Azure. | N/D |
| serviceInfo1 | Campo legacy che acquisisce i metadati specifici del servizio facoltativo. | Metadati interni del servizio di Azure. |
| serviceInfo2 | Campo legacy che acquisisce i metadati specifici del servizio facoltativo. | Informazioni sul servizio. Ad esempio, un tipo di immagine per una macchina virtuale e un nome di ISP per ExpressRoute.|
| additionalInfo | Metadati specifici del servizio. Ad esempio un tipo di immagine per una macchina virtuale. | Eventuali informazioni aggiuntive non incluse in altre colonne. Metadati specifici del servizio. Ad esempio un tipo di immagine per una macchina virtuale.|
| Tag | Tag assegnato al contatore. Usare i tag per raggruppare i record di fatturazione. È possibile, ad esempio, usare i tag per distribuire i costi in base al reparto che usa il contatore. | Tag aggiunti dal cliente.|
| **partnerEarnedCreditRate** | Tasso di sconto applicato se è presente un credito guadagnato dal partner (PEC) in base all'accesso al collegamento dell'amministratore del partner. | Frequenza di credito guadagnato dal partner (PEC). Ad esempio 0% o 15%. |
| **partnerEarnedCreditApplied** | Indica se è stato applicato il credito guadagnato dal partner. | N/D |

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

I partner e i clienti possono usare le API di gestione dei costi descritte nelle sezioni seguenti per le attività comuni.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>API di gestione dei costi di Azure-provider diretti e indiretti

I partner con accesso agli ambiti di fatturazione in un tenant partner possono usare le API seguenti per visualizzare i costi fatturati.

Le API nell'ambito della sottoscrizione possono essere chiamate da un partner indipendentemente dai criteri di costo se hanno accesso alla sottoscrizione. Gli altri utenti con accesso alla sottoscrizione, ad esempio il cliente o il rivenditore, possono chiamare le API solo dopo che il partner Abilita i criteri di costo per il tenant del cliente.


#### <a name="to-get-a-list-of-billing-accounts"></a>Per ottenere un elenco di account di fatturazione

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Per ottenere un elenco dei clienti

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Per ottenere un elenco di sottoscrizioni

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Per ottenere un elenco di fatture per un periodo di tempo

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

La chiamata API restituisce una matrice di fatture con elementi simili al codice JSON seguente.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Usare il valore del campo ID restituito precedente e sostituirlo nell'esempio seguente come ambito per eseguire una query per i dettagli di utilizzo.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

Nell'esempio vengono restituiti i record di utilizzo associati alla fattura specifica.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Per ottenere i criteri per i clienti per visualizzare i costi

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Per impostare i criteri per i clienti per visualizzare i costi

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Per ottenere l'utilizzo dei servizi di Azure per un account di fatturazione

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Per scaricare l'utilizzo dei servizi di Azure di un cliente

La chiamata Get seguente è un'operazione asincrona.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Chiamare l'URI `Location` restituito nella risposta per controllare lo stato dell'operazione. Una volta *completato*lo stato, la proprietà `downloadUrl` contiene un collegamento che è possibile utilizzare per scaricare il report generato.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Per ottenere o scaricare l'elenco prezzi per i servizi di Azure utilizzati

Usare prima di tutto il post seguente.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Chiamare quindi il valore della proprietà Operation asincrona. Ad esempio:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
La chiamata Get precedente restituisce il collegamento di download contenente l'elenco prezzi.


#### <a name="to-get-aggregated-costs"></a>Per ottenere i costi aggregati

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Creare un budget per un partner

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Creare un budget per un cliente

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Eliminare un budget

```
PUT
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Passaggi successivi
- [Inizia ad analizzare i costi](quick-acm-cost-analysis.md) in gestione costi
- [Creare e gestire i budget](tutorial-acm-create-budgets.md) in gestione costi
