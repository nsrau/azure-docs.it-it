---
title: Gestione dell'accesso alle app con Azure AD | Documentazione Microsoft
description: Descrive come Azure Active Directory consente alle organizzazioni di specificare le app a cui ha accesso ogni utente.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 489b15423add03d69070bc32057af97396a85309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409067"
---
# <a name="managing-access-to-apps"></a>Gestione dell'accesso alle app

La gestione dell'accesso, la valutazione dell'utilizzo e la creazione di report durante l'utilizzo continuano a rappresentare una sfida dopo l'integrazione di un'app nel sistema di identità dell'organizzazione. Nella maggior parte dei casi, gli amministratori IT o l'helpdesk devono assumere un ruolo attivo nella gestione dell'accesso alle app. In alcuni casi, l'assegnazione viene eseguita da un team IT generale o di reparto. Spesso, la decisione di assegnazione deve essere delegata al responsabile aziendale, di cui è richiesta l'approvazione prima che l'IT esegua l'assegnazione.  Altre organizzazioni investono nell'integrazione mediante sistemi automatizzati esistenti di gestione dell'accesso e dell'identità, quali il controllo degli accessi in base al ruolo (RBAC) o il controllo degli accessi in base all'attributo (ABAC). Lo sviluppo mediante integrazione e regole tende a essere specializzato e costoso. Il monitoraggio o la creazione di report in entrambi gli approcci di gestione è di per sé un investimento isolato, complesso e costoso.

## <a name="how-does-azure-active-directory-help"></a>Quale contributo può offrire Azure Active Directory?

Azure AD supporta la gestione estesa degli accessi per le applicazioni configurate, consentendo alle organizzazioni di ottenere facilmente gli opportuni criteri di accesso: dall'assegnazione automatica o in base agli attributi (scenari ABAC o RBAC), alla delega, inclusa la gestione degli amministratori. Con Azure AD si possono facilmente ottenere criteri complessi, combinando più modelli di gestione per una singola applicazione, e addirittura riusare regole di gestione nelle applicazioni con gli stessi destinatari.

Con Azure AD, le funzionalità di utilizzo e creazione di report di assegnazione sono completamente integrate e consentono agli amministratori di creare facilmente report su stato dell'assegnazione, errori di assegnazione e utilizzo.

### <a name="assigning-users-and-groups-to-an-app"></a>Assegnazione di utenti e gruppi a un'app

L’assegnazione dell’applicazione di Azure AD riguarda due modalità di assegnazione primaria:

* **Singola assegnazione** : un amministratore IT con autorizzazioni di amministratore globale di directory può selezionare singoli account utente e concedere loro l'accesso all'applicazione.

* **Assegnazione basata su gruppo (richiede Azure AD Premium P1 o P2)Group-based assignment (requires Azure AD Premium P1 or P2)** Un amministratore IT con autorizzazioni di amministratore globale della directory può assegnare un gruppo all'applicazione. L'accesso utente specifico è determinato dall'appartenenza al gruppo dell'utente nel momento in cui tenta di accedere all'applicazione. In altri termini, un amministratore può in pratica creare una regola di assegnazione che indica che tutti i membri del gruppo assegnato hanno accesso all'applicazione. Con questa opzione di assegnazione, gli amministratori possono sfruttare le opzioni di gestione dei gruppi di Azure AD, tra cui [gruppi dinamici basati sugli attributi](../fundamentals/active-directory-groups-create-azure-portal.md), gruppi di sistema esterno (ad esempio, Active Directory locale o Workday), gruppi gestiti dall'amministratore o in modalità self-service. Un singolo gruppo può essere facilmente assegnato a più app, garantendo che le applicazioni con affinità di assegnazione condividano le regole di assegnazione, riducendo la complessità generale della gestione. Si noti che al momento le appartenenze ai gruppi annidati non sono supportate per l'assegnazione alle applicazioni in base al gruppo.

Mediante queste due modalità di assegnazione, gli amministratori possono ottenere qualsiasi approccio di gestione delle assegnazioni.

### <a name="requiring-user-assignment-for-an-app"></a>Richiesta dell'assegnazione utente per un'app

