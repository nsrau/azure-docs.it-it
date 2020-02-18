---
title: Introduzione a Gestione costi di Azure per i partner
description: Questo articolo illustra in che modo i partner usano le funzionalità di Gestione costi di Azure e autorizzano l'accesso alla gestione dei costi da parte dei clienti.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: 9f35f0ae2b310fba462a3f45038496854077da27
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117598"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Introduzione a Gestione costi di Azure per i partner

Gestione costi di Azure è disponibile in modo nativo per i partner diretti che hanno eseguito l'onboarding dei propri clienti a un contratto del cliente Microsoft e hanno [acquistato un piano di Azure](/partner-center/purchase-azure-plan). Questo articolo illustra in che modo i partner usano le funzionalità di [Gestione costi di Azure](../index.yml) per visualizzare i costi per le sottoscrizioni del piano di Azure. Descrive inoltre il modo in cui i partner abilitano l'accesso a Gestione costi per i clienti.

Per i partner diretti e i provider indiretti, l'amministratore globale e gli agenti di amministrazione del provider indiretto possono accedere a Gestione costi nel tenant del partner. I rivenditori e i clienti possono accedere a Gestione costi nel tenant del cliente e visualizzare i costi per le sottoscrizioni, dove i costi sono calcolati e visualizzati con le tariffe al dettaglio. Devono però avere accesso con controllo degli accessi in base al ruolo alla sottoscrizione nel tenant del cliente per visualizzare i costi. I criteri di visibilità dei costi devono essere abilitati dal provider per il tenant del cliente.

I clienti possono usare le funzionalità di Gestione costi quando sono abilitate dal partner CSP.

I partner CSP usano Gestione costi per:

- Comprendere i costi fatturati e associare i costi a clienti, sottoscrizioni, gruppi di risorse e servizi.
- Usufruire di una vista intuitiva dei costi di Azure nell'[analisi dei costi](quick-acm-cost-analysis.md) con funzionalità che consentono di analizzare i costi in base a cliente, sottoscrizione, gruppo di risorse, risorsa, contatore, servizio e molte altre dimensioni.
- Visualizzare i costi delle risorse per i quali è stato applicato il credito ottenuto dai partner nell'analisi dei costi.
- Impostare le notifiche e l'automazione usando [budget](tutorial-acm-create-budgets.md) e avvisi programmatici quando i costi superano i budget.
- Abilitare i criteri di Azure Resource Manager che consentono l'accesso dei clienti ai dati di Gestione costi. I clienti possono quindi visualizzare i dati sui costi di utilizzo per le proprie sottoscrizioni usando [tariffe con pagamento in base al consumo](https://azure.microsoft.com/pricing/calculator/).
- Esportare i dati relativi ai costi e all'utilizzo in un BLOB di archiviazione con una sottoscrizione con pagamento in base al consumo.

Ecco un esempio che illustra i costi per tutti i clienti.
![Esempio con i costi per tutti i clienti](./media/get-started-partners/customer-costs1.png)

Ecco un esempio che illustra i costi per un solo cliente.
![Esempio con i costi per un solo cliente](./media/get-started-partners/customer-costs2.png)

Tutte le funzionalità disponibili in Gestione costi di Azure sono disponibili anche con le API REST. Usare le API per automatizzare le attività di gestione dei costi.

## <a name="prerequisites"></a>Prerequisites

Per i partner Gestione costi di Azure è disponibile a livello nativo solo per le sottoscrizioni esistenti nel piano di Azure.

Per abilitare Gestione costi di Azure nel portale di Azure, è necessario avere confermato l'accettazione da parte del cliente del Contratto del cliente Microsoft (per conto del cliente) e aver trasferito il cliente al piano di Azure. Solo i costi per le sottoscrizioni trasferite al piano di Azure sono disponibili in Gestione costi di Azure.

Gestione costi di Azure richiede l'accesso in lettura all'account di fatturazione o alla sottoscrizione.

Per altre informazioni sull'abilitazione e l'assegnazione dell'accesso a Gestione costi di Azure per un account di fatturazione, vedere [Assegnare autorizzazioni e ruoli utente](/partner-center/permissions-overview). I ruoli **amministratore globale** e **agente amministratore** possono gestire i costi per un account di fatturazione.

