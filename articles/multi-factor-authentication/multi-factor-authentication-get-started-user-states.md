---
title: Stati utente in Microsoft Azure Multi-Factor Authentication
description: Informazioni sugli stati utente in Azure multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: ad8d531d633eb65fe90404fdab0499b8e5332db6
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Come richiedere la verifica in due passaggi per un utente o un gruppo

È possibile eseguire una delle due approcci per la richiesta di verifica in due passaggi. La prima consiste nel consentire a ogni utente per Azure multi-Factor Authentication (MFA). Quando gli utenti vengono abilitati singolarmente, eseguono la verifica ogni volta che accedono (con alcune eccezioni, ad esempio quando si accede dall'indirizzo IP attendibile indirizzi o quando il _dispositivi memorizzati_ funzionalità è attivata). Il secondo modo prevede la configurazione di criteri di accesso condizionale che richiedano la verifica in due passaggi in presenza di determinate condizioni.

>[!TIP] 
>Scegliere uno dei due metodi per richiedere la verifica in due passaggi, non entrambi. Abilitazione di un utente per Azure multi-Factor Authentication esegue l'override di tutti i criteri di accesso condizionale.

## <a name="which-option-is-right-for-you"></a>Quale opzione è adatta alle proprie esigenze?

**Attivazione di Azure multi-Factor Authentication, modificare stati utente** è l'approccio tradizionale per la richiesta di verifica in due passaggi. Può essere usato sia per Azure MFA nel cloud sia per Azure MFA Server. Tutti gli utenti che si attivano eseguono la verifica ogni volta che effettuano l'accesso. Abilitazione di un utente esegue l'override di eventuali criteri di accesso condizionale che potrebbero influire sull'utente. 

**Abilitazione di Azure multi-Factor Authentication con un criterio di accesso condizionale** è un approccio più flessibile per la richiesta di verifica in due passaggi. Funziona solo per Azure MFA nel cloud, tuttavia, e _accesso condizionale_ è un [a pagamento di funzionalità di Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). È possibile creare criteri di accesso condizionale da applicare sia a gruppi che a singoli utenti. È possibile, ad esempio, assegnare ai gruppi ad alto rischio più restrizioni rispetto ai gruppi a basso rischio oppure richiedere la verifica in due passaggi solo per le app cloud ad alto rischio e non per quelle a basso rischio. 

Entrambe le opzioni di richiedono agli utenti di registrarsi per Azure multi-Factor Authentication, la prima volta, che effettuano l'accesso dopo i requisiti di attivazione. Entrambe le opzioni funzionano anche con il configurabile [le impostazioni di Azure multi-Factor Authentication](multi-factor-authentication-whats-next.md).

## <a name="enable-azure-mfa-by-changing-user-status"></a>Abilitare Azure MFA modificando lo stato utente

Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

| Status | DESCRIZIONE | App interessate non basate su browser | Applicazioni browser interessate | Autenticazione moderna interessato |
|:---:|:---:|:---:|:--:|:--:|
| Disabled |Lo stato predefinito per un nuovo utente non registrato in Azure MFA. |No  |No  |No  |
| Attivato |L'utente è stato iscritto ad Azure MFA, ma non ha eseguito la registrazione. Tali riceve una richiesta di registrazione al successivo che accesso. |di serie  Continuano a funzionare fino al completamento della registrazione. | Sì. Dopo la sessione scade, la registrazione di Azure MFA è obbligatoria.| Sì. Alla scadenza del token di accesso, la registrazione di Azure MFA è obbligatoria. |
| Enforced |L'utente è stato iscritto e ha completato il processo di registrazione per Azure MFA. |Sì.  Le app richiedono password per le app. |Sì. Azure MFA è richiesto all'accesso. | Sì. Azure MFA è richiesto all'accesso. |

Lo stato dell'utente indica se un amministratore ha eseguito la relativa iscrizione in Azure MFA e se l'utente ha completato il processo di registrazione.

Tutti gli utenti si incomincia *disabilitato*. Quando si registra gli utenti di Azure MFA, il relativo stato cambia da *abilitato*. Quando gli utenti abilitati l'accesso e completare il processo di registrazione, il relativo stato viene modificato per *applicato*.  

### <a name="view-the-status-for-a-user"></a>Visualizzare lo stato di un utente

Per accedere alla pagina in cui è possibile visualizzare e gestire gli stati utente, procedere come segue:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
   ![Selezionare Multi-Factor Authentication](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. Verrà visualizzata una nuova pagina che visualizza gli stati utente.
   ![Stati utente in Microsoft Azure Multi-Factor Authentication - screenshot](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Modificare lo stato di un utente

1. Utilizzare i passaggi precedenti per ottenere Azure multi-Factor Authentication **utenti** pagina.
2. Trovare l'utente a cui che si desidera abilitare per Azure MFA. Potrebbe essere necessario modificare la visualizzazione nella parte superiore. 
   ![Trova utente - screenshot](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Selezionare la casella accanto al nome.
4. A destra, in **rapide**, scegliere **abilitare** o **disabilitare**.
   ![Abilitare l'utente selezionato - screenshot](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >*Abilitato* agli utenti vengono automaticamente attivati *applicato* quando si registra per Azure MFA. Non modificare manualmente lo stato utente *applicato*. 

5. Confermare la selezione nella finestra popup che viene visualizzata. 

Dopo aver attivato gli utenti, inviare notifica tramite posta elettronica. Informarli anche che verrà chiesto loro di eseguire la registrazione al successivo accesso e che, Inoltre, se l'organizzazione Usa le app non basate su browser che non supportano l'autenticazione moderna, dovranno creare password dell'app. È inoltre possibile includere un collegamento per il [Guida dell'utente finale di Azure MFA](./end-user/multi-factor-authentication-end-user.md) per iniziare.

### <a name="use-powershell"></a>Usare PowerShell
Per modificare lo stato utente utilizzando [Azure AD PowerShell](/powershell/azure/overview), modificare `$st.State`. Esistono tre possibili stati:

* Attivato
* Enforced
* Disabled  

Un utente non può essere spostato direttamente sullo stato *Applicato*. In caso contrario, le app non basate su browser smettono di funzionare perché l'utente non ha effettuato la registrazione di Azure MFA e ottenuto una [password di app](multi-factor-authentication-whats-next.md#app-passwords). 

L'uso di PowerShell è la scelta migliore quando è necessario abilitare utenti in massa. Creare uno script di PowerShell che scorra un intero elenco di utenti e li abiliti:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Lo script seguente è riportato un esempio:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Abilitare Azure MFA con criteri di accesso condizionale

_Accesso condizionale_ è una funzionalità a pagamento di Azure Active Directory, con numerose opzioni di configurazione. Per creare criteri di accesso condizionale, seguire questa procedura. Per altre informazioni, vedere [Accesso condizionale in Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare **Azure Active Directory** > **Accesso condizionale**.
3. Selezionare **Nuovi criteri**.
4. In **Assegnazioni** selezionare **Utenti e gruppi**. Utilizzare il **Include** e **escludere** schede per specificare quali utenti e gruppi consente di gestire i criteri.
5. In **Assegnazioni** selezionare **App cloud**. Scegliere di includere **Tutte le app cloud**.
6. In **Controlli di accesso** selezionare **Concedi**. Selezionare **Richiedi autenticazione a più fattori**.
7. Attivare **abilitare i criteri di** per **su**, quindi selezionare **salvare**.

Altre opzioni di criteri di accesso condizionale offrono la possibilità di specificare esattamente quando è necessaria la verifica in due passaggi. Ad esempio, è possibile apportare un criterio come: quando terzisti tentano di accedere nostra app approvvigionamento da reti non attendibili nei dispositivi che non sono aggiunti a un dominio, richiedere la verifica in due passaggi. 

## <a name="next-steps"></a>Passaggi successivi

- Suggerimenti [procedure consigliate per l'accesso condizionale](../active-directory/active-directory-conditional-access-best-practices.md).

- Gestire le impostazioni di Azure multi-Factor Authentication per [gli utenti e dispositivi](multi-factor-authentication-manage-users-and-devices.md).
