---
title: Informazioni e uso degli ambiti di Gestione costi di Azure
description: Questo articolo consente di comprendere gli ambiti di fatturazione e di gestione delle risorse disponibili in Azure e offre informazioni su come usare gli ambiti in Gestione costi e le API.
author: bandersmsft
ms.author: banders
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 64e7f0eae10f590ffd268b43b63a5d8cb0987c57
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683148"
---
# <a name="understand-and-work-with-scopes"></a>Informazioni e utilizzo degli ambiti

Questo articolo consente di comprendere gli ambiti di fatturazione e di gestione delle risorse disponibili in Azure e offre informazioni su come usare gli ambiti in Gestione costi e le API.

## <a name="scopes"></a>Ambiti

Un _ambito_ è un nodo nella gerarchia delle risorse di Azure a cui gli utenti di Azure AD accedono per gestire i servizi. La maggior parte delle risorse di Azure viene creata e distribuita in gruppi di risorse che fanno parte di sottoscrizioni. Microsoft offre anche due gerarchie nelle sottoscrizioni di Azure che includono ruoli specializzati per la gestione dei dati di fatturazione:
- Dati di fatturazione, ad esempio pagamenti e fatture
- Servizi cloud, ad esempio governance dei costi e dei criteri

Negli ambiti è possibile gestire i dati di fatturazione, avere ruoli specifici per i pagamenti, visualizzare le fatture e gestire l'account in generale. I ruoli di fatturazione e account vengono gestiti separatamente dai ruoli usati per la gestione delle risorse, che usano il [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per distinguerne chiaramente la finalità, incluse le differenze relative al controllo di accesso, gli ambiti sono definiti rispettivamente _ambiti di fatturazione_ e _ambiti del controllo degli accessi in base al ruolo_.

