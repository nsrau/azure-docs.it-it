<properties
	pageTitle="Informazioni su Azure Active Directory"
	description="Usare Azure Active Directory per estendere le identità locali esistenti nel cloud o sviluppare le applicazioni integrate di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="curtand"/>


# Informazioni su Azure Active Directory


[Come funziona?](active-directory-works.md)<br> [Introduzione](active-directory-get-started.md)<br> [Passaggi successivi](active-directory-next-steps.md)<br> [Altre informazioni](active-directory-learn-map.md)

Azure Active Directory (Azure AD) fornisce un modo semplice per l'azienda per estendere gli investimenti locali avvantaggiandosi dei servizi cloud, con la flessibilità e sicurezza offerta da sempre da Active Directory. È una piattaforma cloud che consente di risolvere i problemi dell'era cloud estendendo l'infrastruttura delle identità che già si possiedono senza che sia necessario alcuno spostamento. In questo caso è possibile continuare a sfruttare i vantaggi degli investimenti esistenti e delle funzionalità locali sfruttando al tempo stesso Azure AD per ottenere la gestione delle identità e degli accessi nel cloud.

Con Azure AD è possibile controllare in modo centralizzato l'accesso ad applicazioni e risorse, aggiungere facilmente le risorse esistenti (servizi cloud o applicazioni locali) e integrare le applicazioni che si stanno sviluppando. Gli utenti possono ottenere funzionalità self-service e l'accesso Single Sign-On a tutte le applicazioni in modo che non sia necessario sapere dove trovare ogni app o dover ricordare una password diversa. L'azienda può collaborare nel cloud con partner commerciali, fornitori e clienti aziendali che è possibile invitare nei siti di SharePoint (o partecipare nei loro siti di SharePoint).

Con Azure AD è possibile gestire tutti gli elementi da un'unica posizione ed è possibile avere il controllo sia nel cloud che localmente. Non vi è alcuna duplicazione dell'amministrazione. Azure AD si inserisce perfettamente nell'ambiente già in uso. [Altre informazioni](active-directory-aadconnect.md)




