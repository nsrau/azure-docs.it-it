---
title: Stati utente in Microsoft Azure Multi-Factor Authentication
description: Informazioni sugli stati utente in Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 6bd07439d4c6b1ccb5919fbfb286f714bac3b628
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158897"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Come richiedere la verifica in due passaggi per un utente

Sono disponibili due modi per richiedere la verifica in due passaggi. Il primo prevede l'abilitazione di ogni utente per Azure Multi-Factor Authentication (MFA). Gli utenti abilitati singolarmente devono eseguire la verifica in due passaggi a ogni accesso, con alcune eccezioni, ad esempio se accedono da indirizzi IP attendibili o quando è attiva la funzionalità relativa ai _dispositivi memorizzati_. Il secondo modo prevede la configurazione di criteri di accesso condizionale che richiedano la verifica in due passaggi in presenza di determinate condizioni.

> [!TIP]
> Scegliere uno dei due metodi per richiedere la verifica in due passaggi, non entrambi. L'abilitazione di un utente per Azure Multi-Factor Authentication sostituisce infatti eventuali criteri di accesso condizionale.

## <a name="choose-how-to-enable"></a>Scegliere come eseguire l'abilitazione

**Abilitato modificando lo stato utente**: si tratta del metodo tradizionalmente usato per richiedere la verifica in due passaggi ed è illustrato in questo articolo. Può essere usato sia con Azure MFA nel cloud sia con Azure MFA Server. Con questo metodo gli utenti devono eseguire la verifica in due passaggi **ogni volta** che eseguono l'accesso e vengono ignorati i criteri di accesso condizionale.

Abilitato da criteri di accesso condizionale: è il mezzo più flessibile per abilitare la verifica in due passaggi per gli utenti. L'abilitazione mediante criteri di accesso condizionale funziona solo per Azure MFA nel cloud ed è una funzione Premium di Azure AD. Per altre informazioni su questo metodo, vedere [Implementare Azure Multi-Factor Authentication basato su cloud](howto-mfa-getstarted.md).

Abilitato da Azure AD Identity Protection: questo metodo usa i criteri di rischio di Azure AD Identity Protection per richiedere la verifica in due passaggi basata solo sul rischio di accesso per tutte le applicazioni cloud. Questo metodo richiede una licenza di Azure Active Directory P2. Per altre informazioni su questo metodo, vedere [Azure Active Directory Identity Protection](../active-directory-identityprotection.md#risky-sign-ins)

> [!Note]
> Altre informazioni sulle licenze e i prezzi sono disponibili nelle pagine relative ai prezzi di [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) e [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

## <a name="enable-azure-mfa-by-changing-user-status"></a>Abilitare Azure MFA modificando lo stato utente

Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

| Status | DESCRIZIONE | App interessate non basate su browser | App interessate basate su browser | Autenticazione moderna interessata |
|:---:|:---:|:---:|:--:|:--:|
| Disabled |Stato predefinito per un nuovo utente non registrato in Azure MFA. |No  |No  |No  |
| Attivato |L'utente è stato iscritto ad Azure MFA, ma non ha eseguito la registrazione. Viene richiesto di eseguire la registrazione al successivo accesso. |No.  Continuano a funzionare fino al completamento della registrazione. | Sì. Dopo la scadenza della sessione, è necessaria la registrazione ad Azure MFA.| Sì. Dopo la scadenza dei token di accesso, è necessaria la registrazione ad Azure MFA. |
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
   ![Trova utente - screenshot](./media/howto-mfa-userstates/enable1.png)
3. Selezionare la casella accanto al nome.
4. A destra, sotto **Azioni rapide** scegliere **Abilita** o **Disabilita**.
   ![Abilitare l'utente selezionato - screenshot](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > Gli utenti *abilitati* diventano automaticamente *applicati* quando si registrano ad Azure MFA. Non modificare manualmente lo stato di un utente su *Applicato*.

5. Confermare la selezione nella finestra popup che viene visualizzata.

È consigliabile inviare una notifica tramite posta elettronica agli utenti dopo averli abilitati. Informarli anche che verrà chiesto loro di eseguire la registrazione al successivo accesso e che, se l'organizzazione usa app non basate su browser che non supportano l'autenticazione moderna, devono creare password per l'app. È possibile anche includere un collegamento alla [guida dell'utente finale ad Azure MFA](../user-help/multi-factor-authentication-end-user.md) con informazioni utili per iniziare.

### <a name="use-powershell"></a>Usare PowerShell

Per modificare lo stato dell'utente usando [Azure AD PowerShell](/powershell/azure/overview), modificare `$st.State`. Esistono tre possibili stati:

* Attivato
* Enforced
* Disabled  

Un utente non può essere spostato direttamente sullo stato *Applicato*. Se si esegue questa operazione, le app non basate su browser smettono di funzionare poiché l'utente non ha effettuato la registrazione ad Azure MFA e non ha ottenuto una [password delle app](howto-mfa-mfasettings.md#app-passwords).

L'uso di PowerShell è la scelta migliore quando è necessario abilitare utenti in massa. Creare uno script di PowerShell che scorra un intero elenco di utenti e li abiliti:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Di seguito è riportato uno script di esempio:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="next-steps"></a>Passaggi successivi

Per configurare impostazioni aggiuntive, ad esempio gli indirizzi IP attendibili, i messaggi vocali personalizzati e gli avvisi di illeciti, vedere l'articolo [Configurare le impostazioni di Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)

Informazioni sulla gestione delle impostazioni utente per Azure Multi-Factor Authentication sono disponibili nell'articolo [Gestire le impostazioni utente nel cloud con Azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md)
