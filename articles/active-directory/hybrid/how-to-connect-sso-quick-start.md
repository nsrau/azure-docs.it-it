---
title: 'Azure AD Connect: Accesso Single Sign-On facile - avvio rapido| Microsoft Docs'
description: Questo articolo descrive come iniziare a usare l'accesso Single Sign-On facile di Azure Active Directory.
services: active-directory
keywords: che cos'è Azure AD Connect, installare Active Directory, componenti richiesti per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cf1e5f9f47ebdc132bdc826af3e54d206095085
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603398"
---
# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Accesso Single Sign-On facile di Azure Active Directory: guida introduttiva

## <a name="deploy-seamless-single-sign-on"></a>Distribuire l'accesso Single Sign-On facile

L'accesso Single Sign-On facile di Azure Active Directory (Azure AD) consente l'accesso automatico degli utenti dai desktop collegati alla rete aziendale. Grazie a questa funzionalità, gli utenti possono accedere facilmente alle applicazioni basate sul cloud senza usare altri componenti a livello locale.

Per distribuire l'accesso SSO facile, seguire questa procedura.

## <a name="step-1-check-the-prerequisites"></a>Passaggio 1: Verificare i prerequisiti

Accertarsi di aver soddisfatto i prerequisiti seguenti:

* **Configurare il server Azure AD Connect**: se come metodo di accesso si usa l'[autenticazione pass-through](how-to-connect-pta.md), non sono necessari altri controlli dei prerequisiti. Se si usa invece la [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md) e se è presente un firewall tra Azure AD Connect e Azure AD:
   - Usare Azure AD Connect 1.1.644.0 o versioni successive. 
   - Se il firewall o il proxy consente l'inserimento di DNS nell'elenco elementi consentiti, aggiungere all'elenco le connessioni agli URL di **\*.msappproxy.net** tramite la porta 443. In caso contrario, è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653), che vengono aggiornati ogni settimana. Questo prerequisito è applicabile solo quando si abilita la funzionalità. Non è necessario per gli accessi utente effettivi.

    >[!NOTE]
    >Le versioni 1.1.557.0, 1.1.558.0, 1.1.561.0 e 1.1.614.0 di Azure AD Connect presentano un problema correlato alla sincronizzazione dell'hash delle password. Se _non_ si prevede di usare la sincronizzazione dell'hash delle password insieme all'autenticazione pass-through, leggere le [note sulla versione di Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#116470) per altre informazioni.

* **Usare una topologia di Azure AD Connect supportata**: assicurarsi di usare una delle topologie supportate di Azure AD Connect descritte [qui](plan-connect-topologies.md).

    >[!NOTE]
    >L'accesso Seamless SSO supporta più foreste di AD in presenza di trust di AD o meno.

* **Configurare le credenziali dell'amministratore di dominio**: è necessario disporre di credenziali dell'amministratore di dominio per ogni foresta di Active Directory che:
    * Si sincronizza con Azure AD tramite Azure AD Connect.
    * Contiene gli utenti che si intende abilitare per l'accesso SSO facile.
    
* **Abilitare l'autenticazione moderna**: per eseguire questa funzionalità è necessario abilitare l'[autenticazione moderna](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) sul proprio tenant.

* **Usare le versioni più recenti dei client di Office 365**: per ottenere un'esperienza di accesso invisibile all'utente con i client di Office 365 (Outlook, Word, Excel e altri), gli utenti devono usare le versioni 16.0.8730.xxxx o successive.

## <a name="step-2-enable-the-feature"></a>Passaggio 2: Abilitare la funzionalità

Abilitare l'accesso SSO facile tramite [Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Inoltre, se Azure AD Connect non soddisfa i requisiti, è possibile [abilitare l'accesso Seamless SSO tramite PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature). Usare questa opzione se si dispone di più domini per ogni foresta Active Directory e si desidera rivolgersi in modo più mirato al dominio per cui si vuole abilitare l'accesso Seamless SSO.

Se si esegue una nuova installazione di Azure AD Connect, scegliere il [percorso di installazione personalizzato](how-to-connect-install-custom.md). Nella pagina **Accesso utente** selezionare l'opzione **Abilita Single Sign-On**.

>[!NOTE]
> Questa opzione sarà disponibile per la selezione solo se il metodo Single Sign-On è **Sincronizzazione dell'hash delle password** o **Autenticazione pass-through**.

![Azure AD Connect: Accesso utente](./media/how-to-connect-sso-quick-start/sso8.png)

