---
title: Comprendere e usare gli ambiti di gestione dei costi di Azure | Microsoft Docs
description: Questo articolo consente di comprendere gli ambiti di fatturazione e di gestione delle risorse disponibili in Azure e come usare gli ambiti in gestione costi e API.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 41d83d4a6c5aad4c3b575513c6b3e2e25a425829
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338644"
---
# <a name="understand-and-work-with-scopes"></a>Informazioni e utilizzo degli ambiti

Questo articolo consente di comprendere gli ambiti di fatturazione e di gestione delle risorse disponibili in Azure e come usare gli ambiti in gestione costi e API.

## <a name="scopes"></a>Ambiti

Un _ambito_ è un nodo nella gerarchia di risorse di Azure in cui Azure ad utenti accedono e gestiscono i servizi. La maggior parte delle risorse di Azure viene creata e distribuita in gruppi di risorse, che fanno parte di sottoscrizioni. Microsoft offre anche due gerarchie sopra le sottoscrizioni di Azure con ruoli specializzati per gestire i dati di fatturazione:
- Dati di fatturazione, ad esempio pagamenti e fatture
- Servizi cloud, ad esempio costi e governance dei criteri

Gli ambiti sono la posizione in cui si gestiscono i dati di fatturazione, i ruoli specifici dei pagamenti, la visualizzazione delle fatture e la gestione generale degli account. I ruoli fatturazione e account vengono gestiti separatamente da quelli usati per la gestione delle risorse, [che usano il](../role-based-access-control/overview.md)controllo degli accessi in base al ruolo Per distinguere chiaramente lo scopo degli ambiti distinti, incluse le differenze relative al controllo di accesso, questi sono denominati rispettivamente _ambiti di fatturazione_ e _ambiti RBAC_.

## <a name="how-cost-management-uses-scopes"></a>Modalità di utilizzo degli ambiti in gestione costi

Gestione costi funziona in tutti gli ambiti sopra le risorse per consentire alle organizzazioni di gestire i costi a livello di accesso, sia che si tratti dell'intero account di fatturazione o di un singolo gruppo di risorse. Anche se gli ambiti di fatturazione variano in base al contratto Microsoft (tipo di sottoscrizione), gli ambiti RBAC non lo sono.

## <a name="azure-rbac-scopes"></a>Ambiti RBAC di Azure

Azure supporta tre ambiti per la gestione delle risorse. Ogni ambito supporta la gestione dell'accesso e della governance, tra cui la gestione dei costi.

- [**Gruppi di gestione**](../governance/management-groups/overview.md) : contenitori gerarchici, fino a otto livelli, per organizzare le sottoscrizioni di Azure.

    Tipo di risorsa: [Microsoft. Management/managementGroups](/rest/api/resources/managementgroups)