> [AZURE.NOTE]Per usare Azure Active Directory, è necessario un account Azure. Se non si dispone di un account, è possibile [iscriversi per un account Azure gratuito](http://azure.microsoft.com/pricing/free-trial/).


## Quali sono i vantaggi di Azure Active Directory?

Dal momento che rappresenta un servizio completo, Azure AD è in grado di offrire vantaggi diversi a utenti con ruoli diversi all'interno di un'organizzazione.

- Se si è un responsabile aziendale, usare Azure AD per ottenere tutti i vantaggi delle applicazioni cloud e di una forza lavoro mobile con la certezza di soddisfare i requisiti di governance.
- Se si è un provider di servizi, usare Azure AD per risolvere facilmente tutte le problematiche relative alle identità e agli accessi. Grazie ad Azure AD si possono infatti connettere i propri servizi alle soluzioni di identità esistenti dei clienti, senza rinunciare alla possibilità di raggiungere anche gli utenti di Microsoft Azure e di Office 365. Azure AD può risolvere anche tutte le problematiche back-office relative all'accesso. Che si lavori quindi internamente o in outsourcing, è sempre possibile avere la sicurezza che l'accesso è garantito solo alle persone giuste.
- Se si è un professionista IT, usare Azure AD per aumentare il controllo e la visibilità delle operazioni alla "velocità del cloud". Con Azure AD si conosceranno gli strumenti usati dagli utenti e gli verranno fornite nuove possibilità con le offerte self-service. <br> <br>

![][1]

##Nove cose da sapere su Azure AD

### Gestire le password di Active Directory da qualsiasi luogo

Usare Azure Active Directory per consentire agli utenti di gestire le password di Active Directory o Azure Active Directory o da qualsiasi dispositivo, in qualsiasi posizione. Un amministratore può gestire password e criteri di notifica e può visualizzare l'attività di controllo dettagliata di queste operazioni di reimpostazione password nel momento in cui che si verificano. [Altre informazioni sulla gestione delle password Azure AD](http://aka.ms/ssproverview)

### Impostare le regole per l'accesso alle risorse cloud

È possibile impostare criteri e regole che consentano di controllare gli utenti che hanno accesso ad applicazioni e risorse cloud e in quali condizioni. Ad esempio, è possibile richiedere l'autenticazione a più fattori e gestire l'accesso in base al dispositivo o al percorso. [Altre informazioni sull'autenticazione a più fattori di Azure](../multi-factor-authentication.md).

### Single Sign-On a qualsiasi app

Azure Active Directory fornisce Single Sign-On sicure al cloud e applicazioni locali, tra cui Microsoft Office 365 e migliaia di applicazioni SaaS come Salesforce, Workday, DocuSign, ServiceNow e Box. [Altre informazioni sulle app SaaS](http://azure.microsoft.com/marketplace/active-directory/).

### Funziona con qualsiasi dispositivo

Gli utenti possono avviare le applicazioni da un pannello di accesso personalizzato basato sul Web, app per dispositivi mobili, Office 365 o portali aziendali personalizzati usando le credenziali di lavoro e avere la stessa esperienza se si lavora su dispositivi Windows, Mac OS X, Android o iOS.

### Set di regole per l'accesso esterno

Gli utenti esterni possono raggiungere la rete locale protetta da un firewall in modo sicuro, usando il proxy dell'applicazione incorporata. Tutte le regole e i criteri impostati, compresa l'autenticazione a più fattori, possono essere applicati per poter accedere alle applicazioni cloud o alle applicazioni locali tradizionali mediante il proxy dell'applicazione, senza la necessità di doverli riscrivere o esporre direttamente su Internet. [Altre informazioni sul proxy dell'applicazione di Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx).

### Monitorare l'accesso degli utenti

Infine, Azure AD fornisce informazioni su ciò che accade all'interno dell'organizzazione in modo rapido ed efficiente. Grazie alle funzionalità avanzate di analisi e reporting, è possibile ottenere informazioni riservate sull'accesso degli utenti. Ad esempio, è possibile usare il servizio di individuazione applicazioni per scoprire quali applicazioni vengono usate attivamente all'interno dell'organizzazione. [Altre informazioni sul servizio Azure AD Cloud App Discovery](https://appdiscovery.azure.com/).

### Report, controllo e analisi avanzati
Azure AD fornisce una serie di report sull'utilizzo e accesso giornalieri per gli amministratori ([report Azure AD](active-directory-view-access-usage-reports.md)) e report personalizzati e approfondimenti sui dati sono disponibili tramite l'API di report ([API di report di Azure AD](active-directory-reporting-api-getting-started.md)). Sono disponibili nelle edizioni a pagamento di Azure AD anche più report, tra cui il controllo di azioni con privilegi ([controllo Azure AD](active-directory-view-access-usage-reports.md)).

### Un'esperienza semplice e sicura per tutti gli utenti

Esempi: gli utenti finali usufruiscono della semplicità di questa soluzione, mettendo in un'unica posizione i propri profili, le applicazioni e la capacità di gestire l'accesso alle risorse, senza dover ricorrere a una formazione specializzata. L'autenticazione a più fattori, le applicazioni SaaS, gli strumenti ibridi e le funzionalità self-service sono pronte per essere impiegate.

- Per una gestione completa, gli amministratori possono accedere al portale di gestione di Azure e a Windows PowerShell.

- Gli sviluppatori dispongono di una gamma di API RESTful compatibili e hanno facile accesso alla pubblicazione e all'utilizzo delle interfacce dell'applicazione.

### Selezionare la versione

Azure AD è disponibile in tre versioni:

- L'edizione gratuita, che è semplicemente un servizio directory cloud.
- L'edizione Basic, che è un servizio di directory cloud che consente anche l'accesso app SaaS.
- La Premium Edition, che è una soluzione di servizio directory completa, basata su regole, gestita autonomamente.

Per esaminare le funzionalità descritte qui di seguito, attivare la [versione di valutazione gratuita di Azure](http://azure.microsoft.com/trial/get-started-active-directory/).

[Altre informazioni sulle edizioni di Azure AD](active-directory-editions.md)


## Risorse aggiuntive

* [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
* [Identità di Azure](fundamentals-identity.md)

<!--Image references-->
[1]: ./media/active-directory-whatis/Azure_Active_Directory.png

<!---HONumber=58--> 