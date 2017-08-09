---
title: 'Accesso condizionale alle app locali: Azure AD | Microsoft Docs'
description: Viene descritto come configurare l'accesso condizionale per le applicazioni pubblicate per l'accesso in remoto tramite Proxy dell'applicazione AD Azure.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2e97722b-eb4e-4078-b607-9fed210d8a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 463946256f9e335fa6d98fc904835e5c3dc2725e
ms.contentlocale: it-it
ms.lasthandoff: 08/01/2017

---
# <a name="working-with-conditional-access-in-azure-ad-application-proxy"></a>Uso dell'accesso condizionale nel proxy applicazione di Azure AD

>[!NOTE]
>Questo articolo si applica solo al portale di Azure classico, che sta per essere ritirato. È consigliabile usare il [portale di Azure](https://portal.azure.com). Nel portale di Azure le app del proxy di applicazione hanno le stesse funzionalità di accesso condizionale di qualsiasi altra app SaaS. Per altre informazioni sull'accesso condizionale, vedere [Introduzione all'accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

È possibile configurare regole di accesso per concedere accesso condizionale alle applicazioni pubblicate tramite il proxy di applicazione. Ciò consente di:

* Richiedere l'autenticazione a più fattori per ogni applicazione
* Richiedere l'autenticazione a più fattori solo quando gli utenti non sono al lavoro
* Impedire agli utenti l'accesso all'applicazione quando non sono al lavoro

Queste regole possono essere applicate a tutti gli utenti e gruppi o solo a utenti e gruppi specifici. Per impostazione predefinita, la regola viene applicata a tutti gli utenti che dispongono dell'accesso all'applicazione. Tuttavia la regola può essere limitata agli utenti che sono membri di gruppi di sicurezza specificati.  

Quando un utente accede a un'applicazione federata che usa OAuth 2.0, OpenID Connect, SAML o WS-Federation, vengono valutate le regole di accesso. Le regole di accesso vengono valutate anche quando viene usato un token di aggiornamento con OAuth 2.0 e OpenID Connect per acquisire un token di accesso.

## <a name="conditional-access-prerequisites"></a>Prerequisiti per l'accesso condizionale
* Sottoscrizione di Azure Active Directory Premium
* Un tenant di Azure Active Directory federato o gestito
* I tenant federati richiedono l'autenticazione a più fattori  
    ![Richiedere la Multi-Factor Authentication](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Configurare l'autenticazione a più fattori per applicazione
1. Accedere come amministratore al portale di Azure classico.
2. Passare ad Active Directory e selezionare la directory in cui si desidera abilitare il proxy dell'applicazione.
3. Fare clic su **Applicazioni** e scorrere verso il basso fino alla sezione **Regole di accesso**. La sezione Regole di accesso viene visualizzata solo per le applicazioni pubblicate con il Proxy dell'applicazione che utilizza l'autenticazione federata.
4. Attivare la regola selezionando **Abilitare le regole di accesso** su **On**.
5. Specificare gli utenti e i gruppi a cui applicare le regole. Usare il tasto **Aggiungi gruppo** per selezionare uno o più gruppi a cui viene applicata la regola di accesso. Questa finestra di dialogo può essere utilizzata anche per rimuovere i gruppi selezionati.  Quando si selezionano le regole da applicare ai gruppi, le regole di accesso possono essere applicate solo agli utenti che appartengono a uno dei gruppi di sicurezza specificati.  

   * Per escludere in modo esplicito gruppi di sicurezza dalla regola, selezionare **Escludi** e specificare uno o più gruppi. Gli utenti che sono membri di un gruppo nell'elenco Tranne non devono eseguire l'autenticazione a più fattori.  
   * Se un utente è stato configurato con la funzionalità di autenticazione a più fattori per utente, questa impostazione ha la precedenza sulle regole di tale autenticazione per l'applicazione. Ciò significa che a un utente che è stato configurato per l'autenticazione a più fattori per utente viene richiesto di eseguire l'autenticazione a più fattori, anche se è stato esentato dalle regole di autenticazione a più fattori dell'applicazione. Altre informazioni su [autenticazione a più fattori e impostazioni per utente](../multi-factor-authentication/multi-factor-authentication.md).
6. Selezionare la regola di accesso che si desidera impostare:

   * **Richiedere l'autenticazione a più fattori**: agli utenti a cui si applicano le regole di accesso viene richiesta l'autenticazione a più fattori prima di accedere all'applicazione a cui viene applicata la regola.
   * **Richiedere la Multi-Factor Authentication quando non si è al lavoro**: gli utenti che tentano di accedere all'applicazione da un indirizzo IP attendibile non dovranno eseguire l'autenticazione a più fattori. Gli intervalli di indirizzi IP attendibili possono essere configurati nella pagina Impostazioni di autenticazione a più fattori.
   * **Bloccare l'accesso quando non si è al lavoro**: gli utenti che tentano di accedere all'applicazione dall'esterno della rete aziendale non saranno in grado di accedere all'applicazione.

## <a name="configuring-mfa-for-federation-services"></a>Configurazione di autenticazione a più fattori per servizi di federazione
Per tenant federati, l'autenticazione a più fattori (MFA) potrebbe eseguita da Azure Active Directory o dal server AD FS locale. Per impostazione predefinita, l'autenticazione a più fattori viene eseguita in qualsiasi pagina ospitata da Azure Active Directory. Per configurare l'autenticazione a più fattori in locale, eseguire Windows PowerShell e usare la proprietà SupportsMFA per impostare il modulo di Azure AD.

L'esempio seguente illustra come abilitare MFA in locale usando il [cmdlet Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) nel tenant contoso.com: `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Oltre a impostare questo flag, l'istanza di AD FS del tenant federato deve essere configurata in modo da eseguire l'autenticazione a più fattori. Seguire le istruzioni per la [distribuzione dell'autenticazione a più fattori di Microsoft Azure in locale](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="see-also"></a>Vedere anche
* [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
* [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md)
* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
* [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)

