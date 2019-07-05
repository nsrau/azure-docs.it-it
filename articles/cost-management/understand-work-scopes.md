---
title: Comprendere e lavorare con gli ambiti di gestione costi di Azure | Microsoft Docs
description: Questo articolo aiuta a comprendere la fatturazione e risorse Gestione gli ambiti disponibili in Azure e come usare gli ambiti API e gestione dei costi.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 699707953ae06afa9cbf3cc7286f94917ba0efca
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490120"
---
# <a name="understand-and-work-with-scopes"></a>Informazioni e utilizzo degli ambiti

Questo articolo aiuta a comprendere la fatturazione e risorse Gestione gli ambiti disponibili in Azure e come usare gli ambiti API e gestione dei costi.

## <a name="scopes"></a>Ambiti

Oggetto _ambito_ è un nodo nella gerarchia di risorse di Azure in cui gli utenti di Azure AD accedano e gestire i servizi. Le risorse di Azure più vengono create e distribuite in gruppi di risorse che fanno parte delle sottoscrizioni. Microsoft offre anche due gerarchie sopra le sottoscrizioni di Azure in caso di specifiche dei ruoli per gestire i dati di fatturazione:
- Dati di fatturazione, ad esempio le fatture e pagamenti
- Servizi cloud, quali costi e i criteri di governance

Gli ambiti sono in cui gestire i dati di fatturazione, hanno ruoli specifici per i pagamenti, visualizzazione di fatture e condurre gestione generale degli account. I ruoli di account e fatturazione vengono gestiti separatamente rispetto a quelli utilizzati per la gestione delle risorse, che utilizzano [Azure RBAC](../role-based-access-control/overview.md). Per distinguere chiaramente lo scopo degli ambiti separati, incluse le differenze di controllo di accesso, questi sono detti _ambiti di fatturazione_ e _ambiti RBAC_, rispettivamente.

## <a name="how-cost-management-uses-scopes"></a>Modalità di gestione costi di utilizzo gli ambiti

Funzionamento della gestione dei costi gli ambiti affatto le risorse per consentire alle organizzazioni di gestire i costi a livello di corrispondenza del quale cui hanno accesso, l'intero account di fatturazione o di un singolo gruppo di risorse indicate sopra. Anche se gli ambiti di fatturazione variano a seconda del contratto di Microsoft (tipo di sottoscrizione), gli ambiti RBAC non.

## <a name="azure-rbac-scopes"></a>Ambiti di RBAC di Azure

Azure supporta tre ambiti per la gestione delle risorse. Ogni ambito supporta la gestione di accesso e la governance, tra cui ma non limitato alla, gestione dei costi.

