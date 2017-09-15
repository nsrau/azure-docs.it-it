---
title: Integrazione della rete VPN con Azure MFA tramite l'estensione NPS | Microsoft Docs
description: Questo articolo illustra come integrare l'infrastruttura VPN con Azure MFA usando l'estensione NPS (Network Policy Server, Server dei criteri di rete) per Microsoft Azure.
services: active-directory
keywords: Azure MFA, integrazione VPN, Azure Active Directory, estensione di Server dei criteri di rete
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 3dfcf25856ede50266336c2ebb057dd3f7b8897e
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---

# <a name="integrate-your-vpn-infrastructure-with-azure-multi-factor-authentication-mfa-using-the-network-policy-server-nps-extension-for-azure"></a>Integrare l'infrastruttura VPN con Azure Multi-Factor Authentication (MFA) usando l'estensione NPS (Network Policy Server, Server dei criteri) per Azure

## <a name="overview"></a>Panoramica

L'estensione NPS (Network Policy Server, Server dei criteri) per Azure consente alle organizzazioni di proteggere l'autenticazione di client RADIUS (Remote Authentication Dial-In User Service) tramite il servizio [Azure Multi-Factor Authentication (MFA)](multi-factor-authentication-get-started-server-rdg.md) basato sul cloud, che offre la verifica in due passaggi.

Questo articolo fornisce istruzioni per l'integrazione dell'infrastruttura NPS con Azure MFA tramite l'estensione NPS per Azure, al fine di consentire la verifica in due passaggi sicura per gli utenti che cercano di connettersi alla rete tramite VPN. 

Servizi di accesso e criteri di rete offre alle organizzazioni le possibilità seguenti:

* Specificare posizioni centrali per la gestione e il controllo delle richieste di rete, per indicare quali utenti possono connettersi, le ore del giorno in cui sono consentite le connessioni, la durata delle connessioni, il livello di sicurezza che i client devono usare per la connessione e così via. Invece che specificare questi criteri in ogni VPN o server Gateway Desktop remoto, è possibile specificarli una sola volta in una posizione centrale. Il protocollo RADIUS viene usato per fornire servizi centralizzati di autenticazione, autorizzazione e accounting. 
* Stabilire e applicare criteri di integrità client di Protezione accesso alla rete che determinano se ai dispositivi viene concesso l'accesso alle risorse di rete con o senza restrizioni.
* Fornire un mezzo per imporre l'autenticazione e l'autorizzazione per l'accesso a commutatori Ethernet e punti di accesso wireless che supportano 802.1x.    

