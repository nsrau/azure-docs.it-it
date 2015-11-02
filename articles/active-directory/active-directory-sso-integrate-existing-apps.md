<properties
   pageTitle="Integrazione dei servizi Single Sign-On di Azure Active Directory con le app esistenti | Microsoft Azure"
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
   ms.date="10/16/2015"
   ms.author="kgremban; liviodlc"/>

# Integrazione di servizi Single Sign-On di Azure Active Directory con le app esistenti

## Panoramica

L'impostazione di Single Sign-On (SSO) per un'applicazione in uso nell'organizzazione è un processo diverso rispetto alla creazione di nuovi account per una nuova applicazione. Esistono un paio di passaggi preliminari tra cui il mapping di identità utente nell'applicazione alle identità di Azure Active Directory (AD) e le informazioni sull'esperienza utente durante l'accesso a un'applicazione dopo la relativa integrazione.

> [AZURE.NOTE]Per configurare SSO per un'applicazione esistente, è necessario disporre dei diritti di amministratore globale sia per Azure AD sia per l'applicazione SaaS.

## Mapping degli account utente

Un'identità utente in genere è un identificatore univoco che può essere un indirizzo di posta elettronica o un nome dell'entità utente (UPN). Sarà necessario collegare (mappare) ciascuna identità utente dell'applicazione alla rispettiva identità di Azure AD. Per eseguire questa operazione è possibile procedere in due modi, a seconda dei requisiti di autenticazione dell'applicazione.

Per altre informazioni sul mapping delle identità delle applicazioni alle identità di Azure AD, vedere l'articolo relativo alla [personalizzazione delle attestazioni rilasciate nel token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) e la pagina [Personalizzazione dei mapping degli attributi per il provisioning](active-directory-saas-customizing-attribute-mappings.md).

## Informazioni sull'esperienza utente all'accesso

Quando si integra SSO per un'applicazione già in uso, è importante tenere presente che ci saranno effetti sull'esperienza utente. Per tutte le applicazioni, gli utenti inizieranno a usare le credenziali di Azure AD per l'accesso. Inoltre, potrebbero dover usare un portale diverso per accedere alle applicazioni.

L'accesso SSO per alcune applicazioni può essere eseguito nell'interfaccia di accesso dell'applicazione, ma per accedere ad altre applicazioni l'utente dovrà passare attraverso un portale centrale, ad esempio [App personali](http://myapps.microsoft.com) o [Office 365](http://portal.office.com/myapps). Per altre informazioni sui diversi tipi di SSO e le relative esperienze utente, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

Vedere anche la sezione relativa all'*eliminazione dell'autorizzazione utente* nella pagina della [guida per gli sviluppatori](active-directory-applications-guiding-developers-for-lob-applications.md).

## Articoli correlati
Collegamenti per l'eliminazione dell'autorizzazione utente e altri articoli di guida per sviluppatori

<!---HONumber=Oct15_HO4-->