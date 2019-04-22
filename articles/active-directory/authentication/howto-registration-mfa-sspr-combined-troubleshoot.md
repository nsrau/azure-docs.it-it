---
title: Risolvere i problemi di registrazione combinata per multi-Factor Authentication (anteprima) - Azure Active Directory e Azure AD SSPR
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
ms.openlocfilehash: 40918493071fe0dd694c43e2b087a2bf7eb197d8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489194"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Risoluzione dei problemi di combinare la registrazione di informazioni di sicurezza (anteprima)

Le informazioni contenute in questo articolo sono progettate per guidare gli amministratori che sono i problemi segnalati dagli utenti dell'esperienza di registrazione combinato.

|     |
| --- |
| Registrazione di informazioni di sicurezza combinato per Azure multi-Factor Authentication e reimpostazione della password self-service di Azure Active Directory (Azure AD) è una funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Log di controllo

Gli eventi registrati per la registrazione combinata appartengono alla categoria di metodi di autenticazione in Azure Active Directory log di controllo.

![Interfaccia con la registrazione eventi dei log di controllo di AD Azure](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

La tabella seguente elenca tutti gli eventi di controllo generati dalla registrazione combinata:

| Attività | Stato | Motivo | DESCRIZIONE |
| --- | --- | --- | --- |
| L'utente ha registrato tutte le info di sicurezza necessari | Success | Utente ha registrato tutte le info di sicurezza necessarie. | Questo evento si verifica quando un utente ha completato la registrazione.|
| L'utente ha registrato tutte le info di sicurezza necessari | Esito negativo | L'utente ha annullato la registrazione di info di sicurezza. | Questo evento si verifica quando un utente annulla la registrazione dalla modalità di interrupt.|
| Info di sicurezza registrate dall'utente | Success | Utente registrato *metodo*. | Questo evento si verifica quando un utente si registra un singolo metodo. *Metodo* possono essere Authenticator app, telefono, posta elettronica, protezione domande, App password, telefono alternativo e così via.| 
| Info di sicurezza utente rivisto | Success | Utente è stata revisionata info di sicurezza. | Questo evento si verifica quando un utente sceglie **sembra corretto** nella pagina di verifica info di sicurezza.|
| Info di sicurezza utente rivisto | Esito negativo | Utente non è riuscito a verificare le info di sicurezza. | Questo evento si verifica quando un utente sceglie **sembra corretto** sulle info di sicurezza esaminare pagina ma qualcosa non funziona al back-end.|
| Info di sicurezza utente eliminato | Success | Utente eliminato *metodo*. | Questo evento si verifica quando un utente elimina un singolo metodo. *Metodo* possono essere Authenticator app, telefono, posta elettronica, protezione domande, App password, telefono alternativo e così via.|
| Info di sicurezza utente eliminato | Esito negativo | Utente non è riuscito a eliminare *metodo*. | Questo evento si verifica quando un utente tenta di eliminare un metodo, ma il tentativo non riesce per qualche motivo. *Metodo* possono essere Authenticator app, telefono, posta elettronica, protezione domande, App password, telefono alternativo e così via.|
| Info di sicurezza predefinito utente modificata | Success | L'utente ha modificato le info di sicurezza predefinito per *metodo*. | Questo evento si verifica quando un utente modifica il metodo predefinito. *Metodo* possono essere notifica dell'app Authenticator, un codice dalla mia app authenticator o il token, chiamata + X XXXXXXXXXX, testo, un codice a + X XXXXXXXXX e così via.|
| Info di sicurezza predefinito utente modificata | Esito negativo | Utente non è stato possibile modificare le info di sicurezza predefinito per *metodo*. | Questo evento si verifica quando un utente tenta di modificare il metodo predefinito, ma il tentativo non riesce per qualche motivo. *Metodo* possono essere notifica dell'app Authenticator, un codice dalla mia app authenticator o il token, chiamata + X XXXXXXXXXX, testo, un codice a + X XXXXXXXXX e così via.|

## <a name="troubleshooting-interrupt-mode"></a>Risoluzione dei problemi di modalità di interrupt

| Sintomo | Passaggi per la risoluzione dei problemi |
| --- | --- |
| Non vengono visualizzati i metodi che prevede venga visualizzato. | 1. Controllare se l'utente ha un ruolo di amministratore di Azure AD. In caso affermativo, visualizzare le differenze di criteri SSPR admin. <br> 2. Determinare se l'utente è stata interrotta a causa di imposizione di registrazione multi-Factor Authentication o l'esecuzione di registrazione SSPR. Vedere le [diagramma di flusso](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) in "Modalità di registrazione Combined" per determinare quali metodi devono essere visualizzati. <br> 3. Determinare la modalità di recente è stato modificato i criteri di reimpostazione o multi-Factor Authentication. Se la modifica è recente, potrebbe richiedere tempo per il criterio aggiornato per la propagazione.|

## <a name="troubleshooting-manage-mode"></a>Risoluzione dei problemi di gestione di mode

| Sintomo | Passaggi per la risoluzione dei problemi |
| --- | --- |
| Non ho la possibilità di aggiungere un metodo specifico. | 1. Determinare se il metodo è abilitato per multi-Factor Authentication o per SSPR. <br> 2. Se è abilitato il metodo, salvare di nuovo i criteri e attendere 1-2 ore prima di ripetere la verifica. <br> 3. Se il metodo è abilitato, verificare che l'utente non è già stato configurato il numero massimo di sono autorizzati a configurare il metodo.|

## <a name="disable-combined-registration"></a>Disabilitare la registrazione combinata

Quando un utente si registra un numero di telefono e/o app per dispositivi mobili nella nuova esperienza, i moduli servizio combinati un set di flag (StrongAuthenticationMethods) per tali metodi in quell'utente. Questa funzionalità consente all'utente di eseguire multi-Factor Authentication con tali metodi ogni volta che è necessaria l'autenticazione a più fattori.

Se un amministratore Abilita l'anteprima, gli utenti registrano tramite la nuova esperienza e quindi l'amministratore disabilita l'anteprima, gli utenti potrebbero inconsapevolmente essere registrati per multi-Factor Authentication anche.

Se un utente che ha effettuato la registrazione combinata passa alla pagina di registrazione reimpostazione password self-service corrente in [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup), l'utente verrà richiesto di eseguire l'autenticazione a più fattori prima di poter accedere tale pagina. Questo passaggio è previsto da un punto di vista tecnico, ma è nuova per gli utenti che sono stati precedentemente registrati solo per SSPR. Anche se questo passaggio aggiuntivo per migliorare le condizioni di sicurezza dell'utente fornendo un ulteriore livello di sicurezza, gli amministratori potrebbero voler eseguire il rollback agli utenti in modo che non sono più in grado di eseguire multi-Factor Authentication.  

### <a name="how-to-roll-back-users"></a>Come eseguire il rollback per gli utenti

Eventualmente, l'amministratore di reimpostare le impostazioni di multi-Factor Authentication dell'utente, è possibile usare lo script di PowerShell fornito nella sezione successiva. Lo script cancellerà la proprietà StrongAuthenticationMethods per app per dispositivi mobili dell'utente e/o numero di telefono. Se si esegue questo script per gli utenti, è necessario registrare nuovamente per multi-Factor Authentication se ne hanno bisogno. È consigliabile eseguire il rollback test con uno o due utenti prima di eseguire il rollback di tutti gli utenti interessati.

La procedura seguente consente di ripristinare un utente o gruppo di utenti.

#### <a name="prerequisites"></a>Prerequisiti

1. Installare i moduli di Azure AD PowerShell appropriati. In una finestra di PowerShell eseguire questi comandi per installare i moduli:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Salvare l'elenco di ID oggetto utente interessato al computer come un file di testo con un ID per ogni riga. Prendere nota del percorso del file.
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

In una finestra di PowerShell, eseguire il comando seguente, che fornisce i percorsi dei file script e utente. Immettere le credenziali di amministratore globale, quando richieste. Lo script genererà il risultato dell'operazione di aggiornamento di ogni utente.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Disabilitare l'esperienza di anteprima

Per disabilitare l'esperienza di anteprima per gli utenti, completare questi passaggi:

1. Accedere al portale di Azure come un utente come amministratore.
2. Passare a **Azure Active Directory** > **impostazioni utente** > **gestire le impostazioni per le funzionalità di anteprima di Pannello di accesso**.
3. Sotto **gli utenti possono usare le funzionalità di anteprima per la registrazione e la gestione delle tue info di sicurezza**, impostare il selettore **None**e quindi selezionare **Salva**.

Gli utenti non verranno non è più richiesto di registrare con l'esperienza di anteprima.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sull'anteprima pubblica di registrazione combinata per la reimpostazione password self-service e Azure multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
