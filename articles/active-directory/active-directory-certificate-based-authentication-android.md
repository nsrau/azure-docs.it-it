---
title: Autenticazione basata su certificati di Azure Active Directory in Android | Documentazione Microsoft
description: Informazioni sugli scenari supportati e i requisiti per configurare l'autenticazione basata su certificati nelle soluzioni con i dispositivi Android
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/28/2017
ms.author: markvi
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: d37f0fcf4c91879c31b04dfd19cadee00e98ba91
ms.contentlocale: it-it
ms.lasthandoff: 07/28/2017

---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Autenticazione basata su certificati di Azure Active Directory in Android


L'autenticazione basata su certificati (CBA) consente di essere autenticati da Azure Active Directory con un certificato client su un dispositivo Windows, Android o iOS quando si connette il proprio account Exchange online a: 

* Applicazioni Office per dispositivi mobili, come Microsoft Outlook e Microsoft Word   
* Client Exchange ActiveSync (EAS) 

La configurazione di questa funzionalità elimina la necessità di immettere una combinazione di nome utente e password in determinate applicazioni di posta e applicazioni Microsoft Office sul dispositivo mobile. 

Questo argomento indica i requisiti e gli scenari supportati per la configurazione dell'autenticazione basata su certificati su un dispositivo iOS (Android) per gli utenti dei tenant nei piani di Office 365 Enterprise, Business, Education, US Government, China e Germany.



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
| App Azure Information Protection |![Controllo][1] |
| Microsoft Teams |![Controllo][1] |



### <a name="implementation-requirements"></a>Requisiti di implementazione

La versione del sistema operativo del dispositivo deve essere Android 5.0 (Lollipop) o successiva. 

È necessario configurare un server federativo.  

Perché Azure Active Directory possa revocare un certificato client, il token ADFS deve avere le attestazioni seguenti:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (Il numero di serie del certificato client) 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (La stringa per l'autorità emittente del certificato client) 

Azure Active Directory aggiunge queste attestazioni per il token di aggiornamento se sono disponibili nel token ADFS (o in qualsiasi altro token SAML). Quando il token di aggiornamento deve essere convalidato, queste informazioni vengono usate per controllare la revoca. 

Come procedura consigliata, è necessario aggiornare le pagine di errore di ADFS con le istruzioni su come ottenere un certificato utente.  
Per altre informazioni, vedere [Personalizzazione delle pagine di accesso ad AD FS](https://technet.microsoft.com/library/dn280950.aspx).  

Alcune app di Office, in cui non è abilitata l'autenticazione moderna, inviano "*prompt=login*" ad Azure AD nella richiesta. Per impostazione predefinita, Azure AD lo traduce in una richiesta ad AD FS di eseguire l'autorizzazione di nome utente e password, ovvero "*wauth=usernamepassworduri*", e di ignorare lo stato SSO ed eseguire una nuova autenticazione, ovvero "*wfresh=0*". Per abilitare l'autenticazione basata su certificati per queste applicazioni, è necessario modificare il comportamento predefinito di Azure AD. È sufficiente impostare "*PromptLoginBehavior*" tra le impostazioni del dominio federato su "*Disabled*" (Disabilitato). Per eseguire questa operazione è possibile usare il cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0):

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`



## <a name="exchange-activesync-clients-support"></a>Supporto dei client Exchange ActiveSync
Alcune applicazioni Exchange ActiveSync sono supportate in Android 5.0 (Lollipop) o versioni successive. Per determinare se l'applicazione di posta elettronica supporta questa funzionalità, contattare lo sviluppatore dell'applicazione. 


## <a name="next-steps"></a>Passaggi successivi

Se si desidera configurare l'autenticazione basata su certificati nel proprio ambiente, vedere [Introduzione all'autenticazione basata su certificati in Android](active-directory-certificate-based-authentication-get-started.md) per le istruzioni.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png

