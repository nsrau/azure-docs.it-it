---
title: "Usare i server di Server dei criteri di rete esistenti per offrire le funzionalità MFA di Azure | Microsoft Docs"
description: "L&quot;estensione di Server dei criteri di rete per Multi-Factor Authentication di Azure è una soluzione semplice per aggiungere le funzionalità di verifica in due passaggi basata sul cloud nell&quot;infrastruttura di autenticazione esistente."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017,it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 46f5761caf2883d6083245a9a1fe689ea0529212
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017

---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication---public-preview"></a>Integrare l'infrastruttura NPS esistente con Azure Multi-Factor Authentication - Anteprima pubblica

L'estensione di Server dei criteri di rete (NPS) per Azure MFA aggiunge funzionalità MFA basate su cloud per l'infrastruttura di autenticazione usando i server esistenti. Con l'estensione di Server dei criteri di rete, è possibile aggiungere la verifica con telefonata, SMS o app telefonica al flusso di autenticazione esistente senza dover installare, configurare e gestire nuovi server. 

Questa estensione è stata creata per le organizzazioni che vogliono proteggere le connessioni VPN senza distribuire Azure MFA Server. L'estensione del server dei criteri di rete funge da adattatore tra RADIUS e Azure MFA basato su cloud per fornire un secondo fattore di autenticazione per utenti federati o sincronizzati.

Quando si usa l'estensione dei criteri di rete per di Azure MFA, il flusso di autenticazione include i componenti seguenti: 

1. Il **Server NAS/VPN** riceve le richieste dei client VPN e le converte in richieste RADIUS per il Server dei criteri di rete. 
2. Il **Server dei criteri di rete** si connette ad Active Directory per eseguire l'autenticazione principale per le richieste RADIUS e, al completamento dell'operazione, passa la richiesta alle estensioni installate.  
3. L'**estensione di Server dei criteri di rete** attiva una richiesta di autenticazione secondaria per Azure MFA. Dopo che l'estensione riceve la risposta e se la richiesta di verifica MFA ha esito positivo, la richiesta di autenticazione viene completata, fornendo al server di Server dei criteri di rete i token di sicurezza che includono un'attestazione MFA, emessa dal servizio token di sicurezza di Azure.  
4. **Azure MFA** comunica con Azure Active Directory per recuperare i dettagli dell'utente ed esegue l'autenticazione secondaria grazie al metodo di verifica configurato per l'utente.

Il diagramma seguente illustra questo flusso di richiesta di autenticazione ad alto livello: 

