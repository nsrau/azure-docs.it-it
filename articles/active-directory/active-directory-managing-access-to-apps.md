<properties
  pageTitle="Gestione dell'accesso alle app tramite Azure AD | Microsoft Azure"
  description="Descrive come Azure Active Directory consente alle organizzazioni di specificare le app a cui ha accesso ogni utente."
  services="active-directory"
  documentationCenter=""
  authors="msStevenPo"
  manager="stevenpo"
  editor=""/>

 <tags
  ms.service="active-directory"
  ms.workload="identity"
  ms.tgt_pltfrm="na"
  ms.devlang="na"
  ms.topic="article"
  ms.date="10/16/2015"
  ms.author="stevenpo"/>


# Gestione dell'accesso alle app

La gestione dell'accesso, la valutazione dell'utilizzo e la creazione di report durante l'utilizzo continuano a rappresentare una sfida dopo l'integrazione di un'app nel sistema di identità dell'organizzazione. Nella maggior parte dei casi, l'amministratore IT o l'helpdesk deve assumere un ruolo attivo nella gestione dell'accesso alle app in uso. In alcuni casi, l'assegnazione viene eseguita da un team IT generale o di reparto. Spesso, la decisione di assegnazione deve essere delegata al responsabile aziendale, di cui è richiesta l'approvazione prima che l'IT esegua l'assegnazione. Altre organizzazioni investono nell'integrazione mediante sistemi automatizzati esistenti di gestione dell'accesso e dell'identità, quali il controllo degli accessi in base al ruolo (RBAC) o il controllo degli accessi in base all'attributo (ABAC). Lo sviluppo mediante integrazione e regole tende a essere specializzato e costoso. Il monitoraggio o la creazione di report in entrambi gli approcci di gestione è di per sé un investimento isolato, complesso e costoso.

## Quale contributo può offrire Azure Active Directory?

 Azure AD supporta la gestione estesa degli accessi per le applicazioni configurate, consentendo alle organizzazioni di ottenere facilmente gli opportuni criteri di accesso: dall'assegnazione automatica o in base agli attributi (scenari ABAC o RBAC), alla delega, inclusa la gestione degli amministratori. Con Azure AD si possono facilmente ottenere criteri complessi, mediante la combinazione di più modelli di gestione per una singola applicazione, e si possono anche riutilizzare regole di gestione nelle applicazioni con gli stessi destinatari.

 - [Aggiunta di nuove applicazioni](active-directory-sso-newly-acquired-saas-apps.md)
 - [Aggiunta di applicazioni esistenti](active-directory-sso-integrate-existing-apps)

 L’assegnazione dell’applicazione di Azure AD riguarda due modalità di assegnazione primaria:

- **Singola assegnazione** un amministratore IT con privilegi di tipo globale, utente o di autenticazione può selezionare singoli account utente e concedere loro l'accesso all'applicazione.
- **L'assegnazione basata su gruppo (con il pagamento di solo Azure AD)** un amministratore IT con privilegi di tipo globale, utente o di autenticazione può assegnare un gruppo all'applicazione. Un accesso utente specifico è determinato dall'appartenenza al gruppo al momento dell'accesso all'applicazione. In questa modalità, un amministratore può in pratica creare una regola di assegnazione che indica che tutti i membri del gruppo assegnato hanno accesso all'applicazione. Con questa opzione di assegnazione, gli amministratori possono sfruttare le opzioni di gestione dei gruppi di Azure AD, tra cui gruppi dinamici basati sugli attributi, gruppi di sistema esterno (ad esempio, Active Directory locale o Workday), gruppi amministratore o gruppi gestiti in modalità self-service. Un singolo gruppo può essere facilmente assegnato a più app, garantendo che le applicazioni con affinità di assegnazione condividano le regole di assegnazione, riducendo la complessità di gestione complessiva. Notare che le appartenenze ai gruppi annidati non sono supportate per l’assegnazione in base al gruppo ad applicazioni in questo momento.

Mediante queste due modalità di assegnazione, gli amministratori possono ottenere qualsiasi approccio di gestione delle assegnazioni.

Con Azure AD, le funzionalità di utilizzo e creazione di report di assegnazione sono completamente integrate e consentono agli amministratori di creare facilmente report su stato dell'assegnazione, errori di assegnazione e utilizzo.

