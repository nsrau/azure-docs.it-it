<properties
   pageTitle="Integrare i servizi Single Sign-On di Azure Active Directory con le app esistenti | Microsoft Azure"
   description="Gestire le app SaaS già in uso, abilitando l'autenticazione Single Sign-On e il provisioning degli utenti in Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/05/2015"
   ms.author="kgremban; liviodlc"/>

# Integrare i servizi Single Sign-On di Azure Active Directory con le app esistenti

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

## Altre considerazioni

La configurazione di SSO per le app già in uso è un processo diverso rispetto alla creazione di nuovi account nelle nuove app. Sia gli amministratori che gli utenti noteranno il cambiamento quando l'app viene integrata in Azure AD.

### Cosa devono sapere gli amministratori per configurare SSO per app già in uso?

Per configurare SSO per un'app esistente, è necessario disporre dei diritti di amministratore globale sia per Azure AD che per l'applicazione SaaS.

Dato che l'applicazione è già in uso, sarà necessario collegare le identità dell'app stabilite dall'utente alle rispettive identità di Azure AD. È importante sapere cosa usa l'app come identificatore univoco per l'accesso, sia che si tratti di un indirizzo di posta elettronica, di un nome personale universale (UPN) o di un nome utente. Questo ID verrà collegato all'identificatore univoco dell'utente in Azure AD. Per altre informazioni sul collegamento delle identità delle app alle identità di Azure AD, vedere [Personalizzazione delle attestazioni rilasciate nel token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) e [Personalizzazione dei mapping degli attributi per il provisioning](active-directory-saas-customizing-attribute-mappings.md).

### Quali sono gli effetti per gli utenti finali?

Quando si integra SSO per un'applicazione già in uso, è importante tenere presente che ci saranno effetti sull'esperienza utente. Per tutte le app, gli utenti inizieranno a usare le credenziali di Azure AD per l'accesso. Inoltre, potrebbero dover usare un portale diverso per accedere alle applicazioni. SSO per alcune applicazioni può essere eseguito nel sito Web specifico dell'app, ma per accedere ad altre app l'utente dovrà passare attraverso un portale per le app centrale ([App personali](myapps.microsoft.com) o [Office365](portal.office.com/myapps)). Per altre informazioni sui diversi tipi di SSO e le relative esperienze utente, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Passaggi successivi
- Vedere come [integrare i servizi Single Sign-On di Azure Active Directory nelle nuove app](active-directory-sso-newly-acquired-saas-apps.md)
- Altre informazioni sull'[accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md)
- Vedere le [esercitazioni pratiche sulla procedura di integrazione delle app SaaS](active-directory-saas-tutorial-list.md)
-	Aggiungere un'app personalizzata con la [configurazione SAML self-service di Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)

<!---HONumber=Oct15_HO3-->