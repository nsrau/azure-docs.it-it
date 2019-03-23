---
title: 'Autenticazione basata su certificati in Android: Azure Active Directory'
description: Informazioni sugli scenari supportati e i requisiti per configurare l'autenticazione basata su certificati nelle soluzioni con i dispositivi Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: b55b439f61c76d6d0524c1f01ba5fef745187d04
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368290"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Autenticazione basata su certificati di Azure Active Directory in Android

I dispositivi Android possono usare l'autenticazione basata sui certificati per eseguire l'autenticazione in Azure Active Directory usando un certificato client sul dispositivo durante la connessione a:

* Applicazioni Office per dispositivi mobili, come Microsoft Outlook e Microsoft Word
* Client Exchange ActiveSync (EAS)

La configurazione di questa funzionalità elimina la necessità di immettere una combinazione di nome utente e password in determinate applicazioni di posta e applicazioni Microsoft Office sul dispositivo mobile.

Questo argomento indica i requisiti e gli scenari supportati per la configurazione dell'autenticazione basata su certificati su un dispositivo iOS (Android) per gli utenti dei tenant nei piani di Office 365 Enterprise, Business, Education, US Government, China e Germany.

Questa funzionalità è disponibile in anteprima nei piani di Office 365 US Government Defense e Federal.

## <a name="microsoft-mobile-applications-support"></a>Supporto delle applicazioni per dispositivi mobili Microsoft

| App | Supporto |
| --- | --- |
| App Azure Information Protection |![Segno di spunta per indicare il supporto per questa applicazione][1] |
| Portale aziendale Intune |![Segno di spunta per indicare il supporto per questa applicazione][1] |
| Microsoft Teams |![Segno di spunta per indicare il supporto per questa applicazione][1] |
| OneNote |![Segno di spunta per indicare il supporto per questa applicazione][1] |
| OneDrive |![Segno di spunta per indicare il supporto per questa applicazione][1] |
| Outlook |![Segno di spunta per indicare il supporto per questa applicazione][1] |
| Power BI |![Segno di spunta per indicare il supporto per questa applicazione][1] |
| Skype for Business Online |![Segno di spunta per indicare il supporto per questa applicazione][1] |
| Word / Excel / PowerPoint |![Segno di spunta per indicare il supporto per questa applicazione][1] |
| Yammer |![Segno di spunta per indicare il supporto per questa applicazione][1] |

### <a name="implementation-requirements"></a>Requisiti di implementazione

La versione del sistema operativo del dispositivo deve essere Android 5.0 (Lollipop) o successiva.

È necessario configurare un server federativo.

Perché Azure Active Directory possa revocare un certificato client, il token ADFS deve avere le attestazioni seguenti:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Il numero di serie del certificato client)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (La stringa per l'autorità emittente del certificato client)

Azure Active Directory aggiunge queste attestazioni per il token di aggiornamento se sono disponibili nel token ADFS (o in qualsiasi altro token SAML). Quando il token di aggiornamento deve essere convalidato, queste informazioni vengono usate per controllare la revoca.

Come procedura consigliata, è necessario aggiornare le pagine di errore di AD FS dell'organizzazione con le informazioni seguenti:

* Il requisito dell'installazione di Microsoft Authenticator in Android.
* Istruzioni su come ottenere un certificato utente.

Per altre informazioni, vedere [Personalizzazione delle pagine di accesso ad AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Alcune app di Office, in cui non è abilitata l'autenticazione moderna, inviano "*prompt=login*" ad Azure AD nella richiesta. Per impostazione predefinita, Azure AD traduce "*prompt=login*" nella richiesta ad AD FS di eseguire l'autorizzazione di nome utente e password, ovvero "*wauth=usernamepassworduri*", e di ignorare lo stato SSO ed eseguire una nuova autenticazione, ovvero "*wfresh=0*". Per abilitare l'autenticazione basata su certificati per queste applicazioni, è necessario modificare il comportamento predefinito di Azure AD. Impostare "*PromptLoginBehavior*" tra le impostazioni del dominio federato su "*Disabled*" (Disabilitato).
Per eseguire questa operazione è possibile usare il cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0):

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Supporto dei client Exchange ActiveSync

Alcune applicazioni Exchange ActiveSync sono supportate in Android 5.0 (Lollipop) o versioni successive. Per determinare se l'applicazione di posta elettronica supporta questa funzionalità, contattare lo sviluppatore dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Se si desidera configurare l'autenticazione basata su certificati nel proprio ambiente, vedere [Introduzione all'autenticazione basata su certificati in Android](active-directory-certificate-based-authentication-get-started.md) per le istruzioni.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
