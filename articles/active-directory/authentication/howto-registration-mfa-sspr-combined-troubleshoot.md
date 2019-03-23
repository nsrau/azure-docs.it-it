---
title: Risolvere i problemi di registrazione combinata per MFA (anteprima) - Azure Active Directory e Azure AD SSPR
description: Risolvere i problemi di Azure AD multi-Factor Authentication e la registrazione di combinare la reimpostazione della password self-service (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d926f7312b62e788289939dfd81c236a33503b43
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370466"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Risoluzione dei problemi di combinare la registrazione di informazioni di sicurezza (anteprima)

Le informazioni fornite in questo articolo possono essere utili agli amministratori che sono i problemi con l'esperienza di registrazione combinata segnalato da utenti finali.

|     |
| --- |
| Registrazione di informazioni di sicurezza combinato per la reimpostazione password self-service di Azure multi-Factor Authentication e Azure AD è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle funzioni in anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Log di controllo

Gli eventi registrati per la registrazione combinata sono inclusi nella categoria "Metodi di autenticazione" in Azure Active Directory log di controllo.

![Interfaccia con la registrazione eventi dei log di controllo di AD Azure](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

Di seguito sono elencati tutti gli eventi di controllo generati dalla registrazione combinata:

| Attività | Stato | Motivo | DESCRIZIONE |
| --- | --- | --- | --- |
| L'utente ha registrato tutte le info di sicurezza necessari | Success | Utente ha registrato tutte le info di sicurezza necessarie. | Questo evento si verifica quando un utente ha completato la registrazione.|
| L'utente ha registrato tutte le info di sicurezza necessari | Esito negativo | L'utente ha annullato la registrazione di info di sicurezza. | Questo evento si verifica quando un utente annulla la registrazione dalla modalità di interrupt.|
| Info di sicurezza registrate dall'utente | Success | L'utente è registrato "method". | Questo evento si verifica quando un utente si registra un singolo metodo. "Method" possono essere app Authenticator, telefono, posta elettronica, domande di sicurezza, password dell'App, telefono alternativo, e così via.| 
| Info di sicurezza utente rivisto | Success | Utente è stata revisionata info di sicurezza. | Questo evento si verifica quando un utente fa clic su "Esito positivo" nella pagina di verifica info di sicurezza.|
| Info di sicurezza utente rivisto | Esito negativo | Utente non è riuscito a verificare le info di sicurezza. | Questo evento si verifica quando un utente fa clic su "Sono corrette" sulle info di sicurezza pagina revisione ma qualcosa non funziona nel back-end.|
| Info di sicurezza utente eliminato | Success | Eliminare l'utente "method". | Questo evento si verifica quando un utente elimina un singolo metodo. "Method" possono essere app Authenticator, telefono, posta elettronica, domande di sicurezza, password dell'App, telefono alternativo, e così via.|
| Info di sicurezza utente eliminato | Esito negativo | Utente non è stato possibile eliminare "method". | Questo evento si verifica quando un utente tenta di eliminare un metodo, ma per qualche motivo ha esito negativo. "Method" possono essere app Authenticator, telefono, posta elettronica, domande di sicurezza, password dell'App, telefono alternativo, e così via.|
| Info di sicurezza predefinito utente modificata | Success | Info di sicurezza predefinito modificato in "method". | Questo evento si verifica quando un utente modifica il metodo predefinito. "Method" può essere notifica dell'app Authenticator, dalla mia app authenticator o il token, chiamata + X XXXXXXXXXX, testo di codice un codice a + X XXXXXXXXX e così via.|
| Info di sicurezza predefinito utente modificata | Esito negativo | Utente non è riuscito a modificare info di sicurezza predefinito a "method". | Questo evento si verifica quando un utente tenta di modificare il metodo predefinito, ma per qualche motivo ha esito negativo. Notifica dell'app Authenticator, un codice dalla mia app authenticator o il token, chiamata + X XXXXXXXXXX, testo, un codice a + X XXXXXXXXX e così via, può essere "Method".|

## <a name="troubleshooting-interrupt-mode"></a>Risoluzione dei problemi di modalità di interrupt

| Sintomo | Passaggi per la risoluzione dei problemi |
| --- | --- |
| Non vengono visualizzati i metodi che prevede venga visualizzato. | 1. Controllare se l'utente ha un ruolo di amministratore di Azure AD. In caso affermativo, esaminare le differenze di criteri SSPR amministratore. <br> 2. Determinare se l'utente è stata interrotta a causa dell'applicazione di registrazione MFA o all'applicazione di registrazione SSPR. Esaminare il diagramma di flusso in modalità di registrazione combinati per determinare quali metodi devono essere visualizzati. <br> 3. Determinare la modalità di recente è stato modificato i criteri di autenticazione a più fattori o SSPR. Se la modifica è recente, potrebbe richiedere del tempo per il criterio aggiornato per la propagazione.|

## <a name="troubleshooting-manage-mode"></a>Risoluzione dei problemi di gestione di mode

| Sintomo | Passaggi per la risoluzione dei problemi |
| --- | --- |
| Non ho la possibilità di aggiungere un metodo specifico. | 1. Determinare se il metodo è abilitato per l'autenticazione a più fattori o per SSPR. <br> 2. Se è abilitato il metodo, salvare di nuovo i criteri e attendere 1-2 ore prima di ripetere la verifica. <br> 3. Se il metodo è abilitato, verificare che l'utente non è già stato configurato il numero massimo di sono autorizzati a configurare il metodo.|

## <a name="disable-combined-registration"></a>Disabilitare la registrazione combinata

Quando un utente si registra il numero di telefono e/o app per dispositivi mobili nella nuova esperienza, i moduli servizio combinati un set di flag (StrongAuthenticationMethods) per tali metodi in quell'utente. Questa funzionalità consente all'utente di eseguire Azure Multi-Factor Authentication (MFA) con tali metodi ogni volta che è richiesta l'autenticazione a più fattori.

Nei metodi adottati dagli utenti per la registrazione tramite la nuova esperienza è impostata la proprietà StrongAuthenticationMethods. Se un amministratore abilita l'anteprima, gli utenti eseguono la registrazione tramite la nuova esperienza. Quando poi l'amministratore disabilita l'anteprima, gli utenti potrebbero essere inconsapevolmente registrati anche per MFA.

Se un utente che è stata completata combinata registrazione consente di passare alla pagina di registrazione di reimpostazione della password self-service corrente, in [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), verrà richiesto di eseguire l'autenticazione a più fattori prima di accedere a tale pagina. Ciò costituisce un comportamento prevedibile da un punto di vista tecnico, ma per gli utenti che in precedenza erano registrati solo per la reimpostazione della password self-service, si tratta di un comportamento nuovo. Anche se questo passaggio migliora le condizioni di sicurezza dell'utente offrendo un livello di sicurezza aggiuntivo, gli amministratori potrebbero ritenere opportuno eseguire il rollback del comportamento precedente impedendo agli utenti di eseguire MFA.  

### <a name="how-to-roll-back-users"></a>Come eseguire il rollback per gli utenti

Se come amministratore si vogliono ripristinare le impostazioni di un utente relative a MFA, è ora disponibile uno script di PowerShell che cancella la proprietà StrongAuthenticationMethods per il numero di telefono e/o l'app per dispositivi mobili dell'utente. Se si esegue questo script per gli utenti, questi dovranno registrarsi nuovamente per MFA, se necessario. È consigliabile testare l'operazione di rollback per uno o due utenti prima di applicarla a tutti gli utenti interessati.

I passaggi seguenti consentono di eseguire il rollback per un utente o un gruppo di utenti:

#### <a name="prerequisites"></a>Prerequisiti

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

#### <a name="rollback"></a>Rollback

In una finestra di PowerShell eseguire il comando seguente dopo aver aggiornato i percorsi evidenziati. Immettere le credenziali di amministratore globale, quando richieste. Lo script genererà il risultato dell'operazione di aggiornamento di ogni utente.

`<script location> -path <user file location>`

### <a name="disable-preview-experience"></a>Disabilitare l'esperienza di anteprima

Per disabilitare l'esperienza di anteprima per gli utenti, completare i passaggi seguenti:

1. Accedere al portale di Azure come amministratore globale o amministratore utenti.
2. Passare ad **Azure Active Directory**, **Impostazioni utente**, **Gestisci le impostazioni per le funzionalità in anteprima del pannello di accesso**.
3. In **Gli utenti possono usare le funzionalità in anteprima per la registrazione e la gestione delle informazioni di sicurezza** impostare il selettore su **Nessuno** e fare clic su **Salva**.

Agli utenti non verrà più chiesto di eseguire la registrazione usando l'esperienza di anteprima.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sull'anteprima pubblica di registrazione combinata per la reimpostazione password self-service e Azure multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
