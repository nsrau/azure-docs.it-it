<properties 
	pageTitle="Autenticazione IIS e server Azure Multi-Factor Authentication" 
	description="Questa è la pagina di Azure Multi-Factor Authentication contenente le informazioni utili per distribuire l'autenticazione IIS e il server Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="billmath"/>

# Autenticazione IIS

La sezione Autenticazione IIS del server Azure Multi-Factor Authentication consente di abilitare e configurare l'autenticazione IIS per l'integrazione con le app Web Microsoft IIS. Il server Azure Multi-Factor Authentication installa un plug-in che può filtrare le richieste inoltrate al server Web IIS in modo da aggiungere Azure Multi-Factor Authentication. Il plug-in di IIS fornisce inoltre il supporto per l'autenticazione basata su form e l'autenticazione HTTP integrata di Windows. È possibile anche configurare gli indirizzi IP attendibili in modo da escludere gli indirizzi IP interni dall'autenticazione a due fattori.


![Autenticazione IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## Uso dell'autenticazione IIS basata su form con il server Azure Multi-Factor Authentication

Per proteggere un'applicazione Web IIS che usa l'autenticazione basata su form, installare il server Azure Multi-Factor Authentication nel server Web IIS e configurare il server seguendo questa procedura.

1. Nel Server Azure multi-Factor Authentication fare clic sull'icona autenticazione IIS nel menu a sinistra.
2. Fare clic sulla scheda basata su Form.
3. Fare quindi clic su Add.
4. Per rilevare automaticamente le variabili di nome utente, password e dominio, immettere l'URL di accesso (ad esempio https://localhost/contoso/auth/login.aspx) nella finestra di dialogo Configura automaticamente sito Web e fare clic su OK.
5. Se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti all'autenticazione a più fattori, selezionare la casella di controllo Richiedi corrispondenza utente di Multi-Factor Authentication. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella.
6. Se le variabili di pagina non possono essere rilevate automaticamente, fare clic su specifica manualmente... pulsante nella finestra di dialogo Configura automaticamente sito Web.
7. Nella finestra di dialogo Aggiungi sito Web immettere l'URL alla pagina di accesso nel campo URL di invio e immettere un nome di applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS. Vedere il file della Guida per ulteriori informazioni nell'URL di invio. 
8. Selezionare il formato di richiesta corretto (impostato su "POST or GET" per la maggior parte delle applicazioni Web).
9. Immettere la variabile nome utente, la variabile password e la variabile dominio (se è presente nella pagina di accesso). Potrebbe essere necessario passare alla pagina di accesso in un Web browser. Fare clic con il pulsante destro del mouse sulla pagina e selezionare "HTML" per trovare i nomi delle caselle di input all'interno della pagina.
10. Selezionare la casella di corrispondenza utente di richiedere Azure multi-Factor Authentication se tutti gli utenti sono già stati o verranno importati nel Server e soggetto a multi-factor authentication. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella. Vedere il file della Guida per ulteriori informazioni su questa funzionalità.
11.  Fare clic sul pulsante Avanzate per rivedere le impostazioni avanzate, incluse quelle per selezionare un file di pagina di rifiuto personalizzato, per memorizzare nella cache le autenticazioni al sito Web per un determinato periodo tramite cookie e per selezionare la modalità di autenticazione delle credenziali primarie nel dominio di Windows, nella directory LDAP o in un server RADIUS. Al termine, fare clic su OK per tornare alla finestra di dialogo Aggiungi sito Web basato su form. Vedere il file della Guida per ulteriori informazioni sulle impostazioni avanzate.
12. Fare clic sul pulsante OK.
13. Una volta rilevate o immesse le variabili di pagina e URL, i dati del sito Web verranno visualizzato nel pannello basata su Form.
14. Vedere la sezione Abilitare i plug-in di IIS per il server Azure Multi-Factor Authentication riportata di seguito per completare la configurazione dell'autenticazione IIS. 

## Uso dell'autenticazione integrata di Windows con il server Azure Multi-Factor Authentication

Per proteggere un'applicazione Web IIS che usa l'autenticazione HTTP integrata di Windows, installare il server Azure Multi-Factor Authentication nel server Web IIS e configurare il server seguendo questa procedura.

1. Nel Server Azure multi-Factor Authentication fare clic sull'icona autenticazione IIS nel menu a sinistra.
2. Fare clic sulla scheda HTTP. Fare clic sulla scheda basata su Form.
3. Fare quindi clic su Add.
4. Nella finestra di dialogo Aggiungi URL di base immettere l'URL per il sito Web in cui viene eseguita l'autenticazione HTTP (ad esempio http://localhost/owa) nel campo URL di base e immettere il nome dell'applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS.
5. Modificare i valori relativi al timeout di inattività e al tempo massimo delle sessioni se i valori predefiniti non sono sufficienti.
6. Se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti all'autenticazione a più fattori, selezionare la casella di controllo Richiedi corrispondenza utente di Multi-Factor Authentication. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella. Vedere il file della Guida per ulteriori informazioni su questa funzionalità. 
7. Se lo si desidera, selezionare la casella di cache di cookie.
8. Fare clic sul pulsante OK.
9. Vedere la sezione [Abilitare i plug-in di IIS per il server Azure Multi-Factor Authentication](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) riportata di seguito per completare la configurazione dell'autenticazione IIS. 


## Abilitare i plug-in di IIS per il server Azure Multi-Factor Authentication

Dopo avere configurato gli URL e le impostazioni dell'autenticazione HTTP o basata su form, è necessario selezionare le destinazioni in cui i plug-in di IIS del server Azure Multi-Factor Authentication devono essere caricati e abilitati in IIS. Utilizzare la procedura seguente:

1. Se è in esecuzione IIS 6, fare clic sulla scheda ISAPI e selezionare il sito Web che esegue l'applicazione Web (ad esempio "Sito Web predefinito") per abilitare il plug-in dei filtri ISAPI di Azure Multi-Factor Authentication per tale sito.
2. Se è in esecuzione IIS 7 o versione successiva, fare clic sulla scheda Modulo nativo e selezionare il server, il sito Web o l'applicazione per abilitare il plug-in di IIS al livello desiderato.
3. Scegliere la casella Abilita autenticazione IIS nella parte superiore della schermata. A questo punto l'applicazione IIS selezionata è protetta da Azure Multi-Factor Authentication. Verificare che gli utenti siano stati importati nel server. Se si desidera whitelist che gli indirizzi IP interni in modo che l'autenticazione a due fattori non è obbligatorio quando si accede al sito Web da tali percorsi, vedere la sezione IP attendibili. 


## Provider di identità attendibili

Il provider di identità attendibili consente agli utenti di ignorare Azure multi-Factor Authentication per le richieste del sito Web provenienti da specifici indirizzi IP o subnet. Ad esempio, è possibile escludere gli utenti da Azure multi-Factor Authentication durante l'accesso dall'ufficio. A tale scopo, specificare la subnet dell'ufficio come voce di provider di identità attendibili. Per configurare gli indirizzi IP attendibili, seguire questa procedura:

1. Nella sezione autenticazione IIS, fare clic sulla scheda IP attendibili. 
2. Fare quindi clic su Add.
3. Quando viene visualizzata la finestra di dialogo Aggiungi provider di identità attendibili, selezionare il singolo indirizzo IP, l'intervallo IP o il pulsante di opzione Subnet.
4. Immettere l'indirizzo IP, l'intervallo di indirizzi IP o la subnet da inserire nell'elenco. Se si immette una subnet, selezionare mask appropriata e fare clic sul pulsante OK. L'elenco whitelist è stato aggiunto.

<!---HONumber=AcomDC_1125_2015-->