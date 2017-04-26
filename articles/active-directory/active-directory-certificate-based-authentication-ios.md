---
title: Autenticazione basata su certificati di Azure Active Directory in iOS | Documentazione Microsoft
description: Informazioni sugli scenari supportati e i requisiti per configurare l&quot;autenticazione basata su certificati nelle soluzioni con i dispositivi iOS
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 754fad938fef321c07b95ddb5727ffdb2b0eaa2c
ms.lasthandoff: 03/31/2017


---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Autenticazione basata su certificati di Azure Active Directory in iOS

L'autenticazione basata su certificati (CBA) consente di essere autenticati da Azure Active Directory con un certificato client su un dispositivo Windows, Android o iOS quando si connette il proprio account Exchange online a: 

* Applicazioni Office per dispositivi mobili, come Microsoft Outlook e Microsoft Word   
* Client Exchange ActiveSync (EAS) 

La configurazione di questa funzionalità elimina la necessità di immettere una combinazione di nome utente e password in determinate applicazioni di posta e applicazioni Microsoft Office sul dispositivo mobile. 

Questo argomento indica i requisiti e gli scenari supportati per la configurazione dell'autenticazione basata su certificati su un dispositivo iOS per gli utenti di tenant dei piani Office 365 Enterprise, Business, Education e US Government. 

Questa funzionalità è disponibile in anteprima nei piani di Office 365 US Government Defense e Federal.




## <a name="office-mobile-applications-support"></a>Supporto delle applicazioni Office per dispositivi mobili

| App | Supporto |
| --- | --- |
| Word / Excel / PowerPoint |![Controllo][1] |
| OneNote |![Controllo][1] |
| OneDrive |![Controllo][1] |
| Outlook |![Controllo][1] |
| Yammer |![Controllo][1] |
| Skype for Business Online |![Controllo][1] |

## <a name="requirements"></a>Requisiti 

La versione del sistema operativo del dispositivo deve essere iOS 9 o successiva 

È necessario configurare un server federativo.  

Per le applicazioni Office in iOS è richiesto Microsoft Authenticator.  

Perché Azure Active Directory possa revocare un certificato client, il token ADFS deve avere le attestazioni seguenti:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (Il numero di serie del certificato client) 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (La stringa per l'autorità emittente del certificato client) 

Azure Active Directory aggiunge queste attestazioni per il token di aggiornamento se sono disponibili nel token ADFS (o in qualsiasi altro token SAML). Quando il token di aggiornamento deve essere convalidato, queste informazioni vengono usate per controllare la revoca. 

Come procedura consigliata, è necessario aggiornare le pagine di errore di ADFS con le operazioni seguenti:

* Il requisito dell'installazione di Microsoft Authenticator in iOS
* Istruzioni su come ottenere un certificato utente. 

Per altre informazioni, vedere [Personalizzazione delle pagine di accesso ad AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Alcune app di Office, in cui non è abilitata l'autenticazione moderna, inviano "*prompt=login*" ad Azure AD nella richiesta. Per impostazione predefinita, Azure AD lo traduce in una richiesta ad AD FS di eseguire l'autorizzazione di nome utente e password, ovvero "*wauth=usernamepassworduri*", e di ignorare lo stato SSO ed eseguire una nuova autenticazione, ovvero "*wfresh=0*". Per abilitare l'autenticazione basata su certificati per queste applicazioni, è necessario modificare il comportamento predefinito di Azure AD. È sufficiente impostare "*PromptLoginBehavior*" tra le impostazioni del dominio federato su "*Disabled*" (Disabilitato). Per eseguire questa operazione è possibile usare il cmdlet [MSOLDomainFederationSettings](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msoldomainfederationsettings):

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`
  

## <a name="exchange-activesync-clients-support"></a>Supporto dei client Exchange ActiveSync
In iOS 9 o versioni successive è supportato il client di posta iOS nativo. Per tutte le altre applicazioni Exchange ActiveSync, contattare lo sviluppatore dell'applicazione per determinare se questa funzionalità è supportata.  


## <a name="next-steps"></a>Passaggi successivi

Se si desidera configurare l'autenticazione basata su certificati nel proprio ambiente, vedere [Introduzione all'autenticazione basata su certificati in Android](active-directory-certificate-based-authentication-get-started.md) per le istruzioni.


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png

