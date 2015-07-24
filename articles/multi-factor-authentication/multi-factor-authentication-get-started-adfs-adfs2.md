<properties 
	pageTitle="Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con AD FS 2.0" 
	description="Questa è la pagina di autenticazione a più fattori di Azure che descrive come iniziare a utilizzare l'autenticazione a più fattori Azure e AD FS 2.0." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>
# Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con AD FS 2.0

Se l'organizzazione è federata con Azure Active Directory e si dispone di risorse in locale o nel cloud che si desidera proteggere, è possibile farlo utilizzando il server di Azure multi-Factor Authentication e configurarlo per il funzionamento con ADFS in modo che l'autenticazione a più fattori viene attivata per i punti di fine di valore elevato.

In questa documentazione viene illustrato l'utilizzo di Server Azure multi-Factor Authentication con ADFS 2.0. Per informazioni sull'utilizzo di Azure multi-Factor Authentication con Windows servire ADFS 2012 R2 vedere [proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con ADFS di Windows Server 2012 R2](multi-factor-authentication-get-started-adfs-w2k12.md).


## Proxy di AD FS 2.0
Per proteggere ADFS 2.0 con un proxy, installare il Server Azure multi-Factor Authentication nel server proxy ADFS e configurare il Server seguendo questa procedura.

### Per proteggere ADFS 2.0 con un proxy
<ol>
<li>Nel Server Azure multi-Factor Authentication fare clic sull'icona autenticazione IIS nel menu a sinistra.</li>
<li>Fare clic sulla scheda basata su Form.</li>
<li>Fare quindi clic su Add.</li>

