---
title: Autenticazione basata su certificati in iOS - Azure Active DirectoryCertificate-based authentication on iOS - Azure Active Directory
description: Informazioni sugli scenari supportati e sui requisiti per la configurazione dell'autenticazione basata su certificati per Azure Active Directory nelle soluzioni con dispositivi iOSLearn about the supported scenarios and the requirements for configuring certificate-based authentication for Azure Active Directory in solutions with iOS devices
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c5e18a0bf84e96476eafd7ff35398049f1a492
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639620"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Autenticazione basata su certificati di Azure Active Directory in iOS

Per migliorare la sicurezza, i dispositivi iOS possono usare l'autenticazione basata su certificati (CBA) per eseguire l'autenticazione in Azure Active Directory (Azure AD) usando un certificato client nel dispositivo quando si connettono alle applicazioni o ai servizi seguenti:To improve security, iOS devices can use certificate-based authentication (CBA) to authenticate to Azure Active Directory (Azure AD) using a client certificate on their device when connecting to the following applications or services:

* Applicazioni Office per dispositivi mobili, come Microsoft Outlook e Microsoft Word
* Client Exchange ActiveSync (EAS)

L'utilizzo dei certificati elimina la necessità di immettere una combinazione di nome utente e password in determinate applicazioni di posta elettronica e Microsoft Office sul dispositivo mobile.

Questo articolo descrive in dettaglio i requisiti e gli scenari supportati per la configurazione di CBA in un dispositivo iOS.This article details the requirements and the supported scenarios for configuring CBA on an iOS device. CBA per iOS è disponibile nei cloud pubblici di Azure, Microsoft Government Cloud, Microsoft Cloud Germania e Microsoft Azure China 21Vianet.

## <a name="microsoft-mobile-applications-support"></a>Supporto delle applicazioni per dispositivi mobili Microsoft

| App | Supporto |
| --- | --- |
| App Azure Information Protection |![Segno di spunta che indica il supporto per questa applicazione][1] |
| Portale aziendale Intune |![Segno di spunta che indica il supporto per questa applicazione][1] |
| Microsoft Teams |![Segno di spunta che indica il supporto per questa applicazione][1] |
| OneNote |![Segno di spunta che indica il supporto per questa applicazione][1] |
| OneDrive |![Segno di spunta che indica il supporto per questa applicazione][1] |
| Outlook |![Segno di spunta che indica il supporto per questa applicazione][1] |
| Power BI |![Segno di spunta che indica il supporto per questa applicazione][1] |
| Skype for Business Online |![Segno di spunta che indica il supporto per questa applicazione][1] |
| Word / Excel / PowerPoint |![Segno di spunta che indica il supporto per questa applicazione][1] |
| Yammer |![Segno di spunta che indica il supporto per questa applicazione][1] |

## <a name="requirements"></a>Requisiti

Per usare CBA con iOS, si applicano i requisiti e le considerazioni seguenti:To use CBA with iOS, the following requirements and considerations apply:

* La versione del sistema operativo del dispositivo deve essere iOS 9 o superiore.
* Per le applicazioni Office in iOS è richiesto Microsoft Authenticator.
* È necessario creare una preferenza di identità nel portachiavi macOS che include l'URL di autenticazione del server ADFS. Per ulteriori informazioni, consultate [Creare una preferenza di identità in Accesso Portachiavi su Mac.](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac)

Si applicano i requisiti e le considerazioni seguenti di Active Directory Federation Services (ADFS):

* Il server ADFS deve essere abilitato per l'autenticazione del certificato e utilizzare l'autenticazione federata.
* Il certificato deve utilizzare l'utilizzo chiavi avanzato (EKU) e contenere l'UPN dell'utente nel nome alternativo del *soggetto (nome principale NT)*.

## <a name="configure-adfs"></a>Configurare AD FS

Affinché Azure AD revochi un certificato client, il token ADFS deve avere le attestazioni seguenti. Azure AD aggiunge queste attestazioni al token di aggiornamento se sono disponibili nel token ADFS (o in qualsiasi altro token SAML). Quando il token di aggiornamento deve essere convalidato, queste informazioni vengono utilizzate per controllare la revoca:When the refresh token needs to be validated, this information is used to check the revocation:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`- aggiungere il numero di serie del certificato client
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`- aggiungere la stringa per l'autorità emittente del certificato client

Come procedura consigliata, è inoltre necessario aggiornare le pagine di errore ADFS dell'organizzazione con le informazioni seguenti:As a best practice, you also should update your organization's ADFS error pages with the following information:

* Il requisito per l'installazione di Microsoft Authenticator in iOS.
* Istruzioni su come ottenere un certificato utente.

Per ulteriori informazioni, vedere [Personalizzazione della pagina](https://technet.microsoft.com/library/dn280950.aspx)di accesso di ADFS .

## <a name="use-modern-authentication-with-office-apps"></a>Usare l'autenticazione moderna con le app di Office

Alcune app di Office `prompt=login` con l'autenticazione moderna abilitata inviano ad Azure AD nella richiesta. Per impostazione predefinita, `prompt=login` Azure AD converte nella richiesta in ADFS `wauth=usernamepassworduri` come `wfresh=0` (chiede ad ADFS di eseguire l'autenticazione U/P) e (chiede ad ADFS di ignorare lo stato SSO ed eseguire una nuova autenticazione). Se si vuole abilitare l'autenticazione basata su certificati per queste app, modificare il comportamento predefinito di Azure AD.

Per aggiornare il comportamento predefinito, impostare '*PromptLoginBehavior*' nelle impostazioni del dominio federato *su Disabled*. È possibile utilizzare il cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) per eseguire questa attività, come illustrato nell'esempio seguente:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Supporto per i client Exchange ActiveSync

In iOS 9 o versioni successive è supportato il client di posta iOS nativo. Per determinare se questa funzionalità è supportata per tutte le altre applicazioni di Exchange ActiveSync, contattare lo sviluppatore dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Per configurare l'autenticazione basata su certificati nell'ambiente, vedere [Introduzione all'autenticazione basata](active-directory-certificate-based-authentication-get-started.md) su certificati per istruzioni.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
