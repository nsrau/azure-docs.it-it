---
title: Risolvere i problemi di registrazione combinata - Azure Active DirectoryTroubleshoot combined registration - Azure Active Directory
description: Risolvere i problemi relativi a Azure AD Multi-Factor Authentication e alla reimpostazione combinata della password self-serviceTroubleshoot Azure AD Multi-Factor Authentication and self-service password reset combined
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c840df2c53554519f62a3d1d7a7d8b305187ffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450939"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Risoluzione dei problemi di registrazione combinata delle informazioni di sicurezzaTroubleshooting combined security information registration

Le informazioni contenute in questo articolo hanno lo scopo di guidare gli amministratori che stanno risolvendo i problemi segnalati dagli utenti dell'esperienza di registrazione combinata.

## <a name="audit-logs"></a>Log di controllo

Gli eventi registrati per la registrazione combinata si trovano nella categoria Metodi di autenticazione nei log di controllo di Azure AD.

![Interfaccia dei log di controllo di Azure AD che mostra gli eventi di registrazioneAzure AD Audit logs interface showing registration events](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

La tabella seguente elenca tutti gli eventi di controllo generati dalla registrazione combinata:

| Attività | Stato | Motivo | Descrizione |
| --- | --- | --- | --- |
| L'utente ha registrato tutte le informazioni di sicurezza necessarie | Operazione completata | L'utente ha registrato tutte le informazioni di sicurezza necessarie. | Questo evento si verifica quando un utente ha completato correttamente la registrazione.|
| L'utente ha registrato tutte le informazioni di sicurezza necessarie | Operazioni non riuscite | Registrazione delle informazioni di sicurezza annullata dall'utente. | Questo evento si verifica quando un utente annulla la registrazione dalla modalità di interrupt.|
| Informazioni di sicurezza registrate dall'utente | Operazione completata | Metodo registrato *dall'utente*. | Questo evento si verifica quando un utente registra un singolo metodo. *Il metodo* può essere App Authenticator, Telefono, E-mail, Domande di sicurezza, Password app, Telefono alternativo e così via.| 
| Informazioni di sicurezza esaminate dall'utente | Operazione completata | L'utente ha esaminato correttamente le informazioni di sicurezza. | Questo evento si verifica quando un utente seleziona **Sembra buono** nella pagina di revisione delle informazioni di sicurezza.|
| Informazioni di sicurezza esaminate dall'utente | Operazioni non riuscite | L'utente non è riuscito a esaminare le informazioni di sicurezza. | Questo evento si verifica quando un utente seleziona **Sembra buono** nella pagina di revisione delle informazioni di sicurezza, ma qualcosa non riesce nel back-end.|
| Informazioni di sicurezza eliminate dall'utente | Operazione completata | Metodo eliminato *dall'utente*. | Questo evento si verifica quando un utente elimina un singolo metodo. *Il metodo* può essere App Authenticator, Telefono, E-mail, Domande di sicurezza, Password app, Telefono alternativo e così via.|
| Informazioni di sicurezza eliminate dall'utente | Operazioni non riuscite | Impossibile eliminare il *metodo*. | Questo evento si verifica quando un utente tenta di eliminare un metodo, ma il tentativo ha esito negativo per qualche motivo. *Il metodo* può essere App Authenticator, Telefono, E-mail, Domande di sicurezza, Password app, Telefono alternativo e così via.|
| Informazioni di sicurezza predefinite modificate dall'utente | Operazione completata | L'utente ha modificato le informazioni di sicurezza predefinite per il *metodo*. | Questo evento si verifica quando un utente modifica il metodo predefinito. *Il metodo* può essere una notifica dell'app Authenticator, un codice dall'app o dal token dell'autenticatore, chiamare X XXXXXXXXXX, invia un codice a un codice per l'errore XXXXXXXXXX e così via.|
| Informazioni di sicurezza predefinite modificate dall'utente | Operazioni non riuscite | L'utente non è riuscito a modificare le informazioni di sicurezza predefinite per il *metodo*. | Questo evento si verifica quando un utente tenta di modificare il metodo predefinito, ma il tentativo ha esito negativo per qualche motivo. *Il metodo* può essere una notifica dell'app Authenticator, un codice dall'app o dal token dell'autenticatore, chiamare X XXXXXXXXXX, invia un codice a un codice per l'errore XXXXXXXXXX e così via.|

## <a name="troubleshooting-interrupt-mode"></a>Risoluzione dei problemi relativi alla modalità di interrupt

| Sintomo | Passaggi per la risoluzione dei problemi |
| --- | --- |
| Non vedo i metodi che mi aspettavo di vedere. | 1. Verificare se l'utente dispone di un ruolo di amministratore di Azure AD. In caso affermativo, visualizzare le differenze tra i criteri di amministrazione di SSPR. <br> 2. Determinare se l'utente viene interrotto a causa dell'applicazione della registrazione multi-fattore di autenticazione o dell'applicazione della registrazione SSPR. Vedere il [diagramma di flusso](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) in "Modalità di registrazione combinate" per determinare quali metodi devono essere visualizzati. <br> 3. Determinare la recente modifica dei criteri Di autenticazione a più fattori o SSPR. Se la modifica è stata recente, la propagazione dei criteri aggiornati potrebbe richiedere del tempo.|

## <a name="troubleshooting-manage-mode"></a>Risoluzione dei problemi relativi alla modalità di gestione

| Sintomo | Passaggi per la risoluzione dei problemi |
| --- | --- |
| Non ho la possibilità di aggiungere un particolare metodo. | 1. Determinare se il metodo è abilitato per multi-factor authentication o per SSPR. <br> 2. Se il metodo è abilitato, salvare nuovamente i criteri e attendere 1-2 ore prima di eseguire nuovamente il test. <br> 3. Se il metodo è abilitato, assicurarsi che l'utente non abbia già impostato il numero massimo di tale metodo che è autorizzato a configurare.|

## <a name="disable-combined-registration"></a>Disabilitare la registrazione combinata

Quando un utente registra un numero di telefono e/o un'app per dispositivi mobili nella nuova esperienza combinata, il servizio contrassegna un set di flag (StrongAuthenticationMethods) per tali metodi in tale utente. Questa funzionalità consente all'utente di eseguire Multi-Factor Authentication con tali metodi ogni volta che è necessaria multi-factor Authentication.

Se un amministratore abilita l'anteprima, gli utenti si registrano tramite la nuova esperienza e quindi l'amministratore disabilita l'anteprima, gli utenti potrebbero essere registrati inconsapevolmente anche per l'autenticazione a più fattori.

Se un utente che ha completato la registrazione combinata passa alla pagina di [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)registrazione SSPR (Self-Service Password reset) corrente in , all'utente verrà richiesto di eseguire Multi-Factor Authentication prima di poter accedere a tale pagina. Questo passaggio è previsto da un punto di vista tecnico, ma è nuovo per gli utenti che sono stati precedentemente registrati solo per SSPR. Anche se questo passaggio aggiuntivo migliora la posizione di sicurezza dell'utente fornendo un altro livello di sicurezza, gli amministratori potrebbero voler eseguire il rollback degli utenti in modo che non siano più in grado di eseguire Multi-Factor Authentication.  

### <a name="how-to-roll-back-users"></a>Come eseguire il rollback per gli utenti

Se si, come amministratore, si desidera reimpostare le impostazioni di Autenticazione a più fattori di un utente, è possibile usare lo script di PowerShell fornito nella sezione successiva. Lo script cancellerà la proprietà StrongAuthenticationMethods per l'app per dispositivi mobili e/o il numero di telefono di un utente. Se si esegue questo script per gli utenti, sarà necessario registrarsi nuovamente per Multi-Factor Authentication, se necessario. È consigliabile testare il rollback con uno o due utenti prima di eseguire il rollback di tutti gli utenti interessati.

I passaggi seguenti consentono di eseguire il rollback di un utente o di un gruppo di utenti.

#### <a name="prerequisites"></a>Prerequisiti

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

In una finestra di PowerShell eseguire il comando seguente, specificando i percorsi dei file utente e di script. Immettere le credenziali di amministratore globale, quando richieste. Lo script genererà il risultato dell'operazione di aggiornamento di ogni utente.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>Disabilitare l'esperienza aggiornata

Per disabilitare l'esperienza aggiornata per gli utenti, completare questi passaggi:To disable the updated experience for your users, complete these steps:

1. Accedere al portale di Azure come amministratore utente.
2. Passare a Impostazioni utente di **Azure Active Directory** > Gestire**le** > impostazioni per le funzionalità di anteprima del pannello di**accesso.**
3. In Gli utenti possono utilizzare le funzionalità di **anteprima per la registrazione e la gestione delle informazioni**di sicurezza impostare il **selettore**su Nessuno e quindi selezionare **Salva**.

Agli utenti non verrà più richiesto di registrarsi utilizzando l'esperienza aggiornata.

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sulla registrazione combinata per la reimpostazione della password self-service e Azure Multi-Factor AuthenticationLearn more about combined registration for self-service password reset and Azure Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
