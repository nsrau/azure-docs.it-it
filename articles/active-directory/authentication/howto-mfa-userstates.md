---
title: Abilitare Multi-Factor Authentication per singolo utente - Azure Active Directory
description: Informazioni su come abilitare Azure Multi-Factor Authentication per singolo utente modificando lo stato dell'utente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 433cfa3789aa37f4145982da97719526c0abfc47
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719496"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Abilitare Azure Multi-Factor Authentication per singolo utente per proteggere gli eventi di accesso

Per proteggere gli eventi di accesso degli utenti in Azure AD, è possibile richiedere l'autenticazione a più fattori. L'abilitazione di Azure Multi-Factor Authentication usando i criteri di accesso condizionale è l'approccio consigliato per proteggere gli utenti. L'accesso condizionale è una funzionalità Azure AD Premium P1 o P2 che consente di applicare le regole per richiedere l'autenticazione a più fattori in base alle esigenze in determinati scenari. Per iniziare a usare l'accesso condizionale, vedere l'[esercitazione: Proteggere gli eventi di accesso degli utenti con Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

Per Azure AD tenant gratuiti senza accesso condizionale, è possibile [usare le impostazioni predefinite di sicurezza per proteggere gli utenti](../fundamentals/concept-fundamentals-security-defaults.md). Agli utenti viene richiesto di usare l'autenticazione a più fattori in base alle esigenze, ma non è possibile definire regole personalizzate per controllare il comportamento.

Se necessario, è invece possibile abilitare ogni account per Multi-Factor Authentication Azure per utente. Quando gli utenti sono abilitati singolarmente, eseguono l'autenticazione a più fattori ogni volta che accedono (con alcune eccezioni, ad esempio quando accedono da indirizzi IP attendibili o quando la funzionalità memorizza l'autenticazione a più fattori _su dispositivi attendibili_ è attivata).

La modifica degli stati utente non è consigliata, a meno che le licenze Azure AD non includano l'accesso condizionale e non si desideri usare le impostazioni predefinite di sicurezza. Per altre informazioni sulle diverse modalità di abilitazione dell'autenticazione a più fattori, vedere [funzionalità e licenze per Azure multi-factor authentication](concept-mfa-licensing.md).

> [!IMPORTANT]
>
> Questo articolo illustra come visualizzare e modificare lo stato per Multi-Factor Authentication Azure per utente. Se si usa l'accesso condizionale o i valori predefiniti di sicurezza, non è possibile esaminare o abilitare gli account utente seguendo questa procedura.
>
> Abilitando Azure Multi-Factor Authentication tramite un criterio di accesso condizionale lo stato dell'utente non viene modificato. Non allarmarsi se gli utenti sembrano essere disabilitati. L'accesso condizionale non modifica lo stato.
>
> **Non abilitare o applicare Multi-Factor Authentication di Azure per utente se si usano i criteri di accesso condizionale.**

## <a name="azure-multi-factor-authentication-user-states"></a>Stati degli utenti di Azure Multi-Factor Authentication

Lo stato di un utente indica se un amministratore ha registrato tali utenti in Multi-Factor Authentication di Azure per utente. Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

| State | Descrizione | Autenticazione legacy interessata | App interessate basate su browser | Autenticazione moderna interessata |
|:---:| --- |:---:|:--:|:--:|
| Disabled | Lo stato predefinito per un utente non registrato in Multi-Factor Authentication di Azure per utente. | No | No | No |
| Attivato | L'utente è registrato nel Multi-Factor Authentication di Azure per utente, ma può comunque usare la password per l'autenticazione legacy. Se l'utente non ha ancora registrato i metodi di autenticazione a più fattori, riceve una richiesta di registrazione al successivo accesso usando l'autenticazione moderna, ad esempio tramite un Web browser. | No. L'autenticazione legacy continua a funzionare fino al completamento del processo di registrazione. | Sì. Allo scadere della sessione è richiesta la registrazione ad Azure Multi-Factor Authentication.| Sì. Allo scadere del token di accesso è richiesta la registrazione ad Azure Multi-Factor Authentication. |
| Enforced | L'utente è registrato per utente in Multi-Factor Authentication di Azure. Se l'utente non ha ancora registrato i metodi di autenticazione, riceve una richiesta di registrazione al successivo accesso usando l'autenticazione moderna, ad esempio tramite un Web browser. Gli utenti che completano la registrazione in stato *abilitato* vengono spostati automaticamente nello stato *applicato* . | Sì. Le app richiedono password per le app. | Sì. Al momento dell'accesso è necessario Multi-Factor Authentication di Azure. | Sì. Al momento dell'accesso è necessario Multi-Factor Authentication di Azure. |

Tutti gli utenti iniziano con *Disabilitato*. Quando si registrano gli utenti in Multi-Factor Authentication di Azure per utente, il relativo stato diventa *attivato*. Quando gli utenti abilitati accedono e completano il processo di registrazione, il relativo stato viene modificato in *Applicato*. Gli amministratori possono spostare gli utenti tra gli Stati, inclusi quelli da *applicare* a *abilitato* o *disabilitato*.

