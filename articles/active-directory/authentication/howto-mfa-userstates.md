---
title: Azure multi-Factor Authentication gli stati utente - Azure Active Directory
description: Informazioni sugli stati utente in Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a2c036265049bf4b87435e96f779482da404caa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164809"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Come richiedere la verifica in due passaggi per un utente

Sono disponibili due modi per richiedere la verifica in due passaggi. Per entrambi è necessario un account amministratore globale. Il primo prevede l'abilitazione di ogni utente per Azure Multi-Factor Authentication (MFA). Gli utenti abilitati singolarmente devono eseguire la verifica in due passaggi a ogni accesso, con alcune eccezioni, ad esempio se accedono da indirizzi IP attendibili o quando è attiva la funzionalità relativa ai _dispositivi memorizzati_. La seconda opzione consiste nell'impostare un criterio di accesso condizionale che richiede la verifica in due passaggi in determinate condizioni.

> [!TIP]
> L'abilitazione di Azure multi-Factor Authentication tramite criteri di accesso condizionale è l'approccio consigliato. La modifica degli stati utente non è più consigliata, a meno che le licenze non includono l'accesso condizionale perché sarà necessario agli utenti di eseguire l'autenticazione a più fattori ogni volta che effettuano l'accesso.

## <a name="choose-how-to-enable"></a>Scegliere come eseguire l'abilitazione

**Abilitato modificando lo stato utente**: si tratta del metodo tradizionalmente usato per richiedere la verifica in due passaggi ed è illustrato in questo articolo. Può essere usato sia con Azure MFA nel cloud sia con Azure MFA Server. Con questo metodo richiede agli utenti di eseguire la verifica in due passaggi **ogni volta che** sono l'accesso e si esegue l'override di criteri di accesso condizionale. Si tratta del metodo usato in questi casi con le licenze di Office 365 o Microsoft 365 Business perché non includono funzionalità di accesso condizionale.

Abilitati dai criteri di accesso condizionale - questo è il mezzo più flessibile per abilitare la verifica in due passaggi per gli utenti. Abilitazione con soli criteri di accesso condizionale per Azure MFA nel cloud ed è una funzionalità premium di Azure AD. Per altre informazioni su questo metodo, vedere [Implementare Azure Multi-Factor Authentication basato su cloud](howto-mfa-getstarted.md).

