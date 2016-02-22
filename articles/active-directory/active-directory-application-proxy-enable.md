<properties
	pageTitle="Abilitazione del proxy dell’applicazione di Azure AD | Microsoft Azure"
	description="Viene illustrato come iniziare a utilizzare il proxy dell'applicazione di AD Azure."
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
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# Abilitazione del proxy dell’applicazione di Azure AD
> [AZURE.NOTE] Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](active-directory-editions.md).

Il proxy dell'applicazione di Microsoft Azure Active Directory permette di pubblicare applicazioni, ad esempio siti di SharePoint e app basate su Outlook Web Access e IIS, all'interno della rete privata e fornisce l'accesso sicuro agli utenti esterni alla rete. I dipendenti possono accedere alle app dalla propria abitazione e con i propri dispositivi e possono eseguire l'autenticazione tramite questo proxy basato sul cloud.

Il proxy dell'applicazione funziona mediante l'installazione all'interno della rete di un servizio slim di Windows Server chiamato "connettore", che gestisce una connessione in uscita dalla rete al servizio proxy. Quando gli utenti accedono a un'applicazione pubblicata, il proxy usa questa connessione per consentire l'accesso all'applicazione.

Questo articolo illustra l'attivazione del proxy dell'applicazione di Microsoft Azure AD per la directory cloud in Azure AD, l'installazione del connettore del proxy dell'applicazione nella rete privata e la registrazione del connettore con la sottoscrizione del tenant di Microsoft Azure Active Directory.

## Prerequisiti del proxy dell'applicazione
Prima di poter abilitare e utilizzare i servizi del proxy dell'applicazione, è necessario disporre di:

- Una sottoscrizione di Microsoft Azure AD [Basic o Premium](active-directory-editions.md) e una directory di Microsoft Azure di cui si è un amministratore globale.
- Un server che esegue Windows Server 2012 R2 o Windows 8.1 o versione successiva in cui è possibile installare il connettore del proxy dell’applicazione. Il server deve essere in grado di inviare le richieste HTTPS ai servizi del proxy dell'applicazione nel cloud e deve disporre di una connessione HTTPS alle applicazioni che si intende pubblicare.
- Se nel percorso è presente un firewall, assicurarsi che sia aperto per consentire le richieste HTTPS (TCP) che provengono dal connettore per il proxy dell'applicazione. Il connettore usa queste porte con sottodomini che fanno parte del dominio di livello generale: msappproxy.net e servicebus.windows.net. Assicurarsi di aprire **tutte** le seguenti porte al traffico **in uscita**:

Numero della porta | Descrizione
--- | ---
80 | Per abilitare il traffico HTTP in uscita per la convalida di sicurezza.
443 | Per abilitare l'autenticazione dell’utente in Azure AD (obbligatoria solo per il processo di registrazione del connettore)
10100 - 10120 | Per abilitare risposte LOB HTTP inviate al proxy
9352, 5671 | Per abilitare la comunicazione tra il connettore e il servizio di Azure per le richieste in ingresso.
9350 | Facoltativo, per consentire prestazioni migliori per le richieste in ingresso
8080 | Per abilitare la sequenza di bootstrap del connettore e l'aggiornamento automatico del connettore
9090 | Per abilitare la registrazione del connettore (necessaria solo per il processo di registrazione del connettore)
9091 | Per abilitare il rinnovo automatico dei certificati di attendibilità del connettore

Se il firewall impone il traffico in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete. Inoltre, assicurarsi di abilitare la porta 8080 per NT Authority\\System.


## Passaggio 1: abilitare il proxy dell’applicazione in Azure AD
1. Accedere come amministratore al portale di Azure classico.
2. Passare ad Active Directory e selezionare la directory in cui si desidera abilitare il proxy dell'applicazione.
3. Fare clic su **Configura**, scorrere fino al proxy dell'applicazione e selezionare **Abilitato** in Abilita servizi proxy dell'applicazione per questa directory.

	![Abilitare il proxy dell’applicazione](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

4. Fare clic su **Scarica ora** nella parte inferiore della schermata. Verrà visualizzata la pagina di download. Leggere e accettare le condizioni di licenza e fare clic su **Download** per salvare il file di Windows Installer (con estensione txt) per il connettore del proxy dell'applicazione.

## Passaggio 2: installare e registrare il connettore
1. Eseguire `AADApplicationProxyConnectorInstaller.exe` sul server preparato (vedere la sezione precedente Prerequisiti del proxy dell'applicazione).
2. Seguire le istruzioni della procedura guidata da installare.
3. Durante l'installazione verrà richiesto di registrare il connettore con il proxy dell'applicazione del tenant di Azure AD.

  - Fornire le credenziali di amministratore globale di Azure AD. Il tenant di amministratore globale può essere diverso dalle credenziali di Microsoft Azure.
  - Assicurarsi che l'amministratore che registra il connettore si trovi nella stessa directory in cui è stato abilitato il servizio proxy dell'applicazione, ad esempio se il dominio del tenant è contoso.com, l'amministratore deve essere admin@contoso.com o qualsiasi altro alias del dominio.
  - Se la Sicurezza avanzata di Internet Explorer è impostata su **Attiva** nel server in cui si desidera installare il connettore di Azure AD, la schermata di registrazione potrebbe essere bloccata. In questo caso, seguire le istruzioni nel messaggio di errore per consentire l'accesso. Verificare che Internet Explorer Enhanced Security Context sia disabilitato.
  - Se la Registrazione del connettore non riesce, vedere [Risolvere i problemi del Proxy applicazione](active-directory-application-proxy-troubleshoot.md).  

4. Al termine dell'installazione, due nuovi servizi vengono aggiunti al server, come illustrato di seguito. Si tratta del servizio del connettore, che consente la connettività e di un servizio di aggiornamento automatico, che verifica periodicamente la presenza di nuove versioni del connettore e aggiorna il connettore in base alle esigenze. Fare clic su **Fine** nella finestra di installazione per completare l'installazione

	![Servizi del connettore proxy dell'applicazione](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. A questo punto si è pronti per [pubblicare le applicazioni con il proxy dell'applicazione](active-directory-application-proxy-publish.md).

Per ottenere una disponibilità elevata, è necessario distribuire almeno un connettore aggiuntivo. Per distribuire un connettore aggiuntivo, ripetere i passaggi 2 e 3 descritti in precedenza. Ogni connettore deve essere registrato separatamente.

Se si desidera disinstallare il connettore, disinstallare il servizio del connettore e il servizio di aggiornamento e riavviare il computer per rimuovere completamente il servizio.


## Vedere anche
Si può fare molto di più con il proxy dell'applicazione:

- [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md)
- [Pubblicare applicazioni mediante il proprio nome di dominio](active-directory-application-proxy-custom-domains.md)
- [Abilitare l'accesso Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Lavorare con applicazioni grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)
- [Risolvere i problemi che si verificano con il proxy di applicazione](active-directory-application-proxy-troubleshoot.md)

## Ulteriori informazioni sul proxy dell’applicazione
- [Blog del proxy dell'applicazione](http://blogs.technet.com/b/applicationproxyblog/)
- [Guarda i nostri video su Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Risorse aggiuntive
- [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
- [Iscriversi ad Azure come organizzazione](sign-up-organization.md)
- [Identità di Azure](fundamentals-identity.md)
- [Pubblicare le applicazioni con il proxy di applicazione](active-directory-application-proxy-publish.md)

<!---HONumber=AcomDC_0211_2016-->