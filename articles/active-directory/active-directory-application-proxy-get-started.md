<properties
	pageTitle="Come fornire l'accesso remoto sicuro alle applicazioni locali"
	description="Viene illustrato come utilizzare il proxy dell’applicazione di Azure AD per fornire l'accesso remoto sicuro alle applicazioni locali."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/07/2015"
	ms.author="rkarlin"/>

# Come fornire l'accesso remoto sicuro alle applicazioni locali

Si desidera fornire l'accesso per gli utenti remoti che dispongono di tutti i tipi di dispositivi: gestiti, non gestiti; tablet, smartphone e computer portatili. Fornire un accesso protetto a una miriade di risorse può essere complesso. Negli ultimi anni, i proxy inversi sono stati una modalità comune per fornire l'accesso remoto protetto, ma dovevano essere protetti da firewall che erano difficili da proteggere e da rendere altamente disponibili.

## Accesso remoto protetto nel cloud
In un ambiente cloud moderno, portiamo l'accesso remoto al livello successivo utilizzando il proxy dell’applicazione in Microsoft Azure Active Directory. Il proxy dell'applicazione è una funzionalità di Azure AD che viene fornita come un servizio, vale a dire che è facile da distribuire e utilizzare. Si integra con Azure Active Directory, la stessa piattaforma di identità utilizzata da Office 365.

## Informazioni sul proxy dell’applicazione di Azure Active Directory
Il proxy dell'applicazione fornisce Single Sign-On e accesso remoto protetto per le applicazioni web ospitate in locale, come ad esempio siti di SharePoint e Outlook Web Access. Ora è possibile accedere alle applicazioni Web locali esattamente come alle applicazioni SaaS in Azure Active Directory, senza la necessità di una rete VPN o della modifica dell'infrastruttura di rete. Il proxy dell'applicazione consente di pubblicare le applicazioni. I dipendenti possono accedere alle app dalla propria abitazione e con i propri dispositivi e possono eseguire l'autenticazione tramite questo proxy basato sul cloud.

## Come funziona?
### Abilitazione dell'accesso
Il proxy dell’applicazione funziona mediante l'installazione di un servizio di Windows Server slim chiamato connettore all'interno della rete. Il connettore non deve aprire porte in ingresso e non è necessario inserire niente nella rete perimetrale. Se si dispone di volumi elevati di traffico verso le applicazioni, è possibile aggiungere che ulteriori connettori e il servizio gestirà il bilanciamento del carico. I connettori sono senza stato e prelevano tutto dal cloud in base alle esigenze. Quando un utente accede alle applicazioni da remoto, da qualsiasi dispositivo, viene autenticato dalla Azure Active Directory e ottiene l'accesso all'applicazione.

### Accesso Single Sign-On
Il proxy dell’applicazione di Azure AD fornisce Single Sign-On (SSO) alle applicazioni che utilizzano l'autenticazione integrata di Windows o alle applicazioni in grado di riconoscere attestazioni. Se l'applicazione utilizza l'autenticazione integrata di Windows, il proxy dell’applicazione rappresenta l'utente utilizzando la delega vincolata Kerberos per fornire l'accesso SSO. Il SSO per applicazioni in grado di riconoscere attestazioni viene eseguito perché l'utente è stato già autenticato da Azure Active Directory.

## Attività iniziali
Assicurarsi di disporre di una sottoscrizione di Azure AD di base o premium e di una directory di Windows Azure di cui si è un amministratore globale. Sono inoltre necessarie licenze di base o premium di Windows Azure per l'amministratore della directory e gli utenti che accedono alle applicazioni. Leggere qui per ulteriori informazioni.

### Guida introduttiva per l’abilitazione dell'accesso remoto alle applicazioni locali
La configurazione del proxy dell'applicazione viene eseguita in due passaggi:

1. [Abilitazione del proxy dell’applicazione e configurazione del connettore](active-directory-application-proxy-enable.md)<br>
2. [Pubblicazione delle applicazioni](active-directory-application-proxy-publish.md): utilizzare la procedura guidata semplice e veloce per pubblicare le applicazioni locali e renderle accessibili da remoto.

## Passaggi successivi
Si può fare molto di più con il proxy dell'applicazione:


- [Pubblicare applicazioni mediante il proprio nome di dominio](https://msdn.microsoft.com/library/azure/mt210927.aspx)
- [Abilitare l'accesso Single Sign-On](https://msdn.microsoft.com/library/azure/dn879065.aspx)
- [Lavorare con applicazioni grado di riconoscere attestazioni](https://msdn.microsoft.com/library/azure/mt210926.aspx)
- [Abilitare l'accesso condizionale](https://msdn.microsoft.com/library/azure/dn931796.aspx)


### Ulteriori informazioni sul proxy dell’applicazione
- [Dare un'occhiata alla nostra Guida in linea](https://msdn.microsoft.com/library/azure/dn768219.aspx)
- [Blog del proxy dell'applicazione](http://blogs.technet.com/b/applicationproxyblog/)
- [Guarda i nostri video su Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Risorse aggiuntive
* [Iscriversi ad Azure come organizzazione](../sign-up-organization.md)
* [Identità di Azure](../fundamentals-identity.md)

<!---HONumber=July15_HO4-->