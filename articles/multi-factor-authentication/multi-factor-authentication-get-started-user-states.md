---
title: Stati utente in Microsoft Azure Multi-Factor Authentication
description: Informazioni sugli stati utente in Azure MFA.
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 1869b7a4ef42536a3cd909ba2983ae0fe97185a9
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---

# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Come richiedere la verifica in due passaggi per un utente o un gruppo

Sono disponibili due modi per richiedere la verifica in due passaggi. Il primo prevede l'abilitazione di ogni singolo utente per Azure Multi-Factor Authentication (MFA). Gli utenti abilitati singolarmente devono sempre eseguire la verifica in due passaggi (con alcune eccezioni, ad esempio se accedono da indirizzi IP attendibili o se è attiva la funzionalità relativa ai dispositivi memorizzati). Il secondo modo prevede la configurazione di criteri di accesso condizionale che richiedano la verifica in due passaggi in presenza di determinate condizioni.

>[!TIP] 
>Scegliere uno dei due metodi per richiedere la verifica in due passaggi, non entrambi. L'abilitazione di un utente per Azure MFA sostituisce infatti eventuali criteri di accesso condizionale.

## <a name="which-option-is-right-for-you"></a>Scelta dell'opzione più adatta alle proprie esigenze

L'**abilitazione di Azure MFA modificando gli stati utente** è l'approccio tradizionalmente usato per richiedere la verifica in due passaggi. Può essere usato sia per Azure MFA nel cloud sia per Azure MFA Server. Tutti gli utenti abilitati devono seguire la stessa procedura, ovvero effettuare la verifica in due passaggi ogni volta che accedono. L'abilitazione di un utente sostituisce eventuali criteri di accesso condizionale in vigore per l'utente. 

L'**abilitazione di Azure MFA con criteri di accesso condizionale** è un approccio più flessibile per richiedere la verifica in due passaggi. Può essere tuttavia usato solo per Azure MFA nel cloud e l'accesso condizionale è una [funzionalità a pagamento di Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). È possibile creare criteri di accesso condizionale da applicare sia a gruppi che a singoli utenti. È possibile, ad esempio, assegnare ai gruppi ad alto rischio più restrizioni rispetto ai gruppi a basso rischio oppure richiedere la verifica in due passaggi solo per le app cloud ad alto rischio e non per quelle a basso rischio. 

In entrambi i casi, gli utenti devono registrarsi ad Azure Multi-Factor Authentication la prima volta che accedono dopo l'attivazione dei requisiti. Entrambe le opzioni, inoltre, possono interagire con le [impostazioni di Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md) configurabili.

## <a name="enable-azure-mfa-by-changing-user-status"></a>Abilitare Azure MFA modificando lo stato utente

Gli account utente in modalità Multi-Factor Authentication di Azure presentano i seguenti tre stati distinti:

| Stato | Descrizione | App interessate non basate su browser |
|:---:|:---:|:---:|
| Disabled |Lo stato predefinito per un nuovo utente non registrato alla modalità Multi-Factor Authentication (MFA). |No |
| Enabled |L'utente è stato iscritto ad Azure MFA, ma non ha eseguito la registrazione. Verrà richiesto di eseguire la registrazione al successivo accesso. |No.  Continuano a funzionare fino al completamento della registrazione. |
| Enforced |L'utente è stato iscritto e ha completato il processo di registrazione per Azure MFA. |Sì.  Le app richiedono password per le app. |

Lo stato dell'utente indica se un amministratore ha eseguito la relativa iscrizione in Azure MFA e se l'utente ha completato il processo di registrazione.

Tutti gli utenti iniziano con *disabilitato*. Quando si registrano gli utenti in Azure MFA, il relativo stato cambia in *abilitato*. Quando gli utenti abilitati accedono e completano il processo di registrazione, il relativo stato viene modificato in *applicato*.  

### <a name="view-the-status-for-a-user"></a>Visualizzare lo stato di un utente

