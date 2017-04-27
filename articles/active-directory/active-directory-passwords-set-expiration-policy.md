---
title: Impostare i criteri di scadenza della password in Azure Active Directory | Microsoft Docs
description: Informazioni su come controllare i criteri di scadenza e modificare la password utente scaduta singolarmente o in blocco per le password di Azure Active directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 6887250c-15d4-4b59-a161-f0380c0f0acb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 4bab9f44d1c91f05618ea510b83beb06540429f2
ms.openlocfilehash: f744a29e12fb693dd422f359d0faacaae004351b
ms.lasthandoff: 02/14/2017


---
# <a name="set-password-expiration-policies-in-azure-active-directory"></a>Impostare i criteri di scadenza della password in Azure Active Directory
> [!IMPORTANT]
> **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).
>
>

In qualità di amministratore generale per un servizio cloud Microsoft, è possibile utilizzare il modulo della directory di Microsoft Azure Active per Windows PowerShell per impostare delle password utente che non scadano. È inoltre possibile utilizzare cmdlet Windows PowerShell per rimuovere la configurazione senza scadenza, o per vedere quali password utente vengono impostate senza scadenza. Questo articolo riporta informazioni di guida sui servizi cloud, quali Microsoft Intune e Office 365, che si basano su Microsoft Azure Active Directory per i servizi di identità e directory.

> [!NOTE]
> Solo le password degli account utente che non sono sincronizzate tramite sincronizzazione della directory possono essere configurate senza scadenza. Per altre informazioni sulla sincronizzazione della directory, vedere l'elenco di argomenti in [Roadmap sulla sincronizzazione delle directory](https://msdn.microsoft.com/library/azure/hh967642.aspx).
>
>

Per utilizzare i cmdlet di Windows PowerShell, è innanzitutto necessario installarli.

## <a name="what-do-you-want-to-do"></a>Come procedere
* [Come controllare i criteri di scadenza per una password](#how-to-check-expiration-policy-for-a-password)
* [Impostare una scadenza della password](#set-a-password-to-expire)
* [Impostare una password senza scadenza](#set-a-password-to-never-expire)

## <a name="how-to-check-expiration-policy-for-a-password"></a>Come controllare i criteri di scadenza per una password
1. Connettersi a Windows PowerShell utilizzando le credenziali aziendali di amministratore.
2. Eseguire una di queste operazioni:

   * Per vedere se la password di un singolo utente è impostata per non scadere mai, eseguire il cmdlet seguente usando il nome principale utente (UPN) (ad esempio, aprilr@contoso.onmicrosoft.com)) o l'ID utente dell'utente che si desidera controllare: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Per visualizzare l'impostazione "Nessuna scadenza per la Password" per tutti gli utenti, eseguire il cmdlet seguente: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## <a name="set-a-password-to-expire"></a>Impostare una scadenza della password
1. Connettersi a Windows PowerShell utilizzando le credenziali aziendali di amministratore.
2. Eseguire una di queste operazioni:

   * Per impostare la password di un utente in modo che la password scada, eseguire il cmdlet seguente usando il nome principale utente (UPN) o l'ID utente dell'utente: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Per impostare le password di tutti gli utenti dell'organizzazione in modo che scadano, utilizzare il cmdlet seguente: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

## <a name="set-a-password-to-never-expire"></a>Impostare una password senza scadenza
1. Connettersi a Windows PowerShell utilizzando le credenziali aziendali di amministratore.
2. Eseguire una di queste operazioni:

   * Per impostare la password di un utente senza scadenza, eseguire il cmdlet seguente usando il nome principale utente (UPN) o l'ID utente dell'utente: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Per impostare le password degli utenti in un'organizzazione in modo che non scadano mai, eseguire il cmdlet seguente: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

## <a name="next-steps"></a>Passaggi successivi
* **Se si sta visualizzando questa pagina perché si riscontrano problemi nell'accesso,** [seguire questa procedura per cambiare e reimpostare la password](active-directory-passwords-update-your-own-password.md).

