---
title: Azure AD SSPR data requirements - Azure Active Directory
description: Requisiti dei dati per la Reimpostazione self-service delle password e informazioni su come soddisfarli
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a14338e552250ac63c344365099a16f20616ea9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74964030"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Distribuire la reimpostazione della password senza richiedere la registrazione dell'utente finale

Per distribuire la funzionalità di reimpostazione password self-service di Azure Active Directory (Azure AD) devono essere presenti i dati di autenticazione. Alcune organizzazioni richiedono agli utenti di immettere personalmente i dati di autenticazione. Altre organizzazioni preferiscono sincronizzarsi con i dati già esistenti in Active Directory. Questi dati sincronizzati vengono resi disponibili per Azure AD e SSPR senza richiedere l'interazione dell'utente se si soddisfano i requisiti seguenti:This synced data is made available to Azure AD and SSPR without requiring user interaction if you meet the following requirements:

* I dati sono formattati correttamente nella directory locale dell'utente.
* È stata eseguita la configurazione di [Azure AD Connect usando le impostazioni rapide](../hybrid/how-to-connect-install-express.md).

Per garantirne il corretto funzionamento, i numeri di telefono devono essere nel formato *+PrefissoInternazionale NumeroTelefonico*, ad esempio +1 4255551234.

> [!NOTE]
> È necessario uno spazio tra il prefisso internazionale e il numero di telefono.
>
> La reimpostazione della password non supporta le estensioni del telefono. Anche nel formato +1 4255551234X12345, le estensioni vengono rimosse prima della chiamata.

## <a name="fields-populated"></a>Campi popolati

Se si usano le impostazioni predefinite in Azure AD Connect, vengono eseguiti i mapping seguenti:

| Active Directory locale | Azure AD |
| --- | --- |
| telephoneNumber | Telefono ufficio |
| mobile | Cellulare |

Dopo che un utente ha verificato il proprio numero di telefono cellulare, anche il campo *Telefono* in Informazioni di contatto per l'autenticazione in Azure AD viene popolato con tale numero. **Authentication contact info**

## <a name="authentication-contact-info"></a>Informazioni di contatto per l'autenticazione

Nella pagina Metodi di autenticazione per un utente di Azure AD nel portale di Azure un amministratore globale può impostare manualmente le informazioni di contatto per l'autenticazione, come illustrato nella schermata di esempio seguente:On the **Authentication methods** page for an Azure AD user in the Azure portal, a Global Administrator can manually set the authentication contact information, as shown in the following example screenshot:

![Informazioni di contatto per l'autenticazione in un utente in Azure ADAuthentication contact info on a user in Azure AD][Contact]

* Se il campo **Telefono** è popolato e **il telefono cellulare** è abilitato nei criteri SSPR, l'utente visualizza tale numero nella pagina di registrazione della reimpostazione della password e durante il flusso di lavoro di reimpostazione della password.
* Il campo **Telefono alternativo** non viene utilizzato per la reimpostazione della password.
* Se il campo **Posta elettronica** è popolato e la **posta elettronica** è abilitata nei criteri SSPR, l'utente visualizza tale messaggio di posta elettronica nella pagina di registrazione della reimpostazione della password e durante il flusso di lavoro di reimpostazione della password.
* Se il campo **Posta elettronica alternativa** è popolato e la posta **elettronica** è abilitata nei criteri SSPR, l'utente **non vedrà** tale messaggio nella pagina di registrazione della reimpostazione della password, ma lo vedrà durante il flusso di lavoro di reimpostazione della password.

## <a name="security-questions-and-answers"></a>Domande di sicurezza e risposte

Le domande di sicurezza e le risposte sono archiviate in modo sicuro nel tenant di Azure AD e sono accessibili agli utenti esclusivamente tramite il [portale di registrazione SSPR](https://aka.ms/ssprsetup). Gli amministratori non possono vedere, impostare o modificare il contenuto di domande e risposte di altri utenti.

## <a name="what-happens-when-a-user-registers"></a>Cosa accade quando un utente si registra

Quando un utente si registra, i campi seguenti vengono impostati nella pagina di registrazione:

* **Telefono per l'autenticazione**
* **Indirizzo di posta elettronica per l'autenticazione**
* **Domande di sicurezza e risposte**

Se è stato fornito un valore per **Cellulare** o **Indirizzo di posta elettronica alternativo**, gli utenti possono utilizzare immediatamente tali valori per reimpostare le password, anche se non si sono registrati per il servizio. Gli utenti visualizzano e possono modificare tali valori quando si registrano per la prima volta. Dopo la registrazione, questi valori vengono mantenuti nei campi **Telefono autenticazione** e **Email di autenticazione,** rispettivamente.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Impostare e leggere i dati di autenticazione tramite PowerShell

I campi seguenti possono essere impostati tramite PowerShell:

* **E-mail alternativa**
* **Cellulare**
* **Telefono ufficio**: può essere impostato solo se non in sincronizzazione con una directory locale

### <a name="use-powershell-version-1"></a>Usare PowerShell versione 1

Per iniziare, è necessario [scaricare e installare il modulo di Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Al termine dell'installazione, è possibile seguire la procedura seguente per configurare tutti i campi.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Impostare i dati di autenticazione con PowerShell versione 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>Leggere i dati di autenticazione con PowerShell versione 1

```PowerShell
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>Leggere i valori delle opzioni Telefono per l'autenticazione e Indirizzo di posta elettronica per l'autenticazione

Per leggere i valori delle opzioni **Telefono per l'autenticazione** e **Indirizzo di posta elettronica per l'autenticazione** se si usa PowerShell versione 1, usare i comandi seguenti:

```PowerShell
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>Usare PowerShell versione 2

Per iniziare, è necessario [scaricare e installare il modulo di Azure AD PowerShell versione 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0). Al termine dell'installazione, è possibile seguire la procedura seguente per configurare tutti i campi.

Per eseguire l'installazione rapida da versioni recenti di PowerShell che supportano Install-Module, eseguire i comandi seguenti. La prima riga controlla se il modulo è già installato.

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Impostare i dati di autenticazione con PowerShell versione 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>Leggere i dati di autenticazione con PowerShell versione 2

```PowerShell
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Passaggi successivi

* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](howto-sspr-deployment.md)
* [Reimpostare o modificare la password](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrati per la reimpostazione della password self-service](../user-help/active-directory-passwords-reset-register.md)
* [Hai una domanda di licenza?](concept-sspr-licensing.md)
* [Metodi di autenticazione disponibili per gli utenti](concept-sspr-howitworks.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](concept-sspr-policy.md)
* [Panoramica del writeback delle password](howto-sspr-writeback.md)
* [Come creare un report sull'attività relativa alla reimpostazione della password self-service](howto-sspr-reporting.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Credo che qualcosa sia rotto. Come si risolvono i problemi relativi a SSPR?](active-directory-passwords-troubleshoot.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Gli amministratori globali possono modificare informazioni di contatto per l'autenticazione di un utente"
