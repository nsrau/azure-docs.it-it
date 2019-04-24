---
title: Autenticazione pass-through di Azure AD - Avvio rapido | Microsoft Docs
description: Questo articolo descrive le attività iniziali per l'autenticazione pass-through di Azure Active Directory (Azure AD).
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, installare Active Directory, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba5455680647b90b113d31c55816a2e0b0131b33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60243613"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticazione pass-through di Azure Active Directory: Avvio rapido

## <a name="deploy-azure-ad-pass-through-authentication"></a>Distribuire l'autenticazione pass-through di Azure AD

L'autenticazione pass-through di Azure Active Directory (Azure AD) consente agli utenti di accedere ad applicazioni locali e basate sul cloud usando le stesse password. Prevede infatti l'accesso degli utenti tramite la convalida delle password direttamente in Active Directory locale.

>[!IMPORTANT]
>Se si esegue la migrazione da AD FS (o da altre tecnologia federative) per l'autenticazione pass-through, si consiglia vivamente di seguire la guida per la distribuzione dettagliata pubblicata [qui](https://aka.ms/adfstoPTADPDownload).

Per distribuire l'autenticazione pass-through nel tenant, seguire queste istruzioni:

## <a name="step-1-check-the-prerequisites"></a>Passaggio 1: Verificare i prerequisiti

Accertarsi che siano soddisfatti i prerequisiti seguenti.

### <a name="in-the-azure-active-directory-admin-center"></a>Nell'interfaccia di amministrazione di Azure Active Directory

1. Creare un account amministratore globale di tipo solo cloud nel tenant di Azure AD. In questo modo è possibile gestire la configurazione del tenant in caso di errore o mancata disponibilità dei servizi locali. Informazioni su come [aggiungere un account amministratore globale di tipo solo cloud](../active-directory-users-create-azure-portal.md). Il completamento di questo passaggio è fondamentale ed evita di rimanere bloccati fuori dal tenant.
2. Aggiungere uno o più [nomi di dominio personalizzati](../active-directory-domains-add-azure-portal.md) al tenant di Azure AD. Gli utenti possono accedere usando uno di questi nomi di dominio.

### <a name="in-your-on-premises-environment"></a>Nell'ambiente locale

