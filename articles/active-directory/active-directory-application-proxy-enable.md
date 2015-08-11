<properties
	pageTitle="Abilitazione del proxy dell’applicazione di Azure AD"
	description="Viene illustrato come iniziare a utilizzare il proxy dell'applicazione di AD Azure."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/07/2015"
	ms.author="rkarlin"/>

# Abilitazione del proxy dell’applicazione di Azure AD
> [AZURE.NOTE] Il proxy dell’applicazione di Azure AD è una funzionalità disponibile solo se è stato eseguito l'aggiornamento all'edizione Premium o Basic di Azure Active Directory. Per altre informazioni, vedere [Edizioni di Azure Active Directory](https://msdn.microsoft.com/library/azure/dn532272.aspx).

Il proxy di applicazione di Microsoft Azure AD permette di pubblicare applicazioni, ad esempio siti di SharePoint e app basate su Outlook Web Access e IIS, all'interno della rete privata e fornisce l'accesso sicuro agli utenti esterni alla rete. I dipendenti possono accedere alle app dalla propria abitazione e con i propri dispositivi e possono eseguire l'autenticazione tramite questo proxy basato sul cloud.

Questa sezione illustra l'attivazione del proxy dell’applicazione di Microsoft Azure AD per la directory cloud in Azure AD, l’installazione del connettore del proxy dell’applicazione nella rete privata, la registrazione del connettore con la sottoscrizione del tenant di Microsoft Azure Active Directory.

##Prerequisiti del proxy dell’applicazione
Prima di poter abilitare e utilizzare i servizi del proxy dell'applicazione, è necessario disporre di:

- Un account di amministratore di Microsoft Azure. Se non si dispone di un account, è possibile ottenerlo qui.
- Un server che esegue Windows Server 2012 R2 o Windows 8.1 o versione successiva in cui è possibile installare il connettore del proxy dell’applicazione. Il server deve essere in grado di inviare le richieste HTTPS ai servizi del proxy dell'applicazione nel cloud e deve disporre di una connessione HTTPS alle applicazioni che si intende pubblicare. 
- Se nel percorso è presente un firewall, assicurarsi che sia aperto per consentire le richieste HTTPS (TCP) che provengono dal connettore per il proxy dell'applicazione. Il connettore utilizza queste porte con sottodomini che fanno parte del dominio di alto livello: msappproxy.net. Assicurarsi di aprire **tutte** le seguenti porte al traffico **in uscita**:

Numero della porta | Descrizione
--- | ---
80 | Per abilitare il traffico HTTP in uscita per la convalida di sicurezza.
443 | Per abilitare l'autenticazione dell’utente in Azure AD (obbligatoria solo per il processo di registrazione del connettore)
10100 - 10120 | Per abilitare risposte LOB HTTP inviate al proxy
9352, 5671 | Per abilitare la comunicazione tra il connettore e il servizio di Azure per le richieste in ingresso.
9350 | Facoltativo. Per consentire prestazioni migliori per le richieste in ingresso.
8080 | Per abilitare la sequenza di bootstrap del connettore e abilitare l'aggiornamento automatico del connettore
9090 | Per abilitare la registrazione del connettore (necessaria solo per il processo di registrazione del connettore)
9091 | Per abilitare il rinnovo automatico dei certificati di attendibilità del connettore
 
Se il firewall impone il traffico in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizio di rete. Inoltre, assicurarsi di abilitare la porta 8080 per NT Authority\\System.


##Passaggio 1: abilitare il proxy dell’applicazione in Azure AD
1. Accedere come amministratore al portale di gestione di Azure.
2. Passare ad Active Directory e selezionare la directory in cui si desidera abilitare il proxy dell'applicazione.
3. Fare clic su Configura, scorrere fino al proxy dell'applicazione e selezionare Abilitato in Abilita servizi proxy dell'applicazione per questa directory.

	![Abilitare il proxy dell’applicazione](http://i.imgur.com/87woFzq.png) <p>
4. Fare clic su Scarica ora nella parte inferiore della schermata. Verrà visualizzata la pagina di download. Leggere e accettare le condizioni di licenza e fare clic su Download per salvare il file di Windows Installer (.exe) per il connettore del proxy dell’applicazione. 

##Passaggio 2: installare e registrare il connettore
1. Eseguire AADApplicationProxyConnectorInstaller.exe sul server preparato (vedere la sezione Prerequisiti del proxy dell’applicazione).
2. Seguire le istruzioni della procedura guidata da installare.
3. Durante l'installazione verrà richiesto di registrare il connettore con l'account del proxy dell’applicazione attivo.
<p>Fornire le credenziali di amministratore globale di Azure AD.
- Assicurarsi che l'amministratore che registra il connettore sia nella stessa directory in cui è stato abilitato il servizio proxy dell’applicazione, ad esempio se il dominio del tenant è contoso.com, l'amministratore deve essere admin@contoso.com o qualsiasi altro alias di tale dominio. Assicurarsi inoltre di essere un amministratore globale del tenant di Azure AD. Il tenant di amministratore globale può essere diverso dalle credenziali di Microsoft Azure.
- Se la Sicurezza avanzata di Internet Explorer è impostata su Attiva nel server in cui si desidera installare il connettore di Azure AD, la schermata di registrazione potrebbe essere bloccata. In questo caso, seguire le istruzioni nel messaggio di errore per consentire l'accesso. Assicurarsi che la protezione avanzata di Internet Explorer sia disattivata.
- Se la registrazione del connettore non ha esito positivo, vedere la Risoluzione dei problemi del proxy dell’applicazione.

4. Al termine dell'installazione, due nuovi servizi vengono aggiunti al server, come illustrato di seguito. Si tratta del servizio del connettore, che consente la connettività e di un servizio di aggiornamento automatico, che verifica periodicamente la presenza di nuove versioni del connettore e aggiorna il connettore in base alle esigenze. Fare clic su Fine nella finestra di installazione per completare l'installazione ![Servizio del connettore proxy dell’applicazione](http://i.imgur.com/zsVJKOz.png) <p>
5. A questo punto si è pronti per pubblicare le applicazioni con il proxy dell'applicazione.

Se si desidera disinstallare il connettore, dopo aver disinstallato il servizio del connettore e il servizio di aggiornamento, assicurarsi di riavviare il computer per rimuovere completamente il servizio. <p>Ai fini della disponibilità elevata, è necessario distribuire almeno un connettore aggiuntivo. Per distribuire un connettore aggiuntivo, ripetere i passaggi 2 e 3 descritti in precedenza. Ogni connettore deve essere registrato separatamente.



## Risorse aggiuntive

* [Iscriversi ad Azure come organizzazione](..sign-up-organization.md)
* [Identità di Azure](..fundamentals-identity.md)

<!------HONumber=July15_HO5-->