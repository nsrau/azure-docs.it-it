---
title: Abilitare Multi-Factor Authentication per singolo utente - Azure Active Directory
description: Informazioni su come abilitare Azure Multi-Factor Authentication per singolo utente modificando lo stato dell'utente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bd688a9e488c1206b0c8531698b061f650e2afe
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87417931"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Abilitare Azure Multi-Factor Authentication per singolo utente per proteggere gli eventi di accesso

Esistono due modi per proteggere gli eventi di accesso degli utenti richiedendo l'autenticazione a più fattori in Azure AD. La prima opzione, quella preferita, è configurare un criterio di accesso condizionale che richieda l'autenticazione a più fattori in determinate condizioni. La seconda opzione è abilitare ogni utente ad Azure Multi-Factor Authentication. Quando gli utenti sono abilitati individualmente, effettuano l'autenticazione a più fattori a ogni accesso (con alcune eccezioni, come quando l'accesso viene effettuato da indirizzi IP attendibili o quando la funzione _dispositivi memorizzati_ è attiva).

> [!NOTE]
> L'approccio consigliato consiste nell'abilitare Azure Multi-Factor Authentication con i criteri di accesso condizionale. La modifica degli stati dell'utente non è più consigliata, a meno che le licenze non includano l'accesso condizionale in quanto richiede agli utenti di eseguire l'autenticazione a più fattori ogni volta che accedono. Per iniziare a usare l'accesso condizionale, vedere l'[esercitazione: Proteggere gli eventi di accesso degli utenti con Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Per Azure AD tenant gratuiti senza accesso condizionale, è possibile [usare le impostazioni predefinite di sicurezza per proteggere gli utenti](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Stati degli utenti di Azure Multi-Factor Authentication

Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

> [!IMPORTANT]
> Abilitando Azure Multi-Factor Authentication tramite un criterio di accesso condizionale lo stato dell'utente non viene modificato. Non allarmarsi se gli utenti sembrano essere disabilitati. L'accesso condizionale non modifica lo stato.
>
> **Se si usano criteri di accesso condizionale, non è necessario abilitare o applicare gli utenti.**

| Stato | Descrizione | App interessate non basate su browser | App interessate basate su browser | Autenticazione moderna interessata |
|:---:| --- |:---:|:--:|:--:|
| Disabled | Lo stato predefinito di un nuovo utente non registrato in Azure Multi-Factor Authentication. | No | No | No |
| Attivato | L'utente è stato iscritto in Azure Multi-Factor Authentication, ma non ha registrato metodi di autenticazione. Viene richiesto di eseguire la registrazione al successivo accesso. | No.  Continuano a funzionare fino al completamento della registrazione. | Sì. Allo scadere della sessione è richiesta la registrazione ad Azure Multi-Factor Authentication.| Sì. Allo scadere del token di accesso è richiesta la registrazione ad Azure Multi-Factor Authentication. |
| Enforced | L'utente è stato registrato e ha completato la procedura di registrazione per Azure Multi-Factor Authentication. | Sì. Le app richiedono password per le app. | Sì. Azure Multi-Factor Authentication è richiesto all'accesso. | Sì. Azure Multi-Factor Authentication è richiesto all'accesso. |

Lo stato di un utente indica se un amministratore lo ha registrato in Azure Multi-Factor Authentication e se l'utente ha completato la procedura di registrazione.

Tutti gli utenti iniziano con *Disabilitato*. Quando si registrano gli utenti in Azure Multi-Factor Authentication, il relativo stato si modifica in *Abilitato*. Quando gli utenti abilitati accedono e completano il processo di registrazione, il relativo stato viene modificato in *Applicato*.

> [!NOTE]
> Se si abilita nuovamente MFA su un oggetto utente che già presenta alcuni dettagli di registrazione, come numero di telefono o indirizzo di posta elettronica, gli amministratori devono chiedere all'utente di effettuare nuovamente la registrazione a Microsoft Azure Multi-Factor Authentication tramite il portale di Azure o PowerShell. Se l'utente non effettua nuovamente la registrazione, lo stato di MFA non passa da *Abilitato* ad *Applicato* nell'interfaccia utente di gestione di MFA.

## <a name="view-the-status-for-a-user"></a>Visualizzare lo stato di un utente

