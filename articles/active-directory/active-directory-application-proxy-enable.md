<properties
	pageTitle="Abilitare il proxy di applicazione di Azure AD | Microsoft Azure"
	description="Attivare il proxy di applicazione nel portale di Azure classico e installare i connettori per il proxy inverso."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/17/2016"
	ms.author="kgremban"/>

# Abilitare il proxy di applicazione nel portale di Azure

Questo articolo illustra la procedura per abilitare il proxy di applicazione di Microsoft Azure AD per la directory cloud in Azure AD. Il processo prevede quanto segue:

- Installare nella rete privata il connettore proxy di applicazione, che gestisce la connessione dalla rete al servizio proxy.
- Registrare il connettore con la sottoscrizione del tenant di Microsoft Azure AD.

Se non si ha familiarità con le funzionalità offerte dal proxy di applicazione, per altre informazioni vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md).

## Prerequisiti del proxy dell'applicazione
Prima di poter abilitare e utilizzare i servizi del proxy dell'applicazione, è necessario disporre di:

- Una [sottoscrizione di Microsoft Azure AD Basic o Premium](active-directory-editions.md) e una directory di Azure AD di cui si è un amministratore globale.
- Un server che esegue Windows Server 2012 R2 oppure Windows 8.1 o versioni successive in cui poter installare il connettore proxy di applicazione. Il server invia richieste HTTPS ai servizi proxy di applicazione nel cloud e necessita di una connessione HTTPS alle applicazioni in corso di pubblicazione.
- Se nel percorso è presente un firewall, verificare che sia aperto in modo che il connettore possa inviare richieste HTTPS (TCP) al proxy di applicazione. Il connettore usa queste porte con sottodomini che fanno parte dei domini di alto livello: msappproxy.net e servicebus.windows.net. Assicurarsi di aprire **tutte** le seguenti porte al traffico **in uscita**:

	| Numero della porta | Descrizione |
	| --- | --- |
	| 80 | Abilitare il traffico HTTP in uscita per la convalida di sicurezza |
	| 443 | Abilitare l'autenticazione dell'utente in Azure AD (obbligatoria solo per il processo di registrazione del connettore) |
	| 10100–10120 | Abilitare risposte LOB HTTP inviate al proxy |
	| 9352, 5671 | Abilitare la comunicazione tra il connettore e il servizio di Azure per le richieste in ingresso. |
	| 9350 | Facoltativo, per consentire prestazioni migliori per le richieste in ingresso |
	| 8080 | Abilitare la sequenza di bootstrap del connettore e l'aggiornamento automatico del connettore |
	| 9090 | Abilitare la registrazione del connettore (necessaria solo per il processo di registrazione del connettore) |
	| 9091 | Abilitare il rinnovo automatico dei certificati di attendibilità del connettore |

Se il firewall impone il traffico in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete. Inoltre, assicurarsi di abilitare la porta 8080 per NT Authority\\System.


## Passaggio 1: abilitare il proxy dell’applicazione in Azure AD
1. Accedere come amministratore al [portale di Azure classico](https://manage.windowsazure.com/).
2. Passare ad Active Directory e selezionare la directory in cui si desidera abilitare il proxy dell'applicazione.

	![Active Directory - Icona](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Selezionare **Configura** nella pagina della directory e scorrere verso il basso fino a **Proxy dell'applicazione**.
4. Impostare **Abilita servizi proxy dell'applicazione per questa directory** su **Abilitati**.

	![Abilitare il proxy dell’applicazione](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Selezionare **Scarica ora**. Verrà visualizzata la pagina **Azure AD Application Proxy Connector Download** (Download connettore proxy di applicazione di Azure AD). Leggere e accettare le condizioni di licenza e fare clic su **Download** per salvare il file di Windows Installer (con estensione txt) per il connettore del proxy dell'applicazione.

## Passaggio 2: installare e registrare il connettore
1. Eseguire **AADApplicationProxyConnectorInstaller.exe** nel server preparato in base ai prerequisiti.
2. Seguire le istruzioni della procedura guidata da installare.
3. Durante l'installazione viene richiesto di registrare il connettore con il proxy di applicazione del tenant di Azure AD.

  - Fornire le credenziali di amministratore globale di Azure AD. Il tenant di amministratore globale può essere diverso dalle credenziali di Microsoft Azure.
  - Assicurarsi che l'amministratore che registra il connettore sia nella stessa directory in cui è abilitato il servizio Proxy dell'applicazione. Ad esempio, se il dominio del tenant è contoso.com, l'amministratore sarà admin@contoso.com o qualsiasi altro alias in tale dominio.
  - Se la **sicurezza avanzata di Internet Explorer** è impostata su **Attiva** nel server in cui si vuole installare Azure AD Connector, la schermata di registrazione potrebbe essere bloccata. Seguire le istruzioni nel messaggio di errore per consentire l'accesso. Verificare che Internet Explorer Enhanced Security Context sia disabilitato.
  - Se la registrazione del connettore non riesce, vedere [Risolvere i problemi del Proxy applicazione](active-directory-application-proxy-troubleshoot.md).  

4. Al termine dell'installazione, due nuovi servizi vengono aggiunti al server:

 	- **Microsoft AAD Application Proxy Connector** (Connettore Proxy applicazione Microsoft AAD) abilita la connettività.
	- **Microsoft AAD Application Proxy Connector Updater** (Strumento di aggiornamento connettore Proxy applicazione Microsoft AAD) è un servizio di aggiornamento automatico che verifica periodicamente la presenza di nuove versioni del connettore e aggiorna il connettore in base alle esigenze.

	![Servizi del connettore proxy di applicazione - Screenshot](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Fare clic su **Fine** nella finestra di installazione.

Ora è possibile [pubblicare applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md).

Per ottenere una disponibilità elevata, è consigliabile distribuire almeno due connettori. Per distribuire altri connettori, ripetere i precedenti passaggi 2 e 3. Ogni connettore deve essere registrato separatamente.

Per disinstallare il connettore, disinstallare sia il servizio connettore che il servizio di aggiornamento. Riavviare il computer per rimuovere completamente il servizio.


## Passaggi successivi

- [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md)
- [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)

Per le notizie e gli aggiornamenti più recenti, vedere [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/) (Blog sul proxy di applicazione).

<!---HONumber=AcomDC_0622_2016-->