---
title: Informazioni preliminari sul contatto per la reimpostazione della password self-service-Azure Active Directory
description: Informazioni su come pre-popolare le informazioni di contatto per gli utenti di Azure Active Directory la reimpostazione della password self-service (SSPR) in modo da poter usare la funzionalità senza completare un processo di registrazione.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6fa1c14509a558c1f91d07bf9a73a4ecc39e1413
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89068678"
---
# <a name="pre-populate-user-authentication-contact-information-for-azure-active-directory-self-service-password-reset-sspr"></a>Popolamento preliminare delle informazioni di contatto per l'autenticazione utente per Azure Active Directory reimpostazione della password self-service (SSPR)

Per utilizzare Azure Active Directory (Azure AD) la reimpostazione della password self-service (SSPR), devono essere presenti le informazioni di contatto per l'autenticazione di un utente. Alcune organizzazioni dispongono di utenti che registrano i propri dati di autenticazione. Altre organizzazioni preferiscono sincronizzare i dati di autenticazione già esistenti nel Active Directory Domain Services (AD DS). Questi dati sincronizzati vengono resi disponibili per Azure AD e SSPR senza che sia necessaria l'interazione dell'utente. Se gli utenti devono modificare o reimpostare la password, possono farlo anche se non hanno registrato in precedenza le informazioni di contatto.

È possibile pre-popolare le informazioni di contatto per l'autenticazione se si soddisfano i requisiti seguenti:

* I dati sono stati formattati correttamente nella directory locale.
* Sono stati configurati [Azure ad Connect](../hybrid/how-to-connect-install-express.md) per il tenant di Azure ad.

I numeri di telefono devono essere nel formato *+ CountryCode PhoneNumber*, ad esempio *+ 1 4251234567*.

> [!NOTE]
> Deve esserci uno spazio tra il codice paese e il numero di telefono.
>
> La reimpostazione della password non supporta le estensioni del telefono. Anche nel formato *+1 4251234567X12345*, le estensioni vengono rimosse prima della chiamata.

## <a name="fields-populated"></a>Campi popolati

Se si usano le impostazioni predefinite in Azure AD Connect, vengono eseguiti i mapping seguenti per popolare le informazioni di contatto per l'autenticazione per SSPR:

| Active Directory locale | Azure AD     |
|------------------------------|--------------|
| telephoneNumber              | Telefono ufficio |
| mobile                       | Cellulare |

Quando un utente verifica il numero di telefono cellulare, il campo *telefonico* in **informazioni di contatto per l'autenticazione** in Azure ad viene popolato anche con tale numero.

## <a name="authentication-contact-info"></a>Informazioni di contatto per l'autenticazione

Nella pagina **metodi di autenticazione** per un utente Azure AD nel portale di Azure, un amministratore globale può impostare manualmente le informazioni di contatto per l'autenticazione, come illustrato nella schermata di esempio seguente:

![Informazioni di contatto per l'autenticazione di un utente in Azure AD][Contact]

Le considerazioni seguenti riguardano le informazioni di contatto per l'autenticazione:

* Se il campo *telefono* è popolato e il *telefono cellulare* è abilitato nel criterio SSPR, l'utente visualizza tale numero nella pagina di registrazione per la reimpostazione della password e durante il flusso di lavoro di reimpostazione della password.
* Il campo *telefono alternativo* non viene usato per la reimpostazione della password.
* Se il campo *posta elettronica* è popolato e il *messaggio di posta elettronica* è abilitato nel criterio SSPR, l'utente visualizza tale messaggio nella pagina di registrazione della reimpostazione della password e durante il flusso di lavoro di reimpostazione della password.
* Se il campo *indirizzo di posta elettronica alternativo* è popolato e il *messaggio di posta elettronica* è abilitato nel criterio SSPR, l'utente non visualizzerà il messaggio di posta elettronica nella pagina di registrazione per la reimpostazione della password, ma lo visualizzerà durante il flusso di lavoro

## <a name="security-questions-and-answers"></a>Domande di sicurezza e risposte

Le domande di sicurezza e le risposte sono archiviate in modo sicuro nel tenant di Azure AD e sono accessibili agli utenti esclusivamente tramite il [portale di registrazione SSPR](https://aka.ms/ssprsetup). Gli amministratori non possono vedere, impostare o modificare il contenuto di domande e risposte di altri utenti.

## <a name="what-happens-when-a-user-registers"></a>Cosa accade quando un utente si registra

Quando un utente si registra, i campi seguenti vengono impostati nella pagina di registrazione:

* **Telefono per l'autenticazione**
* **Indirizzo di posta elettronica per l'autenticazione**
* **Domande di sicurezza e risposte**

Se è stato specificato un valore *per cellulare* o *indirizzo di posta elettronica alternativo*, gli utenti possono usare immediatamente questi valori per reimpostare le password, anche se non hanno eseguito la registrazione per il servizio.

Gli utenti visualizzano anche questi valori quando si registrano per la prima volta e possono modificarli se desiderano. Dopo la corretta registrazione, questi valori vengono salvati in permanenza rispettivamente nei campi telefono per l' *autenticazione* e *indirizzo di posta elettronica* per l'autenticazione.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>Impostare e leggere i dati di autenticazione tramite PowerShell

I campi seguenti possono essere impostati tramite PowerShell:

* *Indirizzo di posta elettronica alternativo*
* *Cellulare*
* *Telefono ufficio*
    * Può essere impostato solo se non si esegue la sincronizzazione con una directory locale.

> [!IMPORTANT]
> Nelle funzionalità del comando tra PowerShell V1 e PowerShell V2 esiste una mancanza di parità Nota. L' [API REST di Microsoft Graph (beta) per i metodi di autenticazione](/graph/api/resources/authenticationmethods-overview) è lo stato di progettazione corrente per offrire interazione moderna.

### <a name="use-powershell-version-1"></a>Usare PowerShell versione 1

Per iniziare, [scaricare e installare il modulo Azure ad PowerShell](/previous-versions/azure/jj151815(v=azure.100)#bkmk_installmodule). Al termine dell'installazione, attenersi alla procedura seguente per configurare ogni campo.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>Impostare i dati di autenticazione con PowerShell versione 1

```PowerShell
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 4251234567"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 4252345678"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 4251234567" -PhoneNumber "+1 4252345678"
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

Per iniziare, [scaricare e installare il modulo di PowerShell Azure ad versione 2](/powershell/module/azuread/?view=azureadps-2.0).

Per eseguire rapidamente l'installazione da versioni recenti di PowerShell che supportano `Install-Module` , eseguire i comandi seguenti. La prima riga verifica se il modulo è già installato:

```PowerShell
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

Dopo aver installato il modulo, attenersi alla procedura seguente per configurare ogni campo.

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>Impostare i dati di autenticazione con PowerShell versione 2

```PowerShell
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 4251234567"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 4252345678"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 4251234567" -TelephoneNumber "+1 4252345678"
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

Una volta che le informazioni di contatto per l'autenticazione sono già popolate per gli utenti, completare l'esercitazione seguente per abilitare la reimpostazione self-service della password:

> [!div class="nextstepaction"]
> [Abilitare la reimpostazione della password self-service di Azure AD](tutorial-enable-sspr.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "Gli amministratori globali possono modificare informazioni di contatto per l'autenticazione di un utente"
