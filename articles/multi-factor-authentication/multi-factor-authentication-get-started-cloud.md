---
title: Introduzione ad Azure MFA nel cloud | Documentazione Microsoft
description: "Questa è la pagina relativa a Microsoft Azure Multi-Factor Authentication che descrive come iniziare a usare Azure MFA nel cloud."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d7d909184eb129e46ffc350505101e68c1426c46


---
# <a name="getting-started-with-azure-multifactor-authentication-in-the-cloud"></a>Introduzione ad Azure Multi-Factor Authentication nel cloud
Questo articolo illustra come iniziare a usare Azure Multi-Factor Authentication nel cloud.

> [!NOTE]
> La documentazione seguente fornisce informazioni su come abilitare gli utenti tramite il **portale di Azure classico**. Per informazioni su come configurare Azure Multi-Factor Authentication per gli utenti di Office 365, vedere [Configurare Multi-Factor Authentication per Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)
> 
> 

![MFA nel cloud](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisites"></a>Prerequisiti
Per abilitare Azure multi-Factor Authentication per gli utenti, è necessario soddisfare i prerequisiti seguenti.

1. [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/) : se non si ha già una sottoscrizione ad Azure, è necessario iscriversi. Se si è appena iniziato a usare il servizio Azure MFA, è possibile usare una sottoscrizione di valutazione
2. [Creare un provider di Multi-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md) e assegnarlo alla directory o [assegnare licenze agli utenti](multi-factor-authentication-get-started-assign-licenses.md)

> [!NOTE]
> Le licenze sono disponibili per gli utenti che hanno Azure MFA, Azure AD Premium o Enterprise Mobility Suite (EMS).  MFA è incluso in Azure AD Premium e in EMS. Se è disponibile un numero di licenze sufficiente, non è necessario creare un provider di autenticazione.
> 
> 

## <a name="turn-on-twostep-verification-for-users"></a>Attivare la verifica in due passaggi per gli utenti
Per iniziare a richiedere la verifica in due passaggi per un utente, cambiare lo stato dell'utente da disabilitato ad abilitato.  Per altre informazioni sugli stati utente, vedere [Stati utente in Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

Per abilitare MFA per gli utenti, seguire questa procedura.

### <a name="to-turn-on-multifactor-authentication"></a>Per attivare la modalità Multi-Factor Authentication
1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) come amministratore.
2. A sinistra fare clic su **Active Directory**.
3. In Directory selezionare la directory relativa all'utente da abilitare.
   ![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. Nella parte superiore fare clic su **Utenti**.
5. Nella parte inferiore della pagina fare clic su **Gestisci Multi-Factor Auth**. Si apre una nuova scheda del browser.
   ![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Trovare l'utente da abilitare per la verifica in due passaggi. Potrebbe essere necessario modificare la visualizzazione nella parte superiore. Assicurarsi che lo stato sia **disabilitato**
   ![Abilitare l'utente](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. **Selezionare** la casella accanto al nome.
8. A destra fare clic su **Abilita**.
   ![Attivare l'utente](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. Fare clic su **Abilita Multi-Factor Auth**.
   ![Attivare l'utente](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. Si noti che lo stato dell'utente è cambiato da **disabilitato** in **abilitato**.
    ![Attivare gli utenti](./media/multi-factor-authentication-get-started-cloud/user.png)

È consigliabile inviare una notifica tramite posta elettronica agli utenti dopo averli abilitati. Al successivo tentativo di accesso, agli utenti verrà chiesto di registrare il proprio account per la verifica in due passaggi. Una volta che iniziano a usare la verifica in due passaggi, devono anche impostare le password delle app per evitare il blocco delle app non basate su browser.

## <a name="use-powershell-to-automate-turning-on-twostep-verification"></a>Usare PowerShell per automatizzare l'attivazione della verifica in due passaggi
Per modificare lo [stato](multi-factor-authentication-whats-next.md) usando [Azure AD PowerShell](../powershell-install-configure.md), seguire questa procedura.  È possibile modificare `$st.State` in modo che corrisponda a uno degli stati seguenti:

* Attivato
* Enforced
* Disabled  

> [!IMPORTANT]
> Non è consigliabile spostare gli utenti direttamente dallo stato di disabilitazione a quello di applicazione. Le app non basate su browser smettono di funzionare poiché l'utente non ha effettuato la registrazione a MFA e non ha ottenuto una [password delle app](multi-factor-authentication-whats-next.md#app-passwords). Se sono presenti app non basate su browser e sono necessarie password delle app, è consigliabile passare da uno stato disabilitato a uno stato abilitato. In questo modo gli utenti possono registrarsi e ottenere le password delle app. Dopo questa operazione è possibile spostarli nello stato di applicazione.
> 
> 

PowerShell può essere un'opzione utile per abilitare gli utenti in blocco. Attualmente non è disponibile una funzionalità di abilitazione in blocco nel portale di Azure ed è necessario selezionare ogni utente singolarmente. Questa attività può risultare piuttosto dispersiva se il numero di utenti è elevato. È possibile scorrere a ciclo continuo un elenco di utenti e abilitarli creando uno script PowerShell con il codice seguente.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Di seguito è fornito un esempio:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "\*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Per altre informazioni, vedere [Stati utente in Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

## <a name="next-steps"></a>Passaggi successivi
Dopo avere configurato Azure Multi-Factor Authentication nel cloud, è possibile configurare e impostare la distribuzione. Per altri dettagli, vedere [Configurazione di Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md).




<!--HONumber=Nov16_HO2-->


