---
title: Introduzione a Microsoft Azure Multi-Factor Authentication nel cloud
description: Questa è la pagina relativa a Microsoft Azure Multi-Factor Authentication che descrive come iniziare a usare Azure MFA nel cloud.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban

---
# Introduzione ad Azure Multi-Factor Authentication nel cloud
Questo articolo illustra come iniziare a usare Azure multi-Factor Authentication nel cloud.

> [!NOTE]
> La documentazione seguente fornisce informazioni su come abilitare gli utenti tramite il **portale di Azure classico**. Se occorrono informazioni su come configurare Azure multi-Factor Authentication per gli utenti di Office 365, vedere [Configurare l'autenticazione a più fattori per Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=it-IT&rs=it-IT&ad=US)
> 
> 

![MFA nel cloud](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## Prerequisiti
Per abilitare Azure multi-Factor Authentication per gli utenti, è necessario soddisfare i prerequisiti seguenti.

* [Iscriversi per ottenere una sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/): se non si ha già una sottoscrizione ad Azure, è necessario iscriversi. Se si è appena iniziato a usare il servizio Azure MFA, è possibile usare una sottoscrizione di valutazione
* [Creare un provider di Multi-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md) e assegnarlo alla directory o [assegnare licenze agli utenti](multi-factor-authentication-get-started-assign-licenses.md)

> [!NOTE]
> Le licenze sono disponibili per gli utenti che hanno Azure MFA, Azure AD Premium o Enterprise Mobility Suite (EMS). MFA è incluso in Azure AD Premium e in EMS. Se è disponibile un numero di licenze sufficiente, non è necessario creare un provider di autenticazione.
> 
> 

## Attivare l'autenticazione a più fattori per gli utenti
Per attivare l'autenticazione a più fattori per un utente, è sufficiente modificare lo stato dell'utente da Disattivato in Attivato. Per altre informazioni sugli stati utente, vedere l'articolo relativo agli [stati utente in Azure multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

Per abilitare MFA per gli utenti, seguire questa procedura.

### Per attivare la modalità Multi-Factor Authentication
- - -
1. Accedere al **portale di Azure classico** come amministratore.
2. A sinistra fare clic su **Active Directory**.
3. In **Directory** fare clic sulla directory relativa all'utente che si desidera attivare. ![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. Nella parte superiore fare clic su **Utenti**.
5. Nella parte inferiore della pagina fare clic su **Gestisci Multi-Factor Auth**. ![Fare clic su Directory](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Verrà aperta una nuova scheda del browser. Trovare l'utente che si desidera abilitare alla modalità Multi-Factor Authentication Potrebbe essere necessario modificare la visualizzazione nella parte superiore. Assicurarsi che lo stato sia **Disattivato.** ![Attivare l'utente](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7. **Selezionare** la casella accanto al nome.
8. A destra fare clic su **Abilita**. ![Attivare l'utente](./media/multi-factor-authentication-get-started-cloud/user1.png)
9. Fare clic su **Abilita Multi-Factor Auth**. ![Attivare l'utente](./media/multi-factor-authentication-get-started-cloud/enable2.png)
10. Si noterà che lo stato dell'utente è cambiato da **Disattivato** in **Attivato**. ![Attivare gli utenti](./media/multi-factor-authentication-get-started-cloud/user.png)
11. Dopo avere abilitato gli utenti, è consigliabile inviare una notifica tramite posta elettronica. È anche opportuno informarli su come possono usare le app non basate su browser per evitare di essere bloccati.

## Automatizzare l'attivazione dell'autenticazione a più fattori con PowerShell
Per modificare lo [stato](multi-factor-authentication-whats-next.md) usando [Azure AD PowerShell](../powershell-install-configure.md), si può seguire questa procedura. È possibile modificare `$st.State` in modo che corrisponda a uno degli stati seguenti:

* Enabled
* Enforced
* Disabled

> [!IMPORTANT]
> Tenere presente che se si passa direttamente dallo stato Disattivato allo stato Applicato, i client di autenticazione obsoleti smetteranno di funzionare, perché l'utente non ha eseguito la registrazione a MFA e non ha ottenuto una [password dell'app](multi-factor-authentication-whats-next.md#app-passwords). Se sono presenti client di autenticazione obsoleti e sono necessarie password dell'app, è consigliabile passare da uno stato Disattivato ad Attivato. In questo modo gli utenti potranno registrarsi e ottenere le password dell'app.
> 
> 

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

PowerShell può essere un'opzione utile per abilitare gli utenti in blocco. Attualmente non è disponibile una funzionalità di abilitazione in blocco nel portale di Azure ed è necessario selezionare ogni utente singolarmente. Questa attività può risultare piuttosto dispersiva se si hanno molti utenti. Creando uno script di PowerShell con il codice precedente, è possibile scorrere un elenco di utenti e abilitarli. Di seguito è fornito un esempio:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Per altre informazioni sugli stati utente, vedere [Stati utente in Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md)

## Passaggi successivi
Dopo avere configurato l'autenticazione a più fattori nel cloud, è possibile configurare e impostare la distribuzione. Vedere [Configurazione di Azure Multi-Factor Authentication].

<!---HONumber=AcomDC_0921_2016-->