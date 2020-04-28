---
title: Abilita Multi-Factor Authentication Azure Active Directory per utente
description: Informazioni su come abilitare Multi-Factor Authentication di Azure per utente modificando lo stato utente
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309790"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Abilitare Multi-Factor Authentication di Azure per utente per proteggere gli eventi di accesso

Esistono due modi per proteggere gli eventi di accesso utente richiedendo la funzionalità di autenticazione a più fattori in Azure AD. La prima opzione, e preferita, consiste nell'impostare un criterio di accesso condizionale che richiede l'autenticazione a più fattori in determinate condizioni. La seconda opzione consiste nell'abilitare ogni utente per Multi-Factor Authentication di Azure. Quando gli utenti sono abilitati singolarmente, eseguono l'autenticazione a più fattori ogni volta che accedono (con alcune eccezioni, ad esempio quando accedono da indirizzi IP attendibili o quando la funzionalità dei _dispositivi memorizzati_ è attivata).

> [!NOTE]
> L'abilitazione di Azure Multi-Factor Authentication usando i criteri di accesso condizionale è l'approccio consigliato. La modifica degli stati utente non è più consigliata, a meno che le licenze non includano l'accesso condizionale perché richiede agli utenti di eseguire l'autenticazione a più fattori ogni volta che accedono.
>
> Per iniziare a usare l'accesso condizionale, vedere [esercitazione: proteggere gli eventi di accesso utente con multi-factor authentication di Azure](tutorial-enable-azure-mfa.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Stati utente Multi-Factor Authentication di Azure

Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

> [!IMPORTANT]
> L'abilitazione di Azure Multi-Factor Authentication tramite un criterio di accesso condizionale non modifica lo stato dell'utente. Non allarmarsi se gli utenti appaiono disabilitati. L'accesso condizionale non modifica lo stato.
>
> **Se si usano criteri di accesso condizionale, non è necessario abilitare o applicare utenti.**

| Stato | Descrizione | App interessate non basate su browser | App interessate basate su browser | Autenticazione moderna interessata |
|:---:| --- |:---:|:--:|:--:|
| Disabled | Lo stato predefinito per un nuovo utente non registrato in Azure Multi-Factor Authentication. | No | No | No |
| Attivato | L'utente è stato iscritto in Azure Multi-Factor Authentication, ma non è stato registrato. Viene richiesto di eseguire la registrazione al successivo accesso. | No.  Continuano a funzionare fino al completamento della registrazione. | Sì. Dopo la scadenza della sessione, è richiesta la registrazione Multi-Factor Authentication di Azure.| Sì. Dopo la scadenza del token di accesso, è necessaria la registrazione Multi-Factor Authentication di Azure. |
| Enforced | L'utente è stato registrato e ha completato il processo di registrazione per Azure Multi-Factor Authentication. | Sì. Le app richiedono password per le app. | Sì. Azure Multi-Factor Authentication è necessario all'accesso. | Sì. Azure Multi-Factor Authentication è necessario all'accesso. |

Lo stato di un utente indica se un amministratore ha registrato in Azure Multi-Factor Authentication e se ha completato il processo di registrazione.

Tutti gli utenti iniziano con *Disabilitato*. Quando si registrano gli utenti in Azure Multi-Factor Authentication, il relativo stato cambia in *abilitato*. Quando gli utenti abilitati accedono e completano il processo di registrazione, il relativo stato viene modificato in *Applicato*.

> [!NOTE]
> Se l'autenticazione a più fattori viene riabilitata in un oggetto utente che ha già i dettagli di registrazione, ad esempio telefono o indirizzo di posta elettronica, gli amministratori devono ripetere la registrazione dell'autenticazione a più fattori tramite portale di Azure o PowerShell. Se l'utente non esegue di nuovo la registrazione, lo stato dell'autenticazione a più fattori non passa da *abilitato* a *applicato* nell'interfaccia utente di gestione dell'autenticazione a più fattori.

## <a name="view-the-status-for-a-user"></a>Visualizzare lo stato di un utente