- **Sottoscrizioni** : contenitori primari per le risorse di Azure.

    Tipo di risorsa: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Gruppi di risorse**](../azure-resource-manager/resource-group-overview.md#resource-groups) : raggruppamenti logici di risorse correlate per una soluzione di Azure che condividono lo stesso ciclo di vita. Ad esempio risorse distribuite ed eliminate insieme.

    Tipo di risorsa: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

I gruppi di gestione consentono di organizzare le sottoscrizioni in una gerarchia. Ad esempio, è possibile creare una gerarchia di organizzazioni logiche usando i gruppi di gestione. Assegnare quindi ai team le sottoscrizioni per i carichi di lavoro di produzione e di sviluppo/test. E quindi creare gruppi di risorse nelle sottoscrizioni per gestire ogni sottosistema o componente.

La creazione di una gerarchia organizzativa consente il rollup dei costi e della conformità dei criteri organizzazione. Ogni leader può quindi visualizzare e analizzare i costi correnti. Possono quindi creare budget per limitare i modelli di spesa e ottimizzare i costi con le raccomandazioni di Advisor al livello più basso.

La concessione dell'accesso per visualizzare i costi e, facoltativamente, la gestione della configurazione dei costi, ad esempio i budget e le esportazioni, viene eseguita sugli ambiti di governance usando il controllo degli accessi Usare il controllo degli accessi in base al ruolo di Azure per concedere a Azure AD utenti e gruppi l'accesso per eseguire un set predefinito di azioni definite in un ruolo in un ambito specifico e sotto. Ad esempio, un ruolo assegnato a un ambito del gruppo di gestione concede anche le stesse autorizzazioni per le sottoscrizioni annidate e i gruppi di risorse.

Gestione costi supporta i ruoli predefiniti seguenti per ognuno degli ambiti seguenti:

- [**Proprietario**](../role-based-access-control/built-in-roles.md#owner) : può visualizzare i costi e gestire tutti gli elementi, inclusa la configurazione dei costi.
- [**Collaboratore**](../role-based-access-control/built-in-roles.md#contributor) : può visualizzare i costi e gestire tutti gli elementi, inclusa la configurazione dei costi, escluso il controllo degli accessi.
- [**Reader**](../role-based-access-control/built-in-roles.md#reader) : può visualizzare tutti gli elementi, inclusi i dati relativi ai costi e la configurazione, ma non può apportare modifiche.
- [**Collaboratore gestione costi**](../role-based-access-control/built-in-roles.md#cost-management-contributor) : può visualizzare i costi, gestire la configurazione dei costi e visualizzare le raccomandazioni.
- [**Lettore di gestione costi**](../role-based-access-control/built-in-roles.md#cost-management-reader) : può visualizzare i dati sui costi, la configurazione dei costi e le raccomandazioni per la visualizzazione.

Collaboratore gestione costi è il ruolo con privilegi minimi consigliato. Consente agli utenti di accedere per creare e gestire i budget e le esportazioni in modo da monitorare e segnalare in modo più efficace i costi. I collaboratori di gestione costi potrebbero anche richiedere ruoli aggiuntivi per supportare scenari di gestione dei costi end-to-end. Esaminare gli scenari seguenti:

- **Agire quando vengono superati i budget: i** collaboratori di gestione costi devono anche accedere per creare e/o gestire i gruppi di azioni per rispondere automaticamente alle eccedenze. Considerare la possibilità di concedere il [collaboratore al monitoraggio](../role-based-access-control/built-in-roles.md#monitoring-contributor) a un gruppo di risorse che contiene il gruppo di azioni da usare quando vengono superate le soglie del budget. L'automazione di azioni specifiche richiede ruoli aggiuntivi per i servizi specifici usati, ad esempio automazione e funzioni di Azure.
- **Pianificare l'esportazione dei dati sui costi: i** collaboratori di gestione costi devono anche accedere per gestire gli account di archiviazione per pianificare un'esportazione per la copia dei dati in un account di archiviazione. Prendere in considerazione la concessione di un [collaboratore account di archiviazione](../role-based-access-control/built-in-roles.md#storage-account-contributor) a un gruppo di risorse che contiene l'account di archiviazione in cui vengono esportati i dati
- **Visualizzazione delle raccomandazioni di risparmio** sui costi: i lettori di gestione costi e i collaboratori di gestione costi hanno accesso per *visualizzare* le raccomandazioni sui costi per impostazione predefinita. Tuttavia, l'accesso per agire sulle raccomandazioni sui costi richiede l'accesso alle singole risorse. Si consiglia di concedere un [ruolo specifico del servizio](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) se si desidera agire su una raccomandazione basata sui costi.

## <a name="enterprise-agreement-scopes"></a>Ambiti di Enterprise Agreement

Gli account di fatturazione Enterprise Agreement (EA), denominati anche registrazioni, hanno gli ambiti seguenti:

- [**Account di fatturazione**](../billing/billing-view-all-accounts.md) : rappresenta una registrazione EA. Le fatture vengono generate in questo ambito. Gli acquisti che non sono basati sull'utilizzo, ad esempio il Marketplace e le prenotazioni, sono disponibili solo in questo ambito. Non sono rappresentati nei reparti o negli account di registrazione.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Reparto** : raggruppamento facoltativo di account di registrazione.

    Tipo di risorsa: `Billing/billingAccounts/departments`

- **Account di registrazione** : rappresenta un singolo proprietario dell'account. Non supporta la concessione dell'accesso a più persone.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Sebbene gli ambiti di governance siano associati a una singola directory, gli ambiti di fatturazione con contratto Enterprise non lo sono. Un account di fatturazione EA può avere sottoscrizioni in un numero qualsiasi di directory Azure AD.

Gli ambiti di fatturazione EA supportano i ruoli seguenti:

- **Enterprise Admin** : può gestire le impostazioni e l'accesso agli account di fatturazione, può visualizzare tutti i costi e può gestire la configurazione dei costi. Ad esempio, i budget e le esportazioni. In funzione, l'ambito di fatturazione EA è lo stesso [ruolo di Collaboratore gestione costi di Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Utente di sola lettura aziendale** : può visualizzare le impostazioni dell'account di fatturazione, i dati sui costi e la configurazione dei costi. Ad esempio, i budget e le esportazioni. In funzione, l'ambito di fatturazione EA corrisponde al ruolo di [lettura gestione costi di Azure](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Amministratore del reparto** : può gestire le impostazioni del reparto, ad esempio il centro di costo, e può accedere, visualizzare tutti i costi e gestire la configurazione dei costi. Ad esempio, i budget e le esportazioni.  Per **visualizzare i costi** , è necessario abilitare l'impostazione per l'account di fatturazione per gli amministratori del reparto e per gli utenti di sola lettura. Se gli **addebiti** per le viste sono disabilitati, gli utenti del reparto non possono visualizzare i costi a qualsiasi livello, anche se sono proprietari di un account o di una sottoscrizione.
- **Utente** di sola lettura del reparto: può visualizzare le impostazioni del reparto, i dati relativi ai costi e la configurazione dei costi. Ad esempio, i budget e le esportazioni. Se gli **addebiti** per le viste sono disabilitati, gli utenti del reparto non possono visualizzare i costi a qualsiasi livello, anche se sono proprietari di un account o di una sottoscrizione.
- **Proprietario dell'account** : può gestire le impostazioni dell'account di registrazione, ad esempio il centro di costo, visualizzare tutti i costi e gestire la configurazione dei costi, ad esempio i budget e le esportazioni, per l'account di registrazione. Per visualizzare i costi, è necessario abilitare l'impostazione dell'account di fatturazione per le **visualizzazioni Ao** per i proprietari degli account e gli utenti RBAC.

Gli utenti dell'account di fatturazione EA non hanno accesso diretto alle fatture. Le fatture sono disponibili da un sistema di contratti multilicenza esterno.

Le sottoscrizioni di Azure sono annidate in account di registrazione. Gli utenti di fatturazione hanno accesso ai dati sui costi per le sottoscrizioni e i gruppi di risorse che si trovano nei rispettivi ambiti. Non hanno accesso per visualizzare o gestire le risorse nella portale di Azure. Gli utenti di fatturazione possono visualizzare i costi passando a **Gestione costi e fatturazione** nell'elenco portale di Azure dei servizi. Quindi, possono filtrare i costi per le sottoscrizioni e i gruppi di risorse specifici per i quali è necessario creare un report.

Gli utenti che eseguono la fatturazione non hanno accesso ai gruppi di gestione perché non rientrano in modo esplicito con un account di fatturazione specifico. È necessario concedere l'accesso ai gruppi di gestione in modo esplicito. I gruppi di gestione eseguono il rollup dei costi di tutte le sottoscrizioni nidificate. Tuttavia, includono solo acquisti basati sull'utilizzo. Non includono acquisti come prenotazioni e offerte di Marketplace di terze parti. Per visualizzare questi costi, usare l'account di fatturazione EA.

## <a name="individual-agreement-scopes"></a>Ambiti di singoli contratti

Le sottoscrizioni di Azure create da singole offerte come il pagamento in base al consumo e i tipi correlati come la versione di valutazione gratuita e le offerte per sviluppo/test non hanno un ambito di account di fatturazione esplicito. Al contrario, ogni sottoscrizione ha un proprietario dell'account o un amministratore dell'account, ad esempio il proprietario dell'account EA.

- [**Account di fatturazione**](../billing/billing-view-all-accounts.md) : rappresenta un singolo proprietario dell'account per una o più sottoscrizioni di Azure. Attualmente non supporta la concessione dell'accesso a più persone o l'accesso alle visualizzazioni di costo aggregate.

    Tipo di risorsa: Non applicabile

Gli amministratori dell'account di sottoscrizione di Azure singoli possono visualizzare e gestire i dati di fatturazione, ad esempio fatture e pagamenti, dal [centro account di Azure](https://account.azure.com/subscriptions). Tuttavia, non possono visualizzare i dati sui costi o gestire le risorse nella portale di Azure. Per concedere l'accesso all'amministratore dell'account, usare i ruoli di gestione costi indicati in precedenza.

Diversamente da EA, i singoli amministratori di account di sottoscrizione di Azure possono visualizzare le fatture nel portale di Azure. Tenere presente che i ruoli di collaboratore per la gestione dei costi e i lettori non forniscono l'accesso alle fatture. Per ulteriori informazioni, vedere [come concedere l'accesso alle fatture](../billing/billing-manage-access.md##give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Ambiti del contratto clienti Microsoft

Gli account di fatturazione del contratto cliente Microsoft hanno gli ambiti seguenti:

- **Account di fatturazione** : rappresenta un contratto del cliente per più prodotti e servizi Microsoft. Gli account di fatturazione del contratto clienti non sono funzionalmente identici alle registrazioni EA. Le registrazioni EA sono strettamente allineate ai profili di fatturazione.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Profilo di fatturazione** : definisce le sottoscrizioni incluse in una fattura. I profili di fatturazione sono l'equivalente funzionale di una registrazione EA, poiché si tratta dell'ambito in cui vengono generate le fatture. Analogamente, gli acquisti che non sono basati sull'utilizzo (ad esempio Marketplace e prenotazioni) sono disponibili solo in questo ambito. Non sono incluse nelle sezioni della fattura.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Sezione fattura** : rappresenta un gruppo di sottoscrizioni in una fattura o un profilo di fatturazione. Le sezioni della fattura sono simili ai reparti: più persone possono accedere a una sezione di fattura.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts/invoiceSections`

Diversamente dagli ambiti di fatturazione EA, gli account di fatturazione del contratto cliente _sono_ associati a una singola directory e non possono avere sottoscrizioni tra più directory Azure ad.

Gli ambiti di fatturazione del contratto del cliente supportano i ruoli seguenti:

- **Proprietario** : può gestire le impostazioni di fatturazione e l'accesso, visualizzare tutti i costi e gestire la configurazione dei costi. Ad esempio, i budget e le esportazioni. In funzione, questo ambito di fatturazione del contratto del cliente corrisponde al [ruolo di collaboratore di gestione costi di Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Collaboratore** : può gestire le impostazioni di fatturazione ad eccezione dell'accesso, visualizzare tutti i costi e gestire la configurazione dei costi. Ad esempio, i budget e le esportazioni. In funzione, questo ambito di fatturazione del contratto del cliente corrisponde al [ruolo di collaboratore di gestione costi di Azure RBAC](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Reader** : può visualizzare le impostazioni di fatturazione, i dati relativi ai costi e la configurazione dei costi. Ad esempio, i budget e le esportazioni. In funzione, questo ambito di fatturazione del contratto del cliente corrisponde al [ruolo di lettura gestione costi di Azure](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Responsabile delle fatture** : può visualizzare e pagare le fatture e può visualizzare i dati sui costi e la configurazione. Ad esempio, i budget e le esportazioni. In funzione, questo ambito di fatturazione del contratto del cliente corrisponde al [ruolo di lettura gestione costi di Azure](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Creatore della sottoscrizione di Azure** : può creare sottoscrizioni di Azure, visualizzare i costi e gestire la configurazione dei costi. Ad esempio, i budget e le esportazioni. In funzione, questo ambito di fatturazione del contratto del cliente corrisponde al ruolo di proprietario dell'account di registrazione EA.

Le sottoscrizioni di Azure sono annidate in sezioni di fattura, ad esempio come sono in account di registrazione EA. Gli utenti di fatturazione hanno accesso ai dati sui costi per le sottoscrizioni e i gruppi di risorse che si trovano nei rispettivi ambiti. Tuttavia, non hanno accesso per visualizzare o gestire le risorse nella portale di Azure. Gli utenti di fatturazione possono visualizzare i costi passando a **Gestione costi e fatturazione** nell'elenco portale di Azure dei servizi. Filtrare quindi i costi per le sottoscrizioni e i gruppi di risorse specifici per i quali è necessario creare un report.

Gli utenti che eseguono la fatturazione non hanno accesso ai gruppi di gestione perché non rientrano in modo esplicito con l'account di fatturazione. Tuttavia, quando i gruppi di gestione sono abilitati per l'organizzazione, viene eseguito il rollup di tutti i costi della sottoscrizione all'account di fatturazione e al gruppo di gestione radice perché sono entrambi vincolati a una singola directory. I gruppi di gestione includono solo gli acquisti basati sull'utilizzo. Gli acquisti come le prenotazioni e le offerte di Marketplace di terze parti non sono inclusi nei gruppi di gestione. L'account di fatturazione e il gruppo di gestione radice possono quindi segnalare totali diversi. Per visualizzare questi costi, utilizzare l'account di fatturazione o il rispettivo profilo di fatturazione.

## <a name="aws-scopes"></a>Ambiti AWS

Al termine dell'integrazione di AWS, vedere [Setup and Configure AWS Integration](aws-integration-set-up-configure.md). Sono disponibili gli ambiti seguenti:

- **Account di fatturazione esterno** : rappresenta un contratto del cliente con un fornitore di terze parti. Questa operazione è simile all'account di fatturazione EA.

    Tipo di risorsa: `Microsoft.CostManagement/externalBillingAccounts`
    
- **Sottoscrizione esterna** : rappresenta un account operativo del cliente con un fornitore di terze parti. Questa operazione è simile a una sottoscrizione di Azure.

    Tipo di risorsa: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Ambiti del provider di soluzioni cloud (CSP)

I partner Cloud Solution Provider (CSP) non sono attualmente supportati nella gestione dei costi. È invece possibile usare il [centro](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)per i partner.

## <a name="switch-between-scopes-in-cost-management"></a>Passare da un ambito all'altra in gestione costi

Tutte le viste di gestione dei costi nel portale di Azure includono una pillola di selezione dell' **ambito** nella parte superiore sinistra della visualizzazione. Usarlo per modificare rapidamente l'ambito. Fare clic sulla pillola **ambito** per aprire la selezione ambito. Mostra gli account di fatturazione, il gruppo di gestione radice e tutte le sottoscrizioni non annidate nel gruppo di gestione radice. Per selezionare un ambito, fare clic sullo sfondo per evidenziarlo, quindi fare clic su **Seleziona** nella parte inferiore. Per eseguire il drill-in degli ambiti annidati, ad esempio i gruppi di risorse in una sottoscrizione, fare clic sul collegamento nome ambito. Per selezionare l'ambito padre a qualsiasi livello annidato, fare clic su **Seleziona questo &lt;scope @ no__t-2** nella parte superiore della selezione ambito.

## <a name="identify-the-resource-id-for-a-scope"></a>Identificare l'ID risorsa per un ambito

Quando si lavora con le API di gestione costi, la conoscenza dell'ambito è fondamentale. Usare le informazioni seguenti per compilare l'URI di ambito appropriato per le API di gestione dei costi.

### <a name="billing-accounts"></a>Account di fatturazione

1. Aprire il portale di Azure, quindi passare a **Gestione costi e fatturazione** nell'elenco dei servizi.
2. Scegliere **Proprietà** dal menu account di fatturazione.
3. Copiare l'ID dell'account di fatturazione.
4. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Profili di fatturazione

1. Aprire il portale di Azure, quindi passare a **Gestione costi e fatturazione** nell'elenco dei servizi.
2. Selezionare **profili di fatturazione** nel menu account di fatturazione.
3. Fare clic sul nome del profilo di fatturazione desiderato.
4. Scegliere **Proprietà** dal menu Profilo di fatturazione.
5. Copiare gli ID del profilo di fatturazione e dell'account di fatturazione.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Sezioni della fattura

1. Aprire il portale di Azure, quindi passare a **Gestione costi e fatturazione** nell'elenco dei servizi.
2. Selezionare le **sezioni fattura** nel menu account di fatturazione.
3. Fare clic sul nome della sezione della fattura desiderata.
4. Scegliere **Proprietà** dal menu della sezione fattura.
5. Copiare gli ID della sezione fattura e account di fatturazione.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Dipartimenti EA

1. Aprire il portale di Azure, quindi passare a **Gestione costi e fatturazione** nell'elenco dei servizi.
2. Selezionare **reparti** nel menu account di fatturazione.
3. Fare clic sul nome del reparto desiderato.
4. Scegliere **Proprietà** dal menu reparto.
5. Copiare l'account di fatturazione e gli ID reparto.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Account di registrazione EA

1. Aprire il portale di Azure e passare a **Gestione costi e fatturazione** nell'elenco dei servizi.
2. Selezionare **account di registrazione** nel menu account di fatturazione.
3. Fare clic sul nome dell'account di registrazione desiderato.
4. Selezionare **Proprietà** nel menu account di registrazione.
5. Copiare l'account di fatturazione e gli ID dell'account di registrazione.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Gruppo di gestione

1. Aprire il portale di Azure e passare a **gruppi di gestione** nell'elenco dei servizi.
2. Passare al gruppo di gestione desiderato.
3. Copiare l'ID del gruppo di gestione dalla tabella.
4. L'ambito è: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Sottoscrizione

1. Aprire il portale di Azure e passare alle **sottoscrizioni** nell'elenco dei servizi.
2. Copiare l'ID sottoscrizione dalla tabella.
3. L'ambito è: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Gruppi di risorse

1. Aprire il portale di Azure e passare a **gruppi di risorse** nell'elenco dei servizi.
2. Fare clic sul nome del gruppo di risorse desiderato.
3. Selezionare **Proprietà** nel menu gruppo di risorse.
4. Copiare il valore del campo ID risorsa.
5. L'ambito è: `"/subscriptions/{id}/resourceGroups/{name}"`

Gestione costi è attualmente supportato in Azure [Global](https://management.azure.com) e in [Azure per enti pubblici](https://management.usgovcloudapi.net). Per altre informazioni su Azure per enti pubblici, vedere [endpoint dell'API globale e per enti pubblici di Azure](../azure-government/documentation-government-developer-guide.md#endpoint-mapping) _._

## <a name="next-steps"></a>Passaggi successivi

- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](quick-acm-cost-analysis.md).
