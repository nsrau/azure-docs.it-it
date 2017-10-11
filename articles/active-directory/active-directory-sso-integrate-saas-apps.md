---
title: Integrare i servizi Single Sign-On di Azure Active Directory nelle app SaaS | Microsoft Docs
description: "Abilitare l'autenticazione Single Sign-On e la gestione dell’accesso centralizzata per il provisioning degli utenti delle app SaaS in Azure Active Directory. Cenni preliminari sull'integrazione di Azure Active Directory alle App SaaS."
services: active-directory
keywords: Integrare Azure AD con app SaaS
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 53b9d341-d1fc-4bbb-ac7c-3f4c68fcf00a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: aaronsm
ms.openlocfilehash: fc0d297598c334ca8f6f8a2bd3ae948c87956342
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Integrare i servizi Single Sign-On di Azure Active Directory nelle app SaaS
> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-enterprise-apps-manage-sso.md)
> * [portale di Azure classico](active-directory-sso-integrate-saas-apps.md)
>
>

[!INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Per iniziare a configurare Single Sign-On per un'applicazione che si desidera introdurre nella propria organizzazione, sarà necessario usare una directory esistente in Azure Active Directory (Azure AD). È possibile usare una directory di Azure AD ottenuta tramite Microsoft Azure, Office 365 o Windows Intune. Se si dispone di due o più directory di questo tipo, vedere [Amministrare la directory di Azure AD](active-directory-administer.md) per determinare quale usare.

> [!IMPORTANT]
> Microsoft consiglia di gestire Azure AD usando l'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) nel portale di Azure invece di usare il portale di Azure classico citato nel presente articolo. Per informazioni su come assegnare i ruoli amministratore nell'interfaccia di amministrazione di Azure AD, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).

## <a name="authentication"></a>Autenticazione
Per le applicazioni che supportano i protocolli SAML 2.0, WS-Federation o OpenID Connect, Azure Active Directory usa i certificati di firma per stabilire relazioni di trust. Per altre informazioni su questo aspetto, vedere [Gestione dei certificati per Single Sign-On federato](active-directory-sso-certs.md).

Per le applicazioni che supportano solo l'accesso basato su moduli HTML, Azure Active Directory usa gli insiemi di credenziali per stabilire relazioni di trust. Ciò consente agli utenti dell'organizzazione di accedere automaticamente a un'applicazione SaaS tramite Azure AD utilizzando le informazioni dell'account utente dall'applicazione SaaS. Azure AD raccoglie e archivia in modo sicuro le informazioni sull'account utente e la relativa password. Per altre informazioni, vedere [Single Sign-On basato su password](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

## <a name="authorization"></a>Autorizzazione
Un account con provisioning consente all'utente di essere autorizzato a usare un'applicazione, dopo l'autenticazione tramite Single Sign-On. Il provisioning dell'utente può essere eseguito manualmente o in alcuni casi è possibile aggiungere e rimuovere le informazioni utente dall'applicazione SaaS in base alle modifiche apportate in Azure Active Directory. Per altre informazioni sull'uso dei connettori di Azure AD esistenti per il provisioning automatico, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

In caso contrario, è possibile aggiungere manualmente le informazioni sull'utente a un'applicazione o usare altre soluzioni di provisioning disponibili sul mercato.

## <a name="access"></a>Accesso
Azure AD fornisce diverse soluzioni personalizzabili per distribuire le applicazioni agli utenti finali all'interno dell'organizzazione: Non è obbligatorio usare soluzioni specifiche per la distribuzione o l'accesso. È possibile usare [la soluzione più adatta alle proprie esigenze](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Considerazioni aggiuntive per le applicazioni già in uso.
L'impostazione di Single Sign-On per un'applicazione in uso nell'organizzazione è un processo diverso rispetto alla creazione di nuovi account per una nuova applicazione. Esistono un paio di passaggi preliminari tra cui il mapping di identità utente nell'applicazione alle identità di Azure AD e le informazioni sull'esperienza utente durante l'accesso a un'applicazione dopo la relativa integrazione.

> [!NOTE]
> Per configurare SSO per un'applicazione esistente, è necessario disporre dei diritti di amministratore globale sia per Azure AD sia per l'applicazione SaaS.
>
>

### <a name="mapping-user-accounts"></a>Mapping degli account utente
Un'identità utente ha in genere un identificatore univoco che può essere un indirizzo di posta elettronica o un nome dell'entità utente (UPN). Sarà necessario collegare (mappare) ciascuna identità utente dell'applicazione alla rispettiva identità di Azure AD. Per eseguire questa operazione è possibile procedere in due modi, a seconda dei requisiti di autenticazione dell'applicazione.

Per altre informazioni sul mapping delle identità delle applicazioni alle identità di Azure AD, vedere l'articolo relativo alla [personalizzazione delle attestazioni rilasciate nel token SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) e alla [personalizzazione dei mapping degli attributi per il provisioning](active-directory-saas-customizing-attribute-mappings.md).

### <a name="understanding-the-users-log-in-experience"></a>Informazioni sull'esperienza utente all'accesso
Quando si integra SSO per un'applicazione già in uso, è importante tenere presente che ci saranno effetti sull'esperienza utente. Per tutte le applicazioni, gli utenti inizieranno a usare le credenziali di Azure AD per l'accesso. Inoltre, potrebbero dover usare un portale diverso per accedere alle applicazioni.

L'accesso SSO per alcune applicazioni può essere eseguito nell'interfaccia di accesso dell'applicazione, ma per accedere ad altre applicazioni l'utente dovrà passare attraverso un portale centrale, ad esempio [App personali](http://myapps.microsoft.com) o [Office365](http://portal.office.com/myapps). Per altre informazioni sui diversi tipi di SSO e le relative esperienze utente, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

Vedere anche la sezione relativa all' *eliminazione dell'autorizzazione utente* nella pagina della [guida per gli sviluppatori](active-directory-applications-guiding-developers-for-lob-applications.md) .

## <a name="next-steps"></a>Passaggi successivi
Per le applicazioni SaaS disponibili nella raccolta delle app, Azure AD offre varie [esercitazioni pratiche su come integrare delle app SaaS](active-directory-saas-tutorial-list.md).

Se l'app non è presente nella raccolta, è possibile [aggiungerla alla raccolta delle app di Azure AD come applicazione personalizzata](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

La libreria di Azure.com include molti altri dettagli su tutti questi aspetti, a partire da [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

Vedere anche [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md).
