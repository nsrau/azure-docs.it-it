---
title: Introduzione al server Azure Multi-Factor Authentication | Microsoft Docs
description: "Questa è la pagina di Azure Multi-Factor Authentication in cui viene descritto come iniziare a utilizzare Azure MFA."
services: multi-factor-authentication
keywords: server di autenticazione, pagina di attivazione dell'app azure multi factor authentication, download server di autenticazione
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: e94120e4-ed77-44b8-84e4-1c5f7e186a6b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/26/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 4235dfd0e17b9892787dd86d807b8f1f6e360675
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---

<a id="getting-started-with-the-azure-multi-factor-authentication-server" class="xliff"></a>

# Introduzione al server Azure Multi-Factor Authentication

<center>![MFA locale](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Dopo aver stabilito di usare un server Multi-Factor Authentication locale, è possibile procedere. Questa pagina include una nuova installazione del server che include l'impostazione di Active Directory locale. Se il server MFA è già installato e lo si vuole aggiornare, vedere [Upgrade to the latest Azure Multi-Factor Authentication Server](multi-factor-authentication-server-upgrade.md) (Eseguire l'aggiornamento al server Azure Multi-Factor Authentication più recente). Per informazioni sull'installazione solo del servizio Web, vedere [Distribuzione del servizio Web App Mobile di Azure Multi-Factor Authentication Server](multi-factor-authentication-get-started-server-webservice.md).
 
<a id="plan-your-deployment" class="xliff"></a>

## Pianificare la distribuzione

Prima di scaricare il server Azure Multi-Factor Authentication, valutare i propri requisiti in termini di carico e disponibilità elevata. Usare queste informazioni per decidere come e dove eseguire la distribuzione. 

Un'indicazione valida per la quantità di memoria necessaria è data dal numero di utenti che in base alle previsioni eseguirà regolarmente l'autenticazione. 

| Utenti | RAM |
| ----- | --- |
| 1-10.000 | 4 GB |
| 10.001-50.000 | 8 GB |
| 50.001-100.000 | 12 GB |
| 100.000-200.001 | 16 GB |
| Oltre 200.001 | 32 GB |

Se è necessario configurare più server per la disponibilità elevata o il bilanciamento del carico, sono disponibili diversi modi per definire questa configurazione con il server Azure MFA. Il primo server Azure MFA installato diventa il master. Tutti i server aggiuntivi diventano subordinati ed eseguono automaticamente la sincronizzazione degli utenti e della configurazione con il master. È quindi possibile configurare un server primario e usare il resto come backup oppure configurare il bilanciamento del carico tra tutti i server. 

Quando un server Azure MFA master passa in modalità offline, le richieste di verifica in due passaggi vengono comunque elaborate dai server subordinati. Finché il master non viene riportato online o non viene alzato di livello un subordinato, tuttavia, non è possibile aggiungere nuovi utenti e gli utenti esistenti non possono aggiornare le proprie impostazioni. 

<a id="prepare-your-environment" class="xliff"></a>

## Preparare l'ambiente

Verificare che il server usato per Azure Multi-Factor Authentication soddisfi i requisiti seguenti:

| Requisiti del server Azure Multi-Factor Authentication | Descrizione |
|:--- |:--- |
| Hardware |<li>200 MB di spazio su disco rigido</li><li>processore idoneo per x32 o x64</li><li>1 GB o più di RAM</li> |
| Software |<li>Windows Server 2008 o versione successiva se l'host è un sistema operativo server</li><li>Windows 7 o versione successiva se l'host è un sistema operativo client</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 o versione successiva se si installa il portale utenti o l'SDK servizi Web</li> |

<a id="azure-multi-factor-authentication-server-firewall-requirements" class="xliff"></a>

### Requisiti del firewall del server Azure Multi-Factor Authentication
Ogni server Multi-Factor Authentication deve essere in grado di comunicare sulla porta 443 in uscita negli indirizzi seguenti:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Se i firewall in uscita sono limitati sulla porta 443, sarà necessario aprire gli intervalli di indirizzi IP seguenti:

| Subnet IP | Netmask | Intervallo IP |
|:--- |:--- |:--- |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254 |

Se non si usa la funzionalità di conferma dell'evento e gli utenti non usano app per dispositivi mobili per la verifica da dispositivi nella rete aziendale, sono necessari solo gli intervalli seguenti:

| Subnet IP | Netmask | Intervallo IP |
|:--- |:--- |:--- |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79 |
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79 |
| 70.37.154.200/29 |255.255.255.248 |70.37.154.201 – 70.37.154.206 |

<a id="download-the-azure-multi-factor-authentication-server" class="xliff"></a>