<center>![Installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>

<li>Per rilevare il nome utente, password e dominio variabili automaticamente, immettere l'URL di accesso (ad esempio https://sso.contoso.com/adfs/ls) nella finestra di dialogo Configura automaticamente sito Web e fare clic su OK.</li>
<li>Selezionare la casella di corrispondenza utente di richiedere Azure multi-Factor Authentication se tutti gli utenti sono già stati o verranno importati nel Server e soggetto a multi-factor authentication. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella. Vedere il file della Guida per ulteriori informazioni su questa funzionalità.</li>
<li>Se le variabili di pagina non possono essere rilevate automaticamente, fare clic su specifica manualmente... pulsante nella finestra di dialogo Configura automaticamente sito Web.</li>
<li>Nella finestra di dialogo Aggiungi sito Web immettere l'URL alla pagina di accesso ADFS nel campo URL di invio (ad esempio https://sso.contoso.com/adfs/ls) e immettere un nome di applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS. Vedere il file della Guida per ulteriori informazioni nell'URL di invio.</li>
<li>Impostare il formato della richiesta su "POST o GET".</li>
<li>Immettere la variabile di nome utente (ctl00$ ContentPlaceHolder1$ UsernameTextBox) e la variabile Password (ctl00$ ContentPlaceHolder1$ PasswordTextBox). Se la pagina di accesso basata su form viene visualizzata una casella di testo di dominio, immettere anche la variabile di dominio. Potrebbe essere necessario passare alla pagina di accesso in un browser web, pulsante destro del mouse sulla pagina e selezionare "HTML" per trovare i nomi delle caselle di input all'interno della pagina di accesso.</li>
<li>Selezionare la casella di corrispondenza utente di richiedere Azure multi-Factor Authentication se tutti gli utenti sono già stati o verranno importati nel Server e soggetto a multi-factor authentication. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella.</li>

<center>![Installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>

<li>Fare clic su Avanzate... pulsante per rivedere le impostazioni avanzate, tra cui la possibilità di selezionare un file di pagina di rifiuto personalizzato per memorizzare nella cache di autenticazioni al sito Web per un periodo di tempo tramite cookie e selezionare la modalità di autenticazione delle credenziali primarie.</li>
<li>Poiché il server proxy ADFS non è destinato a essere aggiunto al dominio, utilizzerà probabilmente LDAP per connettersi al controller di dominio per l'importazione utente e la preautenticazione. Nella finestra di dialogo sito Web basato su form avanzato, fare clic sulla scheda autenticazione primaria e selezionare "Binding LDAP" per il tipo di autenticazione pre-autenticazione.</li>
<li>Al termine, fare clic sul pulsante OK per tornare alla finestra di dialogo Aggiungi sito Web. Vedere il file della Guida per ulteriori informazioni sulle impostazioni avanzate.</li>
<li>Fare clic sul pulsante Complete per chiudere la finestra di dialogo.</li>
<li>Una volta rilevate o immesse le variabili di pagina e URL, i dati del sito Web verranno visualizzato nel pannello basata su Form.</li>
<li>Fare clic sulla scheda modulo nativo e selezionare il server, il sito Web in cui il proxy ADFS è in esecuzione (ad es. "Sito Web predefinito") o l'applicazione proxy ADFS (ad esempio "ls" in "adfs") per abilitare il plug-in di IIS al livello desiderato.</li>
<li>Scegliere la casella Abilita autenticazione IIS nella parte superiore della schermata.</li>
<li>L'autenticazione di IIS è abilitato. Tuttavia, per eseguire la pre-autenticazione per l'Active Directory (AD) tramite LDAP, è necessario configurare la connessione LDAP al controller di dominio. A tale scopo, fare clic sull'icona integrazione Directory.</li>
<li>Nella scheda Impostazioni selezionare il pulsante di opzione Usa specifico configurazione LDAP.</li>

<center>![Installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>

<li>Fare clic su Edit.</li>
<li>Nella finestra di dialogo Modifica configurazione LDAP, compilare i campi con le informazioni necessarie per connettersi al controller di dominio Active Directory. Nella tabella seguente sono incluse le descrizioni dei campi. Nota: Queste informazioni sono inoltre incluse nel file della Guida del Server Azure multi-Factor Authentication.</li>

Campo| Descrizione
:------------- | :-------------
Server|Immettere il nome host o indirizzo IP del server che esegue la directory LDAP. Un server di backup può essere specificato separati da un semi‐colon.<br> **Nota:** quando il tipo di associazione è SSL, è in genere è richiesto un nome host fully‐qualified e deve corrispondere al nome sul certificato SSL installato nel server di directory LDAP. 
Nome distinto di base|Immettere il nome distinto dell'oggetto directory di base da cui verranno avviato tutte le query di directory. Ad esempio, dc = contoso, dc = com.
Tipo di binding|Selezionare il tipo di binding appropriato da utilizzare quando si associa a cercare la directory LDAP. Viene utilizzato per la risoluzione del nome utente, sincronizzazione e le importazioni.<ul><li>* * ‐ Anonimo * * verrà eseguito un binding anonimo. Nome distinto di binding e Password di binding non verrà utilizzato. Questa impostazione funziona solo se la directory LDAP consente il binding anonimo e le autorizzazioni consentono l'esecuzione di query dei record appropriati e gli attributi.</li><li>* * Semplice * * ‐ nome distinto di binding e Password di binding verranno passati come testo normale per il binding alla directory LDAP. Questo deve essere utilizzato solo a scopo di test per verificare che il server sia raggiungibile e che l'account di binding disponga dell'accesso appropriato. È consigliabile che usare SSL dopo aver installato il certificato appropriato.</li><li>* * SSL * * ‐ nome distinto di binding e Password di binding verrà crittografata con SSL per il binding alla directory LDAP. Ciò richiede che sia installato un certificato nel server di directory LDAP che considera attendibile il Server Azure multi-Factor Authentication.</li><li>* * Windows * * ‐ associare nome utente e Password di binding verrà utilizzato per connettersi in modo sicuro a un controller di dominio Active Directory o una directory ADAM. Se il nome utente di binding viene lasciato vuoto, account dell'utente di binding verrà utilizzato per l'associazione.</li></ul> 
Associare il nome utente|Immettere il nome distinto del record utente per l'account da utilizzare quando si associa alla directory LDAP. Il nome distinto di binding viene utilizzato solo quando il tipo di binding è semplice o SSL.    
Vecchia password:|Immettere la password di binding per il nome distinto di binding o nome utente usato per il binding alla directory LDAP. Per configurare la password per il servizio AdSync di multi-Factor Authentication Server, deve essere abilitata la sincronizzazione e il servizio deve essere in esecuzione sul computer locale. La password verrà salvata in Gestione nomi utente archiviati di Windows e password con l'account che è in esecuzione il servizio di sincronizzazione di Active Directory di Azure multi-Factor Authentication Server come. La password verrà salvata anche con l'account che è in esecuzione l'interfaccia utente di Server multi-Factor Authentication e con l'account che del servizio di Server Azure multi-Factor Authentication è in esecuzione come. Nota: Poiché la password viene archiviata solo in Gestione nomi utente archiviati di Windows e la password del server locale, questa operazione dovrà essere eseguito su ogni Server di autenticazione a più fattori che richiede l'accesso alla password. 
Limite dimensione query|Specificare le dimensioni massime per il numero massimo di utenti che restituirà una ricerca di directory. Questo limite deve corrispondere alla configurazione nella directory LDAP. Per le ricerche estese dove non è supportato il paging, importazione e la sincronizzazione tenterà di recuperare gli utenti in batch. Se il limite di dimensioni specificato è superiore al limite configurato nella directory LDAP, alcuni utenti non vengano trovati. 



<li>Verificare la connessione LDAP facendo clic sul pulsante Test.</li>

<center>![Installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
<li>Se il test della connessione LDAP ha esito positivo, fare clic sul pulsante OK.</li>
<li>Successivamente, fare clic sull'icona Impostazioni società e selezionare la scheda Risoluzione nome utente.</li>
<li>Selezionare l'attributo dell'identificatore univoco utilizza LDAP per il pulsante di opzione nomi utente corrispondenti.</li>
<li>Se gli utenti immetteranno del nome utente nel modulo di accesso proxy ADFS nel formato "dominio\nomeutente", il Server deve essere in grado di eliminare il dominio dal nome utente durante la creazione di query LDAP. Che può essere eseguita tramite un'impostazione del Registro di sistema.</li>
<li>Aprire l'editor del Registro di sistema e passare a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/positivo Networks/PhoneFactor su un server a 64 bit. Se si dispone di un server a 32 bit, eseguire "Wow6432Node" dal percorso. Creare una nuova chiave del Registro di sistema DWORD denominata "UsernameCxz_stripPrefixDomain" e impostare il valore su 1. Il proxy ADFS è ora protetta da Azure multi-Factor Authentication. Assicurarsi che gli utenti siano stati importati da Active Directory nel Server. Se si desidera whitelist che gli indirizzi IP interni in modo che l'autenticazione a due fattori non è obbligatorio quando si accede al sito Web da tali percorsi, vedere la sezione IP attendibili.</li>

<center>![Installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 diretto senza un proxy

Per proteggere ADFS quando non viene utilizzato il proxy di ADFS, installare il Server Azure multi-Factor Authentication sul server ADFS e configurare il Server seguendo questa procedura.

### Per proteggere ADFS 2.0 senza un proxy
<ol>
<li>Nel Server Azure multi-Factor Authentication fare clic sull'icona autenticazione IIS nel menu a sinistra.</li>
<li>Fare clic sulla scheda HTTP.</li>
<li>Fare quindi clic su Add.</li>
<li>Nella finestra di dialogo Aggiungi URL di Base, immettere l'URL per il sito Web ADFS dove è l'autenticazione HTTP eseguita (ad esempio https://sso.domain.com/adfs/ls/auth/integrated) nel campo URL di Base e immettere un nome di applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS.</li>
<li>Se si desidera, modificare il timeout di inattività e massimo per i tempi di sessione.</li>
<li>Selezionare la casella di corrispondenza utente di richiedere Azure multi-Factor Authentication se tutti gli utenti sono già stati o verranno importati nel Server e soggetto a multi-factor authentication. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella. Vedere il file della Guida per ulteriori informazioni su questa funzionalità.</li>
<li>Se lo si desidera, selezionare la casella di cache di cookie.</li>

<center>![Installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>

<li>Fare clic sul pulsante OK.</li>
<li>Fare clic sulla scheda modulo nativo e selezionare il server, il sito Web che ADFS è in esecuzione (ad es. "Sito Web predefinito") o l'applicazione ADFS (ad esempio "ls" in "adfs") per abilitare il plug-in di IIS al livello desiderato.</li>
<li>Scegliere la casella Abilita autenticazione IIS nella parte superiore della schermata. ADFS è protetto da Azure multi-Factor Authentication. Assicurarsi che gli utenti siano stati importati da Active Directory nel Server. Se si desidera whitelist che gli indirizzi IP interni in modo che l'autenticazione a due fattori non è obbligatorio quando si accede al sito Web da tali percorsi, vedere la sezione IP attendibili.</li>

## Provider di identità attendibili
Il provider di identità attendibili consente agli utenti di ignorare Azure multi-Factor Authentication per le richieste del sito Web provenienti da specifici indirizzi IP o subnet. Ad esempio, è possibile escludere gli utenti da Azure multi-Factor Authentication durante l'accesso dall'ufficio. A tale scopo, specificare la subnet dell'ufficio come voce di provider di identità attendibili.

### Per configurare indirizzi IP attendibili

<ol>
<li>Nella sezione autenticazione IIS, fare clic sulla scheda IP attendibili.</li>
<li>Fare quindi clic su Add.</li>
<li>Quando viene visualizzata la finestra di dialogo Aggiungi provider di identità attendibili, selezionare il singolo indirizzo IP, l'intervallo IP o il pulsante di opzione Subnet.</li>
<li>Immettere l'indirizzo IP, intervallo di indirizzi IP o subnet devono essere incluse nell'elenco. Se si immette una subnet, selezionare mask appropriata e fare clic sul pulsante OK. L'elenco whitelist è stato aggiunto.</li>


<center>![Installazione](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=July15_HO2-->