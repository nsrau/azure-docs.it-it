---
title: Aggiungere un'app locale - Proxy di applicazione in Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) contiene un servizio proxy di applicazione che consente agli utenti di accedere alle applicazioni locali effettuando l'accesso con il proprio account Azure AD. Questa esercitazione illustra come preparare l'ambiente per l'uso con il proxy di applicazione. Viene quindi usato il portale di Azure per aggiungere un'applicazione locale al tenant di Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: aefa6d21488b617b26ddefe5fa4fc61cdd203f96
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032536"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory

Azure Active Directory (Azure AD) contiene un servizio proxy di applicazione che consente agli utenti di accedere alle applicazioni locali effettuando l'accesso con il proprio account Azure AD. Questa esercitazione consente di preparare l'ambiente per l'uso con il proxy di applicazione. Quando l'ambiente è pronto, si userà il portale di Azure per aggiungere un'applicazione locale al tenant di Azure AD.

Questa esercitazione:

> [!div class="checklist"]
> * Apre le porte per il traffico in uscita e consente l'accesso a URL specifici
> * Installa il connettore nel server Windows e lo registra con il proxy di applicazione
> * Verifica che il connettore sia stato installato e registrato correttamente
> * Aggiunge un'applicazione locale al tenant di Azure AD
> * Verifica che un utente di test possa accedere all'applicazione con un account Azure AD

## <a name="before-you-begin"></a>Prima di iniziare

Per aggiungere un'applicazione locale ad Azure AD, sono necessari:

* Una [sottoscrizione di Microsoft Azure AD Basic o Premium](https://azure.microsoft.com/pricing/details/active-directory)
* Un account amministratore dell'applicazione
* Identità utente sincronizzate da una directory in locale o create direttamente all'interno dei tenant di Azure AD. La sincronizzazione delle identità consente ad Azure AD di preautenticare gli utenti prima di concedere loro l'accesso alle applicazioni pubblicate tramite proxy di applicazione e di ottenere le informazioni degli ID utente necessarie per eseguire l'accesso Single Sign-On (SSO).

### <a name="windows-server"></a>Windows Server

Per usare il proxy di applicazione, è necessario un server Windows che esegue Windows Server 2012 R2 o versione successiva. Il connettore del proxy di applicazione verrà installato nel server. Questo server del connettore deve connettersi ai servizi proxy di applicazione in Azure e alle applicazioni locali che si intende pubblicare.

Per la disponibilità elevata nell'ambiente di produzione è consigliabile avere più server Windows. Per questa esercitazione è sufficiente un unico server Windows.

#### <a name="recommendations-for-the-connector-server"></a>Raccomandazioni per il server del connettore

1. Individuare fisicamente il server del connettore vicino ai server applicazioni per ottimizzare le prestazioni tra il connettore e l'applicazione. Per altre informazioni, vedere [Considerazioni sulla topologia di rete](application-proxy-network-topology.md).
1. Il server del connettore e i server applicazioni Web devono appartenere allo stesso dominio di Active Directory o essere suddivisi tra domini trusting. Avere i server nello stesso dominio o in domini trusting è un requisito per l'uso di Single Sign-On (SSO) con l'autenticazione integrata di Windows e la delega vincolata Kerberos (KCD). Se il server del connettore e i server applicazioni Web risiedono in domini di Active Directory diversi, è necessario usare la delega basata su risorse per l'accesso Single Sign-On. Per altre informazioni, vedere [KCD for single sign-on with Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md) (KDC per l'Accesso Single Sign-On con il proxy di applicazione).

#### <a name="tls-requirements"></a>Requisiti TLS

È necessario abilitare TLS 1.2 per il server del connettore Windows prima di installare il connettore proxy di applicazione.

Per abilitare TLS 1.2:

1. Impostare le chiavi del Registro di sistema seguenti:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Riavviare il server.

