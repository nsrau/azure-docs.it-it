<properties 
	pageTitle="Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con AD FS 2.0" 
	description="Questa è la pagina di autenticazione a più fattori di Azure che descrive come iniziare a utilizzare l'autenticazione a più fattori Azure e AD FS 2.0." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2016" 
	ms.author="billmath"/>
# Proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con AD FS 2.0

Se l'organizzazione è federata con Azure Active Directory e si dispone di risorse in locale o nel cloud che si desidera proteggere, è possibile farlo utilizzando il server di Azure multi-Factor Authentication e configurarlo per il funzionamento con ADFS in modo che l'autenticazione a più fattori viene attivata per i punti di fine di valore elevato.

In questa documentazione viene illustrato l'utilizzo di Server Azure multi-Factor Authentication con ADFS 2.0. Per informazioni sull'utilizzo di Azure multi-Factor Authentication con Windows servire ADFS 2012 R2 vedere [proteggere le risorse del cloud e locali mediante Server Azure multi-Factor Authentication con ADFS di Windows Server 2012 R2](multi-factor-authentication-get-started-adfs-w2k12.md).


## Proxy di AD FS 2.0
Per proteggere ADFS 2.0 con un proxy, installare il Server Azure multi-Factor Authentication nel server proxy ADFS e configurare il Server seguendo questa procedura.

### Per proteggere ADFS 2.0 con un proxy