Per accedere alla pagina in cui è possibile visualizzare e gestire gli stati utente, procedere come segue:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare ad **Azure Active Directory** > **Utenti e gruppi** > **Tutti gli utenti**.
3. Selezionare **Multi-Factor Authentication**.
   ![Selezionare Multi-Factor Authentication](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. Verrà visualizzata una nuova pagina in cui sono elencati gli stati utente.
   ![Stati utente in Microsoft Azure Multi-Factor Authentication - screenshot](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Modificare lo stato di un utente

1. Usare la procedura precedente per visualizzare la pagina Utenti Multi-Factor Authentication.
2. Trovare l'utente che si vuole abilitare per Azure MFA. Potrebbe essere necessario modificare la visualizzazione nella parte superiore. 
   ![Trova utente - screenshot](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Selezionare la casella accanto al nome.
4. A destra, sotto Azioni rapide, scegliere **Abilita** o **Disabilita**.
   ![Abilitare l'utente selezionato - screenshot](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >Gli utenti *abilitati* diventano automaticamente *applicati* quando si registrano ad Azure MFA. Non è possibile modificare manualmente lo stato di un utente su applicato. 

5. Confermare la selezione nella finestra popup che viene visualizzata. 

È consigliabile inviare una notifica tramite posta elettronica agli utenti dopo averli abilitati. Informarli anche che verrà chiesto loro di eseguire la registrazione al successivo accesso e che, se l'organizzazione usa app non basate su browser che non supportano l'autenticazione moderna, dovranno creare password per l'app. È possibile anche includere un collegamento alla [guida dell'utente finale ad Azure MFA](./end-user/multi-factor-authentication-end-user.md) con informazioni utili per iniziare.

### <a name="use-powershell"></a>Usare PowerShell
Per modificare lo stato dell'utente usando [Azure AD PowerShell](/powershell/azure/overview), modificare `$st.State`. Esistono tre possibili stati:

* Enabled
* Enforced
* Disabled  

Un utente non può essere spostato direttamente sullo stato *Applicato*. Le app non basate su browser smettono di funzionare poiché l'utente non ha effettuato la registrazione a MFA e non ha ottenuto una [password delle app](multi-factor-authentication-whats-next.md#app-passwords). 

L'uso di PowerShell è la scelta migliore quando è necessario abilitare utenti in massa. Creare uno script di PowerShell che scorra un intero elenco di utenti e li abiliti:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Di seguito è fornito un esempio:

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

L'accesso condizionale è una funzionalità a pagamento di Azure Active Directory caratterizzata da numerose opzioni di configurazione. Per creare criteri di accesso condizionale, seguire questa procedura. Per altre informazioni, vedere [Accesso condizionale in Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Passare **Azure Active Directory** > **Accesso condizionale**.
3. Selezionare **Nuovi criteri**.
4. In **Assegnazioni** selezionare **Utenti e gruppi**. Usare le schede **Includi** ed **Escludi** per specificare quali utenti e i gruppi dovranno essere gestiti dai criteri.
5. In **Assegnazioni** selezionare **App cloud**. Scegliere di includere **Tutte le app cloud**.
6. In **Controlli di accesso** selezionare **Concedi**. Selezionare **Richiedi autenticazione a più fattori**.
7. Impostare **Abilita criterio** su **On** e quindi selezionare **Salva**.

Le altre opzioni relative ai criteri di accesso condizionale consentono di specificare esattamente quando deve essere richiesta la verifica in due passaggi. È possibile, ad esempio, creare criteri in base ai quali se un terzista tenta di accedere all'app per gli acquisti da reti non attendibili su dispositivi non appartenenti al dominio, è necessario richiedere la verifica in due passaggi. 

## <a name="next-steps"></a>Passaggi successivi

- Ottenere suggerimenti sulle [Procedure consigliate per l'accesso condizionale](../active-directory/active-directory-conditional-access-best-practices.md)

- Gestire le impostazioni di Multi-Factor Authentication per [utenti e dispositivi](multi-factor-authentication-manage-users-and-devices.md)