## Scaricare il server Azure Multi-Factor Authentication
Esistono due modi diversi per scaricare il server Azure Multi-Factor Authentication. Entrambi vengono eseguiti con il portale di Azure. Il primo prevede la gestione diretta del provider di Multi-Factor Authentication, il secondo l'uso delle impostazioni del servizio. La seconda opzione richiede un provider Multi-Factor Authentication o una licenza Azure MFA, Azure AD Premium o Enterprise Mobility Suite.

> [!Important]
> Queste due opzioni sembrano simili, ma è importante sapere quale usare. Se gli utenti hanno licenze che includono MFA (Azure MFA, Azure AD Premium o Enterprise Mobility + Security), non creare un provider Multi-Factor Authentication per ottenere il download del server. Usare invece l'opzione 2 per scaricare il server dalla pagina delle impostazioni del servizio. 

<a id="option-1-download-azure-multi-factor-authentication-server-from-the-azure-classic-portal" class="xliff"></a>

### Opzione 1: Scaricare il server Azure Multi-Factor Authentication dal portale di Azure classico

Usare questa opzione di download se si ha già un provider Multi-Factor Authentication perché viene eseguito il pagamento per MFA per utente abilitato o per autenticazione. 

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella pagina Active Directory fare clic su **Provider di Autenticazione a più fattori** ![Provider di Autenticazione a più fattori](./media/multi-factor-authentication-get-started-server/authproviders.png)
4. Nella parte inferiore fare clic su **Gestisci**. Viene aperta una nuova pagina.
5. Fare clic su **Download**.
6. Fare clic sul collegamento **Scarica**.
   ![Scaricare](./media/multi-factor-authentication-get-started-server/download4.png)
7. Salvare il download.

<a id="option-2-download-azure-multi-factor-authentication-server-from-the-service-settings" class="xliff"></a>

### Opzione 2: Scaricare il server Azure Multi-Factor Authentication dalle impostazioni del servizio