> [!NOTE]
> Se l'autenticazione a più fattori per utente viene riabilitata per un utente e l'utente non esegue di nuovo la registrazione, il loro stato di autenticazione a più fattori non passa da *abilitato* a *applicato* nell'interfaccia utente di gestione dell'autenticazione a più fattori L'amministratore deve spostare l'utente direttamente in *applicato*.

## <a name="view-the-status-for-a-user"></a>Visualizzare lo stato di un utente

Per visualizzare e gestire gli stati utente, completare i passaggi seguenti per accedere alla pagina portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
1. Cercare e selezionare *Azure Active Directory*, quindi selezionare **Utenti** > **Tutti gli utenti**.
1. Selezionare **Multi-Factor Authentication**. Potrebbe essere necessario scorrere verso destra per visualizzare l'opzione del menu. Selezionare la schermata di esempio seguente per visualizzare la finestra di portale di Azure completa e il percorso del menu: [ ![ selezionare multi-factor authentication dalla finestra utenti in Azure ad.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Si apre una nuova pagina in cui viene visualizzato lo stato dell'utente, come illustrato nell'esempio seguente.
   ![Screenshot che mostra le informazioni di esempio sullo stato dell'utente per Azure Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Modificare lo stato di un utente

Per modificare lo stato di Azure Multi-Factor Authentication per utente per un utente, seguire questa procedura:

1. Usare i passaggi precedenti per [visualizzare lo stato di un utente](#view-the-status-for-a-user) per ottenere la pagina **utenti** di Azure multi-factor authentication.
1. Trovare l'utente che si vuole abilitare per Multi-Factor Authentication Azure per utente. Potrebbe essere necessario modificare la visualizzazione nella parte superiore impostandola su **utenti**.
   ![Selezionare l'utente per cui modificare lo stato dalla scheda utenti](./media/howto-mfa-userstates/enable1.png)
1. Selezionare la casella accanto ai nomi degli utenti di cui si vuole modificare lo stato.
1. A destra, sotto **Azioni rapide**, scegliere **Abilita** o **Disabilita**. Nell'esempio seguente l'utente *John Smith* presenta un segno di spunta a fianco del nome ed è abilitato per l'uso: ![Per abilitare l'utente selezionato, fare clic su Abilita nel menu Azioni rapide](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Gli utenti *abilitati* diventano automaticamente *Applicati* quando si registrano ad Azure Multi-Factor Authentication. Non modificare manualmente lo stato *utente per impostarlo, a meno* che l'utente non sia già registrato o se è accettabile che l'utente sperimenti le interruzioni nelle connessioni ai protocolli di autenticazione legacy.

1. Confermare la selezione nella finestra popup che viene visualizzata.

È consigliabile inviare una notifica tramite posta elettronica agli utenti dopo averli abilitati. Informare gli utenti che viene visualizzato un messaggio per richiedere la registrazione al successivo accesso. se l'organizzazione usa app non basate su browser che non supportano l'autenticazione moderna, devono creare password per l'app. Per altre informazioni, consultare la [guida per l'utente finale di Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md) come supporto introduttivo all'uso.

## <a name="change-state-using-powershell"></a>Modificare lo stato con PowerShell

Per modificare lo stato utente usando [Azure AD PowerShell](/powershell/azure/) è necessario modificare il parametro `$st.State` per un account utente. Sono disponibili tre possibili stati per un account utente:

* *Enabled*
* *Applicato*
* *Disabilitato*  

In generale, non spostare gli utenti direttamente nello stato *applicato* , a meno che non siano già registrati per l'autenticazione a più fattori. In tal caso, le app di autenticazione legacy smetteranno di funzionare perché l'utente non ha eseguito la registrazione Multi-Factor Authentication di Azure e ha ottenuto una [password dell'app](howto-mfa-app-passwords.md). In alcuni casi è possibile che si desideri questo comportamento, ma influisca sull'esperienza utente fino a quando l'utente non si registra.

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

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>Convertire gli utenti dall'autenticazione a più fattori per utente all'accesso condizionale

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
> Se l'autenticazione a più fattori viene riabilitata per un utente e l'utente non esegue di nuovo la registrazione, il relativo stato di autenticazione a più fattori non viene sottoposto a transizione da *abilitato* a *applicato* nell'interfaccia utente di gestione In questo caso, l'amministratore deve spostare l'utente direttamente in *applicato*.

## <a name="next-steps"></a>Passaggi successivi

Per configurare le impostazioni di Azure Multi-Factor Authentication, vedere  [configurare le impostazioni di azure multi-factor authentication](howto-mfa-mfasettings.md).

Per la gestione delle impostazioni utente per Azure Multi-Factor Authentication, vedere l'articolo [Gestire le impostazioni utente con Azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Per comprendere il motivo per cui all'utente è stato richiesto o non è stato richiesto di eseguire l'autenticazione a più fattori, vedere [Report di Azure Multi-Factor Authentication](howto-mfa-reporting.md).