Per accedere a Gestione costi di Azure nell'ambito della sottoscrizione, qualsiasi utente con accesso RBAC a una sottoscrizione può visualizzare i costi in base alle tariffe al dettaglio (pagamento in base al consumo). È tuttavia necessario abilitare i criteri di visibilità dei costi per il tenant del cliente. Per visualizzare un elenco completo dei tipi di account supportati, vedere [Informazioni sui dati di Gestione costi](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Modalità di utilizzo degli ambiti in Gestione costi

Gli ambiti consentono di gestire i dati di fatturazione, avere ruoli specifici per i pagamenti, visualizzare le fatture e gestire l'account in generale. I ruoli di fatturazione e account vengono gestiti separatamente dagli ambiti usati per la gestione delle risorse, che usano RBAC. Per distinguerne chiaramente le finalità, incluse le differenze relative al controllo di accesso, gli ambiti sono definiti rispettivamente ambiti di fatturazione e ambiti RBAC.

Per informazioni sugli ambiti di fatturazione e sugli ambiti RBAC nonché sul funzionamento della gestione dei costi con gli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Gestire i costi con gli ambiti di fatturazione del tenant del partner

Dopo aver eseguito l'onboarding dei clienti a un Contratto del cliente Microsoft, nel tenant sono disponibili gli _ambiti di fatturazione_ seguenti. Usare gli ambiti per gestire i costi in Gestione costi.

### <a name="billing-account-scope"></a>Ambito dell'account di fatturazione

Usare l'ambito dell'account di fatturazione per visualizzare i costi pre-imposta per tutti i clienti e i profili di fatturazione. I costi di fatturazione vengono visualizzati solo per i prodotti con fatturazione in base al consumo del cliente previsti dal Contratto del cliente Microsoft. Tuttavia, i costi di fatturazione sono indicati in base agli acquisti di prodotti per i clienti sia del Contratto del cliente Microsoft che dell'offerta CSP. Attualmente la valuta predefinita per la visualizzazione dei costi nell'ambito è il dollaro statunitense. Anche i budget impostati per l'ambito sono in USD.

Indipendentemente dalle diverse valute usate dai clienti per la fatturazione, i partner usano l'ambito dell'account di fatturazione per impostare i budget e gestire i costi in USD per tutti i clienti, le sottoscrizioni, le risorse e i gruppi di risorse.

I partner filtrano inoltre i costi in una specifica valuta di fatturazione per tutti i clienti nella visualizzazione dell'analisi dei costi. Selezionare l'elenco **Costo effettivo** per visualizzare i costi nelle valute di fatturazione supportate per il cliente.

![Esempio di selezione dei costi effettivi per le valute](./media/get-started-partners/actual-cost-selector.png)