1. Nel Server Azure multi-Factor Authentication fare clic sull'icona autenticazione IIS nel menu a sinistra.
2. Fare clic sulla scheda basata su Form.
3. Fare quindi clic su Add.
<center>! [Installazione] (. / media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Per rilevare automaticamente le variabili di nome utente, password e dominio, immettere l'URL di accesso (ad esempio https://sso.contoso.com/adfs/ls) nella finestra di dialogo Configura automaticamente sito Web e fare clic su OK.
5. Selezionare la casella di corrispondenza utente di richiedere Azure multi-Factor Authentication se tutti gli utenti sono già stati o verranno importati nel Server e soggetto a multi-factor authentication. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella. Vedere il file della Guida per ulteriori informazioni su questa funzionalità.
6. Se le variabili di pagina non possono essere rilevate automaticamente, fare clic su specifica manualmente... pulsante nella finestra di dialogo Configura automaticamente sito Web.
7. Nella finestra di dialogo Aggiungi sito Web basato su form immettere l'URL alla pagina di accesso ADFS nel campo URL di invio (ad esempio https://sso.contoso.com/adfs/ls) e immettere un nome di applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS. Vedere il file della Guida per ulteriori informazioni nell'URL di invio.
8. Impostare il formato della richiesta su "POST o GET".
9. Immettere la variabile di nome utente (ctl00$ ContentPlaceHolder1$ UsernameTextBox) e la variabile Password (ctl00$ ContentPlaceHolder1$ PasswordTextBox). Se la pagina di accesso basata su form viene visualizzata una casella di testo di dominio, immettere anche la variabile di dominio. Potrebbe essere necessario passare alla pagina di accesso in un browser web, pulsante destro del mouse sulla pagina e selezionare "HTML" per trovare i nomi delle caselle di input all'interno della pagina di accesso.
10. Selezionare la casella di corrispondenza utente di richiedere Azure multi-Factor Authentication se tutti gli utenti sono già stati o verranno importati nel Server e soggetto a multi-factor authentication. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella.
<center>! [Installazione] (. / media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Fare clic su Avanzate... pulsante per rivedere le impostazioni avanzate, tra cui la possibilità di selezionare un file di pagina di rifiuto personalizzato per memorizzare nella cache di autenticazioni al sito Web per un periodo di tempo tramite cookie e selezionare la modalità di autenticazione delle credenziali primarie.
12. Poiché il server proxy ADFS non è destinato a essere aggiunto al dominio, utilizzerà probabilmente LDAP per connettersi al controller di dominio per l'importazione utente e la preautenticazione. Nella finestra di dialogo sito Web basato su form avanzato, fare clic sulla scheda autenticazione primaria e selezionare "Binding LDAP" per il tipo di autenticazione pre-autenticazione.
13. Al termine, fare clic sul pulsante OK per tornare alla finestra di dialogo Aggiungi sito Web. Vedere il file della Guida per ulteriori informazioni sulle impostazioni avanzate.
14. Fare clic sul pulsante Complete per chiudere la finestra di dialogo.
15. Una volta rilevate o immesse le variabili di pagina e URL, i dati del sito Web verranno visualizzato nel pannello basata su Form.
16. Fare clic sulla scheda modulo nativo e selezionare il server, il sito Web in cui il proxy ADFS è in esecuzione (ad es. "Sito Web predefinito") o l'applicazione proxy ADFS (ad esempio "ls" in "adfs") per abilitare il plug-in di IIS al livello desiderato.
17. Scegliere la casella Abilita autenticazione IIS nella parte superiore della schermata.
18. L'autenticazione di IIS è abilitato. Tuttavia, per eseguire la pre-autenticazione per l'Active Directory (AD) tramite LDAP, è necessario configurare la connessione LDAP al controller di dominio. A tale scopo, fare clic sull'icona integrazione Directory.
19. Nella scheda Impostazioni selezionare il pulsante di opzione Usa specifico configurazione LDAP.
<center>! [Installazione] (. / media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
20. Fare clic su Edit.
21. Nella finestra di dialogo Modifica configurazione LDAP, compilare i campi con le informazioni necessarie per connettersi al controller di dominio Active Directory. Nella tabella seguente sono incluse le descrizioni dei campi. Nota: Queste informazioni sono inoltre incluse nel file della Guida del Server Azure multi-Factor Authentication.
22. Verificare la connessione LDAP facendo clic sul pulsante Test.
<center>! [Installazione] (. / media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
23. Se il test della connessione LDAP ha esito positivo, fare clic sul pulsante OK.
24. Successivamente, fare clic sull'icona Impostazioni società e selezionare la scheda Risoluzione nome utente.
25. Selezionare l'attributo dell'identificatore univoco utilizza LDAP per il pulsante di opzione nomi utente corrispondenti.
26. Se gli utenti immetteranno del nome utente nel modulo di accesso proxy ADFS nel formato "dominio\\nomeutente", il Server deve essere in grado di eliminare il dominio dal nome utente durante la creazione di query LDAP. Che può essere eseguita tramite un'impostazione del Registro di sistema.
27. Aprire l'editor del Registro di sistema e passare a HKEY\_LOCAL\_MACHINE/SOFTWARE/Wow6432Node/positivo Networks/PhoneFactor su un server a 64 bit. Se si dispone di un server a 32 bit, eseguire "Wow6432Node" dal percorso. Creare una nuova chiave del Registro di sistema DWORD denominata "UsernameCxz\_stripPrefixDomain" e impostare il valore su 1. Il proxy ADFS è ora protetta da Azure multi-Factor Authentication. Assicurarsi che gli utenti siano stati importati da Active Directory nel Server. Se si desidera whitelist che gli indirizzi IP interni in modo che l'autenticazione a due fattori non è obbligatorio quando si accede al sito Web da tali percorsi, vedere la sezione IP attendibili.

<center>! [Installazione] (. / media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 diretto senza un proxy

Per proteggere ADFS quando non viene utilizzato il proxy di ADFS, installare il Server Azure multi-Factor Authentication sul server ADFS e configurare il Server seguendo questa procedura.

### Per proteggere ADFS 2.0 senza un proxy
1. Nel Server Azure multi-Factor Authentication fare clic sull'icona autenticazione IIS nel menu a sinistra.
2. Fare clic sulla scheda HTTP.
3. Fare quindi clic su Add.
4. Nella finestra di dialogo Aggiungi URL di base immettere l'URL per il sito Web ADFS in cui viene eseguita l'autenticazione HTTP (ad esempio https://sso.domain.com/adfs/ls/auth/integrated) nel campo URL di base e immettere il nome dell'applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS.
5. Se si desidera, modificare il timeout di inattività e massimo per i tempi di sessione.
6. Selezionare la casella di corrispondenza utente di richiedere Azure multi-Factor Authentication se tutti gli utenti sono già stati o verranno importati nel Server e soggetto a multi-factor authentication. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella. Vedere il file della Guida per ulteriori informazioni su questa funzionalità.
7. Se lo si desidera, selezionare la casella di cache di cookie.
<center>! [Installazione] (. / media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Fare clic sul pulsante OK.
9. Fare clic sulla scheda modulo nativo e selezionare il server, il sito Web che ADFS è in esecuzione (ad es. "Sito Web predefinito") o l'applicazione ADFS (ad esempio "ls" in "adfs") per abilitare il plug-in di IIS al livello desiderato.
10. Scegliere la casella Abilita autenticazione IIS nella parte superiore della schermata. ADFS è protetto da Azure multi-Factor Authentication. Assicurarsi che gli utenti siano stati importati da Active Directory nel Server. Se si desidera whitelist che gli indirizzi IP interni in modo che l'autenticazione a due fattori non è obbligatorio quando si accede al sito Web da tali percorsi, vedere la sezione IP attendibili.


## Provider di identità attendibili
Il provider di identità attendibili consente agli utenti di ignorare Azure multi-Factor Authentication per le richieste del sito Web provenienti da specifici indirizzi IP o subnet. Ad esempio, è possibile escludere gli utenti da Azure multi-Factor Authentication durante l'accesso dall'ufficio. A tale scopo, specificare la subnet dell'ufficio come voce di provider di identità attendibili.

### Per configurare indirizzi IP attendibili


1. Nella sezione autenticazione IIS, fare clic sulla scheda IP attendibili.
1. Fare quindi clic su Add.
1. Quando viene visualizzata la finestra di dialogo Aggiungi provider di identità attendibili, selezionare il singolo indirizzo IP, l'intervallo IP o il pulsante di opzione Subnet.
1. Immettere l'indirizzo IP, intervallo di indirizzi IP o subnet devono essere incluse nell'elenco. Se si immette una subnet, selezionare mask appropriata e fare clic sul pulsante OK. È stato aggiunto l'indirizzo IP attendibile.


<center>! [Installazione] (. / media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!---HONumber=AcomDC_0218_2016-->