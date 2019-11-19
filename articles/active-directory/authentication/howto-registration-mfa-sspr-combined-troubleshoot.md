---
title: Risolvere i problemi di registrazione combinata per Azure AD SSPR e Multi-Factor Authentication (anteprima)-Azure Active Directory
description: Risolvere i problemi di Azure AD Multi-Factor Authentication e la registrazione combinata della reimpostazione della password self-service (anteprima)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e586105d8b2ec85e4ebd85046185ddc21112f0e0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167816"
---
# <a name="troubleshooting-combined-security-information-registration-preview"></a>Risoluzione dei problemi relativi alla registrazione di informazioni di sicurezza combinate (anteprima)

Le informazioni contenute in questo articolo sono destinate agli amministratori che eseguono la risoluzione dei problemi segnalati dagli utenti dell'esperienza di registrazione combinata.

|     |
| --- |
| La registrazione delle informazioni di sicurezza combinate per la reimpostazione della password self-service di Azure Multi-Factor Authentication e Azure Active Directory (Azure AD) è una funzionalità di anteprima pubblica di Azure AD. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="audit-logs"></a>Log di controllo

Gli eventi registrati per la registrazione combinata si trovano nella categoria metodi di autenticazione del Azure AD log di controllo.

![Azure AD interfaccia dei log di controllo che Mostra gli eventi di registrazione](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

La tabella seguente elenca tutti gli eventi di controllo generati dalla registrazione combinata:

| Attività | Stato | Motivo | DESCRIZIONE |
| --- | --- | --- | --- |
| L'utente ha registrato tutte le informazioni di sicurezza necessarie | Operazione completata | L'utente ha registrato tutte le informazioni di sicurezza necessarie. | Questo evento si verifica quando un utente ha completato correttamente la registrazione.|
| L'utente ha registrato tutte le informazioni di sicurezza necessarie | Esito negativo | Registrazione delle informazioni di sicurezza annullata dall'utente. | Questo evento si verifica quando un utente annulla la registrazione dalla modalità di interruzione.|
| Info di sicurezza registrate dall'utente | Operazione completata | *Metodo*registrato dall'utente. | Questo evento si verifica quando un utente registra un singolo metodo. Il *Metodo* può essere l'app di autenticazione, il telefono, l'indirizzo di posta elettronica, le domande di sicurezza, la password dell'app, il telefono alternativo e così via.| 
| Informazioni di sicurezza rivedute dall'utente | Operazione completata | L'utente ha rivisto le informazioni di sicurezza. | Questo evento si verifica quando un utente seleziona l' **aspetto corretto** nella pagina di verifica delle informazioni di sicurezza.|
| Informazioni di sicurezza rivedute dall'utente | Esito negativo | L'utente non è riuscito a esaminare le informazioni di sicurezza. | Questo evento si verifica quando un utente seleziona l' **aspetto corretto** nella pagina di verifica delle informazioni di sicurezza, ma si verifica un errore nel back-end.|
| Informazioni di sicurezza eliminate dall'utente | Operazione completata | *Metodo*eliminato dall'utente. | Questo evento si verifica quando un utente elimina un singolo metodo. Il *Metodo* può essere l'app di autenticazione, il telefono, l'indirizzo di posta elettronica, le domande di sicurezza, la password dell'app, il telefono alternativo e così via.|
| Informazioni di sicurezza eliminate dall'utente | Esito negativo | L'utente non è riuscito a eliminare il *Metodo*. | Questo evento si verifica quando un utente tenta di eliminare un metodo, ma il tentativo non riesce per qualche motivo. Il *Metodo* può essere l'app di autenticazione, il telefono, l'indirizzo di posta elettronica, le domande di sicurezza, la password dell'app, il telefono alternativo e così via.|
| Informazioni di sicurezza predefinite modificate dall'utente | Operazione completata | L'utente ha modificato le informazioni di sicurezza predefinite per il *Metodo*. | Questo evento si verifica quando un utente modifica il metodo predefinito. Il *Metodo* può essere una notifica dell'app Authenticator, un codice dell'app o del token di autenticazione, chiamare + x Xxxxxxxxxx, scrivere un codice per + x xxxxxxxxx e così via.|
| Informazioni di sicurezza predefinite modificate dall'utente | Esito negativo | L'utente non è riuscito a modificare le informazioni di sicurezza predefinite per il *Metodo*. | Questo evento si verifica quando un utente tenta di modificare il metodo predefinito, ma il tentativo non riesce per qualche motivo. Il *Metodo* può essere una notifica dell'app Authenticator, un codice dell'app o del token di autenticazione, chiamare + x Xxxxxxxxxx, scrivere un codice per + x xxxxxxxxx e così via.|

## <a name="troubleshooting-interrupt-mode"></a>Risoluzione dei problemi relativi alla modalità di interrupt

| Sintomo | Passaggi per la risoluzione dei problemi |
| --- | --- |
| Non vengono visualizzati i metodi previsti. | 1. verificare se l'utente dispone di un ruolo di amministratore Azure AD. In caso affermativo, visualizzare le differenze dei criteri di amministratore di SSPR. <br> 2. determinare se l'utente è in fase di interruzione a causa di Multi-Factor Authentication imposizione di registrazione o di SSPR. Per determinare i metodi da visualizzare, vedere il [diagramma di flusso](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) in "modalità di registrazione combinate". <br> 3. determinare il modo in cui è stato modificato il criterio Multi-Factor Authentication o SSPR. Se la modifica è recente, la propagazione dei criteri aggiornati potrebbe richiedere del tempo.|

## <a name="troubleshooting-manage-mode"></a>Risoluzione dei problemi relativi alla modalità di gestione

| Sintomo | Passaggi per la risoluzione dei problemi |
| --- | --- |
| Non è possibile aggiungere un particolare metodo. | 1. determinare se il metodo è abilitato per Multi-Factor Authentication o per SSPR. <br> 2. se il metodo è abilitato, salvare di nuovo i criteri e attendere 1-2 ore prima di eseguire di nuovo il test. <br> 3. se il metodo è abilitato, assicurarsi che l'utente non abbia già configurato il numero massimo di tale metodo che è autorizzato a configurare.|

## <a name="disable-combined-registration"></a>Disabilitare la registrazione combinata

Quando un utente registra un numero di telefono e/o un'app per dispositivi mobili nella nuova esperienza combinata, il servizio timbra un set di flag (StrongAuthenticationMethods) per tali metodi su tale utente. Questa funzionalità consente all'utente di eseguire Multi-Factor Authentication con tali metodi ogni volta che è necessario Multi-Factor Authentication.

Se un amministratore abilita l'anteprima, gli utenti effettuano la registrazione tramite la nuova esperienza e quindi l'amministratore disabilita l'anteprima, gli utenti potrebbero essere registrati inconsapevolmente anche per Multi-Factor Authentication.

Se un utente che ha completato la registrazione combinata passa alla pagina di registrazione della reimpostazione della password self-service (SSPR) corrente in [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup), all'utente verrà richiesto di eseguire multi-factor authentication prima di poter accedere a tale pagina. Questo passaggio è previsto dal punto di vista tecnico, ma è una novità per gli utenti registrati in precedenza solo per SSPR. Sebbene questo passaggio aggiuntivo migliori il comportamento di sicurezza dell'utente fornendo un altro livello di sicurezza, gli amministratori potrebbero voler eseguire il rollback degli utenti in modo che non siano più in grado di eseguire Multi-Factor Authentication.  

### <a name="how-to-roll-back-users"></a>Come eseguire il rollback per gli utenti

Se come amministratore si vuole reimpostare le impostazioni Multi-Factor Authentication di un utente, è possibile usare lo script di PowerShell fornito nella sezione successiva. Lo script cancellerà la proprietà StrongAuthenticationMethods per l'app per dispositivi mobili e/o il numero di telefono di un utente. Se si esegue questo script per gli utenti, sarà necessario ripetere la registrazione per Multi-Factor Authentication, se necessario. Prima di eseguire il rollback di tutti gli utenti interessati, è consigliabile testare il rollback con uno o due utenti.

I passaggi seguenti consentono di eseguire il rollback di un utente o di un gruppo di utenti.

#### <a name="prerequisites"></a>prerequisiti

1. Installare i moduli di Azure AD PowerShell appropriati. In una finestra di PowerShell eseguire questi comandi per installare i moduli:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Salvare l'elenco degli ID oggetto utente interessati nel computer come file di testo con un ID per riga. Prendere nota del percorso del file.
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

In una finestra di PowerShell eseguire il comando seguente, specificando lo script e i percorsi dei file utente. Immettere le credenziali di amministratore globale, quando richieste. Lo script genererà il risultato dell'operazione di aggiornamento di ogni utente.

`<script location> -path <user file location>`

### <a name="disable-the-preview-experience"></a>Disabilitare l'esperienza di anteprima

Per disabilitare l'esperienza di anteprima per gli utenti, eseguire i passaggi seguenti:

1. Accedere al portale di Azure come amministratore utente.
2. Passare a **Azure Active Directory** > **impostazioni utente** > **Gestisci impostazioni per le funzionalità di anteprima del pannello di accesso**.
3. In **utenti possono usare le funzionalità di anteprima per la registrazione e la gestione delle informazioni di sicurezza**, impostare il selettore su **nessuno**, quindi selezionare **Salva**.

Agli utenti non verrà più richiesto di effettuare la registrazione tramite l'esperienza di anteprima.

## <a name="next-steps"></a>Passaggi successivi

* [Scopri di più sull'anteprima pubblica della registrazione combinata per la reimpostazione della password self-service e Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
