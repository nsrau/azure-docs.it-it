---
title: Integrare la rete VPN con Multi-Factor Authentication tramite l'estensione del Server dei criteri di rete | Microsoft Docs
description: Questo articolo illustra come integrare l'infrastruttura VPN con Azure MFA usando l'estensione Server dei criteri di rete per Microsoft Azure.
services: active-directory
keywords: Azure MFA, integrazione VPN, Azure Active Directory, estensione di Server dei criteri di rete
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 9b156f80ae2b52ea7cb07bcb2c047d35d07b9154
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrare l'infrastruttura VPN con Azure MFA usando l'estensione Server dei criteri di rete per Azure

## <a name="overview"></a>Panoramica

L'estensione Server dei criteri di rete per Azure consente alle organizzazioni di proteggere l'autenticazione client Remote Authentication Dial-In User Service tramite il servizio [Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-rdg.md) basato sul cloud, che offre la verifica in due passaggi.

Questo articolo contiene istruzioni dettagliate per l'integrazione dell'infrastruttura Server dei criteri di rete con Multi-Factor Authentication tramite l'estensione Server dei criteri di rete per Azure. Questo processo consente di proteggere la verifica in due passaggi per gli utenti che tentano di connettersi alla rete tramite una VPN. 

Servizi di accesso e criteri di rete consente alle organizzazioni di:

* Assegnare una posizione centrale per la gestione e il controllo delle richieste di rete per specificare:

    * quali utenti possono connettersi 
    
    * in quali ore del giorno è consentito connettersi 
    
    * la durata delle connessioni
    
    * il livello di sicurezza che i client devono usare per la connessione

    Invece di specificare subito i criteri in ogni VPN o server Gateway Desktop remoto, è possibile farlo dopo averli collocati in una posizione centrale. Il protocollo RADIUS viene usato per offrire servizi centralizzati di autenticazione, autorizzazione e accounting. 

* Stabilire e applicare criteri di integrità client di Protezione accesso alla rete che determinano se ai dispositivi viene concesso l'accesso alle risorse di rete con o senza restrizioni.

* Indicare un modo per imporre l'autenticazione e l'autorizzazione per l'accesso a commutatori Ethernet e punti di accesso wireless che supportano 802.1x.   
Per altre informazioni, vedere [Network Policy Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) (Server dei criteri di rete). 

Per migliorare la sicurezza e offrire un elevato livello di conformità, le organizzazioni possono integrare Server dei criteri di rete con Azure Multi-Factor Authentication per garantire che gli utenti usino la verifica in due passaggi per connettersi alla porta virtuale sul server VPN. Affinché venga concesso loro l'accesso, gli utenti devono specificare la combinazione di nome utente e password con informazioni sotto il controllo dell'utente. Queste informazioni devono essere attendibili e non facilmente duplicabili. Possono includere un numero di cellulare, un numero di rete fissa o un'applicazione su un dispositivo mobile.

Prima che fosse disponibile l'estensione Server dei criteri di rete per Azure, i clienti che volevano implementare la verifica in due passaggi per ambienti Server dei criteri di rete e Multi-Factor Authentication integrati dovevano configurare e gestire un server Multi-Factor Authentication distinto nell'ambiente locale. Questo tipo di autenticazione viene offerta da Gateway Desktop remoto e server Azure Multi-Factor Authentication tramite RADIUS.

Con l'estensione Server dei criteri di rete per Azure, le organizzazioni possono proteggere l'autenticazione client RADIUS distribuendo una soluzione Multi-Factor Authentication locale o una soluzione Multi-Factor Authentication basata sul cloud.
 
## <a name="authentication-flow"></a>Flusso di autenticazione
Quando gli utenti si connettono a una porta virtuale su un server VPN, devono prima autenticarsi usando diversi protocolli. I protocolli consentono di usare una combinazione di nome utente e password e i metodi di autenticazione basata sui certificati. 

Oltre all'autenticazione e alla verifica dell'identità, gli utenti devono avere le autorizzazioni appropriate per l'accesso. Nelle implementazioni semplici queste autorizzazioni di accesso vengono impostate direttamente negli oggetti utente di Active Directory. 

![Proprietà utente](./media/nps-extension-vpn/image1.png)

