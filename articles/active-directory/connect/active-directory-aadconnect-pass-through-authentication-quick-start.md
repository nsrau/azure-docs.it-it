---
title: Autenticazione pass-through di Azure AD - Avvio rapido | Microsoft Docs
description: "Questo articolo descrive le attività iniziali per l'autenticazione pass-through di Azure Active Directory (Azure AD)."
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 8adca38cc5a783abfe29725dbb0a201de4183dad
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticazione pass-through di Azure Active Directory - Avvio rapido

## <a name="deploy-azure-ad-pass-through-authentication"></a>Distribuire l'autenticazione pass-through di Azure AD

L'autenticazione pass-through di Azure Active Directory (Azure AD) consente agli utenti di accedere ad applicazioni locali e basate sul cloud usando le stesse password. Prevede infatti l'accesso degli utenti tramite la convalida delle password direttamente in Active Directory locale.

>[!IMPORTANT]
>Se si usa questa funzionalità tramite una versione di anteprima, assicurarsi di aggiornare le versioni di anteprima degli agenti di autenticazione attenendosi alle istruzioni fornite in [Autenticazione pass-through di Azure Active Directory - Aggiornare gli agenti di autenticazione di anteprima](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Per distribuire l'autenticazione pass-through, seguire queste istruzioni:

## <a name="step-1-check-the-prerequisites"></a>Passaggio 1: Verificare i prerequisiti

Accertarsi che siano soddisfatti i prerequisiti seguenti.

### <a name="in-the-azure-active-directory-admin-center"></a>Nell'interfaccia di amministrazione di Azure Active Directory

1. Creare un account amministratore globale di tipo solo cloud nel tenant di Azure AD. In questo modo è possibile gestire la configurazione del tenant in caso di errore o mancata disponibilità dei servizi locali. Informazioni su come [aggiungere un account amministratore globale di tipo solo cloud](../active-directory-users-create-azure-portal.md). Il completamento di questo passaggio è fondamentale ed evita di rimanere bloccati fuori dal tenant.
2. Aggiungere uno o più [nomi di dominio personalizzati](../active-directory-domains-add-azure-portal.md) al tenant di Azure AD. Gli utenti possono accedere usando uno di questi nomi di dominio.

### <a name="in-your-on-premises-environment"></a>Nell'ambiente locale

1. Identificare un server che esegue Windows Server 2012 R2 o versione successiva per l'esecuzione di Azure AD Connect. Aggiungere il server alla stessa foresta Active Directory degli utenti di cui devono essere convalidate le password.
2. Installare la [versione più recente di Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) nel server identificato nel passaggio precedente. Se Azure AD Connect è già in esecuzione, assicurarsi che la versione sia 1.1.644.0 o successiva.

    >[!NOTE]
    >Le versioni 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 di Azure AD Connect presentano un problema correlato alla sincronizzazione dell'hash delle password. Se _non_ si prevede di usare la sincronizzazione dell'hash delle password in combinazione con l'autenticazione pass-through, leggere le [note sulla versione di Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470).

3. Identificare un server aggiuntivo (che esegue Windows Server 2012 R2 o versione successiva) in cui eseguire un agente di autenticazione autonomo. La versione dell'agente di autenticazione deve essere 1.5.193.0 o successiva. Questo server aggiuntivo è necessario per garantire la disponibilità elevata delle richieste di accesso. Aggiungere il server alla stessa foresta Active Directory degli utenti di cui devono essere convalidate le password.
4. Se è presente un firewall tra i server e Azure AD, è necessario configurare gli elementi seguenti:
   - Assicurarsi che gli agenti di autenticazione possano effettuare richieste *in uscita* ad Azure AD sulle porte seguenti:
   
    | Numero della porta | Uso |
    | --- | --- |
    | **80** | Scarica gli elenchi di revoche di certificati (CRL) durante la convalida del certificato SSL |
    | **443** | Gestisce tutte le comunicazioni in uscita con il servizio |
   
    Se il firewall applica regole in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizi di rete.
   - Se il firewall o il proxy consente l'inserimento di DNS nell'elenco elementi consentiti, aggiungere le connessioni a **\*.msappproxy.net** e **\*.servicebus.windows.net** all'elenco elementi consentiti. In caso contrario, è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653), che vengono aggiornati ogni settimana.
   - Gli agenti di autenticazione devono poter accedere a **login.windows.net** e **login.microsoftonline.net** per la registrazione iniziale. Aprire il firewall anche per questi URL.
   - Per la convalida del certificato, sbloccare questi URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** e **www.microsoft.com:80**. Poiché vengono usati per la convalida del certificato con altri prodotti Microsoft, è possibile che questi URL siano già sbloccati.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Passaggio 2: Abilitare il supporto di Exchange ActiveSync (facoltativo)

Per abilitare il supporto di Exchange ActiveSync, seguire queste istruzioni:

1. Avviare [PowerShell per Exchange](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) ed eseguire il comando seguente:
```
Get-OrganizationConfig | fl per*
```

2. Controllare il valore dell'impostazione `PerTenantSwitchToESTSEnabled`. Se il valore è **true**, il tenant è correttamente configurato, come avviene, in genere, per la maggior parte dei clienti. Se il valore è **false**, eseguire il comando seguente:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Verificare che il valore di `PerTenantSwitchToESTSEnabled` sia ora impostato su **true**. Attendere un'ora prima di proseguire con il passaggio successivo.

Per eventuali problemi durante questo passaggio, consultare la [guida per la risoluzione dei problemi](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues).

## <a name="step-3-enable-the-feature"></a>Passaggio 3: Abilitare la funzionalità

Abilitare l'autenticazione pass-through attraverso [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>L'autenticazione pass-through può essere abilitata nel server primario o di gestione temporanea di Azure AD Connect. È comunque consigliabile abilitarla dal server primario.

Se si installa Azure AD Connect per la prima volta, scegliere il [percorso di installazione personalizzato](active-directory-aadconnect-get-started-custom.md). Nella pagina **Accesso utente** scegliere **Autenticazione pass-through** come **Metodo di accesso**. Al completamento, nello stesso server di Azure AD Connect viene installato un agente di autenticazione pass-through. Viene inoltre abilitata la funzionalità di autenticazione pass-through nel tenant.

![Azure AD Connect: Accesso utente](./media/active-directory-aadconnect-sso/sso3.png)

Se Azure AD Connect è già installato (usando il percorso di [installazione rapida](active-directory-aadconnect-get-started-express.md) o di [installazione personalizzata](active-directory-aadconnect-get-started-custom.md)), scegliere l'attività **Cambia l'accesso utente** in Azure AD Connect e quindi fare clic su **Avanti**. Selezionare quindi **Autenticazione pass-through** come metodo di accesso. Al completamento, nello stesso server di Azure AD Connect viene installato un agente di autenticazione e la funzionalità è abilitata per il tenant.

![Azure AD Connect: Cambia l'accesso utente](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>L'autenticazione pass-through è una funzionalità a livello di tenant. La sua attivazione influisce sugli accessi degli utenti per _tutti_ i domini gestiti nel tenant. Se si passa da AD FS (Active Directory Federation Services) all'autenticazione pass-through, è consigliabile attendere almeno 12 ore prima di arrestare l'infrastruttura AD FS. Questo tempo di attesa è necessario per garantire che gli utenti possano continuare ad accedere a Exchange ActiveSync durante la transizione.

## <a name="step-4-test-the-feature"></a>Passaggio 4: Testare la funzionalità

Seguire queste istruzioni per verificare che l'autenticazione pass-through sia stata attivata correttamente:

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Nel riquadro sinistro selezionare **Azure Active Directory**.
3. Selezionare **Azure AD Connect**.
4. Verificare che la funzionalità **Autenticazione pass-through** sia impostata su **Abilitato**.
5. Selezionare **Autenticazione pass-through**. Il riquadro **Autenticazione pass-through** elenca i server in cui sono stati installati gli agenti di autenticazione.

![Interfaccia di amministrazione di Azure Active Directory: riquadro Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Interfaccia di amministrazione di Azure Active Directory: riquadro Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

Dopo questo passaggio, gli utenti di tutti i domini gestiti nel tenant possono accedere usando l'autenticazione pass-through. Gli utenti dei domini federati continueranno invece a eseguire l'accesso con AD FS o qualsiasi altro provider di federazione configurato in precedenza. Se si converte un dominio da federato a gestito, tutti gli utenti del dominio inizieranno automaticamente a eseguire l'accesso usando l'autenticazione pass-through. La funzionalità di autenticazione pass-through non ha alcun impatto sugli utenti solo cloud.

## <a name="step-5-ensure-high-availability"></a>Passaggio 5: Garantire la disponibilità elevata

Se si prevede di distribuire l'autenticazione pass-through in un ambiente di produzione, è necessario installare un agente di autenticazione autonomo. Installare questo secondo agente di autenticazione in un server _diverso_ da quello dove è in esecuzione Azure AD Connect e il primo agente di autenticazione. Questa configurazione fornisce la disponibilità elevata delle richieste di accesso. Per distribuire un agente di autenticazione, seguire queste istruzioni:

1. Scaricare la versione più recente dell'agente di autenticazione (versione 1.5.193.0 o successiva). Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Nel riquadro sinistro selezionare **Azure Active Directory**.
3. Selezionare **Azure AD Connect**, **Autenticazione pass-through** e quindi **Scarica agente**.
4. Fare clic sul pulsante **Accetta le condizioni e scarica**.
5. Installare la versione più recente dell'agente di autenticazione eseguendo il file eseguibile scaricato nel passaggio precedente. Quando vengono richieste, fornire le credenziali di amministratore globale del tenant.

![Interfaccia di amministrazione di Azure Active Directory: pulsante Scarica per l'agente di autenticazione](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Interfaccia di amministrazione di Azure Active Directory: riquadro Scarica agente](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>È possibile scaricare anche l'[agente di autenticazione di Azure Active Directory](https://aka.ms/getauthagent). Assicurarsi di leggere e accettare le [Condizioni d'uso](https://aka.ms/authagenteula) dell'agente di autenticazione _prima_ di installarlo.

## <a name="next-steps"></a>Passaggi successivi
- [Blocco smart](active-directory-aadconnect-pass-through-authentication-smart-lockout.md): apprendere come configurare la funzionalità di blocco smart nel tenant per proteggere gli account utente.
- [Limitazioni correnti](active-directory-aadconnect-pass-through-authentication-current-limitations.md): informazioni su quali scenari sono supportati con l'autenticazione pass-through e quali non lo sono.
- [Approfondimento tecnico](active-directory-aadconnect-pass-through-authentication-how-it-works.md): comprendere come funziona l'autenticazione pass-through.
- [Domande frequenti](active-directory-aadconnect-pass-through-authentication-faq.md): risposte alle domande più frequenti.
- [Risoluzione dei problemi](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): apprendere come risolvere i problemi comuni relativi alla funzionalità di autenticazione pass-through.
- [Approfondimento sulla sicurezza](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md): ottenere informazioni tecniche sulla funzionalità di autenticazione pass-through.
- [Accesso Single Sign-On facile di Azure AD](active-directory-aadconnect-sso.md): ottenere altre informazioni su questa funzionalità complementare.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): usare il forum di Azure Active Directory per inviare richieste di nuove funzionalità.