Con alcuni tipi di applicazioni, è possibile richiedere l'assegnazione degli [utenti all'applicazione.](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment) In questo modo, si impedisce a tutti di accedere tranne gli utenti assegnati in modo esplicito all'applicazione. Questa opzione è supportata dai seguenti tipi di applicazioni:

* Applicazioni configurate per l'accesso Single Sign-On (SSO) federato con autenticazione basata su SAML
* Applicazioni proxy di applicazione che usano la preautenticazione di Azure Active DirectoryApplication Proxy applications that use Azure Active Directory Pre-Authentication
* Applicazioni basate sulla piattaforma applicativa di Azure AD che usano OAuth 2.0 / OpenID Connect Authentication dopo che un utente o un amministratore ha acconsentito a tale applicazione. Alcune applicazioni aziendali offrono un controllo aggiuntivo su chi può accedere.

Quando l'assegnazione dell'utente non è *necessaria,* gli utenti non assegnati non vedono l'app nel pannello di accesso App personali, ma possono comunque accedere all'applicazione stessa (nota anche come accesso avviato da SP) oppure possono utilizzare **l'URL di accesso utente** nella pagina **Proprietà** dell'applicazione (nota anche come accesso avviato da IDP).

Per alcune applicazioni, l'opzione per richiedere l'assegnazione dell'utente non è disponibile nelle proprietà dell'applicazione. In questi casi, è possibile usare PowerShell per impostare la proprietà appRoleAssignmentRequired nell'entità servizio.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Determinazione dell'esperienza utente per l'accesso alle appDetermining the user experience for accessing apps

Azure AD offre [diversi modi personalizzabili per distribuire le applicazioni](end-user-experiences.md) agli utenti finali nell'organizzazione:Azure AD provides several customizable ways to deploy applications to end users in your organization:

* Pannello di accesso App personali di Azure ADAzure AD My Apps access panel
* Applicazione di avvio di Office 365
* Accesso diretto alle app federate (service-pr)
* Collegamenti diretti per applicazioni federate, basate su password o esistenti

È possibile determinare se gli utenti assegnati a un'app aziendale possono visualizzarla nel pannello di accesso e nell'icona di avvio delle applicazioni di Office 365.You can determine whether users assigned to an enterprise app can see it in the access panel and Office 365 application launcher.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Esempio: assegnazione di applicazioni complesse con Azure ADExample: Complex application assignment with Azure AD
Si tenga in considerazione un'applicazione come Salesforce. In molte organizzazioni, Salesforce viene principalmente usata dai team di vendita e marketing. Spesso, i membri del team di marketing dispongono di privilegi elevati per l'accesso a Salesforce, mentre i membri del team di vendita ha accesso limitato. In molti casi numerosi information worker hanno accesso limitato all'applicazione ed eventuali eccezioni a tale regola rendono la questione più complessa. Spesso è prerogativa dei team responsabili del marketing o delle vendite concedere a un utente l'accesso o modificare i ruoli indipendentemente da queste regole generiche.

Con Azure AD, applicazioni come Salesforce possono essere preconfigurate per l'accesso Single Sign-On e il provisioning automatizzato. Dopo aver configurato l'applicazione, un amministratore può intraprendere l'azione singola di creazione e assegnazione ai gruppi appropriati. In questo esempio un amministratore può eseguire le assegnazioni seguenti:

* [gruppi dinamici](../fundamentals/active-directory-groups-create-azure-portal.md) possono essere definiti in modo da rappresentare automaticamente tutti i membri dei team di marketing e vendita mediante attributi quali reparto o ruolo:
  
  * In Salesforce, tutti i membri dei gruppi di marketing verrebbero assegnati al ruolo "marketing".
  * In Salesforce, tutti i membri dei gruppi di vendita verrebbero assegnati al ruolo "sales". Un'ulteriore perfezionamento potrebbe prevedere l'utilizzo di più gruppi che rappresentino i team di vendita suddivisi per area assegnati a diversi ruoli in Salesforce.