Abilitato da Azure AD Identity Protection: questo metodo usa i criteri di rischio di Azure AD Identity Protection per richiedere la verifica in due passaggi basata solo sul rischio di accesso per tutte le applicazioni cloud. Questo metodo richiede una licenza di Azure Active Directory P2. Per altre informazioni su questo metodo, vedere [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Altre informazioni sulle licenze e i prezzi sono disponibili nelle pagine relative ai prezzi di [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) e [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="enable-azure-mfa-by-changing-user-state"></a>Abilitare Azure MFA modificando lo stato utente

Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

| Stato | Descrizione | App interessate non basate su browser | App interessate basate su browser | Autenticazione moderna interessata |
|:---:|:---:|:---:|:--:|:--:|
| Disabled |Stato predefinito per un nuovo utente non registrato in Azure MFA. |No |No |No |
| Enabled |L'utente è stato iscritto ad Azure MFA, ma non ha eseguito la registrazione. Viene richiesto di eseguire la registrazione al successivo accesso. |No.  Continuano a funzionare fino al completamento della registrazione. | Sì. Dopo la scadenza della sessione, è necessaria la registrazione ad Azure MFA.| Sì. Dopo la scadenza dei token di accesso, è necessaria la registrazione ad Azure MFA. |
| Enforced |L'utente è stato iscritto e ha completato il processo di registrazione per Azure MFA. |Sì. Le app richiedono password per le app. |Sì. Azure MFA è necessario all'accesso. | Sì. Azure MFA è necessario all'accesso. |

Lo stato dell'utente indica se un amministratore ha eseguito la relativa iscrizione in Azure MFA e se l'utente ha completato il processo di registrazione.

Tutti gli utenti iniziano con *Disabilitato*. Quando si registrano gli utenti in Azure MFA, il relativo stato cambia in *Abilitato*. Quando gli utenti abilitati accedono e completano il processo di registrazione, il relativo stato viene modificato in *Applicato*.  

### <a name="view-the-status-for-a-user"></a>Visualizzare lo stato di un utente

Per accedere alla pagina in cui è possibile visualizzare e gestire gli stati utente, procedere come segue:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
   ![Selezionare Multi-Factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. Verrà visualizzata una nuova pagina in cui sono elencati gli stati utente.
   ![Stati utente in Microsoft Azure Multi-Factor Authentication - screenshot](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Modificare lo stato di un utente

1. Usare la procedura precedente per visualizzare la pagina **utenti** di Azure Multi-Factor Authentication.
2. Trovare l'utente che si vuole abilitare per Azure MFA. Potrebbe essere necessario modificare la visualizzazione nella parte superiore.
   ![Selezionare l'utente a modificare lo stato per nella scheda utenti](./media/howto-mfa-userstates/enable1.png)
3. Selezionare la casella accanto al nome.
4. A destra, sotto **Azioni rapide** scegliere **Abilita** o **Disabilita**.
   ![Abilitare l'utente selezionato, fare clic su Abilita nel menu Azioni rapide](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Gli utenti *abilitati* diventano automaticamente *applicati* quando si registrano ad Azure MFA. Non modificare manualmente lo stato di un utente su *Applicato*.

5. Confermare la selezione nella finestra popup che viene visualizzata.

È consigliabile inviare una notifica tramite posta elettronica agli utenti dopo averli abilitati. Informarli anche che verrà chiesto loro di eseguire la registrazione al successivo accesso e che, se l'organizzazione usa app non basate su browser che non supportano l'autenticazione moderna, devono creare password per l'app. È possibile anche includere un collegamento alla [guida dell'utente finale ad Azure MFA](../user-help/multi-factor-authentication-end-user.md) con informazioni utili per iniziare.

### <a name="use-powershell"></a>Usare PowerShell

Per modificare lo stato dell'utente usando [Azure AD PowerShell](/powershell/azure/overview), modificare `$st.State`. Esistono tre possibili stati:

* Enabled
* Enforced
* Disabled  

Un utente non può essere spostato direttamente sullo stato *Applicato*. Se si esegue questa operazione, le app non basate su browser smettono di funzionare poiché l'utente non ha effettuato la registrazione ad Azure MFA e non ha ottenuto una [password delle app](howto-mfa-mfasettings.md#app-passwords).

Installare prima il modulo usando:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Non dimenticare di connettersi con **Connect-MsolService**

Questo script di PowerShell di esempio abilita MFA per un singolo utente:

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

L'uso di PowerShell è la scelta migliore quando è necessario abilitare utenti in massa. Ad esempio, lo script seguente scorre un elenco di utenti e abilita MFA per i relativi account:

   ```PowerShell
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

Per disabilitare MFA, usare questo script:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationMethods @()
   ```

che può essere abbreviato in:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Agli utenti di conversione da MFA per utente all'accesso condizionale con MFA

Il comando PowerShell seguente consentono di effettuare la conversione a accesso condizionale basato su Azure multi-Factor Authentication.

```PowerShell
# Disable MFA for all users, keeping their MFA methods intact
Get-MsolUser -All | Disable-MFA -KeepMethods

# Enforce MFA for all users
Get-MsolUser -All | Set-MfaState -State Enforced

# Wrapper to disable MFA with the option to keep the MFA
# methods (to avoid having to proof-up again later)
function Disable-Mfa {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline=$True)]
        $User,
        [switch] $KeepMethods
    )

    Process {

        Write-Verbose ("Disabling MFA for user '{0}'" -f $User.UserPrincipalName)
        $User | Set-MfaState -State Disabled

        if ($KeepMethods) {
            # Restore the MFA methods which got cleared when disabling MFA
            Set-MsolUser -ObjectId $User.ObjectId `
                         -StrongAuthenticationMethods $User.StrongAuthenticationMethods
        }
    }
}

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

```

## <a name="next-steps"></a>Passaggi successivi

* Perché a un utente è stato richiesto o non è stato richiesto di eseguire MFA? Vedere la sezione [Report sugli accessi ad Azure AD nel documento Report in Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Per configurare impostazioni aggiuntive, ad esempio gli indirizzi IP attendibili, i messaggi vocali personalizzati e gli avvisi di illeciti, vedere l'articolo [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
* Informazioni sulla gestione delle impostazioni utente per Azure Multi-Factor Authentication sono disponibili nell'articolo [Gestire le impostazioni utente nel cloud con Azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md)
