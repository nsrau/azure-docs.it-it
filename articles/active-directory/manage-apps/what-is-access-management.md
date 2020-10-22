---
title: Gestione dell'accesso alle app tramite Azure AD
description: Descrive come Azure Active Directory consente alle organizzazioni di specificare le app a cui ha accesso ogni utente.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: kenwith
ms.openlocfilehash: c73a5ccd9e16fb5efd9fefca6253701f942c3202
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367789"
---
# <a name="managing-access-to-apps"></a>Gestione dell'accesso alle app

La gestione dell'accesso, la valutazione dell'utilizzo e la creazione di report durante l'utilizzo continuano a rappresentare una sfida dopo l'integrazione di un'app nel sistema di identità dell'organizzazione. Nella maggior parte dei casi, gli amministratori IT o l'helpdesk devono assumere un ruolo attivo nella gestione dell'accesso alle app. In alcuni casi, l'assegnazione viene eseguita da un team IT generale o di reparto. Spesso, la decisione di assegnazione deve essere delegata al responsabile aziendale, di cui è richiesta l'approvazione prima che l'IT esegua l'assegnazione.  Altre organizzazioni investono nell'integrazione mediante sistemi automatizzati esistenti di gestione dell'accesso e dell'identità, quali il controllo degli accessi in base al ruolo (RBAC) o il controllo degli accessi in base all'attributo (ABAC). Lo sviluppo mediante integrazione e regole tende a essere specializzato e costoso. Il monitoraggio o la creazione di report in entrambi gli approcci di gestione è di per sé un investimento isolato, complesso e costoso.

## <a name="how-does-azure-active-directory-help"></a>Quale contributo può offrire Azure Active Directory?

Azure AD supporta la gestione estesa degli accessi per le applicazioni configurate, consentendo alle organizzazioni di ottenere facilmente gli opportuni criteri di accesso: dall'assegnazione automatica o in base agli attributi (scenari ABAC o RBAC), alla delega, inclusa la gestione degli amministratori. Con Azure AD si possono facilmente ottenere criteri complessi, combinando più modelli di gestione per una singola applicazione, e addirittura riusare regole di gestione nelle applicazioni con gli stessi destinatari.

Con Azure AD, le funzionalità di utilizzo e creazione di report di assegnazione sono completamente integrate e consentono agli amministratori di creare facilmente report su stato dell'assegnazione, errori di assegnazione e utilizzo.

### <a name="assigning-users-and-groups-to-an-app"></a>Assegnazione di utenti e gruppi a un'app

L’assegnazione dell’applicazione di Azure AD riguarda due modalità di assegnazione primaria:

* **Singola assegnazione** : un amministratore IT con autorizzazioni di amministratore globale di directory può selezionare singoli account utente e concedere loro l'accesso all'applicazione.

* **Assegnazione basata su gruppo (richiede Azure AD Premium P1 o P2)** : un amministratore IT con autorizzazioni di amministratore globale nella directory può assegnare un gruppo all'applicazione. L'accesso utente specifico è determinato dall'appartenenza al gruppo dell'utente nel momento in cui tenta di accedere all'applicazione. In altri termini, un amministratore può in pratica creare una regola di assegnazione che indica che tutti i membri del gruppo assegnato hanno accesso all'applicazione. Con questa opzione di assegnazione, gli amministratori possono sfruttare le opzioni di gestione dei gruppi di Azure AD, tra cui [gruppi dinamici basati sugli attributi](../fundamentals/active-directory-groups-create-azure-portal.md), gruppi di sistema esterno (ad esempio, Active Directory locale o Workday), gruppi gestiti dall'amministratore o in modalità self-service. Un singolo gruppo può essere facilmente assegnato a più app, garantendo che le applicazioni con affinità di assegnazione condividano le regole di assegnazione, riducendo la complessità generale della gestione. Si noti che al momento le appartenenze ai gruppi annidati non sono supportate per l'assegnazione alle applicazioni in base al gruppo.

Mediante queste due modalità di assegnazione, gli amministratori possono ottenere qualsiasi approccio di gestione delle assegnazioni.

