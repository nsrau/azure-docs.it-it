<properties
	pageTitle="Come fornire l'accesso remoto sicuro alle applicazioni locali"
	description="Viene illustrato come utilizzare il proxy dell’applicazione di Azure AD per fornire l'accesso remoto sicuro alle applicazioni locali."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/10/2016"
	ms.author="kgremban"/>

# Come fornire l'accesso remoto sicuro alle applicazioni locali

> [AZURE.NOTE] Il proxy di applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Si desidera fornire l'accesso per gli utenti remoti che dispongono di tutti i tipi di dispositivi: gestiti, non gestiti; tablet, smartphone e computer portatili. Fornire un accesso protetto a una miriade di risorse può essere complesso. Negli ultimi anni, i proxy inversi sono stati una modalità comune per fornire l'accesso remoto protetto, ma dovevano essere protetti da firewall che erano difficili da proteggere e da rendere altamente disponibili.

## Accesso remoto protetto nel cloud
In un ambiente cloud moderno, si porterà l'accesso remoto al livello successivo usando il proxy dell'applicazione in Microsoft Azure Active Directory. Il proxy dell'applicazione è una funzionalità di Azure AD che viene fornita come un servizio, vale a dire che è facile da distribuire e utilizzare. Si integra con Azure Active Directory, la stessa piattaforma di identità usata da Office 365.

## Informazioni sul proxy dell’applicazione di Azure Active Directory
Il proxy dell'applicazione fornisce sia l'accesso Single Sign-On sia l'accesso remoto sicuro per le applicazioni Web ospitate in locale, quali i siti di SharePoint e Outlook Web Access. Ora è possibile accedere alle applicazioni Web locali esattamente come alle applicazioni SaaS in Azure Active Directory, senza la necessità di una rete VPN o della modifica dell'infrastruttura di rete. Il proxy dell'applicazione consente di pubblicare applicazioni e i dipendenti possono accedere alle app dalla propria abitazione e con i propri dispositivi e possono eseguire l'autenticazione tramite questo proxy basato sul cloud.

## Come funziona?
### Abilitazione dell'accesso
Il proxy dell’applicazione funziona mediante l'installazione di un servizio di Windows Server slim chiamato connettore all'interno della rete. Il connettore non deve aprire porte in ingresso e non è necessario inserire niente nella rete perimetrale. Se si dispone di volumi elevati di traffico verso le applicazioni, è possibile aggiungere che ulteriori connettori e il servizio gestirà il bilanciamento del carico. I connettori sono senza stato e prelevano tutto dal cloud in base alle esigenze. Quando un utente accede alle applicazioni da remoto, da qualsiasi dispositivo, viene autenticato dalla Azure Active Directory e ottiene l'accesso all'applicazione.

### Accesso Single Sign-On
Il proxy dell'applicazione di Azure AD fornisce l'accesso Single Sign-On alle applicazioni che usano l'autenticazione integrata di Windows o alle applicazioni in grado di riconoscere attestazioni. Se l'applicazione usa l'autenticazione integrata di Windows, il proxy dell'applicazione rappresenta l'utente tramite la delega vincolata Kerberos per fornire l'accesso Single Sign-On. Se invece si dispone di un'applicazione con riconoscimento delle attestazione che considera attendibile Azure Active Directory, l'accesso Single Sign-On viene concesso perché l'utente era già stato autenticato da Azure AD.

## Attività iniziali
Assicurarsi di disporre di una sottoscrizione di Azure AD di base o premium e di una directory di Microsoft Azure di cui si è un amministratore globale. Sono inoltre necessarie licenze di base o premium di Microsoft Azure per l'amministratore della directory e gli utenti che accedono alle applicazioni. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

### Guida introduttiva per l’abilitazione dell'accesso remoto alle applicazioni locali
La configurazione del proxy dell'applicazione viene eseguita in due passaggi:

1. [Abilitazione del proxy dell'applicazione e configurazione del connettore](active-directory-application-proxy-enable.md)  
2. [Pubblicazione delle applicazioni](active-directory-application-proxy-publish.md): usare la procedura guidata, veloce e intuitiva, per pubblicare applicazioni locali e renderle accessibili in remoto.

## Passaggi successivi
Si può fare molto di più con il proxy dell'applicazione:

- [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)


### Ulteriori informazioni sul proxy dell’applicazione
- [Dare un'occhiata alla nostra Guida in linea](active-directory-application-proxy-enable.md)
- [Blog del proxy dell'applicazione](http://blogs.technet.com/b/applicationproxyblog/)
- [Guarda i nostri video su Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Risorse aggiuntive
- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
- [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
- [Identità di Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0211_2016-->