## Assegnazione di applicazioni complesse con Azure AD

Si tenga in considerazione un'applicazione come Salesforce. In molte organizzazioni, Salesforce viene principalmente usata dai reparti di vendita e marketing. Spesso, i membri del team di marketing dispongono di privilegi elevati per l'accesso a Salesforce, mentre i membri del team di vendita ha accesso limitato. In molti casi, numerosi information worker hanno accesso limitato all'applicazione ed eventuali eccezioni a tale regola rendono la questione più complessa. Spesso è prerogativa dei team responsabili del marketing o delle vendite concedere a un utente l'accesso o modificare i ruoli indipendentemente da queste regole generiche.

Con Azure AD, applicazioni come Salesforce possono essere preconfigurate per l'accesso Single Sign-On e il provisioning automatizzato. Dopo aver configurato l'applicazione, un amministratore può intraprendere l'azione singola di creazione e assegnazione ai gruppi appropriati. In questo esempio un amministratore può eseguire le assegnazioni seguenti:

- I gruppi basati sugli attributi possono essere definiti in modo da rappresentare automaticamente tutti i membri dei team di marketing e vendita mediante attributi quali reparto o ruolo:
    - In Salesforce, tutti i membri dei gruppi di marketing verrebbe assegnati al ruolo "marketing".
    - In Salesforce, tutti i membri dei gruppi di vendita verrebbe assegnati al ruolo "sales". Un'ulteriore perfezionamento potrebbe prevedere l'uso di più gruppi che rappresentino i team di vendita suddivisi per area assegnati a diversi ruoli in Salesforce.
- Per abilitare il meccanismo delle eccezioni, è possibile creare un gruppo self-service per ogni ruolo. Ad esempio, il gruppo "eccezione marketing salesforce" può essere creato come gruppo self-service. Il gruppo può essere assegnato al ruolo marketing in Salesforce e il team responsabile del marketing può essere definito come proprietario. I membri del team responsabile del marketing potrebbero aggiungere o rimuovere utenti, impostare criteri di join o addirittura approvare o negare le richieste di join dei singoli utenti. Questa opzione è supportata tramite un'appropriata esperienza di un information worker che non richiede una formazione specifica per proprietari o membri.

In questo caso, per tutti gli utenti assegnati viene eseguito il provisioning automatizzato a Salesforce, quando vengono aggiunti a gruppi diversi l'assegnazione al ruolo verrà aggiornata in Salesforce. Gli utenti potranno individuare e accedere a Salesforce tramite il pannello di accesso dell'applicazione Microsoft, i client Web di Office, o anche passando alla pagina di accesso aziendale di Salesforce. Gli amministratori potranno visualizzare facilmente lo stato di assegnazione e utilizzo mediante la funzionalità di creazione di report di Azure AD.

 Gli amministratori possono utilizzare l'[accesso condizionale di Azure AD](active-directory-conditional-access.md) per impostare criteri di accesso per ruoli specifici. Tali criteri possono includere la possibilità di accedere all'esterno dell'ambiente aziendale e anche i requisiti di dispositivo o autenticazione a più fattori per ottenere l'accesso in più situazioni.

## Operazioni iniziali

Innanzitutto, un amministratore IT che non usa già Azure AD:

 - [Può richiedere una versione di valutazione](https://azure.microsoft.com/trial/get-started-active-directory/): è possibile ottenere subito gratuitamente una versione di valutazione di 30 giorni e distribuire la prima soluzione cloud in meno di 5 minuti, usando il collegamento seguente

Le funzionalità di Azure AD che consentono la condivisione di account includono:

- [Assegnazione di gruppi](active-directory-accessmanagement-self-service-group-management.md)
- Aggiunta di applicazioni ad Azure AD
- Introduzione all'assegnazione
- Domande frequenti sull'assegnazione di applicazioni
- [Dashboard/report sull'utilizzo di app](active-directory-passwords-get-insights.md)

## Altre informazioni

- [Sicurezza delle app con l'accesso condizionale](active-directory-conditional-access.md)
- [Gestione di gruppi self-service/SSAA](active-directory-accessmanagement-self-service-group-management.md)

<!---HONumber=AcomDC_1203_2015-->