Usare la [vista dei costi ammortizzati](quick-acm-cost-analysis.md#customize-cost-views) negli ambiti di fatturazione per visualizzare i costi ammortizzati dell'istanza riservata in un periodo di prenotazione.

### <a name="billing-profile-scope"></a>Ambito del profilo di fatturazione

Usare l'ambito del profilo di fatturazione per visualizzare i costi pre-imposta nella valuta di fatturazione di tutti i clienti per tutti i prodotti e le sottoscrizioni inclusi in una fattura. È possibile filtrare i costi in un profilo di fatturazione per una fattura specifica usando il filtro **InvoiceID**. Il filtro indica il consumo e i costi di acquisto del prodotto per una fattura specifica. È anche possibile filtrare i costi per un cliente specifico sulla fattura per visualizzare i costi pre-imposta.

Dopo l'onboarding dei clienti a un Contratto del cliente Microsoft, si riceve una fattura che include tutti gli addebiti per tutti i prodotti (consumo, acquisti ed entitlement) per questi clienti del Contratto del cliente Microsoft. Se fatturate nella stessa valuta, queste fatture includono anche gli addebiti per l'entitlement e i prodotti acquistati, ad esempio SaaS, Azure Marketplace e le prenotazioni per i clienti che sono ancora nell'offerta CSP.

Per favorire la riconciliazione delle spese rispetto alla fattura del cliente, l'ambito del profilo di fatturazione consente di visualizzare tutti i costi accumulati per una fattura per i clienti. Analogamente alla fattura, l'ambito indica i costi di ogni cliente nel nuovo Contratto del cliente Microsoft. L'ambito indica anche ogni addebito per i prodotti con entitlement dei clienti ancora nell'offerta CSP corrente.

Gli ambiti del profilo di fatturazione e dell'account di fatturazione sono gli unici ambiti applicabili che visualizzano gli addebiti per i prodotti basati su entitlement e acquisti, ad esempio Azure Marketplace e acquisti con prenotazione.

I profili di fatturazione definiscono le sottoscrizioni incluse in una fattura. I profili di fatturazione sono l'equivalente funzionale di un'iscrizione Enterprise Agreement. Un profilo di fatturazione è l'ambito in cui vengono generate le fatture.

Attualmente, la valuta di fatturazione del cliente è la valuta predefinita quando si visualizzano i costi nell'ambito del profilo di fatturazione. I budget impostati nell'ambito del profilo di fatturazione sono nella valuta di fatturazione.

I partner possono usare l'ambito per la riconciliazione delle fatture e per impostare i budget nella valuta di fatturazione per gli elementi seguenti:

- Fattura filtrata specifica
- Customer
- Subscription
- Resource group
- Risorsa
- Servizio di Azure
- Contatore
- ResellerMPNID

### <a name="customer-scope"></a>Ambito del cliente

I partner usano l'ambito per gestire i costi associati ai clienti di cui eseguono l'onboarding al Contratto del cliente Microsoft. L'ambito consente ai partner di visualizzare i costi pre-imposta per un cliente specifico in una valuta di fatturazione. È anche possibile filtrare i costi pre-imposta per una sottoscrizione, una risorsa o un gruppo di risorse specifici.

L'ambito del cliente non include i clienti dell'offerta CSP corrente. Include solo i clienti con Contratto del cliente Microsoft. I costi di entitlement, non di utilizzo di Azure, per i clienti dell'offerta CSP corrente sono disponibili negli ambiti dell'account di fatturazione e del profilo di fatturazione quando si applica il filtro specifico del cliente. I budget impostati in questo ambito sono nella valuta di fatturazione.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Accesso dei partner agli ambiti di fatturazione in Gestione costi

Solo gli utenti con i ruoli **Amministratore globale** e **Agente amministratore** possono gestire e visualizzare i costi per gli account di fatturazione, i profili di fatturazione e i clienti direttamente nel tenant di Azure del partner. Per altre informazioni sui ruoli del Centro per i partner, vedere [Assegnare autorizzazioni e ruoli utente](/partner-center/permissions-overview).

## <a name="enable-cost-management-in-the-customer-tenant"></a>Abilitare la gestione dei costi nel tenant del cliente

I partner possono consentire l'accesso a Gestione costi dopo che è stato eseguito l'onboarding dei clienti a un Contratto del cliente Microsoft. I partner possono quindi abilitare un criterio che consente ai clienti di visualizzare i propri costi calcolati applicando le tariffe al dettaglio con pagamento in base al consumo. I costi vengono visualizzati nella valuta di fatturazione del cliente in base all'utilizzo effettivo negli ambiti RBAC di sottoscrizione e del gruppo di risorse.

Quando il criterio per la visibilità dei costi è abilitato dal partner, qualsiasi utente con accesso Azure Resource Manager alla sottoscrizione può gestire e analizzare i costi applicando tariffe con pagamento in base al consumo. Di fatto, i rivenditori e i clienti con accesso RBAC appropriato alle sottoscrizioni di Azure possono visualizzare i costi.

Indipendentemente dai criteri, i partner possono anche visualizzare i costi se hanno accesso alla sottoscrizione e al gruppo di risorse.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Abilitare i criteri per visualizzare gli addebiti per l'utilizzo di Azure

I partner usano le informazioni seguenti per abilitare i criteri che consentono di visualizzare gli addebiti per l'utilizzo di Azure per i clienti.

Nel portale di Azure accedere al tenant partner e selezionare **Gestione dei costi e fatturazione**. Selezionare l'account di fatturazione del Contratto Microsoft Partner pertinente e quindi selezionare **Clienti**. L'elenco dei clienti è associato all'account di fatturazione.

Nell'elenco dei clienti selezionare il cliente a cui si vuole consentire di visualizzare i costi.

![Selezionare i clienti in Gestione costi](./media/get-started-partners/customer-list.png)

In **Impostazioni** selezionare **Criteri**.

Vengono visualizzati i criteri di visibilità dei costi attualmente applicati per gli addebiti per l'**utilizzo di Azure** associati alle sottoscrizioni per il cliente selezionato.
![Criteri per consentire ai clienti di visualizzare gli addebiti con pagamento in base al consumo](./media/get-started-partners/cost-management-billing-policies.png)

Se il criterio è impostato su **No**, Gestione costi di Azure non è disponibile per gli utenti della sottoscrizione associati al cliente. A meno che non siano abilitati da un partner, i criteri di visibilità dei costi sono disabilitati per impostazione predefinita per tutti gli utenti della sottoscrizione.

Se il criterio relativo ai costi è impostato su **Sì**, gli utenti della sottoscrizione associati al tenant del cliente possono visualizzare gli addebiti per l'utilizzo alle tariffe con pagamento in base al consumo.

Quando i criteri di visibilità dei costi sono abilitati, per tutti i servizi con utilizzo delle sottoscrizioni sono visualizzati i costi calcolati applicando le tariffe con pagamento in base al consumo. Per l'utilizzo della prenotazione gli addebiti sono pari a zero per i costi effettivi e ammortizzati. Gli acquisti e gli entitlement non sono associati a una sottoscrizione specifica. Quindi, gli acquisti non vengono visualizzati nell'ambito della sottoscrizione.

Per visualizzare i costi per il tenant del cliente, aprire **Gestione dei costi e fatturazione** e selezionare l'account di fatturazione pertinente del Contratto Microsoft Partner.

![Selezionare un account di fatturazione](./media/get-started-partners/select-billing-account.png)

In **Fatturazione** selezionare **Sottoscrizioni Azure** e quindi selezionare un cliente.

![Selezionare cliente di una sottoscrizione di Azure](./media/get-started-partners/subscriptions-select-customer.png)

Selezionare **Analisi dei costi** e iniziare a esaminare i costi.
L'analisi dei costi, i budget e gli avvisi sono disponibili per gli ambiti RBAC di sottoscrizione e del gruppo di risorse con costi basati sulle tariffe con pagamento in base al consumo.

![Visualizzare l'analisi dei costi come cliente ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Le visualizzazioni ammortizzate e i costi effettivi per le istanze riservate negli ambiti RBAC indicano addebiti pari a zero. I costi delle istanze riservate vengono visualizzati solo negli ambiti di fatturazione in cui sono stati effettuati gli acquisti.

## <a name="analyze-costs-in-cost-analysis"></a>Analizzare i costi nell'analisi dei costi

I partner con accesso agli ambiti di fatturazione nel tenant partner possono esplorare e analizzare nell'analisi dei costi di tutti i clienti i costi fatturati per un cliente o una fattura specifici. Nella vista dell'[analisi dei costi](quick-acm-cost-analysis.md) è anche possibile [salvare le viste](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) ed esportare i dati in [file CSV e PNG](quick-acm-cost-analysis.md#automation-and-offline-analysis).

Gli utenti RBAC con accesso alla sottoscrizione nel tenant del cliente possono anche analizzare i costi al dettaglio per le sottoscrizioni nel tenant del cliente, salvare le viste ed esportare i dati in file CSV e PNG.

Per analizzare i costi in base a più campi, è possibile usare le funzionalità di filtro e di raggruppamento nell'analisi dei costi. Nella sezione successiva sono visualizzati i campi specifici del partner.

## <a name="data-fields"></a>Campi dati

I campi dati seguenti si trovano nei file dei dettagli dell'utilizzo e nelle API di Gestione costi. Se disponibili, vengono visualizzate le informazioni equivalenti del Centro per i partner. Per i campi in grassetto, i partner possono usare le funzionalità di filtro e di raggruppamento nell'analisi dei costi per analizzare i costi in base a più campi. I campi in grassetto si applicano solo ai Contratti del cliente Microsoft supportati dai partner.

| **Nome campo** | **Descrizione** | **Equivalente del Centro per i partner** |
| --- | --- | --- |
| invoiceId | ID fattura indicato sulla fattura per la transazione specifica. | Numero di fattura in cui viene indicata la transazione. |
| previousInvoiceID | Riferimento a una fattura originale se esiste un rimborso (costo negativo). Popolato solo quando se è presente un rimborso. | N/D |
| billingAccountName | Nome dell'account di fatturazione che rappresenta il partner. Accumula tutti i costi dei clienti di cui è stato eseguito l'onboarding a un Contratto del cliente Microsoft e dei clienti CSP che hanno effettuato acquisti di entitlement, ad esempio SaaS, Azure Marketplace e prenotazioni. | N/D |
| billingAccountID | Identificatore dell'account di fatturazione che rappresenta il partner. | ID radice commerciale del partner MCAPI. Usato in una richiesta, ma non incluso in una risposta.|
| billingProfileID | Identificatore del profilo di fatturazione che raggruppa i costi di tutte le fatture in un'unica valuta di fatturazione per i clienti di cui è stato eseguito l'onboarding a un Contratto del cliente Microsoft e i clienti CSP che hanno effettuato acquisti di entitlement, ad esempio SaaS, Azure Marketplace e prenotazioni. | ID del gruppo di fatturazione del partner MCAPI. Usato in una richiesta, ma non incluso in una risposta. |
| billingProfileName | Nome del profilo di fatturazione che raggruppa i costi di tutte le fatture in un'unica valuta di fatturazione per i clienti di cui è stato eseguito l'onboarding a un Contratto del cliente Microsoft e i clienti CSP che hanno effettuato acquisti di entitlement, ad esempio SaaS, Azure Marketplace e prenotazioni. | N/D |
| invoiceSectionName | Nome del progetto che viene addebitato nella fattura. Non applicabile per i Contratti del cliente Microsoft di cui hanno eseguito l'onboarding i partner. | N/D |
| invoiceSectionID | Identificatore del progetto che viene addebitato nella fattura. Non applicabile per i Contratti del cliente Microsoft di cui hanno eseguito l'onboarding i partner. | N/D |
| **CustomerTenantID** | Identificatore del tenant di Azure Active Directory della sottoscrizione del cliente. | ID organizzazione del cliente: ID tenant di Azure Active Directory del cliente. |
| **CustomerName** | Nome del tenant di Azure Active Directory per la sottoscrizione del cliente. | Nome dell'organizzazione del cliente, come indicato nel Centro per i partner. Valore molto importante per riconciliare la fattura con le informazioni di sistema. |
| **CustomerTenantDomainName** | Nome di dominio per il tenant di Azure Active Directory della sottoscrizione del cliente. | Dominio del tenant di Azure Active Directory del cliente. |
| **PartnerTenantID** | Identificatore del tenant di Azure Active Directory per il partner. | ID tenant di Azure Active Directory del partner denominato ID partner, in formato GUID. |
| **PartnerName** | Nome del tenant di Azure Active Directory del partner. | Nome partner. |
| **ResellerMPNID** | ID MPN del rivenditore associato alla sottoscrizione. | ID MPN del rivenditore registrato per la sottoscrizione. Non disponibile per l'attività corrente. |
| costCenter | Centro di costo associato alla sottoscrizione. | N/D |
| billingPeriodStartDate | Data di inizio del periodo di fatturazione, come indicato nella fattura. | N/D |
| billingPeriodEndDate | Data di fine del periodo di fatturazione, come indicato nella fattura. | N/D |
| servicePeriodStartDate | Data di inizio del periodo di valutazione in cui l'utilizzo del servizio è stato valutato per gli addebiti. I prezzi dei servizi di Azure sono determinati per il periodo di valutazione. | ChargeStartDate nel Centro per i partner. Data di inizio del ciclo di fatturazione, ad eccezione dei casi in cui vengono indicate le date relative ai dati di utilizzo non addebitati del ciclo di fatturazione precedente. L'ora è sempre l'inizio del giorno, 0:00. |
| servicePeriodStartDate | Data di fine del periodo in cui l'utilizzo del servizio è stato valutato per gli addebiti. I prezzi dei servizi di Azure sono determinati in base al periodo di valutazione. | N/D |
| Data | Per i dati sul consumo di Azure, visualizza la data relativa all'utilizzo valutato. Per l'istanza riservata, visualizza la data di acquisto. Per gli addebiti periodici e gli addebiti una tantum, ad esempio per Marketplace e supporto tecnico, viene visualizzata la data di acquisto. | N/D |
| productID | Identificatore del prodotto che ha accumulato addebiti per consumo o acquisto. Si tratta della chiave concatenata di productID e SKuID, come indicato nel Centro per i partner. | ID del prodotto. |
| product | Nome del prodotto che ha accumulato gli addebiti per consumo o acquisto indicati nella fattura. | Nome del prodotto nel catalogo. |
| serviceFamily | Indica la famiglia di servizi per il prodotto acquistato o addebitato. Ad esempio, Archiviazione o Calcolo. | N/D |
| productOrderID | Identificatore dell'asset o nome del piano di Azure a cui appartiene la sottoscrizione. Ad esempio, Piano di Azure. | CommerceSubscriptionID |
| productOrderName | Nome del piano di Azure a cui appartiene la sottoscrizione. Ad esempio, Piano di Azure. | N/D|
| consumedService | Servizio consumato (tassonomia legacy) usato nei dettagli di utilizzo di EA legacy. | Servizio visualizzato nel Centro per i partner. Ad esempio, Microsoft.Storage, Microsoft.Compute e microsoft.operationalinsights. |
| meterID | Identificatore a consumo per il consumo misurato. | ID del contatore in uso. |
| meterName | Identifica il nome del contatore per il consumo misurato. | Nome del contatore misurato. |
| meterCategory | Identifica il servizio di primo livello per l'utilizzo. | Il servizio di primo livello per l'utilizzo. |
| meterSubCategory | Definisce il tipo o la sottocategoria del servizio di Azure che può influire sulla tariffa. | Il tipo di servizio di Azure che può influire sulla tariffa.|
| meterRegion | Identifica la posizione del datacenter per determinati servizi il cui prezzo dipende dalla posizione stessa. | Posizione di un data center nell'area per i servizi per cui questo campo è applicabile e popolato. |
| ID sottoscrizione | Identificatore univoco generato da Microsoft per la sottoscrizione di Azure. | EntitlementID |
| subscriptionName | Nome della sottoscrizione di Azure. | N/D |
| Termine | Visualizza il termine della validità dell'offerta. Ad esempio, le istanze riservate indicano 12 mesi di un termine annuale dell'istanza riservata. Per gli acquisti una tantum o gli acquisti periodici, il termine indica un mese per SaaS, Azure Marketplace e supporto tecnico. Non è applicabile per il consumo di Azure. | N/D |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Tipo di server di pubblicazione che identifica il server di pubblicazione come prima entità, rivenditore di terze parti o agenzia di terze parti. | N/D |
| partNumber | Numero articolo dell'istanza riservata non usata e dei servizi di Azure Marketplace. | N/D |
| publisherName | Nome del server di pubblicazione del servizio che include server di pubblicazione di Microsoft o di terze parti. | Nome del server di pubblicazione del prodotto.|
| reservationId | Identificatore per l'acquisto di istanze riservate. | N/D |
| reservationName | Nome dell'istanza riservata. | N/D |
| reservationOrderId | Valore OrderID dell'istanza riservata. | N/D |
| frequency | Frequenza dei pagamenti per un'istanza riservata. | N/D |
| resourceGroup | Nome del gruppo di risorse di Azure usato per la gestione delle risorse del ciclo di vita. | Nome del gruppo di risorse. |
| instanceID (o) ResourceID | Identificatore dell'istanza della risorsa. | Visualizzato come ResourceURI che include le proprietà complete della risorsa. |
| resourceLocation | Nome del percorso della risorsa. | Il percorso della risorsa. |
| Location | Percorso normalizzato della risorsa. | N/D |
| effectivePrice | Prezzo unitario valido del servizio, nella valuta dei prezzi. Univoco per un prodotto, una famiglia di servizi, un contatore e un'offerta. Usato con i prezzi dell'elenco prezzi per l'account di fatturazione. Quando sono presenti prezzi a livelli o una quantità inclusa, viene visualizzato il prezzo misto per il consumo. | Il prezzo unitario dopo che sono state apportate modifiche. |
| Quantità | Quantità misurata di acquisti o consumi. Quantità indicata dal contatore usata durante il periodo di fatturazione. | Numero di unità. Verificare che corrisponda alle informazioni presenti nel sistema di fatturazione durante la riconciliazione. |
| unitOfMeasure | Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB e ore. | Identifica l'unità in base alla quale viene addebitato il servizio. Ad esempio, GB, ore e decine di migliaia. |
| pricingCurrency | La valuta che definisce il prezzo unitario. | La valuta usata nell'elenco prezzi.|
| billingCurrency | La valuta che definisce il costo fatturato. | La valuta dell'area geografica del cliente. |
| chargeType | Definisce il tipo di addebito rappresentato dal costo in Gestione costi di Azure, ad esempio acquisto e rimborso. | Tipo di addebito o rettifica. Non disponibile per l'attività corrente. |
| costinBillingCurrency | ExtendedCost o costo misto pre-imposta nella valuta fatturata. | N/D |
| costinPricingCurrency | ExtendedCost o costo misto pre-imposta nella valuta dei prezzi per la correlazione con i prezzi. | N/D |
| **costinUSD** | Valore stimato di ExtendedCost o del costo misto pre-imposta in USD. | N/D |
| **paygCostInBillingCurrency** | Visualizza i costi se i prezzi sono prezzi di vendita al dettaglio. Indica i prezzi con pagamento in base al consumo nella valuta di fatturazione. Disponibile solo negli ambiti RBAC. | N/D |
| **paygCostInUSD** | Visualizza i costi se i prezzi sono prezzi di vendita al dettaglio. Indica i prezzi con pagamento in base al consumo in USD. Disponibile solo negli ambiti RBAC. | N/D |
| exchangeRate | Tasso di cambio usato per convertire la valuta dei prezzi nella valuta di fatturazione. | Denominato PCToBCExchangeRate nel Centro per i partner. Tasso di cambio da valuta dei prezzi a valuta di fatturazione.|
| exchangeRateDate | Data del tasso di cambio usato per convertire la valuta da valuta dei prezzi in valuta di fatturazione. | Denominato PCToBCExchangeRateDat nel Centro per i partner. Data del tasso di cambio da valuta dei prezzi a valuta di fatturazione.|
| isAzureCreditEligible | Indica se il costo è idoneo per il pagamento con crediti Azure. | N/D |
| serviceInfo1 | Campo legacy che acquisisce i metadati specifici del servizio facoltativo. | Metadati del servizio di Azure interno. |
| serviceInfo2 | Campo legacy che acquisisce i metadati specifici del servizio facoltativo. | Informazioni sul servizio. Ad esempio, un tipo di immagine per una macchina virtuale e un nome di ISP per ExpressRoute.|
| additionalInfo | Metadati specifici del servizio. Ad esempio un tipo di immagine per una macchina virtuale. | Eventuali informazioni aggiuntive non incluse in altre colonne. Metadati specifici del servizio. Ad esempio un tipo di immagine per una macchina virtuale.|
| tags | Tag assegnato al contatore. Usare i tag per raggruppare i record di fatturazione. È possibile, ad esempio, usare i tag per distribuire i costi in base al reparto che usa il contatore. | Tag aggiunti dal cliente.|
| **partnerEarnedCreditRate** | Tasso di sconto applicato se esiste un credito ottenuto dai partner in base all'accesso al collegamento amministratore partner. | Tasso del credito ottenuto dai partner. Ad esempio, 0% o 15%. |
| **partnerEarnedCreditApplied** | Indica se è stato applicato il credito ottenuto dai partner. | N/D |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Visualizzare i costi delle risorse per il credito ottenuto dai partner

In Gestione costi di Azure i partner possono usare l'analisi dei costi per visualizzare i costi che hanno usufruito dei vantaggi offerti dal credito ottenuto dai partner.

Nel portale di Azure accedere al tenant partner e selezionare **Gestione dei costi e fatturazione**. In **Gestione dei costi** selezionare **Analisi dei costi**.

Nella vista Analisi dei costi sono visualizzati i costi dell'account di fatturazione per il partner. Selezionare l'**ambito** in base alle esigenze del partner, di un cliente specifico o di un profilo di fatturazione per riconciliare le fatture.

In un grafico ad anello selezionare l'elenco a discesa e quindi **PartnerEarnedCreditApplied** per esaminare i costi relativi al credito ottenuto dai partner.

![Esempio che illustra come visualizzare il credito ottenuto dai partner](./media/get-started-partners/cost-analysis-pec1.png)

Se la proprietà **PartnerEarnedCreditApplied** è _True_, per il costo associato il partner ha ottenuto l'accesso come amministratore.

Se la proprietà **PartnerEarnedCreditApplied** è _False_, il costo associato non ha soddisfatto i requisiti di idoneità necessari per il credito. Oppure il servizio acquistato non è idoneo per il credito ottenuto dai partner.

I dati sull'utilizzo del servizio in genere vengono visualizzati in Gestione costi dopo 8-24 ore. Per altre informazioni, vedere [Variazione della frequenza di aggiornamento dei dati sull'utilizzo](understand-cost-mgt-data.md#usage-data-update-frequency-varies). I crediti ottenuti dai partner vengono visualizzati entro 48 ore dal momento dell'accesso in Gestione costi di Azure.


È anche possibile raggruppare e filtrare in base alla proprietà **PartnerEarnedCreditApplied** usando le opzioni di **raggruppamento**. Usare le opzioni per esaminare i costi che hanno e non hanno crediti ottenuti dai partner.

![Raggruppare o filtrare in base al credito ottenuto dai partner](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Esportare i dati sui costi in Archiviazione di Azure

I partner con accesso agli ambiti di fatturazione in un tenant partner possono esportare i propri dati su costi e utilizzo in un BLOB del servizio di archiviazione di Azure. Il BLOB deve trovarsi in una sottoscrizione nel tenant partner che non è una sottoscrizione del servizio condiviso o una sottoscrizione di un cliente. Per abilitare l'esportazione dei dati sui costi, è consigliabile configurare una sottoscrizione indipendente con pagamento in base al consumo nel tenant partner per ospitare i dati sui costi esportati. L'account di archiviazione di esportazione viene creato nel BLOB di Archiviazione di Azure ospitato nella sottoscrizione con pagamento in base al consumo. In base all'ambito in cui il partner crea l'esportazione, i dati associati vengono esportati automaticamente nell'account di archiviazione a scadenze regolari.

Gli utenti con accesso RBAC alla sottoscrizione possono anche esportare i dati sui costi in un BLOB di Archiviazione di Azure ospitato in qualsiasi sottoscrizione nel tenant cliente.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Creare un'esportazione in un tenant partner o un tenant cliente

Nel portale di Azure accedere al tenant partner o al tenant cliente e selezionare **Gestione dei costi e fatturazione**. Selezionare un ambito appropriato, ad esempio un account di fatturazione del Contratto Microsoft Partner, quindi selezionare **Analisi dei costi**. Quando la pagina viene caricata, selezionare **Esporta**. Selezionare **Visualizza tutte le esportazioni** in Pianifica l'esportazione.

![Selezionare Esporta e Visualizza tutte le esportazioni](./media/get-started-partners/export01.png)

Selezionare quindi **Aggiungi**, digitare il nome e selezionare un tipo di esportazione. Selezionare la scheda **Archiviazione** e immettere le informazioni necessarie.

![Aggiungere una nuova esportazione e selezionare la scheda Archiviazione](./media/get-started-partners/export02.png)

Quando si crea un'esportazione nel tenant partner, selezionare la sottoscrizione con pagamento in base al consumo nel tenant partner. Creare un account di Archiviazione di Azure usando quella sottoscrizione.

Per gli utenti RBAC del tenant cliente, selezionare una sottoscrizione nel tenant cliente. Creare un account di Archiviazione di Azure usando la sottoscrizione.

Esaminare il contenuto e quindi selezionare **Crea** per pianificare un'esportazione.

Per verificare i dati nell'elenco delle esportazioni selezionare il nome dell'account di archiviazione. Nella pagina dell'account di archiviazione selezionare **Contenitori** e quindi il contenitore. Passare alla cartella corrispondente e selezionare il file CSV. Selezionare **Scaricare** per ottenere il file CSV e aprirlo. I dati esportati rappresentano dati sui costi simili ai dettagli sull'utilizzo disponibili sul portale di Azure.

![Esempio di dati esportati](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>API REST di Gestione costi

I partner e i clienti possono usare le API di Gestione costi descritte nelle sezioni seguenti per le attività comuni.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>API di Gestione costi di Azure - Provider diretti e indiretti

I partner con accesso agli ambiti di fatturazione in un tenant partner possono usare le API seguenti per visualizzare i costi fatturati.

Le API nell'ambito della sottoscrizione possono essere chiamate da un partner indipendentemente dai criteri di costo se il partner ha accesso alla sottoscrizione. Gli altri utenti con accesso alla sottoscrizione, ad esempio il cliente o il rivenditore, possono chiamare le API solo dopo che il partner ha abilitato i criteri di costo per il tenant cliente.


#### <a name="to-get-a-list-of-billing-accounts"></a>Per ottenere un elenco di account di fatturazione

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Per ottenere un elenco di clienti

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

Usare il valore del campo ID restituito in precedenza e sostituirlo nell'esempio seguente come ambito di esecuzione di una query sui dettagli dell'utilizzo.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

L'esempio restituisce i record di utilizzo associati alla fattura specifica.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Per ottenere i criteri per la visualizzazione dei costi da parte dei clienti

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Per impostare i criteri per la visualizzazione dei costi da parte dei clienti

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

Chiamare l'URI `Location` restituito nella risposta per controllare lo stato dell'operazione. Quando lo stato è *completato*, la proprietà `downloadUrl` contiene un collegamento che può essere usato per scaricare il report generato.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Per ottenere o scaricare l'elenco prezzi per i servizi di Azure consumati

Per prima cosa usare il post seguente.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Chiamare quindi il valore della proprietà dell'operazione asincrona. Ad esempio:

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
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Passaggi successivi
- [Avviare l'analisi dei costi](quick-acm-cost-analysis.md) in Gestione costi
- [Creare e gestire i budget](tutorial-acm-create-budgets.md) in Gestione costi