Per le implementazioni semplici, ogni server VPN concede o nega l'accesso in base a criteri definiti in ogni server VPN locale.

Nelle implementazioni più grandi e più scalabili i criteri che concedono o negano l'accesso VPN sono centralizzati nei server RADIUS. In questi casi il server VPN funge da server di accesso, come client RADIUS, che inoltra le richieste di connessione e i messaggi degli account a un server RADIUS. Per connettersi alla porta virtuale nel server VPN, gli utenti devono essere autenticati e soddisfare le condizioni definite in modo centralizzato nei server RADIUS. 

Quando l'estensione Server dei criteri di rete per Azure è integrata con Server dei criteri di rete, si crea un corretto flusso di autenticazione, come illustrato di seguito:

1. Il server VPN riceve da un utente VPN una richiesta di autenticazione che include il nome utente e la password per la connessione a una risorsa, ad esempio una sessione Desktop remoto. 

2. Fungendo da client RADIUS, il server VPN converte la richiesta in un messaggio di *richiesta di accesso* RADIUS e invia il messaggio, con password crittografata, al server RADIUS in cui è installata l'estensione Server dei criteri di rete. 

3. La combinazione di nome utente e password viene verificata in Active Directory. Se il nome di utente o la password sono errati, il server RADIUS invia un messaggio di *rifiuto di accesso*. 

4. Se vengono soddisfatte tutte le condizioni specificate nella richiesta di connessione Server dei criteri di rete e nei criteri di rete, ad esempio le restrizioni relative all'ora del giorno o all'appartenenza a gruppi, l'estensione Server dei criteri di rete attiva una richiesta di autenticazione secondaria con Azure Multi-Factor Authentication. 

5. Azure Multi-Factor Authentication comunica con Azure Active Directory, recupera i dettagli dell'utente ed esegue l'autenticazione secondaria usando il metodo configurato dall'utente, ad esempio chiamata su cellulare, messaggio di testo o app per dispositivi mobili. 

6. Quando la richiesta di verifica di Multi-Factor Authentication ha esito positivo, Azure Multi-Factor Authentication comunica il risultato all'estensione Server dei criteri di rete.

7. Dopo che il tentativo di connessione è stato autenticato e autorizzato, il Server dei criteri di rete in cui è installata l'estensione invia un messaggio di *autorizzazione di accesso* RADIUS al server VPN, ovvero al client RADIUS.

8. All'utente viene concesso l'accesso alla porta virtuale nel server VPN e viene stabilito un tunnel VPN crittografato.

## <a name="prerequisites"></a>prerequisiti
Questa sezione illustra in modo dettagliato i prerequisiti da completare prima di eseguire l'integrazione di Azure Multi-Factor Authentication con Gateway Desktop remoto. Prima di iniziare, è necessario che siano soddisfatti i prerequisiti seguenti:

* Infrastruttura VPN
* Ruolo Servizi di accesso e criteri di rete
* Licenza di Azure Multi-Factor Authentication
* Software Windows Server
* Librerie
* Azure Active Directory con sincronizzazione con Active Directory locale 
* ID GUID di Azure Active Directory

### <a name="vpn-infrastructure"></a>Infrastruttura VPN
Questo articolo presuppone la presenza di un'infrastruttura VPN funzionante che usa Microsoft Windows Server 2016 e che il server VPN non sia attualmente configurato per inoltrare le richieste di connessione a un server RADIUS. In questo articolo si configurerà l'infrastruttura VPN per l'uso di un server RADIUS centrale.

Se non si dispone di un'infrastruttura VPN funzionante, è possibile crearne rapidamente una seguendo le indicazioni presenti nelle numerose esercitazioni sulla configurazione di una rete VPN disponibili nei siti Microsoft e di terze parti. 
            
### <a name="the-network-policy-and-access-services-role"></a>Ruolo di Servizi di accesso e criteri di rete

Servizi di accesso e criteri di rete offre le funzionalità di client e server RADIUS. Questo articolo presuppone che sia stato installato il ruolo Servizi di accesso e criteri di rete in un server membro o un controller di dominio nell'ambiente in uso. In questa guida si configurerà il protocollo RADIUS per una configurazione VPN. Installare il ruolo Servizi di accesso e criteri di rete in un server *diverso dal* server VPN.

