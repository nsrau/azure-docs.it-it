---
title: Autenticazione basata su certificati in iOS-Azure Active Directory
description: Informazioni sugli scenari supportati e i requisiti per la configurazione dell'autenticazione basata su certificati per Azure Active Directory nelle soluzioni con i dispositivi iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fa6c28eaa75485e0b45bb4404e685b6ee223b46
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965591"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Autenticazione basata su certificati di Azure Active Directory in iOS

Per migliorare la sicurezza, i dispositivi iOS possono usare l'autenticazione basata su certificati (CBA) per eseguire l'autenticazione a Azure Active Directory (Azure AD) usando un certificato client sul dispositivo quando si connettono alle applicazioni o ai servizi seguenti:

* Applicazioni Office per dispositivi mobili, come Microsoft Outlook e Microsoft Word
* Client Exchange ActiveSync (EAS)

L'uso dei certificati Elimina la necessità di immettere una combinazione di nome utente e password in alcune applicazioni di posta elettronica e Microsoft Office nel dispositivo mobile.

Questo articolo illustra in dettaglio i requisiti e gli scenari supportati per la configurazione di ACB in un dispositivo iOS. CBA per iOS è disponibile in cloud pubblici di Azure, Microsoft Government cloud, Microsoft Cloud Germania e Microsoft Azure Cina 21Vianet.

## <a name="microsoft-mobile-applications-support"></a>Supporto delle applicazioni per dispositivi mobili Microsoft

| App | Supporto |
| --- | --- |
| App Azure Information Protection |![Segno di spunta che significa supporto per questa applicazione][1] |
| Intune Portale aziendale |![Segno di spunta che significa supporto per questa applicazione][1] |
| Microsoft Teams |![Segno di spunta che significa supporto per questa applicazione][1] |
| Office (dispositivi mobili) |![Segno di spunta che significa supporto per questa applicazione][1] |
| OneNote |![Segno di spunta che significa supporto per questa applicazione][1] |
| OneDrive |![Segno di spunta che significa supporto per questa applicazione][1] |
| Outlook |![Segno di spunta che significa supporto per questa applicazione][1] |
| Power BI |![Segno di spunta che significa supporto per questa applicazione][1] |
| Skype for Business |![Segno di spunta che significa supporto per questa applicazione][1] |
| Word / Excel / PowerPoint |![Segno di spunta che significa supporto per questa applicazione][1] |
| Yammer |![Segno di spunta che significa supporto per questa applicazione][1] |

## <a name="requirements"></a>Requisiti

Per usare l'ACB con iOS, si applicano i requisiti e le considerazioni seguenti:

* La versione del sistema operativo del dispositivo deve essere iOS 9 o versioni successive.
* Per le applicazioni Office in iOS è richiesto Microsoft Authenticator.
* È necessario creare una preferenza di identità nel keychain di macOS che includa l'URL di autenticazione del server ADFS. Per altre informazioni, vedere [creare una preferenza di identità nell'accesso keychain in Mac](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Si applicano i requisiti e le considerazioni seguenti Active Directory Federation Services (ADFS):

* Il server ADFS deve essere abilitato per l'autenticazione del certificato e utilizzare l'autenticazione federata.
* Il certificato deve usare l'utilizzo chiavi avanzato (EKU) e contenere l'UPN dell'utente nel *nome alternativo del soggetto (nome dell'entità NT)*.

## <a name="configure-adfs"></a>Configurare AD FS

Per Azure AD revocare un certificato client, il token ADFS deve disporre delle attestazioni seguenti. Azure AD aggiunge le attestazioni al token di aggiornamento se sono disponibili nel token ADFS (o in qualsiasi altro token SAML). Quando è necessario convalidare il token di aggiornamento, queste informazioni vengono usate per controllare la revoca:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` -aggiungere il numero di serie del certificato client
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` -aggiungere la stringa per l'emittente del certificato client

Come procedura consigliata, è necessario aggiornare anche le pagine di errore ADFS dell'organizzazione con le seguenti informazioni:

* Il requisito per l'installazione del Microsoft Authenticator in iOS.
* Istruzioni su come ottenere un certificato utente.

Per ulteriori informazioni, vedere [personalizzazione della pagina di accesso ad FS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

## <a name="use-modern-authentication-with-office-apps"></a>Usare l'autenticazione moderna con le app di Office

Alcune app di Office con autenticazione moderna abilitate inviano `prompt=login` a Azure ad nella richiesta. Per impostazione predefinita, Azure AD converte `prompt=login` la richiesta in ADFS come `wauth=usernamepassworduri` (chiede ad ADFS di eseguire l'autenticazione U/P) e `wfresh=0` (chiede ad ADFS di ignorare lo stato SSO ed eseguire una nuova autenticazione). Per abilitare l'autenticazione basata su certificati per queste app, modificare il comportamento predefinito del Azure AD.

Per aggiornare il comportamento predefinito, impostare '*PromptLoginBehavior*' nelle impostazioni del dominio federato su *disabled*. Per eseguire questa attività, è possibile usare il cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) , come illustrato nell'esempio seguente:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Supporto per i client di Exchange ActiveSync

In iOS 9 o versioni successive è supportato il client di posta iOS nativo. Per determinare se questa funzionalità è supportata per tutte le altre applicazioni Exchange ActiveSync, contattare lo sviluppatore dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Per configurare l'autenticazione basata su certificati nel proprio ambiente, vedere [Introduzione all'autenticazione basata su certificati](active-directory-certificate-based-authentication-get-started.md) per istruzioni.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png