### <a name="requiring-user-assignment-for-an-app"></a>Richiedere l'assegnazione di utenti per un'app

Con alcuni tipi di applicazioni, è possibile scegliere di [richiedere che gli utenti vengano assegnati all'applicazione](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). In questo modo si impedisce a tutti gli utenti di accedere ad eccezione di quelli assegnati in modo esplicito all'applicazione. Questa opzione è supportata dai tipi di applicazioni seguenti:

* Applicazioni configurate per l'accesso Single Sign-On (SSO) federato con autenticazione basata su SAML
* Applicazioni proxy di applicazione che usano la preautenticazione di Azure Active Directory
* Applicazioni create direttamente sulla piattaforma applicativa Azure AD che usano l'autenticazione OAuth 2.0/OpenID Connect dopo che un utente o un amministratore ha fornito il consenso per tale applicazione. Alcune applicazioni aziendali offrono un controllo aggiuntivo sugli utenti autorizzati ad accedervi.

Quando l'assegnazione dell'utente *non è necessaria*, gli utenti non assegnati non visualizzano l'app nelle app personali, ma possono comunque accedere all'applicazione stessa, nota anche come accesso avviato da SP, oppure possono usare l'URL di **accesso utente** nella pagina delle **proprietà** dell'applicazione (noto anche come accesso avviato da IDP).

Per alcune applicazioni, l'opzione per richiedere l'assegnazione di utenti non è disponibile nelle proprietà dell'applicazione. In questi casi, è possibile usare PowerShell per impostare la proprietà appRoleAssignmentRequired nell'entità servizio.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Determinazione dell'esperienza utente per l'accesso alle app

Azure AD fornisce [diverse soluzioni personalizzabili per distribuire le applicazioni](end-user-experiences.md) agli utenti finali all'interno dell'organizzazione:

* Azure AD app personali
* Utilità di avvio dell'applicazione Microsoft 365
* Accesso diretto alle app federate (service-pr)
* Collegamenti diretti per applicazioni federate, basate su password o esistenti

È possibile determinare se gli utenti assegnati a un'app aziendale possono visualizzarlo nelle app personali e Microsoft 365 l'utilità di avvio delle applicazioni.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Esempio: Assegnazione di applicazioni complesse con Azure AD
Si tenga in considerazione un'applicazione come Salesforce. In molte organizzazioni, Salesforce viene principalmente usata dai team di vendita e marketing. Spesso, i membri del team di marketing dispongono di privilegi elevati per l'accesso a Salesforce, mentre i membri del team di vendita ha accesso limitato. In molti casi numerosi information worker hanno accesso limitato all'applicazione ed eventuali eccezioni a tale regola rendono la questione più complessa. Spesso è prerogativa dei team responsabili del marketing o delle vendite concedere a un utente l'accesso o modificare i ruoli indipendentemente da queste regole generiche.

Con Azure AD, applicazioni come Salesforce possono essere preconfigurate per l'accesso Single Sign-On e il provisioning automatizzato. Dopo aver configurato l'applicazione, un amministratore può intraprendere l'azione singola di creazione e assegnazione ai gruppi appropriati. In questo esempio un amministratore può eseguire le assegnazioni seguenti:

* [gruppi dinamici](../fundamentals/active-directory-groups-create-azure-portal.md) possono essere definiti in modo da rappresentare automaticamente tutti i membri dei team di marketing e vendita mediante attributi quali reparto o ruolo:
  
  * In Salesforce, tutti i membri dei gruppi di marketing verrebbero assegnati al ruolo "marketing".
  * In Salesforce, tutti i membri dei gruppi di vendita verrebbero assegnati al ruolo "sales". Un'ulteriore perfezionamento potrebbe prevedere l'utilizzo di più gruppi che rappresentino i team di vendita suddivisi per area assegnati a diversi ruoli in Salesforce.

* Per abilitare il meccanismo delle eccezioni, è possibile creare un gruppo self-service per ogni ruolo. Ad esempio, è possibile creare come gruppo self-service il gruppo "Eccezione marketing Salesforce". Il gruppo può essere assegnato al ruolo marketing in Salesforce e il team responsabile del marketing può essere definito come proprietario. I membri del team responsabile del marketing potrebbero aggiungere o rimuovere utenti, impostare criteri di join o addirittura approvare o negare le richieste di join dei singoli utenti. Questo meccanismo è supportato attraverso un'appropriata esperienza di un information worker che non richiede una formazione specifica per proprietari o membri.

In questo caso, per tutti gli utenti assegnati viene eseguito il provisioning automatico in Salesforce e, mentre vengono aggiunti a gruppi diversi, in Salesforce viene aggiornata la relativa assegnazione al ruolo. Gli utenti saranno in grado di individuare e accedere a Salesforce tramite le mie app, i client Web di Office o anche passando alla pagina di accesso aziendale di Salesforce. Gli amministratori potranno visualizzare facilmente lo stato di assegnazione e utilizzo mediante la funzionalità di creazione di report di Azure AD.

Gli amministratori possono usare l'[accesso condizionale di Azure AD](../conditional-access/concept-conditional-access-users-groups.md) per impostare criteri di accesso per ruoli specifici. Tali criteri possono includere la possibilità di accedere all'esterno dell'ambiente aziendale e anche i requisiti di dispositivo o autenticazione a più fattori per ottenere l'accesso in più situazioni.

## <a name="access-to-microsoft-applications"></a>Accesso alle applicazioni Microsoft

Le applicazioni Microsoft (ad esempio Exchange, SharePoint, Yammer e così via) vengono assegnate e gestite in modo leggermente diverso rispetto alle applicazioni SaaS di terze parti o ad altre applicazioni che si integrano con Azure AD per Single Sign-on.

Vi sono tre principali modi con cui un utente può accedere a un'applicazione pubblicata da Microsoft.

- Per le applicazioni nel Microsoft 365 o in altri gruppi a pagamento, agli utenti viene concesso l'accesso tramite l' **assegnazione delle licenze** direttamente al proprio account utente oppure tramite un gruppo che usa la funzionalità di assegnazione delle licenze basata sui gruppi.
- Per le applicazioni che Microsoft o una terza parte pubblica per l'utilizzo gratuito da parte di chiunque, gli utenti possono ottenere l'accesso tramite il [consenso utente](configure-user-consent.md). Ciò significa che gli utenti accedono all'applicazione con l'account Azure AD aziendale o dell'istituto di istruzione e consentono a tale applicazione di accedere a un set limitato di dati nell'account.
- Per le applicazioni che Microsoft o una terza parte pubblica per l'utilizzo gratuito da parte di chiunque, gli utenti possono ottenere l'accesso tramite il [consenso dell'amministratore](manage-consent-requests.md). Ciò significa che un amministratore ha determinato che l'applicazione può essere usata da qualsiasi utente dell'organizzazione e, tale scopo, ha effettuato l'accesso all'applicazione con un account di amministratore globale e ha consentito l'accesso a tutti gli utenti dell'organizzazione.

Alcune applicazioni combinano questi metodi. Alcune applicazioni Microsoft, ad esempio, fanno parte di una sottoscrizione di Microsoft 365, ma richiedono comunque il consenso.

Gli utenti possono accedere alle applicazioni Microsoft 365 tramite i portali di Office 365. È anche possibile visualizzare o nascondere le applicazioni Microsoft 365 in app personali con l' [interruttore di visibilità di Office 365](hide-application-from-user-portal.md) nelle **impostazioni utente**della directory. 

Come per le app aziendali, è possibile [assegnare utenti](assign-user-or-group-access-portal.md) a determinate applicazioni Microsoft tramite il portale di Azure o, se l'opzione del portale non è disponibile, usando PowerShell.

## <a name="next-steps"></a>Passaggi successivi
* [Protezione delle app con l'accesso condizionale](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Gestione di gruppi self-service/SSAA](../enterprise-users/groups-self-service-management.md)
