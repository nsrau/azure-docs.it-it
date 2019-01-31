---
title: Disabilitare la registrazione convergente per la reimpostazione della password self-service e MFA di Azure AD (anteprima pubblica)
description: Disabilitare la registrazione per Multi-Factor Authentication e la reimpostazione della password self-service di Azure AD (anteprima pubblica)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: c7cdd2e62c76011f2ff4ada33353b85dcf564ef5
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076656"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>Disabilitare la registrazione convergente di Azure AD (anteprima pubblica)

Quando un utente registra il numero di telefono e/o l'app per dispositivi mobili nella nuova esperienza convergente, il servizio imposta per l'utente un set di flag (StrongAuthenticationMethods) relativi ai metodi usati. Questa funzionalità consente all'utente di eseguire Azure Multi-Factor Authentication (MFA) con tali metodi ogni volta che è richiesta l'autenticazione a più fattori.

Nei metodi adottati dagli utenti per la registrazione tramite la nuova esperienza è impostata la proprietà StrongAuthenticationMethods. Questo comportamento rimarrà invariato anche quando sarà disponibile l'anteprima pubblica. Se un amministratore abilita l'anteprima, gli utenti eseguono la registrazione tramite la nuova esperienza. Quando poi l'amministratore disabilita l'anteprima, gli utenti potrebbero essere inconsapevolmente registrati anche per MFA.

Se un utente che ha completato la registrazione convergente passa alla pagina di registrazione corrente per la reimpostazione della password self-service, in [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup), dovrà eseguire MFA prima di accedere a tale pagina. Ciò costituisce un comportamento prevedibile da un punto di vista tecnico, ma per gli utenti che in precedenza erano registrati solo per la reimpostazione della password self-service, si tratta di un comportamento nuovo. Anche se questo passaggio migliora le condizioni di sicurezza dell'utente offrendo un livello di sicurezza aggiuntivo, gli amministratori potrebbero ritenere opportuno eseguire il rollback del comportamento precedente impedendo agli utenti di eseguire MFA.  

## <a name="how-to-roll-back-users"></a>Come eseguire il rollback per gli utenti

Se come amministratore si vogliono ripristinare le impostazioni di un utente relative a MFA, è ora disponibile uno script di PowerShell che cancella la proprietà StrongAuthenticationMethods per il numero di telefono e/o l'app per dispositivi mobili dell'utente. Se si esegue questo script per gli utenti, questi dovranno registrarsi nuovamente per MFA, se necessario. È consigliabile testare l'operazione di rollback per uno o due utenti prima di applicarla a tutti gli utenti interessati.

I passaggi seguenti consentono di eseguire il rollback per un utente o un gruppo di utenti:

### <a name="pre-requisites"></a>Prerequisiti

1. È necessario installare i moduli di Azure AD per PowerShell appropriati. In una finestra di PowerShell eseguire questi comandi per installare i moduli:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Salvare l'elenco di ID degli oggetti utente interessati nel computer come file di testo con un ID per ogni riga. Prendere nota del percorso del file.
1. Salvare lo script seguente nel computer e prendere nota del percorso dello script:

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

### <a name="rollback"></a>Rollback

In una finestra di PowerShell eseguire il comando seguente dopo aver aggiornato i percorsi evidenziati. Immettere le credenziali di amministratore globale, quando richieste. Lo script genererà il risultato dell'operazione di aggiornamento di ogni utente.

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>Disabilitare l'esperienza di anteprima

Per disabilitare l'esperienza di anteprima per gli utenti, completare i passaggi seguenti:

1. Accedere al portale di Azure come amministratore globale o amministratore utenti.
2. Passare ad **Azure Active Directory**, **Impostazioni utente**, **Gestisci le impostazioni per le funzionalità in anteprima del pannello di accesso**.
3. In **Gli utenti possono usare le funzionalità in anteprima per la registrazione e la gestione delle informazioni di sicurezza** impostare il selettore su **Nessuno** e fare clic su **Salva**.

Agli utenti non verrà più chiesto di eseguire la registrazione usando l'esperienza di anteprima.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sull'anteprima pubblica della registrazione convergente per la reimpostazione della password self-service e Azure Multi-Factor Authentication](concept-registration-mfa-sspr-converged.md)