Per informazioni sull'installazione del servizio ruolo Servizi di accesso e criteri di rete di Windows Server 2012 o versioni successive, vedere [Install a NAP Health Policy Server](https://technet.microsoft.com/library/dd296890.aspx) (Installare un server criteri di integrità Protezione accesso alla rete). Lo strumento Protezione accesso alla rete è deprecato in Windows Server 2016. Per una descrizione delle procedure consigliate per Server dei criteri di rete, inclusi i consigli sull'installazione di Server dei criteri di rete in un controller di dominio, vedere [Best Practices for NPS](https://technet.microsoft.com/library/cc771746) (Procedure consigliate per Server dei criteri di rete).

### <a name="azure-mfa-license"></a>Licenza di Azure MFA

È necessaria una licenza di Azure Multi-Factor Authentication, disponibile tramite una licenza autonoma di Azure AD Premium, Enterprise Mobility + Security o di Multi-Factor Authentication. Le licenze in base al consumo per Azure MFA, ad esempio le licenze per utente o per autenticazione, non sono compatibili con l'estensione Server dei criteri di rete. Per altre informazioni, vedere [Come ottenere Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md). A scopo di test, è possibile usare una sottoscrizione della versione di valutazione gratuita.

### <a name="windows-server-software"></a>Software Windows Server

L'estensione Server dei criteri di rete richiede Windows Server 2008 R2 SP1 o versione successiva, con il ruolo Servizi di accesso e criteri di rete installato. Tutti i passaggi in questa guida sono stati eseguiti con Windows Server 2016.

### <a name="libraries"></a>Librerie

Le librerie seguenti vengono installate automaticamente con l'estensione Server dei criteri di rete:

-   [Visual C++ Redistributable Packages per Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Modulo di Microsoft Azure Active Directory per Windows PowerShell versione 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Se il modulo PowerShell di Microsoft Azure Active Directory non è già presente, viene installato tramite uno script di configurazione eseguito come parte del processo di configurazione. Non è necessario installare il modulo in anticipo, se non è già installato.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory sincronizzato con Active Directory locale 

Per usare l'estensione Server dei criteri di rete, gli utenti locali devono essere sincronizzati con Azure Active Directory e abilitati per MFA. Questa guida presuppone che gli utenti locali siano sincronizzati con Azure Active Directory tramite Azure AD Connect. Le istruzioni per abilitare gli utenti per MFA sono disponibili più avanti.

Per informazioni su Azure AD Connect, vedere [Integrare le directory locali con Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>ID GUID di Azure Active Directory 

Per installare l'estensione Server dei criteri di rete, è necessario conoscere il GUID di Azure Active Directory. Le istruzioni per individuare il GUID di Azure Active Directory sono disponibili nella sezione seguente.

## <a name="configure-radius-for-vpn-connections"></a>Configurare RADIUS per le connessioni VPN

Se è stato installato il ruolo Server dei criteri di rete in un server membro, è necessario eseguirne la configurazione per autenticare e autorizzare il client VPN che richiede le connessioni VPN. 

Questa sezione presuppone che il ruolo Servizi di accesso e criteri di rete sia stato installato ma non configurato per l'uso nell'infrastruttura.

> [!NOTE]
> Se si ha già un server VPN funzionante che usa un server RADIUS centralizzato per l'autenticazione, è possibile ignorare questa sezione.
>

### <a name="register-server-in-active-directory"></a>Registrare il server in Active Directory
Per il corretto funzionamento in questo scenario, è necessario registrare Server dei criteri di rete in Active Directory.

1. Aprire Server Manager.

2. In Gestione server fare clic su **Strumenti** e quindi su **Server dei criteri di rete**. 

3. Nella console di Server dei criteri di rete fare clic con il pulsante destro del mouse su **Server dei criteri di rete (locale)** e quindi scegliere **Registra server in Active Directory**. Selezionare **OK** due volte.

    ![Server dei criteri di rete](./media/nps-extension-vpn/image2.png)

4. Lasciare aperta la console per la procedura successiva.

### <a name="use-wizard-to-configure-the-radius-server"></a>Usare la procedura guidata per configurare il server RADIUS
Per configurare il server RADIUS, è possibile usare un'opzione di configurazione standard (basata su procedura guidata) o avanzata. Questa sezione presuppone l'uso dell'opzione di configurazione standard basata su procedura guidata.

1. Nella console di Server dei criteri di rete selezionare **Server dei criteri di rete (locale)**.

2. In **Configurazione standard** selezionare **Server RADIUS per connessioni remote o VPN** e quindi scegliere **Configurazione VPN o connessioni remote**.

    ![Configurare la rete VPN](./media/nps-extension-vpn/image3.png)

3. Nella pagina **Selezione tipo di connessioni remote o a reti private virtuali** selezionare **Virtual Private Network Connections** (Connessioni a reti private virtuali) e quindi fare clic su **Avanti**.

    ![Rete privata virtuale](./media/nps-extension-vpn/image4.png)

4. Nella finestra **Specifica server di connessione remota o VPN** selezionare **Aggiungi**.

5. Nella finestra **Nuovo client RADIUS** specificare un nome descrittivo, immettere un nome o un indirizzo IP risolvibile del server VPN e quindi immettere una password segreta condivisa. Creare una password segreta condivisa lunga e complessa. Annotarla, poiché sarà necessaria nella sezione successiva.

    ![Nuovo client RADIUS](./media/nps-extension-vpn/image5.png)

6. Selezionare **OK**, quindi **Avanti**.

7. Nella finestra **Configurazione metodi di autenticazione** accettare l'opzione predefinita, ovvero **Autenticazione crittografata Microsoft versione 2 (MS-CHAP v2)**, oppure scegliere un'altra opzione e selezionare **Avanti**.

    > [!NOTE]
    > Se si configura il protocollo Extensible Authentication Protocol, è necessario usare Microsoft Challenge Handshake Authentication Protocol (CHAPv2) o PEAP Protected Extensible Authentication Protocol. Non sono supportate altre opzioni EAP.
 
8. Nella finestra **Specifica gruppi di utenti** selezionare **Aggiungi** e quindi scegliere un gruppo appropriato. Se non esiste alcun gruppo, lasciare vuota l'opzione per concedere l'accesso a tutti gli utenti.

    ![Finestra Specifica gruppi di utenti](./media/nps-extension-vpn/image7.png)

9. Selezionare **Avanti**.

10. Nella finestra **Specifica filtri IP** selezionare **Avanti**.

11. Nella finestra **Specifica impostazioni di crittografia** accettare le impostazioni predefinite e fare clic su **Avanti**.

    ![Finestra Specifica impostazioni di crittografia](./media/nps-extension-vpn/image8.png)

12. Nella finestra **Impostazione del nome dell'area di autenticazione** lasciare vuoto il nome dell'area di autenticazione, accettare l'impostazione predefinita e fare clic su **Avanti**.

    ![Finestra Impostazione del nome dell'area di autenticazione](./media/nps-extension-vpn/image9.png)

13. Nella finestra **Completamento Nuove connessioni remote o a reti private virtuali e client RADIUS** selezionare **Fine**.

    ![Finestra "Completamento Nuove connessioni remote o a reti private virtuali e client RADIUS"](./media/nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Verificare la configurazione RADIUS
Questa sezione descrive in modo dettagliato la configurazione creata usando la procedura guidata.

1. Nel Server dei criteri di rete, nella console Server dei criteri di rete (locale) espandere **Client RADIUS** e selezionare **Client RADIUS**.

2. Nel riquadro dei dettagli fare clic con il pulsante destro del mouse sul client RADIUS creato e scegliere **Proprietà**. Le proprietà del client RADIUS, ovvero del server VPN, dovrebbero essere simili a quelle mostrate qui:

    ![Proprietà VPN](./media/nps-extension-vpn/image11.png)

3. Selezionare **Annulla**.

4. In Server dei criteri di rete, nella console Server dei criteri di rete (locale) espandere **Criteri** e selezionare **Criteri di richiesta di connessione**. Vengono mostrati i criteri delle connessioni VPN come illustrato nella figura seguente:

    ![Richieste di connessione](./media/nps-extension-vpn/image12.png)

5. In **Criteri** selezionare **Criteri di rete**. Vengono visualizzati criteri di connessioni della rete privata virtuale simili a quelli illustrati nell'immagine seguente:

    ![Criteri di rete](./media/nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Configurare il server VPN per l'uso dell'autenticazione RADIUS
In questa sezione si configura il server VPN per l'uso dell'autenticazione RADIUS. Le istruzioni presuppongono che ci sia una configurazione funzionante del server VPN, ma che il server VPN non sia stato configurato per l'uso dell'autenticazione RADIUS. Dopo aver configurato il server VPN, verificare che la configurazione funzioni come previsto.

> [!NOTE]
> Se si ha già una configurazione del server VPN funzionante che usa l'autenticazione RADIUS, è possibile ignorare questa sezione.
>

### <a name="configure-authentication-provider"></a>Configurare il provider di autenticazione
1. Nel server VPN aprire Server Manager.

2. In Gestione server selezionare **Strumenti** e quindi scegliere **Routing e Accesso remoto**.

3. Nella finestra **Routing e Accesso remoto** fare clic con il pulsante destro del mouse sul **\<nome del server> (locale)** e quindi scegliere **Proprietà**.

    ![Finestra Routing e Accesso remoto](./media/nps-extension-vpn/image14.png)
 
4. Nella finestra **\<nome del server> (locale)** selezionare la scheda **Sicurezza**. 

5. Nella scheda **Sicurezza**, in **Provider di autenticazione** selezionare **Autenticazione RADIUS** e quindi **Configura**.

    ![Autenticazione RADIUS](./media/nps-extension-vpn/image15.png)
 
6. Nella finestra **Autenticazione RADIUS** selezionare **Aggiungi**.

7. Nella finestra **Aggiungi server RADIUS** eseguire le operazioni seguenti:

    a. Nella casella **Nome del server** immettere il nome o l'indirizzo IP del server RADIUS configurato nella sezione precedente.

    b. Per **Segreto condiviso** selezionare **Cambia** e immettere la password segreta condivisa creata e registrata in precedenza.

    c. Nella casella **Time-out (seconds)** (Timeout - secondi) selezionare un valore da **30** a **60**.  
    Il valore di timeout è necessario per concedere tempo sufficiente per il completamento del secondo fattore di autenticazione.
 
    ![Finestra Aggiungi server RADIUS](./media/nps-extension-vpn/image16.png)
 
8. Selezionare **OK**.

### <a name="test-vpn-connectivity"></a>Testare la connettività VPN
In questa sezione si verifica che il client VPN sia autenticato e autorizzato dal server RADIUS quando si cerca di connettersi alla porta virtuale nella rete VPN. Le istruzioni presuppongono che si usi Windows 10 come client VPN. 

> [!NOTE]
> Se è già stato configurato un client VPN per connettersi al server VPN e sono state salvate le impostazioni, è possibile ignorare i passaggi relativi alla configurazione e al salvataggio di un oggetto connessione VPN.
>

1. Nel computer del client VPN selezionare il pulsante **Start** e quindi selezionare il pulsante **Impostazioni**.

2. Nella finestra **Impostazioni di Windows** selezionare **Rete e Internet**.

3. Selezionare **VPN**.

4. Selezionare **Aggiungi una connessione VPN**.

5. Nella finestra **Aggiungi una connessione VPN** della casella **Provider VPN** selezionare **Windows (predefinito)**, completare i campi rimanenti in base alle esigenze e quindi fare clic su **Salva**. 

    ![Finestra "Aggiungi una connessione VPN](./media/nps-extension-vpn/image17.png)
 
6. Passare a **Pannello di controllo**, quindi selezionare **Centro connessioni di rete e condivisione**.

7. Fare clic su **Modifica impostazioni scheda**.

    ![Modifica impostazioni scheda](./media/nps-extension-vpn/image18.png)

8. Fare clic con il pulsante destro del mouse sulla connessione di rete VPN e scegliere **Proprietà**. 

    ![Proprietà rete VPN](./media/nps-extension-vpn/image19.png)

9. Nella finestra delle proprietà VPN selezionare la scheda **Sicurezza**. 

10. Nella scheda **Sicurezza** verificare che sia selezionata solo l'opzione **Microsoft CHAP Versione 2 (MS-CHAP v2)** e fare clic su **OK**.

    ![Opzione "Consenti questi protocolli"](./media/nps-extension-vpn/image20.png)

11. Fare clic con il pulsante destro del mouse sulla connessione VPN e scegliere **Connetti**.

12. Nella finestra **Impostazioni** selezionare **Connetti**.  
    Una connessione riuscita viene indicata nel log di sicurezza del server RADIUS con l'ID evento 6272, come illustrato di seguito:

    ![Finestra Proprietà evento](./media/nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Risoluzione dei problemi nel server RADIUS

Si supponga che la configurazione VPN funzionasse prima della configurazione del server VPN per l'uso di un server RADIUS centralizzato per l'autenticazione e l'autorizzazione. In questo caso, è probabile che il problema sia causato da un errore di configurazione del server RADIUS o dall'uso di una password o un nome utente non valido. Ad esempio, se si usa il suffisso alternativo UPN nel nome utente, il tentativo di accesso potrebbe non riuscire. Usare lo stesso nome di account per ottenere risultati ottimali. 

Per risolvere questi problemi, è consigliabile iniziare esaminando i log eventi di sicurezza nel server RADIUS. Per risparmiare tempo nella ricerca degli eventi, è possibile usare la visualizzazione personalizzata basata sui ruoli del server di accesso e dei criteri di rete nel Visualizzatore eventi, come illustrato di seguito. L'"ID evento 6273" indica gli eventi in cui Server dei criteri di rete ha negato l'accesso a un utente. 

![Aprire il Visualizzatore eventi](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Configurare Multi-Factor Authentication

Per informazioni sulla configurazione degli utenti per Multi-Factor Authentication, vedere gli articoli [Come richiedere la verifica in due passaggi per un utente o un gruppo](multi-factor-authentication-get-started-user-states.md) e [Configurare l'account per la verifica in due passaggi](multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Installare e configurare l'estensione Server dei criteri di rete

Questa sezione contiene istruzioni per configurare la rete VPN al fine di usare Multi-Factor Authentication per l'autenticazione client con il server VPN.

Dopo aver installato e configurato l'estensione Server dei criteri di rete, tutte le autenticazioni client basate su RADIUS elaborate da questo server devono usare Multi-Factor Authentication. Se non tutti gli utenti VPN vengono registrati in Azure Multi-Factor Authentication, è possibile eseguire una delle operazioni seguenti:

* Configurare un altro server RADIUS per autenticare gli utenti che non sono configurati al fine di usare Multi-Factor Authentication. 

* Creare una voce del registro di sistema che consenta agli utenti che hanno inviato la richiesta di offrire un secondo fattore di autenticazione se sono registrati in Azure Multi-Factor Authentication. 

Creare un nuovo valore stringa denominato _REQUIRE_USER_MATCH in HKLM\SOFTWARE\Microsoft\AzureMFA_ e impostare il valore su *True* o *False*. 

![Impostazioni della "Richiesta di corrispondenza utente"](./media/nps-extension-vpn/image34.png)
 
Se il valore è impostato su *True* o non è impostato, tutte le richieste di autenticazione sono soggette a verifica MFA. Se il valore è impostato su *False*, le verifiche MFA vengono eseguite solo per gli utenti registrati in Azure Multi-Factor Authentication. Usare l'impostazione *False* solo a scopo di test o negli ambienti di produzione durante un periodo di onboarding.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Ottenere l'ID GUID di Azure Active Directory

Come parte della configurazione dell'estensione Server dei criteri di rete, è necessario inserire le credenziali di amministratore e l'ID per il tenant di Azure AD. Ottenere l'ID eseguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale del tenant di Azure.

2. Selezionare il pulsante **Azure Active Directory** nel riquadro sinistro.

3. Selezionare **Proprietà**.

4. Per copiare l'ID di Azure AD, selezionare il pulsante **Copia**.
 
    ![ID di Azure AD](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Installare l'estensione di Server dei criteri di rete
L'estensione Server dei criteri di rete deve essere installata in un server con il ruolo Servizi di accesso e criteri di rete installato e che funzioni come server RADIUS nella progettazione. *Non* installare l'estensione Server dei criteri di rete nel server Desktop remoto.

1. Scaricare l'estensione di Server dei criteri di rete dall'[Area download Microsoft](https://aka.ms/npsmfa). 

2. Copiare il file eseguibile di configurazione, *NpsExtnForAzureMfaInstaller.exe* nel Server dei criteri di rete.

3. In Server dei criteri di rete fare doppio clic su **NpsExtnForAzureMfaInstaller.exe** e, se viene richiesto, selezionare **Esegui**.

4. Nella finestra **NPS Extension For Azure MFA Setup** (Estensione Server dei criteri di rete per la configurazione di Azure MFA) leggere le condizioni di licenza del software, selezionare la casella di testo **Accetto i termini e le condizioni di licenza** e fare clic su **Installa**.

    ![Finestra "NPS Extension For Azure MFA Setup" (Estensione Server dei criteri di rete per la configurazione di Azure MFA)](./media/nps-extension-vpn/image36.png)
 
5. Nella finestra **NPS Extension For Azure MFA Setup** (Estensione Server dei criteri di rete per la configurazione di Azure MFA) selezionare **Chiudi**.  

    ![Finestra di conferma "Installazione completata"](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Configurare i certificati per l'uso con l'estensione Server dei criteri di rete tramite uno script di PowerShell
Per garantire comunicazioni protette e sicure, configurare i certificati che l'estensione Server dei criteri di rete può usare. I componenti di Server dei criteri di rete includono uno script di Windows PowerShell che configura un certificato autofirmato per l'uso con Server dei criteri di rete. 

Lo script esegue le azioni seguenti:

* Crea un certificato autofirmato.
* Associa la chiave pubblica del certificato all'entità servizio in Azure AD.
* Archivia il certificato nell'archivio del computer locale.
* Concede l'accesso alla chiave privata del certificato all'utente di rete.
* Riavvia il servizio Server dei criteri di rete.

Per usare certificati personali, è necessario associare la chiave pubblica del certificato all'entità servizio in Azure AD e così via.

Per usare lo script, specificare l'estensione con le credenziali amministrative di Azure Active Directory e l'ID tenant di Azure Active Directory copiato in precedenza. Eseguire lo script in ogni server NPS in cui si installa l'estensione NPS.

1. Eseguire Windows PowerShell come amministratore.

2. Nel prompt dei comandi di PowerShell digitare **cd "c:\Programmi\Microsoft\AzureMfa\Config"** e premere Invio.

3. Nel prompt dei comandi successivo digitare **.\AzureMfsNpsExtnConfigSetup.ps1** e quindi premere Invio. Lo script verifica che il modulo di PowerShell per Azure AD sia installato. Se non è installato, lo installa.
 
    ![PowerShell](./media/nps-extension-vpn/image38.png)
 
    Dopo che lo script ha verificato l'installazione del modulo di PowerShell, viene visualizzata la finestra di accesso relativa al modulo di PowerShell per Azure Active Directory. 

4. Immettere le credenziali di amministratore di Azure AD e fare clic su **Accedi**. 
 
    ![Finestra di accesso di PowerShell](./media/nps-extension-vpn/image39.png)
 
5. Nel prompt dei comandi incollare l'ID tenant copiato in precedenza e quindi premere Invio. 

    ![ID tenant](./media/nps-extension-vpn/image40.png)

    Lo script crea un certificato autofirmato e apporta altre modifiche alla configurazione. L'output è simile a quello mostrato nella figura seguente:

    ![Certificato autofirmato](./media/nps-extension-vpn/image41.png)

6. Riavviare il server.

### <a name="verify-the-configuration"></a>Verificare la configurazione
Per verificare la configurazione, è necessario stabilire una nuova connessione VPN con il server VPN. Dopo aver inserito correttamente le credenziali per l'autenticazione primaria, la connessione VPN attende il completamento dell'autenticazione secondaria prima di stabilire la connessione, come illustrato di seguito. 

![Finestra delle Impostazioni VPN di Windows](./media/nps-extension-vpn/image42.png)

Se l'autenticazione con il metodo di verifica secondario configurato in precedenza in Azure MFA avviene correttamente, viene stabilita una connessione alla risorsa. Se invece l'autenticazione secondaria non ha esito positivo, l'accesso alla risorsa viene negato. 

Nell'esempio seguente viene usata l'app Microsoft Authenticator su un dispositivo Windows Phone per eseguire l'autenticazione secondaria:

![Verificare l'account](./media/nps-extension-vpn/image43.png)

Dopo aver eseguito correttamente l'autenticazione con il metodo secondario, viene concesso l'accesso alla porta virtuale nel server VPN. Poiché è stato richiesto di usare un metodo di autenticazione secondaria tramite un'app per dispositivi mobili in un dispositivo attendibile, il processo di accesso è più sicuro di quanto sarebbe stato usando solo una combinazione di nome utente e password.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Visualizzare i log del Visualizzatore eventi per individuare gli eventi di accesso riusciti
Per visualizzare gli eventi di accesso riusciti nei log del Visualizzatore eventi di Windows, usare il comando seguente di PowerShell per eseguire una query sul log di sicurezza di Windows nel Server dei criteri di rete:

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

![Visualizzatore eventi di sicurezza di PowerShell](./media/nps-extension-vpn/image44.png)
 
È anche possibile aprire la visualizzazione personalizzata del log di sicurezza o di Servizi di accesso e criteri di rete, come illustrato di seguito:

![Log di Server dei criteri di rete](./media/nps-extension-vpn/image45.png)

Nel server in cui è installata l'estensione Server dei criteri di rete per Azure Multi-Factor Authentication è possibile trovare i log applicazioni del Visualizzatore eventi specifici per l'estensione in *Registri applicazioni e servizi\Microsoft\AzureMfa*. 

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

![Riquadro "Numero di eventi" del Visualizzatore eventi](./media/nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Guida per la risoluzione dei problemi
Se la configurazione non funziona come previsto, iniziare la risoluzione dei problemi verificando che l'utente sia configurato per l'uso di Azure MFA. Chiedere all'utente di connettersi al [portale di Azure](https://portal.azure.com). Se viene richiesta l'autenticazione secondaria ed possibile eseguire correttamente l'autenticazione, è possibile eliminare una configurazione errata di Multi-Factor Authentication come un problema.

Se Multi-Factor Authentication funziona correttamente, esaminare i log del Visualizzatore eventi pertinenti. I log includo i log operativi del gateway e quelli relativi a eventi di sicurezza, nonché i log di Azure MFA illustrati nella sezione precedente. 

Di seguito è riportato un esempio di registro di protezione che consente di visualizzare un evento di accesso non riuscito, ID evento 6273:

![Registro di protezione che mostra un evento di accesso non riuscito](./media/nps-extension-vpn/image47.png)

Un evento correlato dal log di Azure Multi-Factor Authentication è illustrato di seguito:

![Log di Azure Multi-Factor Authentication](./media/nps-extension-vpn/image48.png)

Per la risoluzione dei problemi avanzata, consultare i file di log in formato database di Server dei criteri di rete nella posizione in cui è installato il servizio Server dei criteri di rete. I file di log vengono creati nella cartella _%SystemRoot%\System32\Logs_ come file di testo con valori delimitati da virgole. Per una descrizione dei file di log, vedere [Interpret NPS Database Format Log Files](https://technet.microsoft.com/library/cc771748.aspx) (Interpretare i file di log in formato database di Server dei criteri di rete). 

Le voci di questi file di log sono difficili da interpretare senza esportarle in un foglio di calcolo o in un database. È possibile trovare molti strumenti di analisi Servizio Autenticazione Internet online per semplificare l'interpretazione dei file di log. Di seguito è riportato l'output di una di queste [applicazioni shareware](http://www.deepsoftware.com/iasviewer) che è possibile scaricare: 

![Applicazione shareware](./media/nps-extension-vpn/image49.png)

Per altre opzioni di risoluzione dei problemi, è possibile usare uno strumento di analisi di protocolli, ad esempio Wireshark o [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). L'immagine seguente relativa a Wireshark mostra i messaggi RADIUS tra il server VPN e il Server dei criteri di rete.

![Microsoft Message Analyzer](./media/nps-extension-vpn/image50.png)

Per altre informazioni, vedere [Integrare l'infrastruttura NPS esistente con Azure Multi-Factor Authentication](multi-factor-authentication-nps-extension.md). 

## <a name="next-steps"></a>Passaggi successivi
[Ottenere Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

[Gateway Desktop remoto e server Azure Multi-Factor Authentication utilizzando RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integrare le directory locali con Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)