1. Identificare un server che esegue Windows Server 2012 R2 o versione successiva per l'esecuzione di Azure AD Connect. Se non è già abilitato, [abilitare TLS 1.2 nel server](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Aggiungere il server alla stessa foresta Active Directory degli utenti di cui devono essere convalidate le password.
2. Installare la [versione più recente di Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) nel server identificato nel passaggio precedente. Se Azure AD Connect è già in esecuzione, assicurarsi che la versione sia 1.1.750.0 o successiva.

    >[!NOTE]
    >Le versioni 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 di Azure AD Connect presentano un problema correlato alla sincronizzazione dell'hash delle password. Se _non_ si prevede di usare la sincronizzazione dell'hash delle password in combinazione con l'autenticazione pass-through, leggere le [note sulla versione di Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Identificare uno o più server aggiuntivi (con Windows Server 2012 R2 o versione successiva e con TLS 1.2 abilitato) in cui è possibile eseguire agenti di autenticazione autonomi. Questi server aggiuntivi sono necessari per garantire la disponibilità elevata delle richieste di accesso. Aggiungere i server alla stessa foresta Active Directory degli utenti di cui devono essere convalidate le password.

    >[!IMPORTANT]
    >Negli ambienti di produzione è consigliabile disporre di almeno 3 agenti di autenticazione in esecuzione nel proprio tenant. Esiste un limite di sistema di 40 agenti di autenticazione per ogni tenant. E come procedura ottimale, considerare tutti i server che eseguono gli agenti di autenticazione come sistemi di livello 0 (vedere [riferimento](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Se è presente un firewall tra i server e Azure AD, è necessario configurare gli elementi seguenti:
   - Assicurarsi che gli agenti di autenticazione possano effettuare richieste *in uscita* ad Azure AD sulle porte seguenti:

     | Numero della porta | Uso |
     | --- | --- |
     | **80** | Scarica gli elenchi di revoche di certificati (CRL) durante la convalida del certificato SSL |
     | **443** | Gestisce tutte le comunicazioni in uscita con il servizio |
     | **8080** (facoltativo) | Se la porta 443 non è disponibile, gli agenti di autenticazione segnalano il proprio stato ogni dieci minuti attraverso la porta 8080. Lo stato viene visualizzato nel portale di Azure AD. La porta 8080 _non_ viene usata per l'accesso degli utenti. |
     
     Se il firewall applica regole in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizi di rete.
   - Se il firewall o il proxy consente l'inserimento di DNS nell'elenco elementi consentiti, aggiungere le connessioni a **\*.msappproxy.net** e **\*.servicebus.windows.net** all'elenco elementi consentiti. In caso contrario, è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653), che vengono aggiornati ogni settimana.
   - Gli agenti di autenticazione devono poter accedere a **login.windows.net** e **login.microsoftonline.net** per la registrazione iniziale. Aprire il firewall anche per questi URL.
   - La convalida del certificato, sbloccare questi URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80**, e **www \.microsoft.com:80**. Poiché vengono usati per la convalida del certificato con altri prodotti Microsoft, questi URL potrebbero essere già sbloccati.

## <a name="step-2-enable-the-feature"></a>Passaggio 2: Abilitare la funzionalità

Abilitare l'autenticazione pass-through attraverso [Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>L'autenticazione pass-through può essere abilitata nel server primario o di gestione temporanea di Azure AD Connect. Si consiglia di abilitarla dal server primario. Se si prevede di configurare un server di gestione temporanea di Azure AD Connect, **è necessario** continuare a scegliere l'autenticazione pass-through come opzione di accesso. Scegliendo un'altra opzione, l'autenticazione pass-through verrà **disabilitata** nel server e l'impostazione verrà sovrascritta nel server primario.

Se si installa Azure AD Connect per la prima volta, scegliere il [percorso di installazione personalizzato](how-to-connect-install-custom.md). Nella pagina **Accesso utente** scegliere **Autenticazione pass-through** come **Metodo di accesso**. Al completamento, nello stesso server di Azure AD Connect viene installato un agente di autenticazione pass-through. Viene inoltre abilitata la funzionalità di autenticazione pass-through nel tenant.

![Azure AD Connect: Accesso utente](./media/how-to-connect-pta-quick-start/sso3.png)

Se Azure AD Connect è già installato (usando il percorso di [installazione rapida](how-to-connect-install-express.md) o di [installazione personalizzata](how-to-connect-install-custom.md)), scegliere l'attività **Cambia l'accesso utente** in Azure AD Connect e quindi fare clic su **Avanti**. Selezionare quindi **Autenticazione pass-through** come metodo di accesso. Al completamento, nello stesso server di Azure AD Connect viene installato un agente di autenticazione e la funzionalità è abilitata per il tenant.

![Azure AD Connect: Cambia l'accesso utente](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>L'autenticazione pass-through è una funzionalità a livello di tenant. La sua attivazione influisce sugli accessi degli utenti per _tutti_ i domini gestiti nel tenant. Se si passa da AD FS (Active Directory Federation Services) all'autenticazione pass-through, è consigliabile attendere almeno 12 ore prima di arrestare l'infrastruttura AD FS. Questo tempo di attesa è necessario per garantire che gli utenti possano continuare ad accedere a Exchange ActiveSync durante la transizione. Per altre informazioni sulla migrazione da Active Directory Federation Services all'autenticazione pass-through, vedere il piano di distribuzione dettagliato pubblicato [qui](https://aka.ms/adfstoptadpdownload).

## <a name="step-3-test-the-feature"></a>Passaggio 3: Testare la funzionalità

Seguire queste istruzioni per verificare che l'autenticazione pass-through sia stata attivata correttamente:

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Nel riquadro sinistro selezionare **Azure Active Directory**.
3. Selezionare **Azure AD Connect**.
4. Verificare che la funzionalità **Autenticazione pass-through** sia impostata su **Abilitato**.
5. Selezionare **Autenticazione pass-through**. Il riquadro **Autenticazione pass-through** elenca i server in cui sono stati installati gli agenti di autenticazione.

![Interfaccia di amministrazione di Azure Active Directory: riquadro di Azure AD Connect](./media/how-to-connect-pta-quick-start/pta7.png)

![Interfaccia di amministrazione di Azure Active Directory: riquadro Autenticazione pass-through](./media/how-to-connect-pta-quick-start/pta8.png)

Dopo questo passaggio, gli utenti di tutti i domini gestiti nel tenant possono accedere usando l'autenticazione pass-through. Gli utenti dei domini federati continueranno invece a eseguire l'accesso con AD FS o qualsiasi altro provider di federazione configurato in precedenza. Se si converte un dominio da federato a gestito, tutti gli utenti del dominio inizieranno automaticamente a eseguire l'accesso usando l'autenticazione pass-through. La funzionalità di autenticazione pass-through non ha alcun impatto sugli utenti solo cloud.

## <a name="step-4-ensure-high-availability"></a>Passaggio 4: Garantire la disponibilità elevata

Se si prevede di distribuire l'autenticazione pass-through in un ambiente di produzione, è necessario installare un agente di autenticazione autonomo aggiuntivo. Installare questi agenti di autenticazione in uno o più server _diversi_ da quello dove è in esecuzione Azure AD Connect. Questa configurazione offre la disponibilità elevata per le richieste di accesso utente.

>[!IMPORTANT]
>Negli ambienti di produzione è consigliabile disporre di almeno 3 agenti di autenticazione in esecuzione nel proprio tenant. Esiste un limite di sistema di 40 agenti di autenticazione per ogni tenant. E come procedura ottimale, considerare tutti i server che eseguono gli agenti di autenticazione come sistemi di livello 0 (vedere [riferimento](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

L'installazione di più agenti di autenticazione pass-through assicura la disponibilità elevata, ma non deterministica bilanciamento del carico tra gli agenti di autenticazione. Per determinare quanti agenti di autenticazione, è necessario per il tenant, prendere in considerazione il carico massimo e medio di richieste di accesso che si desidera vedere nel tenant. Come benchmark, un singolo agente di autenticazione può gestire da 300 a 400 autenticazioni al secondo in un server standard con CPU a 4 core e 16 GB di RAM.

Per stimare il traffico di rete, usare le indicazioni seguenti relative al dimensionamento:
- Ogni richiesta ha una dimensione di payload di (0.5K + 1K * num_of_agents) byte. Ad esempio, i dati da Azure AD per l'agente di autenticazione. In questo caso, "num_of_agents" indica il numero di agenti di autenticazione registrati nel tenant.
- Ogni risposta ha una dimensione di payload di 1 KB. Ad esempio, i dati dall'agente di autenticazione ad Azure AD.

Nella maggior parte dei clienti, tre agenti di autenticazione in totale sono sufficienti per la capacità e disponibilità elevata. È consigliabile installare gli agenti di autenticazione vicino ai controller di dominio per migliorare la latenza di accesso.

Per iniziare, seguire queste istruzioni per scaricare il software dell'agente di autenticazione:

1. Per scaricare la versione più recente dell'agente di autenticazione (versione 1.5.193.0 o successive), accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Nel riquadro sinistro selezionare **Azure Active Directory**.
3. Selezionare **Azure AD Connect**, **Autenticazione pass-through** e quindi **Scarica agente**.
4. Fare clic sul pulsante **Accetta le condizioni e scarica**.

![Interfaccia di amministrazione di Azure Active Directory: pulsante Scarica per l'agente di autenticazione](./media/how-to-connect-pta-quick-start/pta9.png)

![Interfaccia di amministrazione di Azure Active Directory: riquadro Scarica agente](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>È anche possibile [scaricare il software dell'agente di autenticazione](https://aka.ms/getauthagent). Leggere e accettare le [Condizioni d'uso](https://aka.ms/authagenteula) dell'agente di autenticazione _prima_ di installarlo.

Esistono due modi per distribuire un agente di autenticazione autonomo:

In primo luogo è possibile distribuirlo in modo interattivo eseguendo il file eseguibile dell'agente di autenticazione scaricato e, quando richiesto, specificando le credenziali di amministratore globale del tenant.

In secondo luogo è possibile creare ed eseguire uno script di distribuzione automatica. Questa opzione è utile per distribuire contemporaneamente più agenti di autenticazione o per installare gli agenti di autenticazione in istanze di Windows Server che non hanno un'interfaccia utente abilitata o che non sono accessibili con Desktop remoto. Di seguito sono riportate le istruzioni per usare questo approccio:

1. Eseguire il comando seguente per installare l'agente di autenticazione: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. È possibile registrare l'agente di autenticazione nel servizio tramite Windows PowerShell. Creare un oggetto credenziali di PowerShell `$cred` contenente un nome utente e una password di amministratore globale per il tenant. Eseguire il comando seguente sostituendo *\<username\>* e *\<password\>*:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
3. Passare a **C:\Programmi\Microsoft Azure AD Connect Authentication Agent** ed eseguire lo script seguente usando l'oggetto `$cred` creato:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Se è installato un agente di autenticazione in una macchina virtuale, è possibile clonare la macchina virtuale per la configurazione di un altro agente di autenticazione. Questo metodo è **supportato**.

## <a name="step-5-configure-smart-lockout-capability"></a>Passaggio 5: Configurare la funzionalità di blocco Smart

Blocco Smart consente di bloccare gli attori dannosi che tentano di indovinare le password degli utenti o utilizzando metodi di attacchi di forza bruta a parteciparvi. Configurando le impostazioni di blocco Smart in Azure AD e / o le impostazioni di blocco appropriata di un'istanza locale di Active Directory, gli attacchi è possibile filtrare prima che raggiungano Active Directory. Lettura [questo articolo](../authentication/howto-password-smart-lockout.md) per altre informazioni su come configurare le impostazioni di blocco Smart nel tenant per proteggere gli account utente.

## <a name="next-steps"></a>Passaggi successivi
- [Eseguire la migrazione da AD FS all'autenticazione pass-through](https://aka.ms/adfstoptadp): una guida dettagliata per la migrazione da AD FS (o altre tecnologie federative) per l'autenticazione pass-through.
- [Blocco intelligente](../authentication/howto-password-smart-lockout.md): informazioni su come configurare la funzionalità di blocco intelligente nel tenant per proteggere gli account utente.
- [Limitazioni correnti](how-to-connect-pta-current-limitations.md): informazioni sugli scenari supportati e non supportati con l'autenticazione pass-through.
- [Approfondimento tecnico](how-to-connect-pta-how-it-works.md): informazioni sul funzionamento dell'autenticazione pass-through.
- [Domande frequenti](how-to-connect-pta-faq.md): risposte alle domande frequenti.
- [Risolvere i problemi](tshoot-connect-pass-through-authentication.md): informazioni su come risolvere i problemi comuni relativi alla funzionalità di autenticazione pass-through.
- [Approfondimento sulla sicurezza](how-to-connect-pta-security-deep-dive.md): informazioni tecniche sulla funzionalità Autenticazione pass-through.
- [Seamless SSO di Azure AD](how-to-connect-sso.md): altre informazioni su questa funzionalità complementare.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): come usare il forum di Azure Active Directory per inviare richieste di nuove funzionalità.