Per altre informazioni, vedere [Server dei criteri di rete (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Per migliorare la sicurezza e fornire un elevato livello di conformità, le organizzazioni possono integrare Server dei criteri di rete con Azure MFA per garantire che gli utenti usino la verifica in due passaggi per connettersi alla porta virtuale sul server VPN. Affinché venga concesso loro l'accesso, gli utenti devono specificare la combinazione di nome utente/password con informazioni sotto il controllo dell'utente. Queste informazioni devono essere attendibili e non facilmente duplicabili, ad esempio un numero di telefono cellulare, un numero di rete fissa, un'applicazione in un dispositivo mobile e così via.

Prima che fosse disponibile l'estensione NPS per Azure, i clienti che volevano implementare la verifica in due passaggi per ambienti Server dei criteri di rete e Azure MFA integrati dovevano configurare e gestire un server MFA distinto nell'ambiente locale, come documentato nello scenario relativo a Gateway Desktop remoto e server Azure Multi-Factor Authentication con RADIUS.

La disponibilità dell'estensione NPS per Azure offre ora alle organizzazioni la possibilità di scegliere se distribuire una soluzione MFA locale o una soluzione MFA basata sul cloud per l'autenticazione sicura dei client RADIUS.
 
## <a name="authentication-flow"></a>Flusso di autenticazione
Quando un utente si connette a una porta virtuale su un server VPN, deve prima autenticarsi usando diversi protocolli, che consentono l'uso di una combinazione di nome utente/password e metodi di autenticazione basati su certificati. 

Oltre all'autenticazione e alla verifica dell'identità, gli utenti devono avere le autorizzazioni appropriate per l'accesso esterno. Nelle implementazioni semplici, queste autorizzazioni di accesso vengono impostate direttamente negli oggetti utente di Active Directory. 

 ![Proprietà utente](./media/nps-extension-vpn/image1.png)

Per le implementazioni semplici, ogni server VPN concede o nega l'accesso in base a criteri definiti in ogni server VPN locale.

Nelle implementazioni più grandi e più scalabili, i criteri che concedono o negano l'accesso VPN sono centralizzati nei server RADIUS. In questo caso, il server VPN funge da server di accesso (client RADIUS) che inoltra le richieste di connessione e i messaggi degli account a un server RADIUS. Per connettersi alla porta virtuale nel server VPN, gli utenti devono essere autenticati e soddisfare le condizioni definite in modo centralizzato nei server RADIUS. 

Quando l'estensione NPS per Azure è integrata con Server dei criteri di rete, il corretto flusso di autenticazione è quello illustrato di seguito:

1. Il server VPN riceve da un utente VPN una richiesta di autenticazione che include il nome utente e la password per la connessione a una risorsa, ad esempio una sessione Desktop remoto. 
2. Fungendo da client RADIUS, il server VPN converte la richiesta in un messaggio di richiesta di accesso RADIUS e invia il messaggio (con password crittografata) al server RADIUS (Server dei criteri di rete) in cui è installata l'estensione NPS. 
3. La combinazione di nome utente e password viene verificata in Active Directory. Se la combinazione nome di utente e password è errata, il server RADIUS invia un messaggio di rifiuto di accesso. 
4. Se vengono soddisfatte tutte le condizioni specificate nella richiesta di connessione NPS e nei criteri di rete (ad esempio per quanto riguarda le restrizioni relative all'ora del giorno o all'appartenenza a gruppi), l'estensione NPS attiva una richiesta di autenticazione secondaria con Azure MFA. 
5. Azure MFA comunica con Azure Active Directory, recupera i dettagli dell'utente ed esegue l'autenticazione secondaria usando il metodo configurato dall'utente (messaggio di testo, app per dispositivi mobili e così via). 
6. Una volta completata la verifica MFA, Azure MFA comunica il risultato all'estensione NPS.
7. Dopo che il tentativo di connessione è stato autenticato e autorizzato, il server NPS in cui è installata l'estensione invia un messaggio di autorizzazione di accesso RADIUS al server VPN (client RADIUS).
8. All'utente viene concesso l'accesso alla porta virtuale nel server VPN e viene stabilito un tunnel VPN crittografato.

## <a name="prerequisites"></a>Prerequisiti
Questa sezione illustra in modo dettagliato i prerequisiti necessari per l'integrazione di Azure MFA con Gateway Desktop remoto. Prima di iniziare, è necessario che siano soddisfatti i prerequisiti seguenti.

* Infrastruttura VPN
* Ruolo Servizi di accesso e criteri di rete
* Licenza di Azure MFA
* Software Windows Server
* Librerie
* Azure AD con sincronizzazione con AD in locale 
* ID GUID di Azure Active Directory

### <a name="vpn-infrastructure"></a>Infrastruttura VPN
Questo articolo presuppone la presenza di un'infrastruttura VPN funzionante che usa Microsoft Windows Server 2016 sul posto e che il server VPN non sia attualmente configurato per inoltrare le richieste di connessione a un server RADIUS. In questa guida si configurerà l'infrastruttura VPN per l'uso di un server RADIUS centrale.

Se non si ha un'infrastruttura funzionante sul posto, è possibile crearne rapidamente una seguendo le indicazioni fornite nelle numerose esercitazioni sulla configurazione di una rete VPN disponibili nei siti Microsoft e di terze parti. 

### <a name="network-policy-and-access-services-nps-role"></a>Ruolo Servizi di accesso e criteri di rete

Il servizio ruolo NPS fornisce le funzionalità di client e server RADIUS. Questo articolo presuppone che sia stato installato il ruolo NPS in un server membro o un controller di dominio nell'ambiente in uso. In questa guida si configurerà il protocollo RADIUS per una configurazione VPN. Installare il ruolo NPS in un server _diverso_ dal server VPN.

Per informazioni sull'installazione del servizio ruolo NPS di Windows Server 2012 o versioni successive, vedere [Install a NAP Health Policy Server](https://technet.microsoft.com/library/dd296890.aspx) (Installare un server criteri di integrità Protezione accesso alla rete). Lo strumento Protezione accesso alla rete è deprecato in Windows Server 2016. Per una descrizione delle procedure consigliate per Server dei criteri di rete, inclusi i consigli sull'installazione di Server dei criteri di rete in un controller di dominio, vedere [Best Practices for NPS](https://technet.microsoft.com/library/cc771746) (Procedure consigliate per Server dei criteri di rete).

### <a name="licenses"></a>Licenze

È richiesta una licenza per Azure MFA, disponibile tramite una sottoscrizione di Azure AD Premium, Enterprise Mobility + Security (EMS) o MFA. Per altre informazioni, vedere [Come ottenere Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md). A scopo di test, è possibile usare una sottoscrizione della versione di valutazione gratuita.

### <a name="software"></a>Software

L'estensione NPS richiede Windows Server 2008 R2 SP1 o versione successiva con il servizio ruolo NPS installato. Tutti i passaggi in questa guida sono stati eseguiti usando Windows Server 2016.

### <a name="libraries"></a>Librerie

Sono necessarie le due librerie seguenti:

* [Visual C++ Redistributable Packages per Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
* _Modulo di Microsoft Azure Active Directory per Windows PowerShell versione 1.1.166.0_ o successiva. Per la versione più recente e le istruzioni di installazione, vedere [Microsoft Azure Active Directory PowerShell Module Version Release History](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx) (Cronologia delle versioni del modulo di Microsoft Azure Active Directory per Windows PowerShell).

Queste librerie non sono incluse con i file di installazione dell'estensione NPS (versione 0.9.1.2), anche se nella documentazione esistente viene affermato il contrario. È necessario installare almeno Visual C++ Redistributable Package per Visual Studio 2013. Il modulo di Microsoft Azure Active Directory per Windows PowerShell viene installato, se non è già presente, tramite uno script di configurazione eseguito come parte del processo di installazione. Non è necessario installare il modulo in anticipo, se non è già installato.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory con sincronizzazione con Active Directory locale 

Per usare l'estensione NPS, gli utenti locali devono essere sincronizzati con Azure Active Directory e abilitati per Multi-Factor Authentication. Questa guida presuppone che gli utenti locali siano sincronizzati con Azure Active Directory tramite AD Connect. Le istruzioni per abilitare gli utenti per MFA sono disponibili più avanti.
Per informazioni su Azure AD Connect, vedere [Integrare le directory locali con Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>ID GUID di Azure Active Directory 
Per installare Server dei criteri di rete, è necessario conoscere il GUID di Azure Active Directory. Le istruzioni per individuare il GUID di Azure Active Directory sono disponibili nella sezione seguente.

## <a name="configure-radius-for-vpn-connections"></a>Configurare RADIUS per le connessioni VPN

Se è stato installato il ruolo del server NPS in un server membro, è necessario eseguire la configurazione per autenticare e autorizzare il client VPN che richiede le connessioni VPN. 

Questa sezione presuppone che il ruolo Server dei criteri di rete sia stato installato ma non configurato per l'uso nell'infrastruttura.

>[!NOTE]
>Se si ha già un server VPN funzionante che usa un server RADIUS centralizzato per l'autenticazione, è possibile ignorare questa sezione.
>

### <a name="register-server-in-active-directory"></a>Registrare il server in Active Directory
Per il corretto funzionamento in questo scenario, è necessario registrare il server NPS in Active Directory.

1. Aprire Server Manager.
2. In Server Manager fare clic su **Strumenti** e quindi su **Server dei criteri di rete**. 
3. Nella console di Server dei criteri di rete fare clic con il pulsante destro del mouse su **Server dei criteri di rete (locale)** e quindi scegliere **Registra server in Active Directory**. Fare clic su **OK** due volte.

 ![Server dei criteri di rete](./media/nps-extension-vpn/image2.png)

4. Lasciare aperta la console per la procedura successiva.

### <a name="use-wizard-to-configure-radius-server"></a>Usare la procedura guidata per configurare il server RADIUS
Per configurare il server RADIUS, è possibile usare un'opzione di configurazione standard (basata su procedura guidata) o avanzata. Questa sezione presuppone l'uso dell'opzione di configurazione standard basata su procedura guidata.

1. Nella console di Server dei criteri di rete fare clic su **Server dei criteri di rete (locale)**.
2. In Configurazione standard selezionare **Server RADIUS per connessioni remote o VPN** e quindi fare clic su **Configurazione VPN o connessioni remote**.

 ![Configurare la rete VPN](./media/nps-extension-vpn/image3.png)

3. Nella pagina Selezione tipo di connessioni remote o a reti private virtuali selezionare **Connessioni a reti private virtuali (VPN)** e quindi fare clic su **Avanti**.

 ![Rete privata virtuale](./media/nps-extension-vpn/image4.png)

4. Nella pagina Specifica server di connessione remota o VPN fare clic su **Aggiungi**.
5. Nella finestra di dialogo **Nuovo client RADIUS** specificare un nome descrittivo, immettere un nome risolvibile o un indirizzo IP del server VPN e immettere una password segreta condivisa. Creare una password segreta condivisa lunga e complessa. Registrare la password, che sarà necessaria per i passaggi nella sezione successiva.

 ![Nuovo client RADIUS](./media/nps-extension-vpn/image5.png)

6. Fare clic su **OK** e quindi su **Avanti**.
7. Nella pagina **Configurazione metodi di autenticazione** accettare l'opzione predefinita, ovvero Autenticazione crittografata Microsoft versione 2 (MS-CHAP v2), oppure scegliere un'altra opzione e fare clic su **Avanti**.

  >[!NOTE]
  >Se si configura il protocollo EAP (Extensible Authentication Protocol), è necessario usare MS CHAPv2 o PEAP. Non sono supportate altre opzioni EAP.
 
8. Nella pagina Specifica gruppi di utenti fare clic su **Aggiungi** e selezionare un gruppo appropriato, se disponibile. In caso contrario, lasciare vuota l'opzione per concedere l'accesso a tutti gli utenti.

 ![Specificare i gruppi di utenti](./media/nps-extension-vpn/image7.png)

9. Fare clic su **Avanti**.
10. Nella pagina Specifica filtri IP fare clic su **Avanti**.
11. Nella pagina Specifica impostazioni di crittografia accettare le impostazioni predefinite e fare clic su **Avanti**.

 ![Specificare la crittografia](./media/nps-extension-vpn/image8.png)

12. Nella pagina Impostazione del nome dell'area di autenticazione lasciare vuoto il nome, accettare l'impostazione predefinita e fare clic su **Avanti**.

 ![Specificare un nome dell'area di autenticazione](./media/nps-extension-vpn/image9.png)

13. Nella pagina Completamento Nuove connessioni remote o a reti private virtuali e client RADIUS fare clic su **Fine**.

 ![Completare le connessioni](./media/nps-extension-vpn/image10.png)

### <a name="verify-radius-configuration"></a>Verificare la configurazione RADIUS
Questa sezione descrive in modo dettagliato la configurazione creata usando la procedura guidata.

1. Nel server NPS, nella console Server dei criteri di rete (locale), espandere Client RADIUS e selezionare **Client RADIUS**.
2. Nel riquadro dei dettagli fare clic con il pulsante destro del mouse sul client RADIUS creato usando la procedura guidata e scegliere **Proprietà**. Le proprietà del client RADIUS (server VPN) dovrebbero essere simili a quelle riportate di seguito.

 ![Proprietà VPN](./media/nps-extension-vpn/image11.png)

3. Fare clic su **Annulla**.
4. Nel server NPS, nella console Server dei criteri di rete (locale), espandere **Criteri** e selezionare **Criteri di richiesta di connessione**. Dovrebbero venire visualizzati criteri di connessioni VPN simili a quelli illustrati nell'immagine seguente.

 ![Richieste di connessione](./media/nps-extension-vpn/image12.png)

5. In Criteri selezionare **Criteri di rete**. Dovrebbero venire visualizzati criteri di connessioni VPN (rete privata virtuale) simili a quelli illustrati nell'immagine seguente.

 ![Proprietà di rete](./media/nps-extension-vpn/image13.png)

## <a name="configure-vpn-server-to-use-radius-authentication"></a>Configurare il server VPN per l'uso dell'autenticazione RADIUS
In questa sezione si configura il server VPN per l'uso dell'autenticazione RADIUS. Questa sezione presuppone che ci sia una configurazione funzionante del server VPN, ma che il server VPN non sia stato configurato per l'uso dell'autenticazione RADIUS. Dopo aver configurato il server VPN, verificare che la configurazione funzioni come previsto.

>[!NOTE]
>Se si ha già una configurazione del server VPN funzionante che usa l'autenticazione RADIUS, è possibile ignorare questa sezione.
>

### <a name="configure-authentication-provider"></a>Configurare il provider di autenticazione
1. Nel server VPN aprire Server Manager.
2. In Server Manager fare clic su **Strumenti** e quindi su **Routing e Accesso remoto**.
3. Nella console di Routing e accesso remoto fare clic con il pulsante destro del mouse su **\[Nome server\] (locale)** e quindi scegliere **Proprietà**.

 ![Routing e accesso remoto](./media/nps-extension-vpn/image14.png)
 
4. Nella finestra di dialogo **Proprietà di [Nome server] (locale)** fare clic sulla scheda **Sicurezza**. 
5. Nella scheda **Sicurezza**, nella sezione Provider di autenticazione, fare clic su **Autenticazione RADIUS** e quindi su **Configura**.

 ![Autenticazione RADIUS](./media/nps-extension-vpn/image15.png)
 
6. Nella finestra di dialogo Autenticazione RADIUS fare clic su **Aggiungi**.
7. Nella sezione Nome server della finestra Aggiungi server RADIUS aggiungere il nome o l'indirizzo IP del server RADIUS configurato nella sezione precedente.
8. In Segreto condiviso fare clic su **Cambia** e aggiungere la password segreta condivisa creata e registrata in precedenza.
9. In Timeout (secondi) modificare il valore scegliendone uno compreso tra **30** e **60**. Questa impostazione è necessaria per consentire tempo sufficiente per il completamento del secondo fattore di autenticazione.
 
 ![Aggiungere il server RADIUS](./media/nps-extension-vpn/image16.png)
 
10. Fare clic su **OK** fino a chiudere tutte le finestre di dialogo.

### <a name="test-vpn-connectivity"></a>Testare la connettività VPN
In questa sezione si verifica che il client VPN sia autenticato e autorizzato dal server RADIUS quando si cerca di connettersi alla porta virtuale nella rete VPN. Questa sezione presuppone l'uso di Windows 10 come client VPN. 

>[!NOTE]
>Se è già stato configurato un client VPN per connettersi al server VPN e sono state salvate le impostazioni, è possibile ignorare i passaggi relativi alla configurazione e al salvataggio di un oggetto connessione VPN.
>

1. Nel computer client VPN fare clic su **Start** e quindi su **Impostazioni** (icona a forma di ingranaggio).
2. In Impostazioni di Windows fare clic su **Rete e Internet**.
3. Fare clic su **VPN**.
4. Fare clic su **Aggiungi connessione VPN**.
5. In Aggiungi connessione VPN specificare Windows (predefinito) come provider VPN, completare i campi rimanenti come appropriato e quindi fare clic su **Salva**. 

 ![Aggiungere la connessione VPN](./media/nps-extension-vpn/image17.png)
 
6. Aprire **Centro connessioni di rete e condivisione** nel Pannello di controllo.
7. Fare clic su **Modifica impostazioni scheda**.

 ![Modificare le impostazioni della scheda](./media/nps-extension-vpn/image18.png)

8. Fare clic con il pulsante destro del mouse sulla connessione di rete VPN e scegliere Proprietà. 

 ![Proprietà rete VPN](./media/nps-extension-vpn/image19.png)

9. Nella finestra di dialogo Proprietà VPN fare clic sulla scheda **Sicurezza**. 
10. Nella scheda Sicurezza verificare che sia selezionata solo l'opzione **Microsoft CHAP versione 2 (MS-CHAP v2)** e fare clic su OK.

 ![Consentire i protocolli](./media/nps-extension-vpn/image20.png)

11. Fare clic con il pulsante destro del mouse sulla connessione VPN e scegliere **Connetti**.
12. Nella pagina Impostazioni fare clic su **Connetti**.

Una connessione riuscita viene indicata nel log di sicurezza del server RADIUS con l'ID evento 6272, come illustrato di seguito.

 ![Proprietà dell'evento](./media/nps-extension-vpn/image21.png)

## <a name="troubleshoot-guide"></a>Guida per la risoluzione dei problemi
Si supponga che la configurazione VPN funzionasse prima della configurazione del server VPN per l'uso di un server RADIUS centralizzato per l'autenticazione e l'autorizzazione. In questo caso, è probabile che il problema sia causato da un errore di configurazione del server RADIUS o dall'uso di una password o un nome utente non valido. Se, ad esempio, si usa il suffisso UPN alternativo nel nome utente, il tentativo di accesso potrebbe non riuscire (per ottenere risultati ottimali, è necessario usare lo stesso nome account). 

Per risolvere questi problemi, è consigliabile iniziare esaminando i log eventi di sicurezza nel server RADIUS. Per risparmiare tempo nella ricerca degli eventi, è possibile usare la visualizzazione predefinita basata sui ruoli del server di accesso e dei criteri di rete nel Visualizzatore eventi, come illustrato di seguito. L'ID evento 6273 indica gli eventi in cui Server dei criteri di rete ha negato l'accesso a un utente. 

 ![Aprire il Visualizzatore eventi](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Configurare Multi-Factor Authentication
Questa sezione fornisce istruzioni per abilitare gli utenti per MFA e configurare gli account per la verifica in due passaggi. 

### <a name="enable-multi-factor-authentication"></a>Abilitare Multi-Factor Authentication
In questa sezione si abilitano gli account Azure AD per MFA. Per abilitare gli utenti per MFA, usare il **portale classico**. 

1. Aprire un browser e passare a [https://manage.windowsazure.com](https://manage.windowsazure.com). 
2. Accedere come amministratore.
3. Nel riquadro di spostamento sinistro del portale fare clic su **ACTIVE DIRECTORY**.

 ![Directory predefinita](./media/nps-extension-vpn/image23.png)

4. Nella colonna NOME fare clic su **Directory predefinita** (o un'altra directory, se appropriato).
5. Nella pagina Avvio rapido fare clic su **Configura**.

 ![Impostazioni predefinite di configurazione](./media/nps-extension-vpn/image24.png)

6. Nella pagina CONFIGURA scorrere verso il basso e nella sezione autenticazione a più fattori fare clic su **Gestisci impostazioni del servizio**.

 ![Gestire le impostazioni MFA](./media/nps-extension-vpn/image25.png)
 
7. Nella pagina autenticazione a più fattori esaminare le impostazioni predefinite del servizio e quindi fare clic su **Utenti**. 

 ![Utenti MFA](./media/nps-extension-vpn/image26.png)
 
8. Nella pagina Utenti selezionare gli utenti da abilitare per MFA e quindi fare clic su **Abilita**.

 ![Proprietà](./media/nps-extension-vpn/image27.png)
 
9. Quando viene chiesto, fare clic su **Abilita Multi-Factor Auth**.

 ![Abilitare MFA](./media/nps-extension-vpn/image28.png)
 
10. Fare clic su **Close**. 
11. Aggiornare la pagina. Lo stato di MFA viene indicato come abilitato.

Per informazioni su come abilitare gli utenti per Multi-Factor Authentication, vedere [Introduzione ad Azure Multi-Factor Authentication nel cloud](multi-factor-authentication-get-started-cloud.md). 

### <a name="configure-accounts-for-two-step-verification"></a>Configurare gli account per la verifica in due passaggi
Dopo che un account è stato abilitato per MFA, gli utenti possono accedere alle risorse governate dai criteri MFA solo dopo aver configurato un dispositivo attendibile da usare per il secondo fattore di autenticazione con la verifica in due passaggi.

In questa sezione si configura un dispositivo attendibile per l'uso con la verifica in due passaggi. A tale scopo sono disponibili diverse opzioni, tra cui le seguenti:

* **App per dispositivi mobili**. Installare l'app Microsoft Authenticator in un dispositivo Windows Phone, Android o iOS. A seconda dei criteri dell'organizzazione, è necessario usare l'app in una delle due modalità disponibili: ricevere notifiche per le verifiche (viene eseguito il push di una notifica nel dispositivo) oppure usare il codice di verifica (è necessario immettere un codice di verifica che si aggiorna ogni 30 secondi). 
* **Chiamata o SMS sul telefono cellulare**. È possibile ricevere una chiamata o un SMS automatico. Se si sceglie una chiamata, rispondere e premere # per l'autenticazione. Se si sceglie un SMS, è possibile rispondere al messaggio oppure immettere il codice di verifica nell'interfaccia di accesso.
* **Chiamata al telefono dell'ufficio**. Questo processo è uguale a quello descritto sopra per le chiamate automatiche.

Seguire queste istruzioni per configurare un dispositivo per l'uso dell'app per dispositivi mobili per ricevere una notifica push per la verifica.

1. Accedere a [https://aka.ms/mfasetup](https://aka.ms/mfasetup) o a qualsiasi sito, ad esempio [https://portal.azure.com](https://portal.azure.com), dove viene richiesto di eseguire l'autenticazione con le credenziali abilitate per MFA. 
2. Dopo avere eseguito l'accesso con nome utente e password, viene visualizzata una schermata che chiede di configurare l'account per la verifica aggiuntiva di sicurezza.

 ![Sicurezza aggiuntiva](./media/nps-extension-vpn/image29.png)

3. Fare clic su **Imposta ora**.
4. Nella pagina Verifica di sicurezza aggiuntiva selezionare un tipo di contatto (telefono per l'autenticazione, telefono ufficio o app per dispositivi mobili). Selezionare quindi un paese o un'area geografica e un metodo. Il metodo varia in base al tipo di contatto selezionato. Se ad esempio si sceglie l'app per dispositivi mobili, è possibile scegliere se ricevere le notifiche per la verifica o usare un codice di verifica. I passaggi seguenti presuppongono che sia stata scelta l'opzione **App per dispositivi mobili** come tipo di contatto.

 ![Autenticazione tramite telefono](./media/nps-extension-vpn/image30.png)

5. Selezionare App per dispositivi mobili, fare clic su **Ricevi notifiche per la verifica** e quindi su **Imposta**. 

 ![Verifica tramite app per dispositivi mobili](./media/nps-extension-vpn/image31.png)
 
6. Se l'app autenticatore per dispositivi mobili non è ancora stata installata, installarla. 
7. Seguire le istruzioni nell'app per dispositivi mobili per eseguire la scansione del codice a barre visualizzato o immettere manualmente le informazioni e quindi fare clic su **Fine**.

 ![Configurare l'app per dispositivi mobili](./media/nps-extension-vpn/image32.png)

8. Nella pagina Verifica di sicurezza aggiuntiva fare clic su **Contatti** e rispondere alla notifica inviata al dispositivo.
9. Nella pagina Verifica di sicurezza aggiuntiva immettere un numero di contatto da usare nel caso in cui non sia più possibile accedere all'app per dispositivi mobili e fare clic su **Avanti**.

 ![Numero del telefono cellulare](./media/nps-extension-vpn/image33.png)
 
10. In Verifica di sicurezza aggiuntiva fare clic su **Fine**.

Il dispositivo è ora configurato per fornire un secondo metodo di verifica. Per informazioni sull'impostazione degli account per la verifica in due passaggi, vedere [Configurare l'account per la verifica in due passaggi](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-nps-extension"></a>Installare e configurare l'estensione NPS

Questa sezione fornisce istruzioni per configurare la rete VPN per l'uso di Azure MFA per l'autenticazione client con il server VPN.

Una volta installata e configurata l'estensione NPS, tutte le operazioni di autenticazione client basata su RADIUS elaborate da questo server devono usare Azure MFA. Se non tutti gli utenti VPN sono registrati in Azure MFA, è possibile configurare un altro server RADIUS per autenticare gli utenti che non sono configurati per l'uso di MFA. In alternativa, è possibile creare una voce del Registro di sistema che consente agli utenti sottoposti a verifica di fornire un secondo fattore di autenticazione solo se sono registrati in MFA. 

Creare un nuovo valore stringa denominato _REQUIRE_USER_MATCH in HKLM\SOFTWARE\Microsoft\AzureMfa_ e impostare il valore su TRUE o FALSE. 

 ![Richiesta della corrispondenza utente](./media/nps-extension-vpn/image34.png)
 
Se il valore è impostato su TRUE o non è impostato, tutte le richieste di autenticazione sono soggette a verifica MFA. Se il valore è impostato su FALSE, le verifiche MFA vengono eseguite solo per gli utenti registrati in MFA. Usare l'impostazione FALSE solo a scopo di test o negli ambienti di produzione durante un periodo di onboarding.

### <a name="acquire-azure-active-directory-guid-id"></a>Acquisire l'ID GUID di Azure Active Directory

Come parte della configurazione dell'estensione NPS, è necessario fornire le credenziali di amministratore e l'ID di Azure Active Directory per il tenant di Azure AD. La procedura seguente mostra come ottenere l'ID tenant.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com) come amministratore globale del tenant di Azure.
2. Nel riquadro di spostamento sinistro fare clic sull'icona di **Azure Active Directory**.
3. Fare clic su **Proprietà**.
4. Per copiare l'ID directory negli Appunti, selezionare l'icona **Copia**.
 
 ![ID directory](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Installare l'estensione di Server dei criteri di rete
L'estensione NPS deve essere installata in un server con il ruolo Servizi di accesso e criteri di rete installato e che funzioni come server RADIUS nella progettazione. Non installare l'estensione NPS nel server desktop remoto.

1. Scaricare l'estensione NPS da [https://aka.ms/npsmfa](https://aka.ms/npsmfa). 
2. Copiare il file eseguibile di installazione (NpsExtnForAzureMfaInstaller.exe) nel server NPS.
3. Nel server NPS fare doppio clic su **NpsExtnForAzureMfaInstaller.exe**. Se richiesto, fare clic su **Esegui**.
4. Nella finestra di dialogo relativa all'estensione NPS per Azure MFA leggere le condizioni di licenza software, selezionare **Accetto i termini e le condizioni di licenza** e fare clic su **Installa**.

 ![Estensione NPS](./media/nps-extension-vpn/image36.png)
 
5. Nella finestra di dialogo relativa all'estensione NPS per Azure MFA fare clic su **Chiudi**.  

 ![Installazione completata](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Configurare i certificati per l'uso con l'estensione NPS tramite uno script di PowerShell
Per garantire comunicazioni protette e sicure, è necessario configurare i certificati che l'estensione NPS può usare. I componenti di Server dei criteri di rete includono uno script di Windows PowerShell che configura un certificato autofirmato per l'uso con Server dei criteri di rete. 

Lo script esegue le azioni seguenti:

* Crea un certificato autofirmato
* Associa la chiave pubblica del certificato all'entità servizio in Azure AD
* Archivia il certificato nell'archivio del computer locale
* Concede l'accesso alla chiave privata del certificato all'utente di rete
* Riavvia il servizio Server dei criteri di rete

Per usare certificati personalizzati, è necessario associare la parte pubblica del certificato all'entità servizio in Azure AD e così via.
Per usare lo script, specificare l'estensione con le credenziali amministrative di Azure Active Directory e l'ID tenant di Azure Active Directory copiato in precedenza. Eseguire lo script in ogni server NPS in cui si installa l'estensione NPS.

1. Aprire un prompt di Windows PowerShell come amministratore.
2. Al prompt di PowerShell digitare _cd 'c:\Programmi\Microsoft\AzureMfa\Config'_ e premere **INVIO**.
3. Digitare _.\AzureMfsNpsExtnConfigSetup.ps1_ e premere **INVIO**. 
 * Lo script verifica se il modulo di PowerShell per Azure Active Directory è installato. Se non è installato, lo installa.
 
 ![PowerShell](./media/nps-extension-vpn/image38.png)
 
4. Dopo che lo script ha verificato l'installazione del modulo di PowerShell, viene visualizzata la finestra di dialogo relativa al modulo di PowerShell per Azure Active Directory. Nella finestra di dialogo immettere le credenziali e la password di amministratore di Azure AD e fare clic su **Accedi**. 
 
 ![Accesso a PowerShell](./media/nps-extension-vpn/image39.png)
 
5. Quando viene chiesto, incollare l'ID tenant copiato negli Appunti in precedenza e premere **INVIO**. 

 ![ID tenant](./media/nps-extension-vpn/image40.png)

6. Lo script crea un certificato autofirmato e apporta altre modifiche alla configurazione. L'output è come quello nell'immagine seguente.

 ![Certificato autofirmato](./media/nps-extension-vpn/image41.png)

7. Riavviare il server.
 
### <a name="verify-configuration"></a>Verificare la configurazione
Per verificare la configurazione, è necessario stabilire una nuova connessione VPN con il server VPN. Dopo che sono state immesse le credenziali corrette per l'autenticazione primaria, la connessione VPN attende il completamento dell'autenticazione secondaria prima di stabilire la connessione, come illustrato di seguito. 

 ![Verificare la configurazione](./media/nps-extension-vpn/image42.png)

Se l'autenticazione con il metodo di verifica secondario configurato in precedenza in Azure MFA avviene correttamente, viene stabilita una connessione alla risorsa. Se invece l'autenticazione secondaria non ha esito positivo, l'accesso alla risorsa viene negato. 

Nell'esempio seguente viene usata l'app Authenticator su un dispositivo Windows Phone per fornire l'autenticazione secondaria.

 ![Verificare l'account](./media/nps-extension-vpn/image43.png)

Dopo aver eseguito correttamente l'autenticazione con il metodo secondario, viene concesso l'accesso alla porta virtuale nel server VPN. Tuttavia, poiché è stato richiesto di usare un metodo di autenticazione secondaria tramite un'app per dispositivi mobili in un dispositivo attendibile, il processo di accesso è più sicuro di quanto sarebbe stato usando solo una combinazione di nome utente/password.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Visualizzare i log del Visualizzatore eventi per individuare gli eventi di accesso riusciti
Per visualizzare gli eventi di accesso riusciti nei log del Visualizzatore eventi di Windows, è possibile usare il comando seguente di Windows PowerShell per eseguire una query sul log di sicurezza di Windows nel server NPS.

Per eseguire una query per individuare gli eventi di accesso riusciti nei log del visualizzatore eventi di sicurezza, usare il comando seguente:
* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

 ![Visualizzatore eventi di sicurezza](./media/nps-extension-vpn/image44.png)
 
È anche possibile aprire la visualizzazione personalizzata del log di sicurezza o di Servizi di accesso e criteri di rete, come illustrato di seguito:

 ![Accesso ai criteri di rete](./media/nps-extension-vpn/image45.png)

Nel server in cui è installata l'estensione NPS per Azure MFA è possibile trovare i log applicazioni del Visualizzatore eventi specifici per l'estensione in **Registri applicazioni e servizi\Microsoft\AzureMfa**. 

* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

 ![Numero di eventi](./media/nps-extension-vpn/image46.png)

## <a name="troubleshoot-guide"></a>Guida per la risoluzione dei problemi
Se la configurazione non funziona come previsto, è consigliabile iniziare verificando che l'utente sia configurato per usare Azure MFA. Chiedere all'utente di connettersi a [https://portal.azure.com](https://portal.azure.com). Se viene richiesta l'autenticazione secondaria ed possibile eseguire l'autenticazione, la configurazione di Azure MFA è corretta.

Se Azure MFA funziona correttamente, esaminare i log eventi pertinenti. I log da esaminare sono i log operativi del gateway e quelli relativi a eventi di sicurezza e Azure MFA, illustrati nella sezione precedente. 

Di seguito è illustrato un esempio di output di un log di sicurezza che mostra un evento di accesso non riuscito (ID evento 6273):

 ![Log di sicurezza](./media/nps-extension-vpn/image47.png)

Di seguito è illustrato un evento correlato dei log di Azure MFA:

 ![Log di Azure MFA](./media/nps-extension-vpn/image48.png)

Per opzioni avanzate di risoluzione dei problemi, consultare i file di log in formato database di Server dei criteri di rete nella posizione in cui è installato il servizio Server dei criteri di rete. Questi file di log vengono creati nella cartella _%SystemRoot%\System32\Logs_ come file di testo con valori delimitati da virgole. Per una descrizione di questi file di log, vedere [Interpret NPS Database Format Log Files](https://technet.microsoft.com/library/cc771748.aspx) (Interpretare i file di log in formato database di Server dei criteri di rete). 

Le voci di questi file di log sono difficili da interpretare senza importarle in un foglio di calcolo o in un database. Online sono disponibili numerosi parser IAS che possono aiutare a interpretare i file di log. Di seguito è riportato l'output di una di queste [applicazioni shareware](http://www.deepsoftware.com/iasviewer) che è possibile scaricare: 

 ![Applicazione shareware](./media/nps-extension-vpn/image49.png)

Infine, per altre opzioni di risoluzione dei problemi, è possibile usare uno strumento di analisi di protocolli, ad esempio Wireshark o [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). L'immagine seguente relativa a Wireshark mostra i messaggi RADIUS tra il server VPN e il server NPS.

 ![Microsoft Message Analyzer](./media/nps-extension-vpn/image50.png)

Per altre informazioni, vedere [Integrare l'infrastruttura NPS esistente con Azure Multi-Factor Authentication](multi-factor-authentication-nps-extension.md).  

## <a name="next-steps"></a>Passaggi successivi
[Come ottenere Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

[Gateway Desktop remoto e server Azure Multi-Factor Authentication utilizzando RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integrare le directory locali con Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)


