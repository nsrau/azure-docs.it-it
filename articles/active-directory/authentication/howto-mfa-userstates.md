---
title: Abilitare l'autenticazione a più fattori per utente - Azure Active DirectoryEnable per-user Multi-Factor Authentication - Azure Active Directory
description: Informazioni su come abilitare L'autenticazione a più fattori di Azure per utente modificando lo stato utente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309790"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Abilitare l'autenticazione a più fattori di Azure per utente per proteggere gli eventi di accessoEnable per-user Azure Multi-Factor Authentication to secure sign-in events

Esistono due modi per proteggere gli eventi di accesso degli utenti richiedendo l'autenticazione a più fattori in Azure AD. La prima opzione consiste nell'impostare un criterio di accesso condizionale che richiede l'autenticazione a più fattori in determinate condizioni. La seconda opzione consiste nell'abilitare ogni utente per Azure Multi-Factor Authentication.The second option is to enable each user for Azure Multi-Factor Authentication. Quando gli utenti sono abilitati singolarmente, eseguono l'autenticazione a più fattori ogni volta che accedono (con alcune eccezioni, ad esempio quando accedono da indirizzi IP attendibili o quando la funzionalità _dei dispositivi memorizzati_ è attivata).

> [!NOTE]
> L'approccio consigliato è abilitare Azure Multi-Factor Authentication usando i criteri di accesso condizionale. La modifica degli stati utente non è più consigliata a meno che le licenze non includano l'accesso condizionale in quanto richiede agli utenti di eseguire l'autenticazione a più fattori ogni volta che accedono.
>
> Per iniziare a usare l'accesso condizionale, vedere Esercitazione: Proteggere gli eventi di [accesso degli utenti con Azure Multi-Factor Authentication.](tutorial-enable-azure-mfa.md)

## <a name="azure-multi-factor-authentication-user-states"></a>Stati utente di Azure Multi-Factor AuthenticationAzure Multi-Factor Authentication user states

Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

> [!IMPORTANT]
> L'abilitazione di Azure Multi-Factor Authentication tramite criteri di accesso condizionale non modifica lo stato dell'utente. Non allarmarti se gli utenti appaiono disabilitati. L'accesso condizionale non modifica lo stato.
>
> **Non è consigliabile abilitare o applicare gli utenti se si utilizzano i criteri di accesso condizionale.**

| Stato | Descrizione | App interessate non basate su browser | App interessate basate su browser | Autenticazione moderna interessata |
|:---:| --- |:---:|:--:|:--:|
| Disabled | Stato predefinito per un nuovo utente non registrato in Azure Multi-Factor Authentication.The default state for a new user not enrolled in Azure Multi-Factor Authentication. | No | No | No |
| Attivato | L'utente è stato registrato in Azure Multi-Factor Authentication, ma non è stato registrato. Viene richiesto di eseguire la registrazione al successivo accesso. | No.  Continuano a funzionare fino al completamento della registrazione. | Sì. Dopo la scadenza della sessione, è necessaria la registrazione di Azure Multi-Factor Authentication.After the session expires, Azure Multi-Factor Authentication registration is required.| Sì. Dopo la scadenza del token di accesso, è necessaria la registrazione di Azure Multi-Factor Authentication.After the access token expires, Azure Multi-Factor Authentication registration is required. |
| Enforced | The user has been enrolled and has completed the registration process for Azure Multi-Factor Authentication. | Sì. Le app richiedono password per le app. | Sì. Azure Multi-Factor Authentication è necessario all'accesso. | Sì. Azure Multi-Factor Authentication è necessario all'accesso. |

Lo stato di un utente indica se un amministratore li ha registrati in Azure Multi-Factor Authentication e se ha completato il processo di registrazione.

Tutti gli utenti iniziano con *Disabilitato*. Quando si registrano utenti in Azure Multi-Factor Authentication, il relativo stato diventa *Abilitato*. Quando gli utenti abilitati accedono e completano il processo di registrazione, il relativo stato viene modificato in *Applicato*.

> [!NOTE]
> Se l'autenticazione a più fattori viene riabilitata in un oggetto utente che dispone già di dettagli di registrazione, ad esempio telefono o posta elettronica, gli amministratori devono fare in modo che l'utente registri nuovamente l'autenticazione a più fattori tramite il portale di Azure o PowerShell.If a mFA is re-enabled on a user object that already has registration details, such as phone or email, then administrators need to have that user re-register MFA via Azure portal or PowerShell. Se l'utente non esegue nuovamente la registrazione, lo stato di autenticazione a più fattori non passa da *Abilitato* a *Applicato* nell'interfaccia utente di gestione dell'autenticazione a più fattori.

## <a name="view-the-status-for-a-user"></a>Visualizzare lo stato di un utente

Usare la procedura seguente per accedere alla pagina del portale di Azure in cui è possibile visualizzare e gestire gli stati utente:Use the following steps to access the Azure portal page where you can view and manage user states:

1. Accedere al [portale](https://portal.azure.com) di Azure come amministratore.
1. Cercare e selezionare *Azure Active Directory*, quindi selezionare **Utenti** > **tutti gli utenti**.
1. Selezionare **Multi-Factor Authentication**. Potrebbe essere necessario scorrere verso destra per visualizzare questa opzione di menu. Selezionare la schermata di esempio seguente per visualizzare la finestra completa del portale di Azure e la posizione del menu:Select the example screenshot below to see the full Azure portal window and menu location:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Selezionare Multi-Factor Authentication nella finestra Utenti in Azure ADSelect Multi-Factor Authentication from the Users window in Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Viene visualizzata una nuova pagina che visualizza lo stato utente, come illustrato nell'esempio seguente.
   ![Screenshot che mostra informazioni sullo stato utente di esempio per Azure Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Modificare lo stato di un utente

Per modificare lo stato di Azure Multi-Factor Authentication per un utente, completare i passaggi seguenti:To change the Azure Multi-Factor Authentication state for a user, complete the following steps:

1. Usare la procedura precedente per visualizzare la pagina **utenti** di Azure Multi-Factor Authentication.
1. Trovare l'utente che si vuole abilitare per Azure Multi-Factor Authentication. Potrebbe essere necessario modificare la visualizzazione nella parte superiore per **gli utenti**.
   ![Selezionare l'utente per cui modificare lo stato dalla scheda Utenti](./media/howto-mfa-userstates/enable1.png)
1. Selezionare la casella accanto ai nomi degli utenti per cui modificare lo stato.
1. Sul lato destro, in **Passaggi rapidi**, scegliere **Abilita** o **Disabilita**. Nell'esempio seguente, l'utente *John Smith* ha un segno di ![spunta accanto al nome ed è abilitato per l'uso: Abilita utente selezionato facendo clic su Abilita nel menu dei passaggi rapidi](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Gli* utenti abilitati vengono automaticamente passati a Enforced quando si registrano per Azure Multi-Factor Authentication.Enabled users are automatically switched to Enforced when they *registerd* for Azure Multi-Factor Authentication. Non modificare manualmente lo stato dell'utente in *Applicato*.

1. Confermare la selezione nella finestra popup che viene visualizzata.

È consigliabile inviare una notifica tramite posta elettronica agli utenti dopo averli abilitati. Comunicare agli utenti che viene visualizzato un prompt per chiedere loro di registrarsi al successivo accesso. se l'organizzazione usa app non basate su browser che non supportano l'autenticazione moderna, devono creare password per l'app. Per altre informazioni, vedere la [guida per l'utente finale di Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md) per iniziare.

## <a name="change-state-using-powershell"></a>Modificare lo stato tramite PowerShellChange state using PowerShell

Per modificare lo stato utente tramite Azure `$st.State` AD [PowerShell,](/powershell/azure/overview)modificare il parametro per un account utente. Esistono tre stati possibili per un account utente:There are three possible states for a user account:

* *Attivato*
* *Enforced*
* *Disabled*  

Un utente non può essere spostato direttamente sullo stato *Applicato*. In questo caso, le app non basate su browser smettono di funzionare perché l'utente non ha eseguito la registrazione di Azure Multi-Factor Authentication e ha ottenuto una [password dell'app.](howto-mfa-mfasettings.md#app-passwords)

Per iniziare, installare il modulo *MSOnline* utilizzando [Install-Module](/powershell/module/powershellget/install-module) come segue:

```PowerShell
Install-Module MSOnline
```

Successivamente, connettersi utilizzando [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Lo script di PowerShell di esempio seguente *bsimon@contoso.com*abilita l'autenticazione a più fattori per un singolo utente denominato :The following example PowerShell script enables MFA for an individual user named :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

L'uso di PowerShell è la scelta migliore quando è necessario abilitare utenti in massa. Lo script seguente scorre in ciclo un elenco di utenti e abilita l'autenticazione a più fattori nei propri account. Definire gli account utente impostati `$users` nella prima riga per quanto segue:

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Per disabilitare l'autenticazione a più fattori, nell'esempio seguente viene ottenuto un utente con [Get-MsolUser](/powershell/module/msonline/get-msoluser), quindi viene rimosso qualsiasi *ForteAuthenticationRequirements* impostato per l'utente definito utilizzando [Set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

È anche possibile disabilitare direttamente l'autenticazione a più fattori per un utente che utilizza [Set-MsolUser](/powershell/module/msonline/set-msoluser) come segue:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convertire gli utenti dall'autenticazione a più fattori per utente all'autenticazione a più fattori basata sull'accesso condizionaleConvert users from per-user MFA to Conditional Access based MFA

PowerShell seguente può essere utili per eseguire la conversione in Azure Multi-Factor Authentication basato sull'accesso condizionale.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Di recente è stato modificato il comportamento e questo script di PowerShell.We recently changed the behavior and this PowerShell script. In precedenza, lo script salvato i metodi di autenticazione a più fattori, disabilitato MFA e ripristinato i metodi. Questo non è più necessario ora che il comportamento predefinito per disable non cancella i metodi.
>
> Se l'autenticazione a più fattori viene riabilitata in un oggetto utente che dispone già di dettagli di registrazione, ad esempio telefono o posta elettronica, gli amministratori devono fare in modo che l'utente registri nuovamente l'autenticazione a più fattori tramite il portale di Azure o PowerShell.If a mFA is re-enabled on a user object that already has registration details, such as phone or email, then administrators need to have that user re-register MFA via Azure portal or PowerShell. Se l'utente non esegue nuovamente la registrazione, lo stato di autenticazione a più fattori non passa da *Abilitato* a *Applicato* nell'interfaccia utente di gestione dell'autenticazione a più fattori.

## <a name="next-steps"></a>Passaggi successivi

Per configurare le impostazioni di Azure Multi-Factor Authentication come IP attendibili, messaggi vocali personalizzati e avvisi di frode, vedere [Configure Azure Multi-Factor Authentication settings](howto-mfa-mfasettings.md). Per gestire le impostazioni utente per Azure Multi-Factor Authentication, vedere [Gestire le impostazioni utente con Azure Multi-Factor Authentication.](howto-mfa-userdevicesettings.md)

Per comprendere il motivo per cui a un utente è stato richiesto o non richiesto di eseguire l'autenticazione a più fattori, vedere Report di Azure Multi-Factor Authentication .To understand why a user was prompted or notprompted to perform MFA, see [Azure Multi-Factor Authentication reports](howto-mfa-reporting.md#azure-ad-sign-ins-report).