Usare questa opzione di download se si hanno licenze Enterprise Mobility Suite, Azure AD Premium o Enterprise Cloud Suite. 

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Fare doppio clic sull'istanza di Azure AD.
4. Nella parte superiore fare clic su **Configura**
5. Scorrere verso il basso fino alla sezione **Multi-Factor Authentication**, selezionare **Gestisci impostazioni del servizio**.
6. Nella parte inferiore della schermata della pagina Impostazioni servizio, fare clic su **Vai al portale**. Viene aperta una nuova pagina.
   ![Scaricare](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Fare clic su **Download**
8. Fare clic sul collegamento **Scarica**.
    ![Scaricare](./media/multi-factor-authentication-get-started-server/download4.png)
9. Salvare il download.

<a id="install-and-configure-the-azure-multi-factor-authentication-server" class="xliff"></a>

## Installare e configurare il server Azure Multi-Factor Authentication
Dopo averlo scaricato, è possibile installare e configurare il server.  Assicurarsi che il server in cui viene installato soddisfi i requisiti elencati nella sezione relativa alla pianificazione. 

Questi passaggi consentono di eseguire un'installazione rapida con la configurazione guidata. Se non viene visualizzata la procedura guidata o si vuole eseguirla di nuovo, è possibile selezionarla dal menu **Strumenti** nel server.

1. Fare doppio clic sul file eseguibile. 
2. Nella schermata di selezione della cartella di installazione, assicurarsi che la cartella sia corretta e fare clic su **Avanti**.
3. Al termine dell'installazione, fare clic su **Fine**.  Viene avviata la configurazione guidata.
4. Nella schermata iniziale della configurazione guidata selezionare **Non utilizzare la Configurazione guidata autenticazione** e fare clic su **Avanti**.  La procedura guidata viene chiusa e viene avviato il server.
    ![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Tornare nella pagina da cui è stato scaricato il server, scegliere il pulsante relativo alla **generazione delle credenziali di attivazione** . Copiare queste informazioni nel server Azure MFA nelle apposite caselle e fare clic su **Attiva**.

<a id="import-users-from-active-directory" class="xliff"></a>

## Importare gli utenti da Active Directory
Ora che il server è stato installato e configurato, è possibile importare rapidamente gli utenti nel server Azure MFA.

1. Nel server Azure MFA, a sinistra, selezionare **Utenti**.
2. Nella parte inferiore, selezionare **Importa da Active Directory**.
3. A questo punto è possibile eseguire la ricerca di singoli utenti o effettuare una ricerca delle unità organizzative con utenti all'interno di Active Directory.  In questo caso, viene specificata l'unità organizzativa utenti.
4. Selezionare tutti gli utenti a destra e fare clic su **Importa**.  Verrà visualizzata una finestra popup che informa che tutte le operazioni sono state eseguite correttamente.  Chiudere la finestra di importazione.
   ![Cloud](./media/multi-factor-authentication-get-started-server/import2.png)

<a id="send-users-an-email" class="xliff"></a>

## Inviare agli utenti un messaggio di posta elettronica
Dopo aver importato gli utenti nel server di MFA, inviare un messaggio di posta elettronica per informarli che sono stati registrati per la verifica in due passaggi.

Il messaggio di posta elettronica inviato deve essere determinato dalla configurazione degli utenti per la verifica in due passaggi. Ad esempio, se è stata completata l'importazione dei numeri di telefono dalla directory aziendale, il messaggio di posta elettronica deve includere i numeri di telefono predefiniti in modo che gli utenti sappiano cosa aspettarsi. Se i numeri di telefono non sono stati importati o è previsto che gli utenti usino l'app per dispositivi mobili, inviare un messaggio di posta elettronica per invitarli a completare la registrazione dell'account. Includere nel messaggio un collegamento ipertestuale al portale utenti di Azure Multi-Factor Authentication.

Il contenuto del messaggio di posta elettronica varia a seconda del metodo di verifica impostato per l'utente (telefonata, SMS o app per dispositivi mobili).  Se, ad esempio, l'utente deve usare un PIN quando esegue l'autenticazione, il messaggio di posta elettronica indicherà quale PIN iniziale è stato impostato.  Agli utenti viene richiesto di modificare il PIN nel corso della prima verifica.


<a id="configure-email-and-email-templates" class="xliff"></a>

### Configurare l'indirizzo di posta elettronica e i modelli di messaggio di posta elettronica
Fare clic sull'icona del messaggio di posta elettronica a sinistra per configurare le impostazioni per l'invio dei messaggi di posta elettronica. In questa pagina è possibile immettere le informazioni SMTP del server di posta elettronica e inviare un messaggio selezionando la casella di controllo **Invia messaggi agli utenti**.

![Impostazioni di posta elettronica](./media/multi-factor-authentication-get-started-server/email1.png)

Nella scheda Contenuto messaggio è possibile visualizzare i modelli di messaggio di posta elettronica disponibili per la selezione. A seconda della modalità di configurazione scelta per l'esecuzione della verifica in due passaggi, è possibile scegliere il modello che meglio si adatta alle proprie esigenze.

![Modelli di posta elettronica](./media/multi-factor-authentication-get-started-server/email2.png)

<a id="how-the-azure-multi-factor-authentication-server-handles-user-data" class="xliff"></a>

## Come gestire i dati utente tramite il server Multi-Factor Authentication
Quando si usa il server Multi-Factor Authentication (MFA) locale, i dati di un utente vengono archiviati nel server locale. Nel cloud non vengono archiviati dati utente persistenti. Quando l'utente esegue una verifica in due passaggi, Azure MFA Server invia i dati al servizio cloud Azure MFA per eseguire la verifica. Quando queste richieste di autenticazione vengono inviate al servizio cloud, i campi seguenti vengono inviati nella richiesta e dei log, in modo che siano disponibili nei report di autenticazione/utilizzo del cliente. Alcuni campi sono facoltativi e possono essere abilitati o disabilitati nel server Multi-Factor Authentication. La comunicazione dal server MFA al servizio cloud MFA usa SSL/TLS sulla porta 443 in uscita. Questi campi sono:

* ID univoco: nome utente o ID interno del MFA
* Nome e cognome (facoltativo)
* Indirizzo di posta elettronica (facoltativo)
* Numero di telefono: quando si esegue una chiamata vocale o l'autenticazione tramite SMS
* Token del dispositivo: quando si esegue l'autenticazione con l'app per dispositivi mobili
* Modalità di autenticazione
* Risultato dell'autenticazione
* Nome del server MFA
* IP del server MFA
* IP client: se disponibile

Oltre a questi campi, il risultato della verifica (esito positivo/rifiuto) e il motivo di eventuali rifiuti vengono archiviati insieme ai dati di autenticazione e sono disponibili nei report di autenticazione/utilizzo.

<a id="next-steps" class="xliff"></a>

## Passaggi successivi

- Installare e configurare il [portale utenti](multi-factor-authentication-get-started-portal.md) per la modalità self-service per gli utenti finali.

- Installare e configurare il server Azure MFA con [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md), l'[autenticazione RADIUS](multi-factor-authentication-get-started-server-radius.md) o l'[autenticazione LDAP](multi-factor-authentication-get-started-server-ldap.md).

- Installare e configurare [Gateway Desktop remoto e server Azure Multi-Factor Authentication utilizzando RADIUS](multi-factor-authentication-get-started-server-rdg.md). 

- [Distribuire il servizio Web App Mobile del server Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server-webservice.md).

- [Scenari avanzati con Azure Multi-Factor Authentication e soluzioni VPN di terze parti](multi-factor-authentication-advanced-vpn-configurations.md).

