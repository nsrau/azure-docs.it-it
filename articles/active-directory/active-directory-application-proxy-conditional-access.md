---
title: Accesso condizionale per le applicazioni pubblicate con Proxy dell&quot;applicazione AD Azure
description: "Viene descritto come configurare l&quot;accesso condizionale per le applicazioni pubblicate per l&quot;accesso in remoto tramite Proxy dell’applicazione AD Azure."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 2e97722b-eb4e-4078-b607-9fed210d8a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3b1ec45073a7d9abb7eb12c009ce60357a0d1545


---
# <a name="working-with-conditional-access"></a>Utilizzo di access condizionale
È possibile configurare regole di accesso per concedere accesso condizionale alle applicazioni pubblicate tramite il proxy di applicazione. Ciò consente di:

* Richiedere l'autenticazione a più fattori per ogni applicazione
* Richiedere l'autenticazione a più fattori solo quando gli utenti non sono al lavoro
* Impedire agli utenti l'accesso all'applicazione quando non sono al lavoro

Queste regole possono essere applicate a tutti gli utenti e gruppi o solo a utenti e gruppi specifici. Per impostazione predefinita, la regola verrà applicata a tutti gli utenti che dispongono dell'accesso all'applicazione. Tuttavia la regola può essere limitata agli utenti che sono membri di gruppi di sicurezza specificati.  

Quando un utente accede a un'applicazione federata che utilizza OAuth 2.0, OpenID Connect, SAML o WS-Federation, vengono valutate le regole di accesso. Le regole di accesso vengono valutate anche quando viene usato un token di aggiornamento con OAuth 2.0 e OpenID Connect per acquisire un token di accesso.

## <a name="conditional-access-prerequisites"></a>Prerequisiti per l'accesso condizionale
* Sottoscrizione di Azure Active Directory Premium
* Un tenant di Azure Active Directory federato o gestito
* I tenant federati richiedono l'abilitazione dell'autenticazione a più fattori   
    ![Richiedere la Multi-Factor Authentication](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Configurare l'autenticazione a più fattori per applicazione
1. Accedere come amministratore al portale di Azure classico.
2. Passare ad Active Directory e selezionare la directory in cui si desidera abilitare il proxy dell'applicazione.
3. Fare clic su **Applicazioni** e scorrere verso il basso fino alla sezione **Regole di accesso**. La sezione Regole di accesso viene visualizzata solo per le applicazioni pubblicate con il Proxy dell'applicazione che utilizza l'autenticazione federata.
4. Attivare la regola selezionando **Abilitare le regole di accesso** su **On**.
5. Specificare gli utenti e i gruppi a cui verranno applicate le regole. Utilizzare il tasto **Aggiungi gruppo** per selezionare uno o più gruppi a cui verrà applicata la regola di accesso. Questa finestra di dialogo può essere utilizzata anche per rimuovere i gruppi selezionati.  Quando si selezionano le regole da applicare ai gruppi, le regole di accesso possono essere imposte solo agli utenti che appartengono a uno dei gruppi di sicurezza specificati.  
   
   * Per escludere in modo esplicito gruppi di sicurezza dalla regola, selezionare **Escludi** e specificare uno o più gruppi. Gli utenti che sono membri di un gruppo nell'elenco Tranne non dovranno eseguire l'autenticazione a più fattori.  
   * Se un utente è stato configurato utilizzando la funzionalità di Multi-Factor Authentication per utente, questa impostazione avrà la precedenza sulle regole della Multi-Factor Authentication dell’applicazione. Ciò significa che a un utente che è stato configurato per l'autenticazione a più fattori per utente verrà richiesto di eseguire l'autenticazione a più fattori, anche se è stato esentato dalle regole di autenticazione a più fattori dell'applicazione. Altre informazioni su [autenticazione a più fattori e impostazioni per utente](../multi-factor-authentication/multi-factor-authentication.md).
6. Selezionare la regola di accesso che si desidera impostare:
   
   * **Richiedere la Multi-Factor Authentication**: agli utenti a cui si applicano le regole di accesso verrà richiesta l'autenticazione a più fattori prima di accedere all'applicazione a cui viene applicata la regola.
   * **Richiedere la Multi-Factor Authentication quando non si è al lavoro**: gli utenti che tentano di accedere all'applicazione da un indirizzo IP attendibile non dovranno eseguire l'autenticazione a più fattori. Gli intervalli di indirizzi IP attendibili possono essere configurati nella pagina Impostazioni di autenticazione a più fattori.
   * **Bloccare l'accesso quando non si è al lavoro**: gli utenti che tentano di accedere all'applicazione dall'esterno della rete aziendale non saranno in grado di accedere all'applicazione.

## <a name="configuring-mfa-for-federation-services"></a>Configurazione di autenticazione a più fattori per servizi di federazione
Per tenant federati, l’autenticazione a più fattori (MFA) potrebbe eseguita da Azure Active Directory o dal server AD FS locale. Per impostazione predefinita, verrà eseguita l'autenticazione a più fattori in qualsiasi pagina ospitata da Azure Active Directory. Per configurare l'autenticazione a più fattori in locale, eseguire Windows PowerShell e utilizzare la proprietà – SupportsMFA per impostare il modulo di Azure AD.

L'esempio seguente illustra come abilitare MFA in locale usando il [cmdlet Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) nel tenant contoso.com: `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Oltre a impostare questo flag, l'istanza di AD FS del tenant federato deve essere configurata in modo da eseguire l'autenticazione a più fattori. Seguire le istruzioni per la [distribuzione dell'autenticazione a più fattori di Microsoft Azure in locale](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="see-also"></a>Vedere anche
* [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
* [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md)
* [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
* [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/)




<!--HONumber=Dec16_HO4-->


