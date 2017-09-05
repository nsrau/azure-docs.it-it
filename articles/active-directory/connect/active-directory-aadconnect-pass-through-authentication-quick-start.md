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
ms.date: 08/23/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 07063ea53e96c6467e40e8a7ca70e5c03ce53284
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---

# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticazione pass-through di Azure Active Directory - Avvio rapido

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Come distribuire l'autenticazione pass-through di Azure AD

L'autenticazione pass-through di Azure Active Directory (Azure AD) consente agli utenti di accedere sia ad applicazioni in locale che a quelle basate sul cloud usando le stesse password. L'accesso degli utenti avviene tramite la convalida delle password direttamente in Active Directory locale.

>[!IMPORTANT]
>L'autenticazione pass-through di Azure AD è attualmente in fase di anteprima. Se si usa questa funzionalità tramite l'anteprima, è necessario assicurarsi di aggiornare le versioni di anteprima degli agenti di autenticazione attenendosi alle istruzioni fornite [qui](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Per distribuire l'autenticazione pass-through, è necessario seguire queste istruzioni:

## <a name="step-1-check-prerequisites"></a>Passaggio 1: Verificare i prerequisiti

Accertarsi di aver soddisfatto i prerequisiti seguenti:

### <a name="on-the-azure-active-directory-admin-center"></a>Nell'interfaccia di amministrazione di Azure Active Directory

1. Creare un account Administrator globale solo cloud nel tenant di Azure AD. In questo modo è possibile gestire la configurazione del tenant in caso di errore o mancata disponibilità dei servizi locali. Informazioni su come [aggiungere un account amministratore globale di tipo solo cloud](../active-directory-users-create-azure-portal.md). L'esecuzione di questo passaggio è fondamentale ed evita di rimanere bloccati fuori dal tenant.
2. Aggiungere uno o più [nomi di dominio personalizzati](../active-directory-add-domain.md) al tenant di Azure AD. Gli utenti accedono usando uno di questi nomi di dominio.

### <a name="in-your-on-premises-environment"></a>Nell'ambiente locale

1. Identificare un server che esegue Windows Server 2012 R2 o versione successiva in cui eseguire Azure AD Connect. Aggiungere il server alla stessa foresta AD degli utenti le cui password devono essere convalidate.
2. Installare la [versione più recente di Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) nel server identificato nel passaggio precedente. Se Azure AD Connect è già in esecuzione, assicurarsi che la versione sia 1.1.557.0 o successiva.
3. Identificare un server aggiuntivo che esegue Windows Server 2012 R2 o versione successiva in cui eseguire un agente di autenticazione autonomo. La versione dell'agente di autenticazione deve essere 1.5.193.0 o successiva. Questo server è necessario per garantire la disponibilità elevata delle richieste di accesso. Aggiungere il server alla stessa foresta AD degli utenti le cui password devono essere convalidate.
4. Se è presente un firewall tra i server e Azure AD, è necessario configurare gli elementi seguenti:
   - Assicurarsi che gli agenti di autenticazione possano effettuare richieste **in uscita** ad Azure AD sulle porte seguenti:
   
   | Numero della porta | Uso |
   | --- | --- |
   | **80** | Download degli elenchi di revoche di certificati (CRL) durante la convalida del certificato SSL |
   | **443** | Tutte le comunicazioni in uscita con il servizio |
   
   Se il firewall applica regole in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizi di rete.
   - Se il firewall o il proxy consente l'inserimento di DNS nell'elenco elementi consentiti, aggiungere le connessioni a **\*.msappproxy.net** e **\*.servicebus.windows.net** all'elenco elementi consentiti. In caso contrario, è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653), che vengono aggiornati ogni settimana.
   - Poiché gli agenti di autenticazione devono poter accedere a **login.windows.net** e **login.microsoftonline.net** per la registrazione iniziale, aprire il firewall anche per questi URL.
   - Per la convalida del certificato, sbloccare questi URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** e **www.microsoft.com:80**. Poiché vengono usati per la convalida del certificato con altri prodotti Microsoft, questi URL potrebbero essere già sbloccati.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Passaggio 2: Abilitare il supporto di Exchange ActiveSync (facoltativo)

Per abilitare il supporto di Exchange ActiveSync, seguire queste istruzioni:

1. Avviare [PowerShell per Exchange](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) ed eseguire il comando seguente:
```
Get-OrganizationConfig | fl per*
```

2. Controllare il valore dell'impostazione `PerTenantSwitchToESTSEnabled`. Se il valore è **true**, il tenant è configurato correttamente, come avviene in genere per la maggior parte dei clienti. Se il valore è **false**, eseguire il comando seguente:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Verificare che il valore di `PerTenantSwitchToESTSEnabled` sia ora impostato su **true**. Attendere un'ora prima di continuare con il passaggio successivo.

In caso di problemi durante questo passaggio, vedere la [guida alla risoluzione dei problemi](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues) per altre informazioni.

## <a name="step-3-enable-the-feature"></a>Passaggio 3: Abilitare la funzionalità

L'autenticazione pass-through di Azure AD può essere abilitata tramite [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>L'autenticazione pass-through può essere abilitata nel server primario o di gestione temporanea di Azure AD Connect. Si consiglia di abilitarla dal server primario.

Se si installa Azure AD Connect per la prima volta, scegliere il [percorso di installazione personalizzato](active-directory-aadconnect-get-started-custom.md). Nella pagina di **Accesso utente** scegliere **Autenticazione pass-through** come metodo di accesso. Al completamento, nello stesso server di Azure AD Connect viene installato un agente di autenticazione. Viene inoltre abilitata la funzionalità di autenticazione pass-through nel tenant.

![Azure AD Connect - Accesso utente](./media/active-directory-aadconnect-sso/sso3.png)

Se Azure AD Connect è già installata, (usando il percorso di [installazione rapida](active-directory-aadconnect-get-started-express.md) o di [installazione personalizzata](active-directory-aadconnect-get-started-custom.md)), scegliere la pagina **Cambia l'accesso utente** in Azure AD Connect e fare clic su **Avanti**. Selezionare quindi **Autenticazione pass-through** come il metodo di accesso. Al completamento, nello stesso server di Azure AD Connect viene installato un agente di autenticazione e la funzionalità è abilitata per il tenant.

![Azure AD Connect - Cambiare l'accesso utente](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>L'autenticazione pass-through è una funzionalità a livello di tenant. La sua attivazione influisce sugli accessi degli utenti per _tutti_ i domini gestiti nel tenant. Se si passa da AD FS all'autenticazione pass-through, è consigliabile attendere almeno 12 ore prima di arrestare l'infrastruttura AD FS. Questo tempo di attesa è necessario per garantire che gli utenti possano continuare ad accedere a Exchange ActiveSync durante la transizione.

## <a name="step-4-test-the-feature"></a>Passaggio 4: Testare la funzionalità

Seguire queste istruzioni per verificare che l'autenticazione pass-through sia stata attivata correttamente:

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Selezionare **Azure Active Directory** nell'opzione di spostamento a sinistra.
3. Selezionare **Azure AD Connect**.
4. Verificare che la funzionalità di **autenticazione pass-through** indichi **Abilitato**.
5. Selezionare **Autenticazione pass-through**. Questo pannello elenca i server sono installati gli agenti di autenticazione.

![Interfaccia di amministrazione di Azure Active Directory - Pannello Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Interfaccia di amministrazione di Azure Active Directory - Pannello Autenticazione pass-through](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

Dopo questo passaggio, gli utenti di tutti i domini gestiti nel tenant possono accedere usando l'autenticazione pass-through. Gli utenti dei domini federati continueranno invece a eseguire l'accesso con Active Directory Federation Services (ADFS) o qualsiasi altro provider di federazione configurato in precedenza. Se si converte un dominio da federato a gestito, tutti gli utenti del dominio inizieranno automaticamente a eseguire l'accesso usando l'autenticazione pass-through. La funzionalità di autenticazione pass-through non ha alcun impatto sugli utenti solo cloud.

## <a name="step-5-ensure-high-availability"></a>Passaggio 5: Garantire la disponibilità elevata

Se si prevede di distribuire l'autenticazione pass-through in un ambiente di produzione, è necessario installare un agente di autenticazione autonomo. Installare questo secondo agente di autenticazione in un server _diverso_ da quello dove è in esecuzione Azure AD Connect e il primo agente di autenticazione. Questa configurazione fornisce la disponibilità elevata delle richieste di accesso. Per distribuire un agente di autenticazione, seguire queste istruzioni:

1. **Scaricare la versione più recente dell'agente di autenticazione (versioni 1.5.193.0 o successive)**: accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Selezionare **Azure Active Directory** nell'opzione di spostamento a sinistra.
3. Selezionare **Azure AD Connect****, quindi** Autenticazione pass-through. Selezionare quindi **Scarica agente**.
4. Fare clic sul pulsante **Accept terms & download** (Accetta i termini e scarica).
5. **Installare la versione più recente dell'agente di autenticazione**: eseguire il file eseguibile scaricato nel passaggio precedente. Fornire le credenziali di amministratore globale del tenant quando viene richiesto.

![Interfaccia di amministrazione di Azure Active Directory - Pulsante Scarica per l'agente di autenticazione](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Interfaccia di amministrazione di Azure Active Directory - Pannello Scarica agente](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>È inoltre possibile scaricare l'agente di autenticazione da [qui](https://aka.ms/getauthagent). Assicurarsi di rivedere e accettare i [Termini del servizio](https://aka.ms/authagenteula) _dell'agente di autenticazione prima di installarlo_ .

## <a name="next-steps"></a>Passaggi successivi
- [**Current limitations**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) (Limitazioni correnti): questa funzionalità è attualmente in anteprima. Informazioni su quali scenari sono supportati e quali non lo sono.
- [**Approfondimento tecnico**](active-directory-aadconnect-pass-through-authentication-how-it-works.md): informazioni sul funzionamento di questa funzionalità.
- [**Domande frequenti**](active-directory-aadconnect-pass-through-authentication-faq.md): risposte alle domande più frequenti.
- [**Risoluzione dei problemi**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md): informazioni su come risolvere i problemi comuni relativi a questa funzionalità.
- [**Seamless Single Sign-On di Azure AD**](active-directory-aadconnect-sso.md): altre informazioni su questa funzionalità complementare.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): per l'invio di richieste di nuove funzionalità.