> [!IMPORTANT]
> Per fornire ai nostri clienti la crittografia migliore della categoria, stiamo aggiornando il servizio Application Proxy per limitare l'accesso ai soli protocolli TLS 1.2. In base alla disponibilità dei clienti, le modifiche verranno implementate gradualmente per i clienti che usano solo i protocolli TLS 1.2 e non ne vedranno l'impatto. La deprecazione di TLS 1.0 e 1.1 verrà completata il 31 agosto 2019 e i clienti riceveranno un preavviso per prepararsi a questa modifica. Per prepararsi a questa modifica, assicurarsi che tutte le combinazioni client-server e browser-server siano aggiornate per usare TLS 1.2 per mantenere la connessione al servizio Application Proxy. Queste includono i client usati dagli utenti per accedere alle applicazioni pubblicate tramite Application Proxy. Vedere [Preparazione per l'uso di TLS 1.2 in Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) per riferimenti e risorse utili.

## <a name="prepare-your-on-premises-environment"></a>Preparare l'ambiente locale

Per iniziare, abilitare la comunicazione ai data center di Azure per preparare l'ambiente per il proxy di applicazione di Azure AD,. Se nel percorso è presente un firewall, assicurarsi che sia aperto. Un firewall aperto consente al connettore di eseguire richieste HTTPS (TCP) al proxy di applicazione.

### <a name="open-ports"></a>Aprire le porte

Aprire le porte seguenti al traffico **in uscita**.

   | Numero della porta | Uso |
   | --- | --- |
   | 80 | Download degli elenchi di revoche di certificati (CRL) durante la convalida del certificato SSL |
   | 443 | Tutte le comunicazioni in uscita con il servizio proxy di applicazione |

Se il firewall regola il traffico in base agli utenti di origine, aprire anche le porte 80 e 443 per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete.

### <a name="allow-access-to-urls"></a>Consentire l'accesso agli URL

Consentire l'accesso agli URL seguenti:

| URL | Uso |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Comunicazione tra il connettore e il servizio cloud proxy di applicazione |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure usa questi URL per verificare i certificati. |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | Il connettore usa questi URL durante il processo di registrazione. |

È possibile consentire le connessioni a \*.msappproxy.net e \*.servicebus.windows.net se il firewall o il proxy consente di configurare elenchi di elementi consentiti DNS. In caso contrario, è necessario consentire l'accesso agli [intervalli di indirizzi IP e ai tag di servizio di Azure - Cloud pubblico](https://www.microsoft.com/download/details.aspx?id=56519). Gli intervalli di indirizzi IP vengono aggiornati ogni settimana.

## <a name="install-and-register-a-connector"></a>Installare e registrare un connettore

Per usare il proxy di applicazione, installare un connettore in ogni server Windows usato con il servizio proxy di applicazione. Il connettore è un agente che gestisce la connessione in uscita dai server applicazioni locali al proxy di applicazione in Azure AD. È possibile installare un connettore nei server in cui sono installati anche altri agenti di autenticazione come ad esempio Azure AD Connect.

Per installare il connettore:

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore dell'applicazione della directory che usa il proxy di applicazione. Se il dominio del tenant è contoso.com, ad esempio, l'amministratore deve essere admin@contoso.com o qualsiasi altro alias amministratore di tale dominio.
1. Selezionare il nome utente nell'angolo superiore destro. Verificare di aver effettuato l'accesso a una directory che usa il proxy di applicazione. Se è necessario cambiare directory, selezionare **Cambia directory** e scegliere una directory che usa il proxy di applicazione.
1. Nel riquadro di spostamento a sinistra selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Proxy di applicazione**.
1. Selezionare **Scarica servizio connettore**.

    ![Scaricare il servizio connettore per visualizzare le condizioni d'uso](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Leggere le condizioni d'uso. Al termine, selezionare **Accetta le condizioni e scarica**.
1. Nella parte inferiore della finestra selezionare **Esegui** per installare il connettore. Viene visualizzata l'installazione guidata.
1. Seguire le istruzioni della procedura guidata per installare il servizio. Quando viene chiesto di registrare il connettore con il proxy di applicazione per il tenant di Azure AD, fornire le credenziali di amministratore dell'applicazione.
    - Per Internet Explorer (IE), se l'opzione **Configurazione sicurezza avanzata IE** è impostata su **Sì**, è possibile che la schermata di registrazione non venga visualizzata. Per ottenere l'accesso, seguire le istruzioni contenute nel messaggio di errore. Verificare che **Sicurezza avanzata di Internet Explorer** sia impostato su **No**.

### <a name="general-remarks"></a>Osservazioni generali

Se in precedenza è stato installato un connettore, reinstallarlo per ottenere la versione più recente. Per informazioni sulle versioni rilasciate in precedenza e le modifiche incluse, vedere [Cronologia delle versioni del proxy di applicazione](application-proxy-release-version-history.md).

Se si sceglie di avere più server Windows per le applicazioni locali, è necessario installare e registrare il connettore in ogni server. È possibile organizzare i connettori in gruppi di connettori. Per altre informazioni, vedere [Gruppi di connettori](application-proxy-connector-groups.md).

Se l'organizzazione usa server proxy per connettersi a internet, è necessario configurarli per il proxy di applicazione.  Per altre informazioni, vedere [Uso di server proxy locali esistenti](application-proxy-configure-connectors-with-proxy-servers.md). 

Per informazioni sui connettori, sulla pianificazione della capacità e su come vengono mantenuti aggiornati i connettori, vedere [Comprendere i connettori del proxy applicazione Azure AD](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verificare che il connettore sia stato installato e registrato correttamente

È possibile usare il portale di Azure o il server Windows per verificare che un nuovo connettore sia stato installato correttamente.

### <a name="verify-the-installation-through-azure-portal"></a>Verificare l'installazione tramite il portale di Azure

Per verificare che il connettore sia stato installato e registrato correttamente:

1. Accedere alla directory del tenant nel [portale di Azure](https://portal.azure.com).
1. Nel pannello di spostamento a sinistra selezionare **Azure Active Directory** e quindi selezionare **Proxy di applicazione** nella sezione **Gestisci**. Tutti i connettori e i gruppi di connettori vengono visualizzati in questa pagina.
1. Visualizzare un connettore per verificarne i dettagli. I connettori devono essere espansi per impostazione predefinita. Se il connettore che si vuole visualizzare non lo è, espandere il connettore per visualizzare i dettagli. Un'etichetta verde (Attivo) indica che il connettore può connettersi al servizio. Tuttavia, anche se l'etichetta è verde, un problema di rete potrebbe comunque impedire al connettore di ricevere messaggi.

    ![Connettori di Azure AD Application Proxy](./media/application-proxy-connectors/app-proxy-connectors.png)

Per ulteriore assistenza con l'installazione di un connettore, vedere [Problemi di installazione del connettore dell'agente proxy dell'applicazione](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Verificare l'installazione tramite il server di Windows

Per verificare che il connettore sia stato installato e registrato correttamente:

1. Aprire Gestore servizi Windows facendo clic sul tasto **Windows** e immettendo *services.msc*.
1. Verificare se lo stato per i due servizi seguenti sia **In esecuzione**.
   - **Microsoft AAD Application Proxy Connector** abilita la connettività.
   - **Microsoft AAD Application Proxy Connector Updater** è un servizio di aggiornamento automatico. Lo strumento di aggiornamento controlla la presenza di nuove versioni del connettore e aggiorna il connettore in base alle esigenze.

     ![Servizi del connettore proxy di applicazione - Screenshot](./media/application-proxy-enable/app_proxy_services.png)

1. Se lo stato dei servizi non è **In esecuzione**, fare clic con il pulsante destro del mouse su ogni servizio e scegliere **Avvia**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Aggiungere un'app locale in Azure AD

Dopo aver preparato l'ambiente e aver installato un connettore, si è pronti per aggiungere le applicazioni locali ad Azure AD.  

1. Accedere come amministratore al [portale di Azure](https://portal.azure.com/).
1. Nel riquadro di spostamento a sinistra selezionare **Azure Active Directory**.
1. Selezionare **Applicazioni aziendali** e quindi selezionare **Nuova applicazione**.
1. Selezionare **Applicazione locale**.  
1. Nella sezione **Aggiungi applicazione locale personalizzata** fornire le informazioni seguenti sull'applicazione:

    | Campo | DESCRIZIONE |
    | :---- | :---------- |
    | **Nome** | Il nome dell'applicazione che verrà visualizzato nel pannello di accesso e nel portale di Azure. |
    | **URL interno** | URL per accedere all'applicazione dall'interno della rete privata. È possibile indicare un percorso specifico nel server back-end per la pubblicazione, mentre il resto del server non è pubblicato. In questo modo, si possono pubblicare siti diversi nello stesso server come app differenti, assegnando a ognuno un nome e regole di accesso specifici.<br><br>Se si pubblica un percorso, verificare che includa tutte le immagini, gli script e i fogli di stile necessari per l'applicazione. Se l'app si trova in https:\//yourapp/app e usa immagini che si trovano in https:/\/yourapp/media, si dovrà pubblicare come percorso https:/\/yourapp/. Questo URL interno non deve necessariamente corrispondere alla pagina di destinazione visualizzata dagli utenti. Per altre informazioni, vedere [Impostare una home page personalizzata per le app pubblicate tramite il proxy applicazione di Azure AD](application-proxy-configure-custom-home-page.md). |
    | **URL esterno** | L'indirizzo per gli utenti per accedere all'app dall'esterno della rete. Se non si desidera usare il dominio del Proxy di applicazione predefinito, trovare informazioni sui [domini personalizzati nel Proxy dell'applicazione di Azure AD](application-proxy-configure-custom-domain.md).|
    | **Preautenticazione** | Come il proxy di applicazione verifica gli utenti prima di concedere loro l'accesso all'applicazione.<br><br>**Azure Active Directory**: il proxy di applicazione reindirizza gli utenti in modo che eseguano l'accesso con Azure AD, che ne autentica le autorizzazioni per la directory e l'applicazione. È consigliabile lasciare questa opzione come impostazione predefinita, in modo da poter usufruire delle funzionalità di sicurezza di Azure AD come Accesso condizionale e Multi-Factor Authentication. Per monitorare l'applicazione con Microsoft Cloud App Security, è richiesto **Azure Active Directory**.<br><br>**Passthrough**: gli utenti non devono eseguire l'autenticazione ad Azure AD per accedere all'applicazione. È comunque possibile configurare i requisiti di autenticazione sul back-end. |
    | **Gruppo di connettori** | I connettori elaborano l'accesso remoto all'applicazione, mentre i gruppi di connettori aiutano a organizzare connettori e app in base all'area geografica, alla rete o allo scopo. Se ancora non si dispone di tutti i gruppi connettore creati, l'app viene assegnata a **Impostazione predefinita**.<br><br>Se l'applicazione usa WebSocket per la connessione, la versione di tutti i connettori nel gruppo deve essere 1.5.612.0 o versione successiva.|

1. Se necessario, configurare le **impostazioni aggiuntive**. Per la maggior parte delle applicazioni, è consigliabile mantenere queste impostazioni nei rispettivi stati predefiniti. 

    | Campo | DESCRIZIONE |
    | :---- | :---------- |
    | **Timeout applicazione back-end** | Impostare questo valore su **Lungo** solo se l'applicazione è lenta nell'autenticazione e nella connessione. Per impostazione predefinita, il timeout dell'applicazione back-end ha una durata di 85 secondi. Se viene impostato su Lungo, il timeout del back-end viene aumentato a 180 secondi. |
    | **Usa cookie solo HTTP** | Impostare questo valore su **Sì** per includere nei cookie del proxy di applicazione il flag HTTPOnly nell'intestazione della risposta HTTP. Se si usa Servizi Desktop remoto, impostare questo valore su **No**.|
    | **Usa cookie protetti**| Impostare questo valore su **Sì** per trasmettere i cookie tramite un canale protetto, ad esempio una richiesta HTTPS crittografata.
    | **Usa cookie persistente**| Mantenere questo valore impostato su **No**. Usare questa impostazione solo per le applicazioni che non possono condividere cookie tra processi. Per altre informazioni sulle impostazioni dei cookie, vedere [Cookie settings for accessing on-premises applications in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings) (Impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory).
    | **Convertire l'URL nelle intestazioni** | Mantenere questo valore su **Sì**, a meno che l'applicazione non richieda l'intestazione host originale nella richiesta di autenticazione. |
    | **Convertire gli URL nel corpo dell'applicazione** | Mantenere questo valore su **No**, a meno che non si sia in possesso di collegamenti HTML hardcoded ad altre applicazioni locali e non si usino domini personalizzati. Per altre informazioni, vedere [Reindirizzare i collegamenti hardcoded per le app pubblicate con il proxy di app di Azure AD](application-proxy-configure-hard-coded-link-translation.md).<br><br>Impostare questo valore su **Sì** se si intende monitorare questa applicazione con Microsoft Cloud App Security (MCAS). Per altre informazioni, vedere [Configurare il monitoraggio in tempo reale degli accessi alle applicazioni con Microsoft Cloud App Security e Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

1. Selezionare **Aggiungi**.

## <a name="test-the-application"></a>Test dell'applicazione

È ora possibile testare se l'applicazione è stata aggiunta correttamente. Nei passaggi successivi si aggiungerà un account utente all'applicazione e si proverà a eseguire l'accesso.

### <a name="add-a-user-for-testing"></a>Aggiungere utente per il test

Prima di aggiungere un utente all'applicazione, verificare che l'account utente abbia già le autorizzazioni per accedere all'applicazione dall'interno della rete aziendale.

Per aggiungere un utente di test:

1. Selezionare **Applicazioni aziendali** e quindi selezionare l'applicazione da testare.
1. Selezionare **Attività iniziali** e quindi selezionare **Assegna utente per il test**.
1. In **Utenti e gruppi** selezionare **Aggiungi utente**.
1. In **Aggiungi assegnazione** selezionare **Utenti e gruppi**. Viene visualizzata la sezione **Utenti e gruppi**.
1. Scegliere l'account da aggiungere.
1. Scegliere **Seleziona** e quindi selezionare **Assegna**.

### <a name="test-the-sign-on"></a>Testare l'accesso

Per testare l'accesso all'applicazione:

1. Nel browser passare all'URL esterno configurato durante la fase di pubblicazione. Dovrebbe essere visualizzata la schermata iniziale.
1. Accedere con l'account utente creato nella sezione precedente.

Per la risoluzione dei problemi, vedere [Risolvere i problemi e i messaggi di errore del proxy dell'applicazione](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è preparato l'ambiente locale per l'uso del proxy di applicazione e quindi si è installato e registrato il connettore del proxy di applicazione. È stata quindi aggiunta un'applicazione al tenant di Azure AD ed è stato verificato che un utente di test potesse accedere all'applicazione con un account Azure AD.

Sono state eseguite queste operazioni:
> [!div class="checklist"]
> * Sono state aperte le porte per il traffico in uscita ed è stato consentito l'accesso a URL specifici
> * È stato installato il connettore nel server Windows ed è stato registrato il connettore con il proxy di applicazione
> * Si è verificato che il connettore sia stato installato e registrato correttamente
> * È stata aggiunta un'applicazione locale al tenant di Azure AD
> * Si è verificato che un utente di test possa accedere all'applicazione con un account Azure AD

È ora possibile configurare l'applicazione per l'accesso Single Sign-On. Usare il collegamento seguente per scegliere un metodo di accesso Single Sign-On e per trovare esercitazioni su Single Sign-On.

> [!div class="nextstepaction"]
> [Configurare l'accesso Single Sign-On](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