Se Azure AD Connect è già installato, scegliere la pagina **Cambia l'accesso utente** in Azure AD Connect e quindi fare clic su **Avanti**. Se si usa Azure AD Connect versione 1.1.880.0 o successive, per impostazione predefinita sarà selezionata l'opzione **Abilita Single Sign-On**. Se si usano versioni precedenti di Azure AD Connect, selezionare l'opzione **Abilita Single Sign-On**.

![Azure AD Connect: Cambia l'accesso utente](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Continuare la procedura guidata finché non si visualizza la pagina **Abilita Single Sign-On**. Specificare le credenziali dell'amministratore di dominio per ogni foresta di Active Directory che:

* Si sincronizza con Azure AD tramite Azure AD Connect.
* Contiene gli utenti che si intende abilitare per l'accesso SSO facile.

Al termine della procedura guidata, l'accesso SSO facile è abilitato nel tenant.

>[!NOTE]
> Le credenziali dell'amministratore di dominio non vengono archiviate in Azure AD Connect o in Azure AD, ma vengono usate solo per abilitare la funzionalità.

Seguire queste istruzioni per verificare di aver abilitato correttamente l'accesso Single Sign-On facile:

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con le credenziali di amministratore globale del tenant.
2. Nel riquadro sinistro selezionare **Azure Active Directory**.
3. Selezionare **Azure AD Connect**.
4. Verificare che la funzionalità **Accesso Single Sign-On facile** sia impostata su **Abilitato**.

![Portale di Azure: riquadro Azure AD Connect](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Seamless SSO crea un account computer denominato `AZUREADSSOACC` nell'Active Directory locale (AD) in ogni foresta di Active Directory. Per motivi di sicurezza, è necessario che l'account computer `AZUREADSSOACC` sia fortemente protetto. Solo gli amministratori di dominio devono essere in grado di gestire l'account computer. Verificare che la delega Kerberos nell'account computer sia disabilitata e che nessun altro account in Active Directory disponga delle autorizzazioni di delega per l'account del computer `AZUREADSSOACC`. Archiviare l'account computer in un'unità organizzativa in cui sono protetti da eliminazioni accidentali e in cui solo gli amministratori di dominio hanno accesso.

>[!NOTE]
> Se si usano le architetture di mitigazione del furto di credenziali e pass-the-hash nell'ambiente locale, apportare le modifiche appropriate per assicurarsi che l'account del computer `AZUREADSSOACC` non finisca nel contenitore di quarantena. 

## <a name="step-3-roll-out-the-feature"></a>Passaggio 3: Distribuire la funzionalità

È possibile distribuire gradualmente la funzionalità Accesso Single Sign-On facile agli utenti usando le istruzioni seguenti. Prima di tutto, aggiungere l'URL di Azure AD seguente alle impostazioni dell'area Intranet di tutti gli utenti o di quelli selezionati usando Criteri di gruppo in Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Inoltre, attraverso Criteri di gruppo è necessario abilitare l'impostazione **Consenti aggiornamenti alla barra di stato tramite script** relativa ai criteri dell'area Intranet. 

>[!NOTE]
> Le istruzioni seguenti sono valide solo per Internet Explorer e Google Chrome in Windows, se condivide l'insieme di URL di siti attendibili con Internet Explorer. Per istruzioni sulla configurazione di Mozilla Firefox e Google Chrome su macOS, leggere la sezione successiva.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Motivo per cui è necessario modificare le impostazioni della zona Intranet degli utenti

Per impostazione predefinita, il browser calcola automaticamente l'area corretta, Internet o Intranet, in base a un URL specifico. Ad esempio, `http://contoso/` esegue il mapping all'area Intranet, mentre `http://intranet.contoso.com/` esegue il mapping all'area Internet (perché l'URL contiene un punto). I browser non invieranno ticket Kerberos a un endpoint del cloud, come l'URL di Azure AD, a meno che l'URL in questione non venga esplicitamente aggiunto all'area Intranet del browser.

È possibile modificare le impostazioni dell'area Intranet degli utenti in due modi:

| Opzione | Considerazione relativa all'amministratore | Esperienza utente |
| --- | --- | --- |
| Criteri di gruppo | L'amministratore blocca la modifica delle impostazioni dell'area Intranet | Gli utenti non possono modificare le proprie impostazioni |
| Preferenza di Criteri di gruppo |  L'amministratore consente la modifica delle impostazioni dell'area Intranet | Gli utenti non possono modificare le proprie impostazioni |

### <a name="group-policy-option---detailed-steps"></a>Opzione "Criteri di gruppo" - Procedura dettagliata

1. Aprire l'Editor Gestione Criteri di gruppo.
2. Modificare i criteri di gruppo applicati a tutti gli utenti o solo ad alcuni. Questo esempio è basato su **Criterio dominio predefinito**.
3. Passare a **Configurazione utente** > **criteri** > **Modelli amministrativi** > **componenti di Windows** > **Internet Explorer** > il **Pannello di controllo Internet** >  **Pagina sicurezza**. Selezionare quindi **Elenco di assegnazione siti ad aree**.
    ![Single Sign-On](./media/how-to-connect-sso-quick-start/sso6.png)
4. Abilitare i criteri e quindi immettere i valori seguenti nella finestra di dialogo:
   - **Nome valore**: l'URL di Azure AD a cui vengono inoltrati i ticket Kerberos.
   - **Valore** (dati): **1** indica l'area Intranet.

     Il risultato sarà analogo a questo:

     Nome valore: `https://autologon.microsoftazuread-sso.com`
  
     Valore (dati): 1

   >[!NOTE]
   > Se si vuole impedire ad alcuni utenti di usare l'accesso SSO facile, ad esempio se questi utenti accedono da chioschi multimediali condivisi, impostare i valori precedenti su **4**. In questo modo si aggiunge l'URL di Azure AD all'area con restrizioni e l'accesso SSO facile avrà sempre esito negativo.
   >

5. Fare clic su **OK** e quindi nuovamente su **OK**.

    ![Single sign-on](./media/how-to-connect-sso-quick-start/sso7.png)

6. Passare a **Configurazione utente** > **criteri** > **Modelli amministrativi** > **componenti di Windows** > **Internet Explorer** > il **Pannello di controllo Internet** >  **Pagina sicurezza** > **area Intranet**. Selezionare quindi **Consenti aggiornamenti alla barra di stato tramite script**.

    ![Single sign-on](./media/how-to-connect-sso-quick-start/sso11.png)

7. Abilitare l'impostazione del criterio e quindi fare clic su **OK**.

    ![Single sign-on](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Opzione "Preferenza di Criteri di gruppo" - Procedura dettagliata

1. Aprire l'Editor Gestione Criteri di gruppo.
2. Modificare i criteri di gruppo applicati a tutti gli utenti o solo ad alcuni. Questo esempio è basato su **Criterio dominio predefinito**.
3. Passare a **Configurazione utente** > **Preferenze** > **Impostazioni di Windows** > **Registro di sistema** > **Nuovo** > **Elemento Registro di sistema**.

    ![Single sign-on](./media/how-to-connect-sso-quick-start/sso15.png)

4. Immettere i valori seguenti nei campi appropriati e fare clic su **OK**.
   - **Percorso chiave**: ***Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-sso.com\autologon***
   - **Nome valore**: ***https***.
   - **Tipo di valore**: ***REG_DWORD***.
   - **Dati valore**: ***00000001***.
 
     ![Single sign-on](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Single sign-on](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Considerazioni sui browser

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (tutte le piattaforme)

Mozilla Firefox non usa automaticamente l'autenticazione Kerberos. Ogni utente deve aggiungere manualmente l'URL di Azure AD alle impostazioni di Firefox eseguendo questi passaggi:
1. Eseguire Firefox e immettere `about:config` nella barra degli indirizzi. Eliminare tutte le notifiche visualizzate.
2. Cercare la preferenza **network.negotiate-auth.trusted-uris**. Questa preferenza elenca i siti attendibili di Firefox per l'autenticazione Kerberos.
3. Fare clic con il pulsante destro del mouse e scegliere **Modify**.
4. Immettere `https://autologon.microsoftazuread-sso.com` nel campo.
5. Fare clic su **OK** e quindi riaprire il browser.

#### <a name="safari-macos"></a>Safari (macOS)

Verificare che il computer che esegue macOS sia stato aggiunto ad AD. Le istruzioni per l'aggiunta ad AD del dispositivo macOS non rientrano nell'ambito di questo articolo.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge basato su cromo (tutte le piattaforme)

Se sono state sostituite le impostazioni dei criteri [AuthNegotiateDelegateAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) o [AuthServerAllowlist](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) nell'ambiente in uso, assicurarsi di aggiungervi anche l'URL di Azure ad (`https://autologon.microsoftazuread-sso.com`).

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge basato su Chromium (macOS e altre piattaforme non Windows)

Per Microsoft Edge basato su Chromium su Mac OS e altre piattaforme non Windows, fare riferimento all' [elenco Microsoft Edge basato su criteri di cromo](https://docs.microsoft.com/DeployEdge/microsoft-edge-policies#authserverallowlist) per informazioni su come aggiungere l'URL Azure ad per l'autenticazione integrata all'elenco di consentiti.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (tutte le piattaforme)

Se nell'ambiente in uso si è scelto di ignorare le impostazioni dei criteri [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) o [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist), aggiungere a tali impostazioni anche l'URL di Azure AD (`https://autologon.microsoftazuread-sso.com`).

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS e altre piattaforme non Windows)

Per Google Chrome su Mac OS e altre piattaforme non Windows, vedere l'[elenco dei criteri dei progetti Chromium](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) per informazioni su come aggiungere l'URL di Azure AD all'elenco elementi consentiti per l'autenticazione integrata.

L'uso di estensioni di terze parti di Criteri di gruppo di Active Directory per distribuire l'URL di Azure AD a utenti di Firefox e Google Chrome su Mac esula dall'ambito di questo articolo.

#### <a name="known-browser-limitations"></a>Limitazioni note dei browser

L'accesso SSO facile non funziona in modalità di esplorazione privata nei browser Firefox e Microsoft Edge. Non funziona nemmeno in Internet Explorer se il browser è in esecuzione in modalità protetta avanzata. Per la prossima versione di Microsoft Edge basata sul cromo, non funzionerà in modalità InPrivate e Guest in base alla progettazione.

## <a name="step-4-test-the-feature"></a>Passaggio 4: Testare la funzionalità

Per testare la funzionalità per un utente specifico, verificare che siano soddisfatte tutte le condizioni seguenti:
  - L'utente esegue l'accesso da un dispositivo aziendale.
  - Il dispositivo è aggiunto al dominio di Active Directory. _Non_ è necessario che il dispositivo sia [aggiunto ad Azure AD](../active-directory-azureadjoin-overview.md).
  - Il dispositivo ha una connessione diretta al controller di dominio, nella rete aziendale cablata o wireless oppure tramite una connessione di accesso remoto, ad esempio di tipo VPN.
  - La [funzionalità è stata distribuita](##step-3-roll-out-the-feature) all'utente tramite Criteri di gruppo.

Per testare lo scenario in cui l'utente immette solo il nome utente ma non la password:
   - Accedere a `https://myapps.microsoft.com/` in una nuova sessione privata del browser.

Per testare lo scenario in cui l'utente non è obbligato a immettere il nome utente o la password, seguire una di queste procedure: 
   - Accedere a `https://myapps.microsoft.com/contoso.onmicrosoft.com` in una nuova sessione privata del browser. Sostituire *contoso* con il nome del tenant.
   - Accedere a `https://myapps.microsoft.com/contoso.com` in una nuova sessione privata del browser. Sostituire *contoso.com* con un dominio verificato, non federato, nel tenant.

## <a name="step-5-roll-over-keys"></a>Passaggio 5: Rinnovare le chiavi

Nel passaggio 2 Azure AD Connect crea account computer, che rappresentano Azure AD, in tutte le foreste di Active Directory in cui è stato abilitato l'accesso SSO facile. Per altre informazioni, vedere [Accesso Single Sign-On facile di Azure Active Directory: approfondimento tecnico](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>In caso di perdita, la chiave di decrittografia Kerberos su un account computer può essere usata per generare ticket Kerberos per qualsiasi utente nella relativa foresta AD. Eventuali attori malintenzionati possono quindi rappresentare gli accessi di Azure AD per gli utenti di cui è stata compromessa la chiave. È altamente consigliabile eseguire periodicamente il rollover delle chiavi di decrittografia di Kerberos, almeno ogni 30 giorni.

Per istruzioni su come rinnovare le chiavi, vedere [Accesso Single Sign-On facile di Azure Active Directory: domande frequenti](how-to-connect-sso-faq.md). Microsoft sta sviluppando una funzionalità per introdurre il rollover automatizzato delle chiavi.

>[!IMPORTANT]
>Non è necessario farlo _subito_ dopo aver abilitato la funzionalità. Rinnovare le chiavi di decrittografia Kerberos almeno una volta ogni 30 giorni.

## <a name="next-steps"></a>Passaggi successivi

- [Approfondimento tecnico](how-to-connect-sso-how-it-works.md): comprendere come funziona l'accesso Single Sign-On facile.
- [Domande frequenti](how-to-connect-sso-faq.md): ottenere risposte alle domande frequenti sull'accesso Single Sign-On facile.
- [Risoluzione dei problemi](tshoot-connect-sso.md): apprendere come risolvere i problemi comuni relativi alla funzionalità di accesso Single Sign-On facile.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): usare il forum di Azure Active Directory per inviare richieste per nuove funzionalità.