![Diagramma del flusso di autenticazione](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Pianificare la distribuzione

L'estensione di Server dei criteri di rete gestisce automaticamente la ridondanza, pertanto non è necessaria una configurazione speciale.

È possibile creare il tutti i Server dei criteri di rete necessari abilitati per Azure Multi-Factor Authentication. Se si installano più server, è consigliabile usare un certificato client di differenza per ciascuno. La creazione di un certificato per ogni server significa che è possibile aggiornare singolarmente ogni certificato senza doversi preoccupare dei tempi di inattività in tutti i server.

I server VPN indirizzano le richieste di autenticazione, quindi è necessario essere a conoscenza dei nuovi Server dei criteri di rete abilitati per Azure MFA.

## <a name="prerequisites"></a>Prerequisiti

L'estensione di Server dei criteri di rete è progettata per funzionare con l'infrastruttura esistente. Prima di iniziare, verificare che i prerequisiti seguenti siano disponibili.

### <a name="licenses"></a>Licenze

L'estensione di Server dei criteri di rete per Azure MFA è disponibile per i clienti dotati di [licenze per Multi-Factor Authentication di Azure](multi-factor-authentication.md) (tra cui una sottoscrizione con Azure AD Premium, EMS o MFA).

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 o versione successiva.

### <a name="libraries"></a>Librerie

Queste librerie vengono installate automaticamente con l'estensione.
-   [Visual C++ Redistributable Packages per Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Modulo di Microsoft Azure Active Directory per Windows PowerShell versione 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

### <a name="azure-active-directory"></a>Azure Active Directory

Per gli utenti che usano l'estensione di Server dei criteri di rete è necessaria la sincronizzazione con Azure Active Directory grazie ad Azure AD Connect e l'abilitazione per MFA.

Quando si installa l'estensione, per il tenant di Azure AD sono necessarie le credenziali di amministrazione e l'ID della directory. L'ID della directory si trova nel [Portale di Azure](https://portal.azure.com). Accedere come amministratore, selezionare l'icona di **Azure Active Directory** sulla sinistra, quindi selezionare **Proprietà**. Copiare il GUID nella casella **ID directory** e salvare. Questo GUID verrà usato come ID tenant quando si installa l'estensione di Server dei criteri di rete.

![L'ID directory si trova nelle proprietà di Azure Active Directory](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="prepare-your-environment"></a>Preparare l'ambiente

Prima di installare l'estensione di Server dei criteri di rete, è necessario preparare l'ambiente per gestire il traffico di autenticazione.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Abilitare il ruolo del Server dei criteri di rete in un server appartenente a un dominio

Il Server dei criteri di rete si connette ad Azure Active Directory e autentica le richieste di MFA. Scegliere un server per questo ruolo. Si consiglia di scegliere un server che non gestisce le richieste provenienti da altri servizi, poiché l'estensione di Server dei criteri di rete genera errori per qualsiasi richiesta non RADIUS.

1. Sul server avviare l'**aggiunta guidata ruoli e funzionalità** dal menu Avvio rapido di Server Manager.
2. Come tipo di installazione scegliere **Installazione basata su ruoli o basata su funzionalità**.
3. Selezionare il ruolo del server **Servizi di accesso e criteri di rete**. Potrebbe essere visualizzata una finestra per informare l'utente riguardo alle funzionalità necessarie per eseguire questo ruolo.
4. Continuare la procedura guidata fino alla pagina di conferma. Selezionare **Installa**.

Ora che si dispone di un server designato come Server dei criteri di rete, è inoltre necessario configurarlo per gestire le richieste RADIUS in ingresso dalla soluzione VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurare la soluzione VPN in modo che comunichi con il Server dei criteri di rete

La procedura per configurare i criteri di autenticazione RADIUS può variare a seconda della soluzione VPN in uso. Configurare i criteri in modo che la soluzione punti al Server dei criteri di rete RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Sincronizzare gli utenti del dominio con il cloud

Questo passaggio potrebbe essere già completato nel tenant, tuttavia è consigliabile verificare che Azure AD Connect abbia sincronizzato i database di recente.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore.
2. Selezionare **Azure Active Directory** > **Azure AD Connect**
3. Verificare che lo stato della sincronizzazione sia **Abilitata** e che l'ultima sincronizzazione sia stata eseguita da meno di un'ora.

Se si desidera avviare un nuovo ciclo di sincronizzazione, usare istruzioni presenti in [Servizio di sincronizzazione Azure AD Connect: utilità di pianificazione](../active-directory/connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinare i metodi di autenticazione che è possibile usare

Sono due i fattori che determinano i metodi di autenticazione disponibili con una distribuzione dell'estensione di Server dei criteri di rete:

1. L'algoritmo di crittografia della password usato tra il client RADIUS (VPN, server Netscaler o altri) e i Server dei criteri di rete.
   - **PAP** supporta tutti i metodi di autenticazione di Azure MFA nel cloud: chiamata telefonica, SMS, notifica dell'app per dispositivi mobili e codice di verifica dell'app per dispositivi mobili.
   - **CHAPV2** supporta la chiamata telefonica e la notifica dell'app per dispositivi mobili.
   - **EAP** non è supportato.
2. I metodi di input che l'applicazione client (VPN, server Netscaler o altra) può gestire. Ad esempio, gli strumenti usati dal client VPN per consentire all'utente di digitare un codice di verifica da un testo o da un'app per dispositivi mobili.

Quando si distribuisce l'estensione di Server dei criteri di rete, usare questi fattori per valutare i metodi disponibili per gli utenti. Se il client RADIUS supporta PAP, ma nel client non esistono campi di input per un codice di verifica, la chiamata telefonica e la notifica dell'app per dispositivi mobili sono le due opzioni supportate.

È possibile [disabilitare i metodi di autenticazione non supportati](multi-factor-authentication-whats-next.md#selectable-verification-methods) in Azure.

### <a name="enable-users-for-mfa"></a>Abilitare gli utenti per l'MFA

Prima di distribuire l'estensione completa di Server dei criteri di rete, è necessario abilitare l'MFA per gli utenti su cui si desidera eseguire la verifica in due passaggi. Per testare l'estensione in modo più immediato mentre viene distribuita, è necessario almeno un account di test completamente registrato per l'MFA.

Seguire questa procedura per avviare un account di test:
1. [Abilitare un account per l'MFA](multi-factor-authentication-get-started-user-states.md).
2. Accedere a qualsiasi sito Web in cui venga avviata l'autenticazione di Azure AD, ad esempio https://portal.azure.com.
3. [Registrarsi alla verifica in due passaggi](./end-user/multi-factor-authentication-end-user-first-time.md).

Gli utenti devono inoltre eseguire la procedura per la registrazione prima di potersi autenticare con l'estensione del server dei criteri di rete.

## <a name="install-the-nps-extension"></a>Installare l'estensione di Server dei criteri di rete

> [!IMPORTANT]
> Installare l'estensione di Server dei criteri di rete in un server diverso rispetto al punto di accesso della VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Scaricare e installare l'estensione di Server dei criteri di rete per Azure MFA

1.  [Scaricare l'estensione di Server dei criteri di rete](https://aka.ms/npsmfa) dall'Area download di Microsoft.
2.  Copiare il file binario nel Server dei criteri di rete da configurare.
3.  Eseguire *setup.exe* e seguire le istruzioni di installazione. Se si verificano errori, controllare che le due librerie indicate nella sezione sui prerequisiti siano state installate correttamente.

### <a name="run-the-powershell-script"></a>Eseguire lo script di PowerShell

Il programma di installazione crea uno script di PowerShell in questa posizione: `C:\Program Files\Microsoft\AzureMfa\Config` (dove C:\ è l'unità di installazione). Lo script di PowerShell esegue le azioni seguenti:

-   Creare un certificato autofirmato.
-   Associare la chiave pubblica del certificato all'entità servizio su Azure AD.
-   Archiviare il certificato nell'archivio certificati del computer locale.
-   Concedere l'accesso alla chiave privata del certificato all'utente di rete.
-   Riavviare il Server dei criteri di rete.

A meno che non si desideri utilizzare i propri certificati (invece dei certificati autofirmati generati dallo script di PowerShell), eseguire lo script di PowerShell per completare l'installazione. Se si installa l'estensione su più server, ciascun server dovrebbe avere il proprio certificato.

1. Eseguire Windows PowerShell come amministratore.
2. Cambiare le directory.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Eseguire lo script di PowerShell creato dal programma di installazione.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Prompt di PowerShell per l'ID tenant. Usare il GUID dell'ID directory copiato dal portale di Azure nella sezione relativa ai prerequisiti.
5. Accedere ad Azure AD come amministratore.
6. Al termine dello script, PowerShell mostra un messaggio di conferma.  

Ripetere questi passaggi per tutti i server dei criteri di rete aggiuntivi che si intende configurare per il bilanciamento del carico.

## <a name="configure-your-nps-extension"></a>Configurare l'estensione di Server dei criteri di rete

In questa sezione sono disponibili considerazioni e suggerimenti sulla progettazione per una corretta distribuzione dell'estensione di Server dei criteri di rete.

### <a name="configurations-limitations"></a>Limitazioni delle configurazioni

- L'estensione di Server dei criteri di rete per Azure MFA non include strumenti per la migrazione degli utenti e impostazioni dal Server MFA al cloud. Per questo motivo, è consigliabile usare l'estensione per le distribuzioni nuove piuttosto che per quelle esistenti. Se si usano le estensioni in una distribuzione esistente, gli utenti dovranno eseguire di nuovo la prova per popolare i dettagli della propria MFA nel cloud.  
- L'estensione di Server dei criteri di rete usa UPN dell'Active Directory locale per identificare l'utente in Azure MFA che deve eseguire l'autenticazione secondaria. L'estensione non può essere configurata per usare un identificatore come ID di accesso alternativo o campo AD personalizzato diverso dall'UPN.  
- Non tutti i protocolli di crittografia supportano tutti i metodi di verifica.
   - **PAP** supporta la chiamata telefonica, SMS, la notifica dell'app per dispositivi mobili e il codice di verifica app per dispositivi mobili
   - **CHAPV2** supporta la chiamata telefonica e la notifica dell'app per dispositivi mobili
   - **EAP** non è supportato

### <a name="control-radius-clients-that-require-mfa"></a>Client RADIUS di controllo che richiedono MFA

Dopo aver abilitato MFA per un client RADIUS utilizzando l'estensione di Server dei criteri di rete, tutte le autenticazioni per questo client devono eseguire MFA. Se si desidera abilitare MFA solo per alcuni client RADIUS, è possibile configurare due server di Server dei criteri di rete e installare l'estensione solo su uno di questi. Configurare i client RADIUS per cui si vuole fare in modo che MFA invii richieste al server di Server dei criteri di rete configurato con l'estensione e gli altri client RADIUS al server di Server dei criteri di rete senza configurazione per l'estensione.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Impostazioni per gli utenti che non sono registrati per MFA

Se sono presenti utenti che non sono registrati per MFA, è possibile stabilire cosa succede quando questi tentano di eseguire l'autenticazione. Usare l'impostazione del registro di sistema *REQUIRE_USER_MATCH* nel percorso del registro di sistema *HKLM\Software\Microsoft\AzureMFA* per controllare il comportamento della funzionalità. Questa impostazione non ha un'unica opzione di configurazione:

| Chiave | Valore | Default |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | VERO/FALSO | Non impostato (equivalente a VERO) |

Lo scopo di questa impostazione è stabilire cosa fare quando un utente non è registrato per MFA. Quando la chiave non esiste, non è impostata o è impostata su VERO e l'utente non è registrato, allora l'estensione non esegue correttamente la richiesta di verifica MFA. Quando la chiave è impostata su FALSO e l'utente non è registrato, l'autenticazione procede senza eseguire MFA.

È possibile scegliere di creare questa chiave e impostarla su FALSE, durante il caricamento degli utenti che potrebbero non essere ancora registrati per Azure MFA. Poiché l'impostazione della chiave consente agli utenti che non sono registrati all'MFA di accedere, è necessario rimuovere la chiave prima di passare all'ambiente di produzione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Come verificare che il certificato client sia installato come previsto?

Cercare il certificato autofirmato creato dal programma di installazione nell'archivio dei certificati e verificare che la chiave privata disponga delle autorizzazioni concesse all'utente **Servizio di rete**. Il certificato ha come nome oggetto **CN \<tenantid\>, OU = Estensione di Server dei criteri di rete Microsoft**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Come verificare che il certificato client sia associato al tenant in Azure Active Directory?

Aprire il prompt dei comandi di PowerShell ed eseguire i comandi seguenti:

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Questi comandi consentono di stampare tutti i certificati associando il tenant con l'istanza dell'estensione di Server dei criteri di rete nella sessione di PowerShell. Cercare il certificato esportando il certificato client come file "Codificato Base 64 X.509 (.CER)" senza la chiave privata e confrontarlo con l'elenco di PowerShell.

I timbri data/ora Valido-dal e Valido-fino al, che sono in formato leggibile, possono essere usati per filtrare i risultati errati se il comando restituisce più di un certificato.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Perché le richieste hanno esito negativo con errore di token ADAL?

Questo errore potrebbe essere dovuto a diverse ragioni. Usare la procedura seguente per la risoluzione:

1. Riavviare il server di Server dei criteri di rete.
2. Verificare che il certificato client sia installato come previsto.
3. Verificare che il certificato sia associato al tenant in Azure AD.
4. Verificare che https://login.windows.net/ sia accessibile dal server che esegue l'estensione.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Perché l'autenticazione ha esito negativo e restituisce un errore nei log HTTP che indica che l'utente non è stato trovato?

Verificare che AD Connect sia in esecuzione e che l'utente sia presente sia in Active Directory di Windows sia in Azure Active Directory.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Perché vengono visualizzati errori di connessione HTTP nei log che contengono le autenticazioni non riuscite?

Verificare che https://adnotifications.windowsazure.com sia raggiungibile dal server che esegue l'estensione di Server dei criteri di rete.


## <a name="next-steps"></a>Passaggi successivi

Consultare gli articoli su come integrare Azure MFA con [Active Directory](multi-factor-authentication-get-started-server-dirint.md), [l'autenticazione RADIUS](multi-factor-authentication-get-started-server-radius.md) e [l'autenticazione LDAP](multi-factor-authentication-get-started-server-ldap.md).

