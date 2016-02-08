<properties
   pageTitle="Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory"
   description="Come elencare un'applicazione che supporta l'accesso Single Sign-On nella raccolta di Azure Active Directory | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/29/2015"
   ms.author="mbaldwin"/>


# Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory

Per inserire un'applicazione che supporta Single Sign-On con Azure Active Directory nella [raccolta di Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/), l'applicazione deve innanzitutto implementare una delle modalità di integrazione seguenti:

* **OpenID Connect**: integrazione diretta con Azure AD usando OpenID Connect per l'autenticazione e l'API di consenso di Azure AD per la configurazione. Se si sta iniziando un'integrazione e l'applicazione non supporta SAML, questa è la modalità consigliata.

* **SAML**: l'applicazione può già configurare i provider di identità di terze parti tramite il protocollo SAML.

Di seguito è riportato un elenco dei requisiti di ogni modalità.

##Integrazione di OpenID Connect

Per integrare l'applicazione con Azure AD, seguire [le istruzioni per sviluppatori](active-directory-authentication-scenarios.md). Completare quindi i dati seguenti e inviare all'indirizzo waadpartners@microsoft.com.

* Fornire le credenziali per un tenant o un account di prova con l'applicazione che possono essere usate dal team di Azure AD per testare l'integrazione.  

* Fornire istruzioni su come il team di Azure AD può accedere e connettere un'istanza di Azure AD all'applicazione usando il [framework di consenso di Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/#overview-of-the-consent-framework/).

* Fornire eventuali istruzioni aggiuntive necessarie per il team di Azure AD per testare l'accesso Single Sign-On con l'applicazione.

* Specificare le informazioni seguenti:

> Nome società:
> 
> Sito Web società:
> 
> Nome dell'applicazione:
> 
> Descrizione dell'applicazione (max 256 caratteri):
> 
> Sito Web dell'applicazione (informazioni):
> 
> Sito Web del supporto tecnico dell'applicazione o informazioni di contatto:
> 
> ID client dell'applicazione, indicato nei dettagli dell'applicazione all'indirizzo https://manage.windowsazure.com:
> 
> URL di iscrizione dell'applicazione, cui i clienti accedono per iscriversi e/o acquistare l'applicazione:
> 
> Scegliere fino a tre categorie a cui associare l'applicazione. Per informazioni sulle categorie disponibili, vedere il Marketplace di Azure Active Directory:
> 
> Allegare l'icona piccola dell'applicazione (file PNG, 45x45 pixel, colore di sfondo a tinta unita):
> 
> Allegare l'icona grande dell'applicazione (file PNG, 215x215 pixel, colore di sfondo a tinta unita):
> 
> Allegare il logo dell'applicazione (file PNG, 150x122 pixel, colore di sfondo a tinta unita):

##Integrazione di SAML

Qualsiasi app che supporta SAML 2.0 può essere integrata direttamente con un tenant di Azure AD usando [queste istruzioni per l'aggiunta di un'applicazione personalizzata](active-directory-saas-custom-apps.md). Dopo aver verificato che l'integrazione dell'applicazione funziona con Azure AD, inviare le informazioni seguenti a <waadpartners@microsoft.com>.

* Fornire le credenziali per un tenant o un account di prova con l'applicazione che possono essere usate dal team di Azure AD per testare l'integrazione.  

* Fornire l'URL Single Sign-On SAML, l'URL dell'autorità di certificazione (ID entità) e i valori dell'URL di risposta (servizio consumer di asserzione) per l'applicazione, come descritto [qui](active-directory-saas-custom-apps.md). Se si forniscono in genere questi valori come parte di un file di metadati SAML, inviare anche quest'ultimo.

* Fornire una breve descrizione di come configurare Azure AD come provider di identità nell'applicazione usando SAML 2.0. Se l'applicazione supporta la configurazione di Azure AD come provider di identità attraverso un portale di amministrazione self-service, assicurarsi che le credenziali specificate in precedenza includano la possibilità di impostare questa funzionalità.

* Specificare le informazioni seguenti:

> Nome società:
> 
> Sito Web società:
> 
> Nome dell'applicazione:
> 
> Descrizione dell'applicazione (max 256 caratteri):
> 
> Sito Web dell'applicazione (informazioni):
> 
> Sito Web del supporto tecnico dell'applicazione o informazioni di contatto:
> 
> URL di iscrizione dell'applicazione, cui i clienti accedono per iscriversi e/o acquistare l'applicazione:
> 
> Scegliere fino a tre categorie a cui associare l'applicazione. Per informazioni sulle categorie disponibili, vedere il [Marketplace di Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/):
> 
> Allegare l'icona piccola dell'applicazione (file PNG, 45x45 pixel, colore di sfondo a tinta unita):
> 
> Allegare l'icona grande dell'applicazione (file PNG, 215x215 pixel, colore di sfondo a tinta unita):
> 
> Allegare il logo dell'applicazione (file PNG, 150x122 pixel, colore di sfondo a tinta unita):

<!---HONumber=AcomDC_0128_2016-->