- [**I gruppi di gestione** ](../governance/management-groups/index.md) -contenitori gerarchici, fino a otto livelli, per organizzare le sottoscrizioni di Azure.

    Tipo di risorsa: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Le sottoscrizioni** -contenitori primari per le risorse di Azure.

    Tipo di risorsa: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Gruppi di risorse** ](../azure-resource-manager/resource-group-overview.md#resource-groups) -raggruppamenti logici di risorse correlate per una soluzione di Azure che condividono lo stesso ciclo di vita. Ad esempio le risorse distribuite e eliminate insieme.

    Tipo di risorsa: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

I gruppi di gestione consentono di organizzare le sottoscrizioni in una gerarchia. Ad esempio, si potrebbe creare una gerarchia logica dell'organizzazione tramite i gruppi di gestione. Quindi, assegnare le sottoscrizioni ai team per carichi di lavoro di sviluppo/test e produzione. E quindi creare gruppi di risorse nelle sottoscrizioni per gestire ogni sottosistema o componente.

Creazione di che una gerarchia organizzativa consente di costi e la conformità ai criteri rollup livello di organizzazione. Quindi, ogni carattere di riempimento possono visualizzare e analizzare i costi correnti. E quindi possono creare i budget per inquadrano errati modelli di spesa e ottimizzare i costi con i consigli di Advisor al livello inferiore.

Concessione dell'accesso per visualizzare i costi e, facoltativamente, gestire la configurazione di costo, ad esempio i budget e le esportazioni, viene eseguito sugli ambiti di governance tramite RBAC di Azure. Si usa Azure RBAC per concedere agli utenti di Azure AD e i gruppi di accesso per l'esecuzione di un set predefinito di azioni definite in un ruolo in un ambito specifico e versioni precedenti. Ad esempio, un ambito del gruppo di gestione assegnato un ruolo concede inoltre le stesse autorizzazioni per le sottoscrizioni nidificate e gruppi di risorse.

Gestione dei costi supporta i seguenti ruoli predefiniti per ognuno degli ambiti seguenti:

- [**Proprietario** ](../role-based-access-control/built-in-roles.md#owner) : può visualizzare i costi e gestire tutto, inclusa la configurazione di costo.
- [**Per i collaboratori** ](../role-based-access-control/built-in-roles.md#contributor) : è possibile visualizzare i costi e gestire tutto, inclusa la configurazione di costo, escluso il controllo di accesso.
- [**Lettore** ](../role-based-access-control/built-in-roles.md#reader) : può visualizzare tutto, compresi i dati relativi ai costi e la configurazione, ma non è possibile apportare modifiche.
- [**Collaboratore gestione dei costi** ](../role-based-access-control/built-in-roles.md#cost-management-contributor) : può visualizzare i costi, gestire la configurazione di costi e visualizzare le raccomandazioni corrispondenti.
- [**Lettore di gestione dei costi** ](../role-based-access-control/built-in-roles.md#cost-management-reader) : può visualizzare i dati di costo, la configurazione di costo e visualizzare le raccomandazioni corrispondenti.

Collaboratore gestione costi è il ruolo con privilegi minimi consigliato. La consente agli utenti l'accesso creare e gestire i budget e l'esportazione in modo efficace più monitorare e creare report sui costi. I collaboratori di gestione costi può inoltre richiedere ruoli aggiuntivi per supportare scenari di gestione costi di end-to-end. Esaminare gli scenari seguenti:

- **Intervenire quando vengono superati il budget** – gestione costose anche necessario accedere per creare e/o gestire gruppi di azione per rispondere automaticamente a un quantità in eccedenza. Provare a concedere [collaboratore al monitoraggio](../role-based-access-control/built-in-roles.md#monitoring-contributor) a un gruppo di risorse che contiene il gruppo di azioni da utilizzare quando vengono superate le soglie di budget. Automazione delle azioni specifiche richiede che i ruoli aggiuntivi per i servizi specifici usati, ad esempio l'automazione e funzioni di Azure.
- **Pianificazione di esportazione dei dati di costo** – gestione costose anche necessario accedere per gestire gli account di archiviazione per pianificare un'esportazione per copiare dati in un account di archiviazione. Provare a concedere [collaboratore Account di archiviazione](../role-based-access-control/built-in-roles.md#storage-account-contributor) in un gruppo di risorse che contiene lo spazio di archiviazione viene esportato account in cui i dati sui costi.
- **Visualizzazione delle raccomandazioni convenienti** – i lettori di gestione dei costi e i collaboratori di gestione dei costi hanno accesso a *visualizzazione* consigli sui costi per impostazione predefinita. Tuttavia, l'accesso ad agire per i consigli sui costi richiede l'accesso alle singole risorse. Provare a concedere un [specifici del servizio ruolo](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) se si desidera eseguire operazioni su una raccomandazione basato sui costi.

## <a name="enterprise-agreement-scopes"></a>Ambiti di contratto Enterprise Agreement

Account di fatturazione di Enterprise Agreement (EA), chiamato anche le registrazioni, sono i seguenti ambiti:

- [**Account di fatturazione** ](../billing/billing-view-all-accounts.md) -rappresenta una registrazione EA. Le fatture vengono generate in questo ambito. Gli acquisti che non sono basate su utilizzo, ad esempio Marketplace e le prenotazioni, sono disponibili solo in questo ambito. Non sono rappresentate in ogni reparto o gli account di registrazione.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Reparto** - facoltativo di raggruppamento di account di registrazione.

    Tipo di risorsa: `Billing/billingAccounts/departments`

- **Account di registrazione** -rappresenta il proprietario di un singolo account. Non supporta la concessione dell'accesso a più persone.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Anche se gli ambiti di governance sono associati a una singola directory, non sono ambiti di fatturazione EA. Un account di fatturazione EA può avere sottoscrizioni in un numero qualsiasi delle directory Azure AD.

Gli ambiti di fatturazione EA supportano i seguenti ruoli:

- **Amministratore dell'organizzazione** : può gestire le impostazioni dell'account e accesso della fatturazione, può visualizzare tutti i costi e può gestire la configurazione di costo. Ad esempio, budget e l'esportazione. Nella funzione, il contratto Enterprise l'ambito di fatturazione equivale [il ruolo RBAC di Azure Cost Management collaboratore](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Utente di sola lettura Enterprise** : può visualizzare impostazioni dell'account di fatturazione, i dati di costo e configurazione di costo. Ad esempio, budget e l'esportazione. Nella funzione, il fatturazione ambito EA è quello utilizzato per il [ruolo RBAC di Azure Cost Management lettore](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Amministratore di reparto** : può gestire le impostazioni di reparto, ad esempio il centro di costo e possono accedere, visualizzare tutti i costi e gestire la configurazione di costo. Ad esempio, budget e l'esportazione.  Il **DA Visualizza addebiti** impostazione dell'account di fatturazione deve essere abilitata per gli amministratori di reparto e utenti di sola lettura per visualizzare i costi. Se **DA Visualizza addebiti** è disabilitata, gli utenti di reparto non è possibile visualizzare i costi a qualsiasi livello, anche se sono un proprietario di account o la sottoscrizione.
- **Utente di sola lettura reparto** : può visualizzare le impostazioni di reparto, dati relativi ai costi e configurazione di costo. Ad esempio, budget e l'esportazione. Se **DA Visualizza addebiti** è disabilitata, gli utenti di reparto non è possibile visualizzare i costi a qualsiasi livello, anche se sono un proprietario di account o la sottoscrizione.
- **Proprietario dell'account** : può gestire le impostazioni di account di registrazione (ad esempio, il centro di costo), visualizzare tutti i costi e gestire la configurazione di costo (ad esempio i budget e le esportazioni) per l'account di registrazione. Il **Visualizza addebiti AO** impostazione dell'account di fatturazione deve essere abilitata per i proprietari di account e utenti RBAC per visualizzare i costi.

Utenti di account di fatturazione EA non hanno accesso diretto alle fatture. Le fatture sono disponibili da un sistema esterno contratti multilicenza.

Le sottoscrizioni di Azure vengono nidificate sotto gli account di registrazione. La fatturazione utenti hanno accesso ai dati dei costi per le sottoscrizioni e gruppi di risorse che sono sotto dei rispettivi ambiti. Non hanno accesso per visualizzare o gestire le risorse nel portale di Azure. Gli utenti di fatturazione possono visualizzare i costi passando al **gestione costi + fatturazione** nell'elenco del portale Azure dei servizi. Quindi, è possibile filtrare i costi per le sottoscrizioni specifiche e i gruppi di risorse su che necessarie per eseguire segnalazioni.

La fatturazione utenti autorizzati ad accedere ai gruppi di gestione perché non rientrano in modo esplicito con un account di fatturazione specifico. L'accesso deve essere concessa esplicitamente ai gruppi di gestione. Gruppi di gestione costi di rollup da nidificata tutte le sottoscrizioni. Tuttavia, solo includono gli acquisti e basata sull'utilizzo. Non includono gli acquisti, ad esempio le prenotazioni e le offerte di Marketplace di terze parti. Per visualizzare questi costi, utilizzare l'account di fatturazione EA.

## <a name="individual-agreement-scopes"></a>Ambiti di contratto singolo

Le sottoscrizioni di Azure create da singole offerte come tipi di pagamento a consumo e correlati, ad esempio versione di valutazione gratuita e offerte per sviluppo/test, non hanno un ambito di account di fatturazione esplicito. Al contrario, ogni sottoscrizione ha un proprietario dell'account o l'amministratore dell'account, ad esempio il proprietario dell'account EA.

- [**Account di fatturazione** ](../billing/billing-view-all-accounts.md) -rappresenta il proprietario di un singolo account per uno o più sottoscrizioni di Azure. Non supporta attualmente la concessione dell'accesso a più persone o accedere alle visualizzazioni di costo aggregato.

    Tipo di risorsa: Non applicabile

Gli amministratori degli account singola sottoscrizione di Azure è possibile visualizzare e gestire i dati di fatturazione, ad esempio le fatture e pagamenti, dai [centro Account Azure](https://account.azure.com/subscriptions). Però non è possibile visualizzare i dati di costo o gestire le risorse nel portale di Azure. Per concedere l'accesso per l'amministratore dell'account, usare i ruoli di gestione dei costi indicati in precedenza.

A differenza di EA, gli amministratori degli account di sottoscrizione di Azure singoli possibile visualizzare le fatture nel portale di Azure. Tenere presente che i ruoli Cost Management lettore e collaboratore gestione costi non forniscono accesso alle fatture. Per altre informazioni, vedere [come concedere l'accesso alle fatture](../billing/billing-manage-access.md##give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Ambiti di contratto di Microsoft dal cliente

Gli account di fatturazione di contratto dei clienti Microsoft hanno i seguenti ambiti:

- **Account di fatturazione** -rappresenta un contratto del cliente per più prodotti e servizi Microsoft. Account di fatturazione di Customer contratto non sono funzionalmente identico iscrizioni di contratti Enterprise. Iscrizioni di contratti Enterprise sono più allineati ai profili di fatturazione.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Profilo di fatturazione** -definisce le sottoscrizioni incluse in una fattura. I profili di fatturazione sono l'equivalente funzionale di una registrazione EA, dal momento che è l'ambito che le fatture vengono generate in. Analogamente, gli acquisti che non sono basate su utilizzo, come Marketplace e le prenotazioni, sono disponibili solo in questo ambito. Questi non sono inclusi nelle sezioni della fattura.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Sezione della fattura** -rappresenta un gruppo di sottoscrizioni in una fattura o un profilo di fatturazione. Le sezioni della fattura sono come i reparti, più persone possono avere accesso a una sezione della fattura.

    Tipo di risorsa: `Microsoft.Billing/billingAccounts/invoiceSections`

A differenza di EA fatturazione ambiti, gli account di fatturazione del contratto dei clienti _sono_ associata a una singola directory e le sottoscrizioni non sono in più directory di Azure AD.

Gli ambiti di fatturazione di Customer contratto supportano i seguenti ruoli:

- **Proprietario** : può gestire le impostazioni di fatturazione e l'accesso, visualizzare tutti i costi e gestire la configurazione di costo. Ad esempio, budget e l'esportazione. Nella funzione, questo contratto cliente usufruendo della fatturazione ambito è quello utilizzato il [il ruolo RBAC di Azure Cost Management collaboratore](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Collaboratore** : può gestire le impostazioni di fatturazione, tranne l'accesso, visualizzare tutti i costi e gestire la configurazione di costo. Ad esempio, budget e l'esportazione. Nella funzione, questo contratto cliente usufruendo della fatturazione ambito è quello utilizzato il [il ruolo RBAC di Azure Cost Management collaboratore](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Lettore** : può visualizzare le impostazioni di fatturazione, i dati di costo e configurazione di costo. Ad esempio, budget e l'esportazione. Nella funzione, questo contratto cliente usufruendo della fatturazione ambito è quello utilizzato il [ruolo RBAC di Azure Cost Management lettore](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Manager di fattura** : può visualizzare e pagare le fatture e può visualizzare sui costi di dati e la configurazione. Ad esempio, budget e l'esportazione. Nella funzione, questo contratto cliente usufruendo della fatturazione ambito è quello utilizzato il [ruolo RBAC di Azure Cost Management lettore](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Creazione sottoscrizione Azure** : possono creare sottoscrizioni di Azure, visualizzare i costi e gestire la configurazione di costo. Ad esempio, budget e l'esportazione. Nella funzione, questo contratto cliente usufruendo della fatturazione corrisponde all'ambito del ruolo di proprietario account di registrazione EA.

Le sottoscrizioni di Azure vengono annidate in sezioni di fattura, ad esempio il modo in cui sotto gli account di registrazione con contratto Enterprise. La fatturazione utenti hanno accesso ai dati dei costi per le sottoscrizioni e gruppi di risorse che sono sotto dei rispettivi ambiti. Tuttavia, non hanno accesso per visualizzare o gestire le risorse nel portale di Azure. Gli utenti di fatturazione possono visualizzare i costi passando al **gestione costi + fatturazione** nell'elenco del portale Azure dei servizi. Quindi, filtrare i costi per le sottoscrizioni specifiche e i gruppi di risorse su che necessarie per eseguire segnalazioni.

La fatturazione utenti autorizzati ad accedere ai gruppi di gestione perché non rientrino in modo esplicito con l'account di fatturazione. Tuttavia, quando i gruppi di gestione sono abilitati per l'organizzazione, tutti i costi di sottoscrizione sono roll-up per l'account di fatturazione e per il gruppo di gestione radice perché entrambi sono vincolate a una singola directory. I gruppi di gestione includono solo gli acquisti che sono basati sull'utilizzo. Gli acquisti, ad esempio le prenotazioni e le offerte di Marketplace di terze parti non sono inclusi nei gruppi di gestione. Pertanto, il gruppo di gestione radice e account di fatturazione può segnalare totali diversi. Per visualizzare questi costi, utilizzare l'account di fatturazione o il rispettivo profilo di fatturazione.

## <a name="cloud-solution-provider-csp-scopes"></a>Ambiti di cloud Solution Provider (CSP)

I partner cloud Solution Provider (CSP) non sono supportati in Gestione costi di oggi. In alternativa, è possibile usare [Partner Center](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Spostarsi tra ambiti in Gestione costi

Tutte le viste a gestione costi nel portale di Azure includono una **ambito** partite di selezione in alto a sinistra della visualizzazione. Usarlo per modificare rapidamente l'ambito. Scegliere il **ambito** partite per aprire il selettore di ambito. Mostra gli account di fatturazione, il gruppo di gestione radice e tutte le sottoscrizioni che non sono nidificate sotto il gruppo di gestione radice. Per selezionare un ambito, fare clic sull'icona per evidenziarlo e quindi fare clic su **seleziona** nella parte inferiore. Per eseguire il drill-in per gli ambiti annidati, ad esempio i gruppi di risorse in una sottoscrizione, fare clic sul collegamento di nome di ambito. Per selezionare l'ambito padre a qualsiasi livello nidificata, fare clic su **selezionare questa opzione &lt;ambito&gt;**  all'inizio del selettore di ambito.

## <a name="identify-the-resource-id-for-a-scope"></a>Identificare l'ID risorsa per un ambito

Quando si usano le API gestione dei costi, conoscendo che l'ambito è fondamentale. Usare le informazioni seguenti per creare l'URI dell'ambito appropriato per le API gestione dei costi.

### <a name="billing-accounts"></a>Account di fatturazione

1. Aprire il portale di Azure e quindi passare al **gestione costi + fatturazione** nell'elenco dei servizi.
2. Selezionare **proprietà** nel menu dell'account di fatturazione.
3. Copiare l'ID dell'account fatturazione.
4. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Profili di fatturazione

1. Aprire il portale di Azure e quindi passare al **gestione costi + fatturazione** nell'elenco dei servizi.
2. Selezionare **fatturazione profili** nel menu dell'account di fatturazione.
3. Fare clic sul nome del profilo di fatturazione desiderato.
4. Selezionare **proprietà** nel menu del profilo di fatturazione.
5. Copiare l'account di fatturazione e gli ID del profilo di fatturazione.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Sezioni della fattura

1. Aprire il portale di Azure e quindi passare al **gestione costi + fatturazione** nell'elenco dei servizi.
2. Selezionare **sezioni della fattura** nel menu dell'account di fatturazione.
3. Fare clic sul nome della sezione della fattura desiderato.
4. Selezionare **proprietà** nel menu della sezione della fattura.
5. Copiare l'account di fatturazione e gli ID sezione della fattura.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Reparti di EA

1. Aprire il portale di Azure e quindi passare al **gestione costi + fatturazione** nell'elenco dei servizi.
2. Selezionare **reparti** nel menu dell'account di fatturazione.
3. Fare clic sul nome del reparto desiderato.
4. Selezionare **proprietà** nel menu di reparto.
5. Copiare l'ID account e reparto fatturazione.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Account di registrazione EA

1. Aprire il portale di Azure e passare a **gestione costi + fatturazione** nell'elenco dei servizi.
2. Selezionare **gli account di registrazione** nel menu dell'account di fatturazione.
3. Fare clic sul nome dell'account di registrazione desiderato.
4. Selezionare **proprietà** nel menu dell'account di registrazione.
5. Copiare l'account di fatturazione e gli ID account di registrazione.
6. L'ambito è: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Gruppo di gestione

1. Aprire il portale di Azure e passare a **gruppi di gestione** nell'elenco dei servizi.
2. Passare al gruppo di gestione desiderato.
3. Copiare l'ID del gruppo di gestione dalla tabella.
4. L'ambito è: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Sottoscrizione

1. Aprire il portale di Azure e passare a **sottoscrizioni** nell'elenco dei servizi.
2. Copiare l'ID sottoscrizione dalla tabella.
3. L'ambito è: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Gruppi di risorse

1. Aprire il portale di Azure e passare a **gruppi di risorse** nell'elenco dei servizi.
2. Fare clic sul nome del gruppo di risorse desiderato.
3. Selezionare **proprietà** nel menu del gruppo di risorse.
4. Copiare il valore del campo ID risorsa.
5. L'ambito è: `"/subscriptions/{id}/resourceGroups/{name}"`

Gestione costi è attualmente supportata nelle [Azure globale](https://management.azure.com) e [Azure per enti pubblici](https://management.usgovcloudapi.net). Per altre informazioni su Azure per enti pubblici, vedere [endpoint di Azure globale e API per enti pubblici](../azure-government/documentation-government-developer-guide.md#endpoint-mapping) _._

## <a name="next-steps"></a>Passaggi successivi

- Se non è stata ancora completata la prima guida introduttiva di Gestione costi, esaminarla in [Avviare l’analisi dei costi](quick-acm-cost-analysis.md).
