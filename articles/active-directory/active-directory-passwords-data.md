---
title: Requisiti dei dati di Azure AD SSPR | Documentazione Microsoft
description: Requisiti dei dati per la Reimpostazione self-service delle password e informazioni su come soddisfarli
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: f7ecb22ee46d83867453e035f8a639bc7f7f2d81
ms.contentlocale: it-it
ms.lasthandoff: 05/08/2017


---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>Distribuire la reimpostazione della password senza richiedere la registrazione dell'utente finale

La distribuzione della Reimpostazione self-service delle password (SSPR) richiede che siano presenti i dati di autenticazione. Alcune organizzazioni fanno immettere autonomamente agli utenti i dati di autenticazione, ma molte organizzazioni preferiscono sincronizzarsi con i dati esistenti in Active Directory. Se i dati sono stati formattati correttamente nella directory locale e si configura [Azure AD Connect tramite le impostazioni rapide](./connect/active-directory-aadconnect-get-started-express.md), quei dati vengono resi disponibili in Azure AD e SSPR senza che si richieda l'intervento dell'utente.

Tutti i numeri di telefono devono essere nel formato +CountryCode PhoneNumber Esempio: + 1 4255551234 per funzionare correttamente.

> [!NOTE]
> La reimpostazione della password non supporta le estensioni del telefono. Anche nel formato +1 4255551234X12345, le estensioni vengono rimosse prima della chiamata.

## <a name="fields-populated"></a>Campi popolati

Se si usano le impostazioni predefinite in Azure AD Connect, vengono eseguiti i mapping seguenti.

| Active Directory locale | Azure AD | Informazioni di contatto per l'autenticazione di Azure AD |
| --- | --- | --- |
| telephoneNumber | Telefono ufficio | Telefono alternativo |
| mobile | Cellulare | Telefono |


## <a name="security-questions-and-answers"></a>Domande di sicurezza e risposte

Domande di sicurezza e risposte sono archiviate in modo sicuro nel tenant di Azure AD e sono accessibili agli utenti esclusivamente tramite il [portale di registrazione SSPR](https://aka.ms/ssprsetup). Gli amministratori non possono vedere o modificare il contenuto di domande e risposte di altri utenti.

### <a name="what-happens-when-a-user-registers"></a>Cosa accade quando un utente si registra

Quando un utente si registra, i campi seguenti vengono impostati nella pagina di registrazione:

* Telefono per l'autenticazione
* Indirizzo di posta elettronica per l'autenticazione
* Domande di sicurezza e risposte

Se è stato specificato un valore per **Cellulare** o **Indirizzo di posta elettronica alternativo**, gli utenti possono usare immediatamente questi valori per reimpostare le password, anche se non hanno eseguito la registrazione per il servizio. Gli utenti visualizzano e possono anche modificare tali valori quando si registrano per la prima volta. Dopo aver completato la registrazione, questi valori vengono salvati in modo permanente rispettivamente nei campi **Telefono per l'autenticazione** e **Indirizzo di posta elettronica per l'autenticazione**.

## <a name="set-and-read-authentication-data-using-powershell"></a>Impostare e leggere i dati di autenticazione tramite PowerShell

I campi seguenti possono essere impostati tramite PowerShell

* Indirizzo di posta elettronica alternativo
* Cellulare
* Telefono ufficio - può essere impostato solo se non in sincronizzazione con una directory locale

### <a name="using-powershell-v1"></a>Tramite PowerShell V1

Per iniziare, è necessario [scaricare e installare il modulo di Azure AD PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule). Al termine dell'installazione, è possibile seguire la procedura seguente per configurare tutti i campi.

#### <a name="set-authentication-data-with-powershell-v1"></a>Impostare i Dati di autenticazione con PowerShell V1

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-authentication-data-with-powershellpowershell-v1"></a>Leggere i Dati di autenticazione con PowerShell V1

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="authentication-phone-and-authentication-email-can-only-be-read-using-powershell-v1-using-the-commands-that-follow"></a>L'Autenticazione tramite telefono e l'Autenticazione tramite posta elettronica possono essere lette solo tramite Powershell V1 usando i comandi che seguono

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="using-powershell-v2"></a>Tramite PowerShell V2

Per iniziare, è necessario [scaricare e installare il modulo di Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md). Al termine dell'installazione, è possibile seguire la procedura seguente per configurare tutti i campi.

Per eseguire rapidamente l'installazione da versioni recenti di PowerShell che supportano Install-Module, eseguire questi comandi (la prima riga verifica semplicemente se l'installazione è già stata eseguita):

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-authentication-data-with-powershell-v2"></a>Impostare i Dati di autenticazione con PowerShell V2

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

### <a name="read-authentication-data-with-powershell-v2"></a>Leggere i Dati di autenticazione con PowerShell V2

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>Passaggi successivi

I collegamenti seguenti forniscono altre informazioni sull'uso della reimpostazione della password con Azure AD

* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - Iniziare a usare la gestione self-service delle password di Azure AD 
* [**Licenze**](active-directory-passwords-licensing.md) - configurare le licenze di Azure AD
* [**Implementazione**](active-directory-passwords-best-practices.md) - pianificare e distribuire agli utenti la reimpostazione password self-service usando le istruzioni disponibili in questo articolo
* [**Personalizzazione**](active-directory-passwords-customize.md) - personalizzare l'aspetto dell'esperienza della reimpostazione password self-service per l'azienda.
* [**Criteri**](active-directory-passwords-policy.md) - comprendere e impostare i criteri password di Azure AD
* [**Creazione di report**](active-directory-passwords-reporting.md) - verificare se, quando e dove gli utenti accedono alla reimpostazione password self-service
* [**Approfondimento tecnico**](active-directory-passwords-how-it-works.md): approfondimento sul funzionamento
* [**Domande frequenti**](active-directory-passwords-faq.md) - Come Perché? Cosa? Dove? Chi? Quando? - Risposte alle domande di maggiore interesse
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md): informazioni su come risolvere i problemi comuni con la reimpostazione password self-service