Per accedere alla pagina del portale di Azure in cui è possibile visualizzare e gestire gli stati utente, procedere come segue:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
1. Cercare e selezionare *Azure Active Directory*, quindi selezionare **Utenti** > **Tutti gli utenti**.
1. Selezionare **Multi-Factor Authentication**. Potrebbe essere necessario scorrere verso destra per visualizzare l'opzione del menu. Selezionare la schermata di esempio seguente per visualizzare la finestra di portale di Azure completa e il percorso del menu: [ ![ selezionare multi-factor authentication dalla finestra utenti in Azure ad.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Si apre una nuova pagina in cui viene visualizzato lo stato dell'utente, come illustrato nell'esempio seguente.
   ![Screenshot che mostra le informazioni di esempio sullo stato dell'utente per Azure Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Modificare lo stato di un utente

Per modificare lo stato di un utente di Azure Multi-Factor Authentication, completare i passaggi seguenti:

1. Usare la procedura precedente per visualizzare la pagina **utenti** di Azure Multi-Factor Authentication.
1. Trovare l'utente che si desidera abilitare ad Azure Multi-Factor Authentication. Potrebbe essere necessario modificare la visualizzazione nella parte superiore impostandola su **utenti**.
   ![Selezionare l'utente per cui modificare lo stato dalla scheda utenti](./media/howto-mfa-userstates/enable1.png)
1. Selezionare la casella accanto ai nomi degli utenti di cui si vuole modificare lo stato.
1. A destra, sotto **Azioni rapide**, scegliere **Abilita** o **Disabilita**. Nell'esempio seguente l'utente *John Smith* presenta un segno di spunta a fianco del nome ed è abilitato per l'uso: ![Per abilitare l'utente selezionato, fare clic su Abilita nel menu Azioni rapide](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Gli utenti *abilitati* diventano automaticamente *Applicati* quando si registrano ad Azure Multi-Factor Authentication. Non modificare manualmente lo stato utente in *Applicato*.

1. Confermare la selezione nella finestra popup che viene visualizzata.

È consigliabile inviare una notifica tramite posta elettronica agli utenti dopo averli abilitati. Informare gli utenti che viene visualizzato un messaggio per richiedere la registrazione al successivo accesso. se l'organizzazione usa app non basate su browser che non supportano l'autenticazione moderna, devono creare password per l'app. Per altre informazioni, consultare la [guida per l'utente finale di Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md) come supporto introduttivo all'uso.

## <a name="change-state-using-powershell"></a>Modificare lo stato con PowerShell

Per modificare lo stato utente usando [Azure AD PowerShell](/powershell/azure/) è necessario modificare il parametro `$st.State` per un account utente. Sono disponibili tre possibili stati per un account utente:

* *Enabled*
* *Applicato*
* *Disabilitato*  

Un utente non può essere spostato direttamente sullo stato *Applicato*. Se si esegue questa operazione, le app non basate su browser smettono di funzionare poiché l'utente non ha effettuato la registrazione ad Azure Multi-Factor Authentication e non ha ottenuto una [password delle app](howto-mfa-app-passwords.md).

Per iniziare, installare il modulo *MSOnline* usando [Install-Module](/powershell/module/powershellget/install-module) come segue:

```PowerShell
Install-Module MSOnline
```

A questo punto, connettersi usando [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Lo script di PowerShell di esempio che segue abilita l'autenticazione a più fattori per un singolo utente denominato *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

L'uso di PowerShell è la scelta migliore quando è necessario abilitare utenti in massa. Lo script seguente scorre un elenco di utenti e abilita l'autenticazione a più fattori per i relativi account. Definire gli account utente impostati nella prima riga per `$users` come indicato di seguito:

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

Per disabilitare l'autenticazione a più fattori, nell'esempio seguente si prende un utente con [Get-MsolUser](/powershell/module/msonline/get-msoluser) e quindi si rimuovono eventuali *StrongAuthenticationRequirements* impostati per l'utente definito usando [Set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

È possibile anche disabilitare l'autenticazione a più fattori direttamente per un utente usando [Set-MsoIUser](/powershell/module/msonline/set-msoluser) come indicato di seguito:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convertire gli utenti dall'autenticazione a più fattori per singolo utente all'autenticazione a più fattori basata sull'accesso condizionale

Lo script di PowerShell seguente consente di eseguire la conversione all'autenticazione a più fattori di Azure basata sull'accesso condizionale.

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
> Se si abilita nuovamente MFA su un oggetto utente che già presenta alcuni dettagli di registrazione, come numero di telefono o indirizzo di posta elettronica, gli amministratori devono chiedere all'utente di effettuare nuovamente la registrazione a Microsoft Azure Multi-Factor Authentication tramite il portale di Azure o PowerShell. Se l'utente non effettua nuovamente la registrazione, lo stato di MFA non passa da *Abilitato* ad *Applicato* nell'interfaccia utente di gestione di MFA.

## <a name="next-steps"></a>Passaggi successivi

Per configurare le impostazioni di Azure Multi-Factor Authentication, vedere [configurare le impostazioni di azure multi-factor authentication](howto-mfa-mfasettings.md).

Per la gestione delle impostazioni utente per Azure Multi-Factor Authentication, vedere l'articolo [Gestire le impostazioni utente con Azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Per comprendere il motivo per cui all'utente è stato richiesto o non è stato richiesto di eseguire l'autenticazione a più fattori, vedere [Report di Azure Multi-Factor Authentication](howto-mfa-reporting.md).
