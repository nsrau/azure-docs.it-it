<properties
   pageTitle="Integrare i servizi Single Sign-On di Azure Active Directory nelle nuove app | Microsoft Azure"
   description="Azure Active Directory supporta l'accesso Single Sign-On per le nuove app SaaS per rendere possibile la gestione centralizzata degli accessi nel portale di Azure"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/09/2015"
      ms.author="curtand"/>

# Integrare i servizi Single Sign-On di Azure Active Directory nelle nuove app  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Per iniziare a configurare Single Sign-On per un'applicazione che si desidera introdurre nella propria organizzazione, sarà necessario usare una directory esistente in Azure Active Directory. È possibile usare una directory di Azure AD ottenuta tramite Microsoft Azure, Office 365 o Windows Intune. Se si dispone di due o più directory di questo tipo, vedere [Amministrare la directory di Azure AD](active-directory-administer.md) per determinare quale usare.

## Altre considerazioni

### Autenticazione

Per le applicazioni che supportano i protocolli SAML 2.0, WS-Federation o OpenID Connect, Azure Active Directory usa i certificati di firma per stabilire relazioni di trust. Per altre informazioni su questo aspetto, vedere [Gestione dei certificati per Single Sign-On federato](active-directory-sso-certs.md).

Per le applicazioni che supportano solo l'accesso basato su moduli HTML, Azure Active Directory usa gli insiemi di credenziali per stabilire relazioni di trust. Ciò consente agli utenti dell'organizzazione di accedere automaticamente a un'applicazione SaaS tramite Azure AD utilizzando le informazioni dell'account utente dall'applicazione SaaS. Azure AD raccoglie e archivia in modo sicuro le informazioni sull'account utente e la relativa password. Per altre informazioni, vedere [Single Sign-On basato su password](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

### Autorizzazione

Un account con provisioning consente all'utente di essere autorizzato a usare un'applicazione, dopo l'autenticazione tramite Single Sign-On. Il provisioning dell'utente può essere eseguito manualmente o in alcuni casi è possibile aggiungere e rimuovere le informazioni utente dall'applicazione SaaS in base alle modifiche apportate in Azure Active Directory. Per altre informazioni sull'uso dei connettori di Azure AD esistenti per il provisioning automatico, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)

In caso contrario, è possibile aggiungere manualmente le informazioni sull'utente a un'applicazione o usare altre soluzioni di provisioning disponibili sul mercato.

### Accesso

Azure AD fornisce diverse soluzioni personalizzabili per distribuire le applicazioni agli utenti finali all'interno dell'organizzazione. Non è obbligatorio usare soluzioni specifiche per la distribuzione o l'accesso. È possibile usare [la soluzione più adatta alle proprie esigenze](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## Passaggi successivi

Per le applicazioni SaaS già all'interno dell'organizzazione che si desidera abilitare per Single Sign-On, vedere [Integrare i servizi Single Sign-On di Azure Active Directory con le applicazioni esistenti](active-directory-sso-integrate-existing-apps.md)

Per le applicazioni SaaS disponibili nella raccolta delle applicazioni, Azure Active Directory offre varie [esercitazioni pratiche su come integrare applicazioni SaaS](active-directory-saas-tutorial-list.md).

Se l'applicazione non è presente nella raccolta, è possibile [aggiungerla alla raccolta delle applicazioni di Azure Active Directory come applicazione personalizzata](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

La libreria di Azure.com include molti altri dettagli su tutti questi aspetti, a partire da [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!---HONumber=Oct15_HO3-->