Per accedere alla pagina portale di Azure in cui è possibile visualizzare e gestire gli stati utente, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
1. Cercare e selezionare *Azure Active Directory*, quindi selezionare **utenti** > **tutti gli utenti**.
1. Selezionare **multi-factor authentication**. Potrebbe essere necessario scorrere verso destra per visualizzare questa opzione di menu. Selezionare la schermata di esempio seguente per visualizzare la finestra di portale di Azure completa e il percorso del menu:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Selezionare Multi-Factor Authentication dalla finestra utenti in Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Verrà visualizzata una nuova pagina in cui viene visualizzato lo stato utente, come illustrato nell'esempio seguente.
   ![Screenshot che mostra le informazioni sullo stato utente di esempio per Multi-Factor Authentication di Azure](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Modificare lo stato di un utente

Per modificare lo stato di Azure Multi-Factor Authentication per un utente, attenersi alla procedura seguente:

1. Usare la procedura precedente per visualizzare la pagina **utenti** di Azure Multi-Factor Authentication.
1. Trovare l'utente che si vuole abilitare per Azure Multi-Factor Authentication. Potrebbe essere necessario modificare la visualizzazione in alto per **gli utenti**.
   ![Selezionare l'utente per cui modificare lo stato dalla scheda utenti](./media/howto-mfa-userstates/enable1.png)
1. Selezionare la casella accanto ai nomi degli utenti per i quali modificare lo stato.
1. Sul lato destro, sotto **azioni rapide**, scegliere **Abilita** o **Disabilita**. Nell'esempio seguente l'utente *John Smith* dispone di un segno di spunta accanto al nome e viene abilitato per l'uso: ![Abilita l'utente selezionato facendo clic su Abilita nel menu passaggi rapidi](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Gli utenti *abilitati* vengono automaticamente attivati quando si registrano per *multi-factor authentication di Azure* . Non modificare manualmente lo stato utente impostando *applicato*.

1. Confermare la selezione nella finestra popup che viene visualizzata.

È consigliabile inviare una notifica tramite posta elettronica agli utenti dopo averli abilitati. Informare gli utenti che viene visualizzato un messaggio per richiedere la registrazione al successivo accesso. se l'organizzazione usa app non basate su browser che non supportano l'autenticazione moderna, devono creare password per l'app. Per ulteriori informazioni, vedere la [Guida per l'utente finale di Azure multi-factor authentication](../user-help/multi-factor-authentication-end-user.md) per iniziare.

## <a name="change-state-using-powershell"></a>Modificare lo stato con PowerShell

Per modificare lo stato utente usando [Azure ad PowerShell](/powershell/azure/overview), è necessario modificare il `$st.State` parametro per un account utente. Esistono tre possibili stati per un account utente:

* *Attivata*
* *Enforced*
* *Disattivata*  

Un utente non può essere spostato direttamente sullo stato *Applicato*. In tal caso, le app non basate su browser smetteranno di funzionare perché l'utente non ha eseguito la registrazione Multi-Factor Authentication di Azure e ha ottenuto una [password dell'app](howto-mfa-mfasettings.md#app-passwords).

Per iniziare, installare il modulo *MSOnline* con [install-module](/powershell/module/powershellget/install-module) come indicato di seguito:

```PowerShell
Install-Module MSOnline
```

Connettersi quindi usando [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Lo script di PowerShell di esempio seguente abilita l'autenticazione a più *bsimon@contoso.com*fattori per un singolo utente denominato:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

L'uso di PowerShell è la scelta migliore quando è necessario abilitare utenti in massa. Lo script seguente esegue il ciclo di un elenco di utenti e Abilita l'autenticazione a più fattori nei propri account. Definire gli account utente impostati nella prima riga per `$users` come segue:

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

Per disabilitare l'autenticazione a più fattori, nell'esempio seguente viene ottenuto un utente con [Get-MsolUser](/powershell/module/msonline/get-msoluser), quindi vengono rimossi tutti i set di *StrongAuthenticationRequirements* per l'utente definito usando [set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

È anche possibile disabilitare direttamente l'autenticazione a più fattori per un utente usando [set-MsolUser](/powershell/module/msonline/set-msoluser) come indicato di seguito:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Convertire gli utenti dall'autenticazione a più fattori per utente all'autenticazione a più fattori

Il seguente PowerShell consente di eseguire la conversione in Azure Multi-Factor Authentication basato sull'accesso condizionale.

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
> Il comportamento e lo script di PowerShell sono stati modificati di recente. In precedenza, lo script salvava i metodi di autenticazione a più fattori, disabilitazione dell'autenticazione a più fattori e ripristina i metodi. Questa operazione non è più necessaria, ora che il comportamento predefinito di Disable non cancella i metodi.
>
> Se l'autenticazione a più fattori viene riabilitata in un oggetto utente che ha già i dettagli di registrazione, ad esempio telefono o indirizzo di posta elettronica, gli amministratori devono ripetere la registrazione dell'autenticazione a più fattori tramite portale di Azure o PowerShell. Se l'utente non esegue di nuovo la registrazione, lo stato dell'autenticazione a più fattori non passa da *abilitato* a *applicato* nell'interfaccia utente di gestione dell'autenticazione a più fattori.

## <a name="next-steps"></a>Passaggi successivi

Per configurare le impostazioni di Azure Multi-Factor Authentication come gli indirizzi IP attendibili, i messaggi vocali personalizzati e gli avvisi di illecito, vedere [configurare le impostazioni di azure multi-factor authentication](howto-mfa-mfasettings.md). Per gestire le impostazioni utente per Multi-Factor Authentication di Azure, vedere [gestire le impostazioni utente con multi-factor authentication di Azure](howto-mfa-userdevicesettings.md).

Per comprendere il motivo per cui all'utente è stato richiesto o non è stato richiesto di eseguire l'autenticazione a più fattori, vedere [Azure multi-factor authentication report](howto-mfa-reporting.md#azure-ad-sign-ins-report)