* Per abilitare il meccanismo delle eccezioni, è possibile creare un gruppo self-service per ogni ruolo. Ad esempio, è possibile creare come gruppo self-service il gruppo "Eccezione marketing Salesforce". Il gruppo può essere assegnato al ruolo marketing in Salesforce e il team responsabile del marketing può essere definito come proprietario. I membri del team responsabile del marketing potrebbero aggiungere o rimuovere utenti, impostare criteri di join o addirittura approvare o negare le richieste di join dei singoli utenti. Questo meccanismo è supportato attraverso un'appropriata esperienza di un information worker che non richiede una formazione specifica per proprietari o membri.

In questo caso, per tutti gli utenti assegnati viene eseguito il provisioning automatico in Salesforce e, mentre vengono aggiunti a gruppi diversi, in Salesforce viene aggiornata la relativa assegnazione al ruolo. Gli utenti potranno individuare e accedere a Salesforce tramite il pannello di accesso dell'applicazione Microsoft, i client Web di Office, o anche passando alla pagina di accesso aziendale di Salesforce. Gli amministratori potranno visualizzare facilmente lo stato di assegnazione e utilizzo mediante la funzionalità di creazione di report di Azure AD.

Gli amministratori possono usare [l'accesso condizionale di Azure AD](../active-directory-conditional-access-azure-portal.md) per impostare i criteri di accesso per ruoli specifici. Tali criteri possono includere la possibilità di accedere all'esterno dell'ambiente aziendale e anche i requisiti di dispositivo o autenticazione a più fattori per ottenere l'accesso in più situazioni.

## <a name="access-to-microsoft-applications"></a>Accesso alle applicazioni Microsoft

Le applicazioni Microsoft (ad esempio Office 365 Exchange, SharePoint, Yammer e così via) vengono assegnate e gestite in modo leggermente diverso dalle applicazioni SaaS di terza parte o da altre applicazioni che si integrano con Azure AD per Single Sign On.

Vi sono tre principali modi con cui un utente può accedere a un'applicazione pubblicata da Microsoft.

- Per le applicazioni in Office 365 o in altre famiglie di prodotti a pagamento, agli utenti è consentito l'accesso tramite l'**assegnazione di una licenza** direttamente all'account utente o tramite un gruppo utilizzando la funzionalità di assegnazione di licenze di gruppo.
- Per le applicazioni che Microsoft o una terza parte pubblica liberamente per chiunque di utilizzare, agli utenti può essere concesso l'accesso tramite [il consenso dell'utente](configure-user-consent.md). Ciò significa che accedono all'applicazione con il proprio account aziendale o dell'istituto di istruzione di Azure AD e consentono di accedere ad alcuni set limitati di dati nel proprio account.
- Per le applicazioni che Microsoft o una terza parte pubblica liberamente per chiunque di utilizzare, agli utenti può essere concesso l'accesso anche tramite [il consenso dell'amministratore](manage-consent-requests.md). Ciò significa che un amministratore ha determinato che l'applicazione può essere usata da qualsiasi utente dell'organizzazione e, tale scopo, ha effettuato l'accesso all'applicazione con un account di amministratore globale e ha consentito l'accesso a tutti gli utenti dell'organizzazione.

Alcune applicazioni combinano questi metodi. Ad esempio, alcune applicazioni Microsoft fanno parte di un abbonamento a Office 365, ma richiedono comunque il consenso.

Gli utenti possono accedere alle applicazioni di Office 365 tramite i portali di Office 365. È inoltre possibile visualizzare o nascondere le applicazioni di Office 365 nel pannello di accesso App personali con [l'interruttore di visibilità di Office 365](hide-application-from-user-portal.md) nelle **impostazioni utente**della directory. 

Come per le app aziendali, è possibile [assegnare gli utenti](assign-user-or-group-access-portal.md) a determinate applicazioni Microsoft tramite il portale di Azure o, se l'opzione del portale non è disponibile, tramite PowerShell.As with enterprise apps, you can assign users to certain Microsoft applications via the Azure portal or, if the portal option isn't available, by using PowerShell.

## <a name="next-steps"></a>Passaggi successivi
* [Protezione delle app con l'accesso condizionale](../active-directory-conditional-access-azure-portal.md)
* [Gestione di gruppi self-service/SSAA](../users-groups-roles/groups-self-service-management.md)