Per altre informazioni sugli ambiti, vedere il video sulla [configurazione di gerarchie per Gestione costi](https://www.youtube.com/watch?v=n3TLRaYJ1NY). Per guardare altri video, visitare il [canale YouTube di Gestione costi](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

## <a name="how-cost-management-uses-scopes"></a>Modalità di utilizzo degli ambiti in Gestione costi

Gestione costi usa gli ambiti nelle risorse per consentire alle organizzazioni di gestire i costi al livello di accesso, sia che si tratti dell'intero account di fatturazione che di un singolo gruppo di risorse. Mentre gli ambiti di fatturazione variano in base al contratto Microsoft (tipo di sottoscrizione), gli ambiti del controllo degli accessi in base al ruolo rimangono invariati.

## <a name="azure-rbac-scopes"></a>Ambiti del controllo degli accessi in base al ruolo di Azure

Azure supporta tre ambiti per la gestione delle risorse. Ogni ambito supporta la gestione degli accessi e la governance, tra cui la gestione dei costi.

- [**Gruppi di gestione**](../../governance/management-groups/overview.md): contenitori gerarchici fino a otto livelli per l'organizzazione delle sottoscrizioni di Azure.

    Tipo di risorsa: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Sottoscrizioni**: contenitori principali per le risorse di Azure.

    Tipo di risorsa: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Gruppi di risorse**](../../azure-resource-manager/management/overview.md#resource-groups): raggruppamenti logici di risorse correlate per una soluzione di Azure che condividono lo stesso ciclo di vita, ad esempio le risorse distribuite ed eliminate nello stesso momento.

    Tipo di risorsa: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

I gruppi di gestione consentono di organizzare le sottoscrizioni in una gerarchia. Usando i gruppi di gestione è ad esempio possibile creare una gerarchia organizzativa logica. Assegnare quindi ai team le sottoscrizioni per i carichi di lavoro di produzione e di sviluppo/test. Quindi creare gruppi di risorse nelle sottoscrizioni per gestire ogni sottosistema o componente.

La creazione di una gerarchia organizzativa consente il rollup dei costi e della conformità dei criteri all'interno dell'organizzazione. Tutti i leader possono quindi visualizzare e analizzare i costi correnti. Possono poi creare budget per limitare modelli di spesa non adeguati e ottimizzare i costi usando le raccomandazioni di Advisor al livello più basso.

L'accesso alla visualizzazione dei costi ed eventualmente alla gestione della configurazione dei costi, ad esempio budget ed esportazioni, viene concesso negli ambiti di governance usando il controllo degli accessi in base al ruolo di Azure. Usare il controllo degli accessi in base al ruolo di Azure per concedere a utenti e gruppi di Azure AD l'accesso per eseguire un set predefinito di azioni definite in un ruolo in un ambito specifico e al di sotto. Ad esempio un ruolo assegnato a un ambito del gruppo di gestione concede le stesse autorizzazioni anche alle sottoscrizioni annidate e ai gruppi di risorse.

Gestione costi supporta i ruoli predefiniti seguenti per ogni ambito riportato di seguito:

- [**Proprietario**](../../role-based-access-control/built-in-roles.md#owner): può visualizzare i costi e gestirne tutti gli aspetti, inclusa la configurazione dei costi.
- [**Collaboratore**](../../role-based-access-control/built-in-roles.md#contributor): può visualizzare i costi e gestirne tutti gli aspetti, inclusa la configurazione dei costi, ma è escluso il controllo degli accessi.
- [**Lettore**](../../role-based-access-control/built-in-roles.md#reader): può visualizzare tutto, inclusi i dati relativi ai costi e la configurazione, ma non può apportare modifiche.
- [**Collaboratore di Gestione costi**](../../role-based-access-control/built-in-roles.md#cost-management-contributor): può visualizzare i costi, gestire la configurazione dei costi e visualizzare le raccomandazioni.
- [**Lettore di Gestione costi**](../../role-based-access-control/built-in-roles.md#cost-management-reader): può visualizzare i dati sui costi, la configurazione dei costi e visualizzare le raccomandazioni.

Collaboratore di Gestione costi è il ruolo con privilegi minimi consigliato. Il ruolo consente di creare e gestire i budget e le esportazioni, in modo da monitorare i costi e creare report in modo più efficace. I Collaboratori di Gestione costi possono anche richiedere ruoli aggiuntivi per supportare scenari di gestione dei costi complessi. Esaminare gli scenari seguenti:

- **Creazione di report sull'utilizzo delle risorse**: Gestione costi di Azure mostra i costi nel portale di Azure. Include l'utilizzo per quanto riguarda i costi nei modelli di utilizzo completi. Questo report può anche visualizzare gli addebiti per API e download, ma è anche possibile esaminare le metriche di utilizzo dettagliate in Monitoraggio di Azure per informazioni più approfondite. Considerare la possibilità di concedere il [Ruolo con autorizzazioni di lettura dei dati di monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-reader) in qualsiasi ambito in cui è necessario anche creare report sulle metriche di utilizzo dettagliate.
- **Agire quando i budget vengono superati**: i Collaboratori di Gestione costi devono anche accedere per creare e gestire gruppi di azioni che possano automaticamente reagire in caso di eccedenza. Considerare la possibilità di concedere il ruolo [Collaboratore per il monitoraggio](../../role-based-access-control/built-in-roles.md#monitoring-contributor) a un gruppo di risorse che contiene il gruppo di azioni da usare quando vengono superate le soglie del budget. L'automazione di azioni specifiche richiede ruoli aggiuntivi per i servizi specifici usati, ad esempio Automazione e Funzioni di Azure.
- **Pianificare l'esportazione dei dati sui costi**: i Collaboratori di Gestione costi devono anche accedere per gestire gli account di archiviazione per pianificare un'esportazione per la copia dei dati in un account di archiviazione. Considerare la possibilità di concedere il ruolo [Collaboratore Account di archiviazione](../../role-based-access-control/built-in-roles.md#storage-account-contributor) a un gruppo di risorse che contiene l'account di archiviazione in cui vengono esportati i dati sui costi.
- **Visualizzazione delle raccomandazioni per il risparmio sui costi**: per impostazione predefinita, i Lettori di Gestione costi e i Collaboratori di Gestione costi hanno accesso per *visualizzare* le raccomandazioni sui costi. L'accesso per agire sulle raccomandazioni sui costi richiede tuttavia l'accesso alle singole risorse. Considerare la possibilità di concedere un [ruolo specifico del servizio](../../role-based-access-control/built-in-roles.md#all) se si vuole agire su una raccomandazione basata sui costi.

I gruppi di gestione sono supportati solo se contengono Contratto Enterprise (EA), il pagamento in base al consumo (PAYG) o sottoscrizioni interne Microsoft. I gruppi di gestione con qualsiasi altro tipo di sottoscrizione, ad esempio il Contratto del cliente Microsoft o le sottoscrizioni Azure Active Directory, non possono visualizzare i costi. Se si dispone di una combinazione di sottoscrizioni, spostare le sottoscrizioni non supportate in un ramo separato della gerarchia del gruppo di gestione per consentire la gestione dei costi per le sottoscrizioni supportate. Ad esempio, creare due gruppi di gestione nel gruppo di gestione radice: **Azure AD** e **Organizzazione personale**. Spostare la sottoscrizione di Azure AD sul gruppo di gestione **Azure AD** e quindi visualizzare e gestire i costi usando il gruppo di gestione **Organizzazione personale**.

## <a name="enterprise-agreement-scopes"></a>Ambiti del Contratto Enterprise

Gli account di fatturazione del Contratto Enterprise (EA, Enterprise Agreement), denominati anche registrazioni, dispongono degli ambiti seguenti:

- [**Account di fatturazione**](../manage/view-all-accounts.md): rappresenta una registrazione EA. Le fatture vengono generate in questo ambito. Gli acquisti che non sono basati sull'utilizzo, ad esempio acquisti del Marketplace e prenotazioni, sono disponibili solo in questo ambito. Non sono rappresentati nei reparti o negli account di registrazione. L'utilizzo delle prenotazioni, insieme a tutti gli altri utilizzi, viene applicato alle singole risorse. L'utilizzo viene riepilogato nelle sottoscrizioni all'interno dell'account di fatturazione. Per vedere i costi delle prenotazioni suddivisi in ogni risorsa, passare alla visualizzazione **Costo ammortizzato** nell'analisi dei costi.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Reparto**: raggruppamento facoltativo di account di registrazione.

    Tipo di risorsa: `Billing/billingAccounts/departments`

- **Account di registrazione**: rappresenta un singolo proprietario dell'account. Non supporta la concessione dell'accesso a più persone.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Gli ambiti di governance sono associati a una singola directory, mentre gli ambiti di fatturazione EA non lo sono. Un account di fatturazione EA può avere sottoscrizioni in un numero qualsiasi di directory di Azure AD.

Gli ambiti di fatturazione EA supportano i ruoli seguenti:

- **Amministratore aziendale**: può gestire le impostazioni e l'accesso degli account di fatturazione, può visualizzare tutti i costi e gestire la configurazione dei costi, ad esempio i budget e le esportazioni. In funzione l'ambito di fatturazione EA è identico a quello del [ruolo di Azure di collaboratore di Gestione costi](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Utente aziendale con accesso in sola lettura** : può visualizzare le impostazioni dell'account di fatturazione, i dati sui costi e la configurazione dei costi, ad esempio i budget e le esportazioni. In funzione l'ambito di fatturazione EA è identico a quello del [ruolo di Azure di lettore per Gestione costi](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Amministratore del reparto**: può gestire le impostazioni del reparto, ad esempio il centro di costo, accedere, visualizzare tutti i costi e gestire la configurazione dei costi, ad esempio i budget e le esportazioni.  L'impostazione **DA view charges** (Visualizzazione addebiti per amministratori di reparto) deve essere abilitata perché gli amministratori del reparto e gli utenti con accesso in sola lettura possano visualizzare i costi. Se l'opzione **DA view charges** (Visualizzazione addebiti per amministratori di reparto) è disabilitata, gli utenti del reparto non possono visualizzare i costi a nessun livello, neanche se sono proprietari di un account o di una sottoscrizione.
- **Utente del reparto con accesso in sola lettura**: può visualizzare le impostazioni del reparto, i dati sui costi e la configurazione dei costi, ad esempio i budget e le esportazioni. Se l'opzione **DA view charges** (Visualizzazione addebiti per amministratori di reparto) è disabilitata, gli utenti del reparto non possono visualizzare i costi a nessun livello, neanche se sono proprietari di un account o di una sottoscrizione.
- **Proprietario dell'account**: può gestire le impostazioni dell'account di registrazione, ad esempio il centro di costo, visualizzare tutti i costi e gestire la configurazione dei costi, ad esempio i budget e le esportazioni, per l'account di registrazione. L'impostazione **OA view charges** (Visualizzazione addebiti per proprietari dell'account) deve essere abilitata perché i proprietari dell'account e gli utenti del controllo degli accessi in base al ruolo possano visualizzare i costi.

Gli utenti dell'account di fatturazione EA non hanno accesso diretto alle fatture. Le fatture sono disponibili da un sistema di contratti multilicenza esterno.

Le sottoscrizioni di Azure sono annidate negli account di registrazione. Gli utenti di fatturazione hanno accesso ai dati sui costi per le sottoscrizioni e i gruppi di risorse che si trovano nei rispettivi ambiti. Non possono accedere per visualizzare o gestire le risorse nella portale di Azure. Gli utenti possono visualizzare i costi passando a **Gestione dei costi e fatturazione** nell'elenco dei servizi del portale di Azure. Possono quindi filtrare i costi per le sottoscrizioni e i gruppi di risorse specifici per i quali è necessario creare un report.

Gli utenti di fatturazione non hanno accesso ai gruppi di gestione perché non rientrano in modo esplicito in un account di fatturazione specifico. È necessario concedere l'accesso ai gruppi di gestione in modo esplicito. I gruppi di gestione eseguono il rollup dei costi da tutte le sottoscrizioni annidate. Includono tuttavia solo gli acquisti basati sull'utilizzo. Non includono acquisti come ad esempio prenotazioni e offerte del Marketplace di terze parti. Per visualizzare questi costi, usare l'account di fatturazione EA.

## <a name="individual-agreement-scopes"></a>Ambiti di singoli contratti

Le sottoscrizioni di Azure create da singole offerte, ad esempio il pagamento in base al consumo e tipi correlati come la versione di valutazione gratuita e le offerte per sviluppo/test, non hanno un ambito dell'account di fatturazione esplicito. Al contrario, ogni sottoscrizione ha un proprietario dell'account o un amministratore dell'account, ad esempio il proprietario dell'account EA.

- [**Account di fatturazione**](../manage/view-all-accounts.md): rappresenta un singolo proprietario dell'account per una o più sottoscrizioni di Azure. Attualmente non supporta la concessione dell'accesso a più persone o l'accesso alle viste aggregate dei costi.

    Tipo di risorsa: Non applicabile

I singoli amministratori dell'account di sottoscrizione di Azure possono visualizzare e gestire i dati di fatturazione, ad esempio le fatture e i pagamenti, dal [Centro account di Azure](https://account.azure.com/subscriptions). Non possono tuttavia visualizzare i dati sui costi o gestire le risorse nel portale di Azure. Per concedere l'accesso all'amministratore dell'account, usare i ruoli di Gestione costi descritti in precedenza.

Diversamente dal Contratto Enterprise, i singoli amministratori dell'account di sottoscrizione di Azure possono visualizzare le fatture nel portale di Azure. Tenere presente che i ruoli Lettore e Collaboratore di Gestione costi non consentono l'accesso alle fatture. Per altre informazioni, vedere [Come concedere l'accesso alle fatture](../manage/manage-billing-access.md#give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Ambiti del Contratto del cliente Microsoft

Gli account di fatturazione del Contratto del cliente Microsoft dispongono degli ambiti seguenti:

- **Account di fatturazione**: rappresenta un contratto del cliente per più prodotti e servizi Microsoft. Gli account di fatturazione del Contratto del cliente non sono dal punto di vista funzionale identici alle registrazioni EA. Le registrazioni EA sono strettamente allineate ai profili di fatturazione.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Profilo di fatturazione**: definisce le sottoscrizioni incluse in una fattura. I profili di fatturazione sono dal punto di vista funzionale identici a una registrazione EA, poiché si tratta dell'ambito in cui vengono generate le fatture. Analogamente gli acquisti che non sono basati sull'utilizzo, ad esempio acquisti del Marketplace e prenotazioni, sono disponibili solo in questo ambito. Non sono inclusi nelle sezioni della fattura.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Sezione della fattura**: rappresenta un gruppo di sottoscrizioni in una fattura o in un profilo di fatturazione. Le sezioni della fattura sono simili ai reparti, vale a dire che più persone possono accedere a una sezione della fattura.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts/invoiceSections`

- **Cliente**: rappresenta un gruppo di sottoscrizioni associate a un cliente specifico di cui il partner ha eseguito l'onboarding in un Contratto del cliente Microsoft. Questo ambito è specifico per i CSP (Cloud Solution Provider).

A differenza degli ambiti di fatturazione del Contratto Enterprise, gli account di fatturazione del Contratto del cliente _vengono_ associati a una singola directory e non possono avere sottoscrizioni tra più directory di Azure AD.

Gli ambiti di fatturazione del Contratto del cliente non si applicano ai partner. I ruoli e le autorizzazioni dei partner sono documentati in [Assegnare autorizzazioni e ruoli utente](/partner-center/permissions-overview).

Gli ambiti di fatturazione del Contratto del cliente supportano i ruoli seguenti:

- **Proprietario**: può gestire le impostazioni di fatturazione e l'accesso, visualizzare tutti i costi e gestire la configurazione dei costi, ad esempio i budget e le esportazioni. In funzione l'ambito di fatturazione del Contratto del cliente è identico a quello del [ruolo di Azure di collaboratore di Gestione costi](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Collaboratore**: può gestire le impostazioni di fatturazione ad eccezione dell'accesso, può visualizzare tutti i costi e gestire la configurazione dei costi, ad esempio i budget e le esportazioni. In funzione l'ambito di fatturazione del Contratto del cliente è identico a quello del [ruolo di Azure di collaboratore di Gestione costi](../../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Lettore**: può visualizzare le impostazioni di fatturazione, i dati sui costi e la configurazione dei costi, ad esempio i budget e le esportazioni. In funzione l'ambito di fatturazione del Contratto del cliente è identico a quello del [ruolo di Azure di lettore per Gestione costi](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Gestione fatture**: può visualizzare e pagare le fatture e può visualizzare i dati sui costi e la configurazione, ad esempio i budget e le esportazioni. In funzione l'ambito di fatturazione del Contratto del cliente è identico a quello del [ruolo di Azure di lettore per Gestione costi](../../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Autore di sottoscrizioni di Azure**: può creare sottoscrizioni di Azure, visualizzare i costi e gestire la configurazione dei costi, ad esempio i budget e le esportazioni. In funzione l'ambito di fatturazione del Contratto del cliente è identico a quello del ruolo Proprietario dell'account di registrazione EA.

Le sottoscrizioni di Azure sono annidate nelle sezioni della fattura, come lo sono negli account di registrazione EA. Gli utenti di fatturazione hanno accesso ai dati sui costi per le sottoscrizioni e i gruppi di risorse che si trovano nei rispettivi ambiti. Non possono tuttavia accedere per visualizzare o gestire le risorse nella portale di Azure. Gli utenti di fatturazione possono visualizzare i costi passando a **Gestione dei costi e fatturazione** nell'elenco dei servizi del portale di Azure. Possono quindi filtrare i costi per le sottoscrizioni e i gruppi di risorse specifici per i quali è necessario creare un report.

Gli utenti di fatturazione non hanno accesso ai gruppi di gestione perché non rientrano in modo esplicito in un account di fatturazione. Tuttavia, quando i gruppi di gestione sono abilitati per l'organizzazione, il rollup di tutti i costi della sottoscrizione viene eseguito all'account di fatturazione e al gruppo di gestione radice, perché sono entrambi vincolati a una singola directory. I gruppi di gestione includono solo gli acquisti basati sull'utilizzo. Acquisti come ad esempio le prenotazioni e le offerte del Marketplace di terze parti non sono inclusi nei gruppi di gestione. L'account di fatturazione e il gruppo di gestione radice possono quindi segnalare totali diversi. Per visualizzare questi costi, usare l'account di fatturazione o il rispettivo profilo di fatturazione.

## <a name="aws-scopes"></a>Scopi AWS

Al termine dell'integrazione di AWS, vedere [Impostare e configurare l'integrazione di AWS](aws-integration-set-up-configure.md). Sono disponibili gli ambiti seguenti:

- **Account di fatturazione esterno**: rappresenta un contratto del cliente con un fornitore di terze parti. È simile all'account di fatturazione del contratto Enterprise.

    Tipo di risorsa: `Microsoft.CostManagement/externalBillingAccounts`

- **Sottoscrizione esterna**: rappresenta un account operativo del cliente con un fornitore di terze parti. È simile a una sottoscrizione di Azure.

    Tipo di risorsa: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Ambiti di Cloud Solution Provider (CSP)

Gli ambiti seguenti sono supportati per i CSP con clienti in un Contratto del cliente Microsoft:

- **Account di fatturazione**: rappresenta un contratto del cliente per più prodotti e servizi Microsoft. Gli account di fatturazione del Contratto del cliente non sono dal punto di vista funzionale identici alle registrazioni EA. Le registrazioni EA sono strettamente allineate ai profili di fatturazione.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Profilo di fatturazione**: definisce le sottoscrizioni incluse in una fattura. I profili di fatturazione sono dal punto di vista funzionale identici a una registrazione EA, poiché si tratta dell'ambito in cui vengono generate le fatture. Analogamente gli acquisti che non sono basati sull'utilizzo, ad esempio acquisti del Marketplace e prenotazioni, sono disponibili solo in questo ambito.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Cliente**: rappresenta un gruppo di sottoscrizioni associate a un cliente specifico di cui il partner ha eseguito l'onboarding in un Contratto del cliente Microsoft.

Solo gli utenti con i ruoli *Amministratore globale* e *Agente amministratore* possono gestire e visualizzare i costi per gli account di fatturazione, i profili di fatturazione e i clienti direttamente nel tenant di Azure del partner. Per altre informazioni sui ruoli del Centro per i partner, vedere [Assegnare autorizzazioni e ruoli utente](/partner-center/permissions-overview).

Gestione costi di Azure supporta i clienti del Centro per i partner CSP solo se dispongono di un Contratto del cliente Microsoft. Per i clienti supportati da CSP che non hanno ancora un Contratto del cliente Microsoft, vedere [Centro per i partner](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

I gruppi di gestione negli ambiti CSP non sono supportati da Gestione costi. Se si ha una sottoscrizione CSP e si imposta l'ambito su un gruppo di gestione nell'analisi dei costi, verrà visualizzato un errore simile al seguente:

`Management group <ManagementGroupName> does not have any valid subscriptions`

## <a name="switch-between-scopes-in-cost-management"></a>Passare da un ambito all'altro in Gestione costi

Tutte le viste di Gestione costi nel portale di Azure includono un'etichetta di selezione **Ambito** nella parte superiore sinistra della vista. Usare questa etichetta per modificare rapidamente l'ambito. Selezionare l'etichetta **Ambito** per aprire il selettore di ambiti. Vengono visualizzati gli account di fatturazione, il gruppo di gestione radice e tutte le sottoscrizioni non annidate nel gruppo di gestione radice. Per selezionare un ambito, selezionare lo sfondo per evidenziarlo, quindi scegliere **Seleziona** nella parte inferiore. Per espandere gli ambiti annidati e vedere ad esempio i gruppi di risorse in una sottoscrizione, fare clic sul collegamento con il nome dell'ambito. Per selezionare l'ambito padre a qualsiasi livello annidato, scegliere **Seleziona questo &lt;ambito&gt;** nella parte superiore del selettore di ambiti.

## <a name="identify-the-resource-id-for-a-scope"></a>Identificare l'ID risorsa per un ambito

Quando si usano le API di Gestione costi, conoscere l'ambito è fondamentale. Usare le informazioni seguenti per compilare l'URI dell'ambito appropriato per le API di Gestione costi.

### <a name="billing-accounts"></a>Account di fatturazione

1. Aprire il portale di Azure e passare a **Gestione dei costi e fatturazione** nell'elenco dei servizi.
2. Selezionare **Proprietà** nel menu dell'account di fatturazione.
3. Copiare l'ID dell'account di fatturazione.
4. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Profili di fatturazione

1. Aprire il portale di Azure e passare a **Gestione dei costi e fatturazione** nell'elenco dei servizi.
2. Selezionare **Profili di fatturazione** nel menu dell'account di fatturazione.
3. Selezionare il nome del profilo di fatturazione.
4. Selezionare **Proprietà** nel menu del profilo di fatturazione.
5. Copiare gli ID dell'account di fatturazione e del profilo di fatturazione.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Sezioni della fattura

1. Aprire il portale di Azure e passare a **Gestione dei costi e fatturazione** nell'elenco dei servizi.
2. Selezionare **Sezioni della fattura** nel menu dell'account di fatturazione.
3. Selezionare il nome della sezione della fattura.
4. Selezionare **Proprietà** nel menu della sezione della fattura.
5. Copiare gli ID dell'account di fatturazione e della sezione fattura.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Reparti EA

1. Aprire il portale di Azure e passare a **Gestione dei costi e fatturazione** nell'elenco dei servizi.
2. Selezionare **Reparti** nel menu dell'account di fatturazione.
3. Selezionare il nome del reparto.
4. Selezionare **proprietà** dal menu del reparto.
5. Copiare gli ID dell'account di fatturazione e del reparto.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Account di registrazione EA

1. Aprire il portale di Azure e passare a **Gestione dei costi e fatturazione** nell'elenco dei servizi.
2. Selezionare **Enrollment accounts** (Account di registrazione) nel menu dell'account di fatturazione.
3. Selezionare il nome dell'account di registrazione.
4. Selezionare **Proprietà** nel menu dell'account di registrazione.
5. Copiare gli ID dell'account di fatturazione e dell'account di registrazione.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Gruppo di gestione

1. Aprire il portale di Azure e passare a **Gruppi di gestione** nell'elenco dei servizi.
2. Passare al gruppo di gestione.
3. Copiare l'ID del gruppo di gestione dalla tabella.
4. L'ambito è: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subscription

1. Aprire il portale di Azure e passare a **Sottoscrizioni** nell'elenco dei servizi.
2. Copiare l'ID della sottoscrizione dalla tabella.
3. L'ambito è: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Gruppi di risorse

1. Aprire il portale di Azure e passare a **Gruppi di risorse** nell'elenco dei servizi.
2. Selezionare il nome del gruppo di risorse.
3. Selezionare **Proprietà** nel menu del gruppo di risorse.
4. Copiare il valore del campo ID risorsa.
5. L'ambito è: `"/subscriptions/{id}/resourceGroups/{name}"`

Gestione costi è attualmente supportato in [Servizi globali di Azure](https://management.azure.com) e in [Azure per enti pubblici](https://management.usgovcloudapi.net). Per altre informazioni su Azure per enti pubblici, vedere [Endpoint dell'API di Servizi globali di Azure e di Azure per enti pubblici](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping).

## <a name="next-steps"></a>Passaggi successivi

- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](quick-acm-cost-analysis.md).
