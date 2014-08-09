
# Abilitazione di HTTPS per un sito Web di Azure

Quando un utente visita un sito Web utilizzando il protocollo HTTPS, le comunicazioni tra il sito Web e il browser vengono protette mediante la crittografia SSL (Secure Socket Layer). Si tratta del metodo più utilizzato per la protezione dei dati inviati in Internet e assicura ai visitatori che le transazioni con il sito sono sicure. In questo articolo viene illustrato come abilitare HTTPS per un sito Web di Azure.

> [WACOM.NOTE] Per abilitare HTTPS per nomi di dominio personalizzati, è
> necessario configurare i siti Web per la modalità standard. Ciò
> potrebbe comportare costi aggiuntivi, se la modalità attualmente in
> uso è gratuita o condivisa. Per ulteriori informazioni sui prezzi per
> le modalità condivisa e standard, vedere [Dettagli prezzi][1].

[](bkmk_azurewebsites)<h2>Il dominio *.azurewebsites.net</h2>

Se non si prevede di utilizzare un nome di dominio personalizzato, ma si desidera utilizzare il dominio *.azurewebsites.net assegnato al sito Web da Azure (ad esempio, contoso.azurewebsites.net), il sito sarà già protetto da un certificato fornito da Microsoft. È possibile utilizzare **https://mywebsite.azurewebsites.net** per accedere al sito in modo sicuro.

In questo documento sono inoltre disponibili informazioni dettagliate sull'abilitazione di HTTPS per nomi di dominio personalizzati, ad esempio **contoso.com**, **www.contoso.com** o ***.contoso.com**

[](bkmk_domainname)<h2>Nomi di dominio personalizzati</h2>

Per abilitare HTTPS per un nome di dominio personalizzato, ad esempio **contoso.com**, è necessario registrare un nome di dominio personalizzato presso un registrar. Per ulteriori informazioni su come configurare il nome di dominio di un sito Web di Azure, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](/en-us/develop/net/common-tasks/custom-dns-web-site/). Dopo la registrazione di un nome di dominio personalizzato e la configurazione del sito Web per la risposta al nome personalizzato, è necessario richiedere un certificato SSL per il dominio.

La registrazione di un nome di dominio consente inoltre di creare sottodomini, ad esempio **www.contoso.com** o **posta.contoso.com**. Prima di richiedere un certificato SSL, è necessario innanzitutto determinare i nomi di dominio da proteggere tramite il certificato, in modo da stabilire il tipo di certificato che è necessario ottenere. Se si desidera proteggere solo un singolo nome di dominio, ad esempio **contoso.com** o **www.contoso.com**, sarà probabilmente sufficiente un certificato di base. Se si desidera proteggere più nomi di dominio, ad esempio **contoso.com**, **www.contoso.com** e **posta.contoso.com**, sarà necessario un certificato con caratteri jolly o un certificato con Nome soggetto alternativo (SAN, Subject Alternate Name, subjectAltName).

> [WACOM.NOTE] Nella maggior parte dei browser verrà visualizzato un
> avviso se il nome di dominio specificato nel certificato non
> corrisponde al nome di dominio immesso nel browser. Ad esempio, se nel
> certificato è stato indicato solo www.contoso.com, ma
> accesso.contoso.com è il nome di dominio utilizzato per accedere al
> sito in Internet Explorer, verrà visualizzato un avviso che indica che
> "Il certificato di sicurezza presentato dal sito web è stato emesso
> per l'indirizzo di un altro sito".

I **certificati di base** sono certificati in cui il nome comune (CN, Common Name) del certificato è impostato sul dominio o sottodominio specifico che sarà utilizzato dai client per visitare il sito. Ad esempio, **www.contoso.com**. Questi certificati consentono di proteggere solo il singolo nome di dominio specificato dal nome comune.

I **certificati con caratteri jolly** sono certificati in cui il nome comune del certificato include un carattere jolly '*' al livello corrispondente al sottodominio. Ciò consente al certificato di corrispondere a un singolo livello di sottodomini per un determinato dominio. Ad esempio, un certificato con caratteri jolly per
***.contoso.com** risulterebbe valido per **www.contoso.com**, **pagamenti.contoso.com** e **accesso.contoso.com**. Non sarebbe invece valido per **prova.accesso.contoso.com**, poiché in questo caso è stato aggiunto un livello di sottodomini. Non risulterebbe inoltre valido per **contoso.com**, poiché questo è il livello del dominio radice, non un sottodominio.

Per il nome di dominio *.azurewebsites.net creato automaticamente per i siti Web degli utenti, Microsoft rende disponibile un certificato con caratteri jolly.

**subjectAltName** è un'estensione di certificato che consente l'associazione di diversi valori o nomi soggetto alternativi a un certificato. Ai fini dei certificati SSL, ciò consente di aggiungere ulteriori nomi DNS per i quali il certificato risulterà valido. Ad esempio, un certificato che utilizza subjectAltName potrebbe avere un nome comune quale **contoso.com**, ma potrebbe disporre anche di nomi alternativi quali **www.contoso.com**, **pagamenti.contoso.com**, **prova.accesso.contoso.com** e addirittura **fabrikam.com**. Questo tipo di certificato risulterebbe valido per tutti i nomi di dominio specificati nel nome comune e in subjectAltName.

Un certificato può supportare sia i caratteri jolly che subjectAltName.

[](bkmk_getcert)<h2>Ottenere un certificato</h2>

È necessario che i certificati SSL utilizzati con Siti Web di Azure siano firmati da un'Autorità di certificazione (CA, Certificate Authority), ovvero un'entità di terze parti ritenuta attendibile, che emette certificati con questa specifica finalità. Se non se ne dispone già, è necessario ottenerne uno da un rivenditore di certificati SSL. Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][2] in Microsoft TechNet Wiki.

Il certificato deve soddisfare i requisiti seguenti per i certificati SSL in Azure:

* Il certificato deve includere una chiave privata.

* Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).

* Il nome del soggetto del certificato deve corrispondere al dominio utilizzato per accedere al sito Web. Se questo certificato deve essere utilizzato da più domini, sarà necessario utilizzare un valore carattere jolly o specificare valori di tipo subjectAltName, come illustrato in precedenza.
  
  * Per informazioni sulla configurazione di un nome di dominio personalizzato per un sito Web di Azure, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](/en-us/develop/net/common-tasks/custom-dns-web-site/).


 
  > [WACOM.NOTE] Non tentare di ottenere o generare un certificato per
  > il dominio azurewebsites.net.

* È necessario che per il certificato venga utilizzata una crittografia di almeno 2048 bit.

> [WACOM.NOTE] I certificati emessi da server di Autorità di
> certificazione private non sono supportati da Siti Web di Azure.

Per ottenere un certificato SSL da un'Autorità di certificazione, è necessario generare una richiesta di firma del certificato (CSR, Certificate Signing Request), che viene inviata all'Autorità di certificazione. L'Autorità di certificazione restituirà quindi un certificato, che viene utilizzato per completare la richiesta di firma del certificato. Due modi comuni per generare una richiesta di firma del certificato consistono nell'utilizzo delle applicazioni certmgr.exe oppure [OpenSSL][3]. Certmgr.exe è disponibile solo su Windows, mentre OpenSSL è disponibile per la maggior parte delle piattaforme. Di seguito è illustrata la procedura per l'utilizzo di entrambe le utilità.

Potrebbe essere inoltre necessario ottenere **certificati intermedi**, noti anche come certificati della catena, se sono utilizzati dall'Autorità di certificazione. L'utilizzo di certificati intermedi è considerato più sicuro rispetto ai "certificati non in catena", quindi questi certificati sono utilizzati spesso dalle Autorità di certificazione. I certificati intermedi vengono spesso forniti come download separato dal sito Web dell'Autorità di certificazione. Le procedure illustrate in questo articolo includono informazioni dettagliate su come assicurarsi che eventuali certificati intermedi vengano uniti al certificato caricato nel sito Web di Azure.

> [WACOM.NOTE] Quando si esegue una delle procedure, verrà richiesta
> l'immissione di un **Common Name**. Se si prevede di ottenere un
> certificato con caratteri jolly da utilizzare con più domini
> (www.contoso.com, vendite.contoso.com), questo valore dovrebbe essere
> *.nomedominio (ad esempio, *.contoso.com). Se si prevede di ottenere
> un certificato per un singolo nome di dominio, questo valore deve
> corrispondere esattamente al valore che gli utenti immetteranno nel
> browser per visitare il sito Web. Ad esempio, www.contoso.com.
> 
> Se è necessario supporto sia per un nome con caratteri jolly quale
> *.contoso.com che per un nome di dominio radice quale contoso.com, è
> possibile utilizzare un certificato Nome soggetto alternativo (SAN,
> Subject Alternate Name) con caratteri jolly. Per un esempio della
> creazione di una richiesta di certificato che utilizza le estensioni
> SubjectAltName, vedere [Certificato
> SubjectAltName](#bkmk_subjectaltname).
> 
> Per ulteriori informazioni su come configurare il nome di dominio di
> un sito Web di Azure, vedere [Configurazione di un nome di dominio
> personalizzato per un sito Web di
> Azure](/en-us/develop/net/common-tasks/custom-dns-web-site/).

### Ottenere un certificato tramite Certreq.exe (solo Windows)

Certreq.exe è un'utilità Windows per la creazione di richieste di certificato. È stata inclusa nelle installazioni di base di Windows da Windows XP/Windows Server 2000 in poi, quindi dovrebbe essere disponibile nei sistemi Windows recenti. Per ottenere un certificato SSL utilizzando certreq.exe, eseguire la procedura seguente.

Se si desidera creare un certificato autofirmato per finalità di test, vedere la sezione [Certificati autofirmati](#bkmk_selfsigned) di questo documento.

Se si desidera utilizzare Gestione IIS per creare una richiesta di certificato, vedere la sezione [Ottenere un certificato tramite Gestione IIS](#bkmk_iismgr).

1.  Aprire **Blocco note** e creare un nuovo documento contenente gli elementi seguenti. Sostituire **mysite.com** nella riga Subject con il nome di dominio personalizzato per il sito. Ad esempio, Subject = "CN=www.contoso.com".
    
         [NewRequest]
         Subject = "CN=mysite.com"
         Exportable = TRUE
         KeyLength = 2048
         KeySpec = 1
         KeyUsage = 0xA0
         MachineKeySet = True
         ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
         ProviderType = 12
         RequestType = CMC
        
         [EnhancedKeyUsageExtension]
         OID=1.3.6.1.5.5.7.3.1
    
    Per ulteriori informazioni sulle opzioni indicate, oltre che sulle altre opzioni disponibili, vedere la [documentazione di riferimento relativa a Certreq][4].

2.  Salvare il file di testo come **myrequest.txt**.

3.  Nella **schermata iniziale** o nel **menu Start** eseguire **cmd.exe**.

4.  Nel prompt dei comandi utilizzare il comando seguente per creare il file di richiesta del certificato:
    
         certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr
    
    Specificare il percorso del file **myrequest.txt** creato nel passaggio 1 e il percorso da utilizzare durante la creazione del file **myrequest.csr**.

5.  Inviare il file **myrequest.csr** a un'Autorità di certificazione per ottenere un certificato SSL. Potrebbe essere necessario caricare il file oppure aprirlo in Blocco note e incollarne i contenuti direttamente in un modulo Web.
    
    Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][2] in Microsoft TechNet Wiki.

6.  Dopo che l'Autorità di certificazione ha fornito un file di certificato (con estensione CER), salvare il file nel computer utilizzato per generare la richiesta, quindi utilizzare il comando seguente per accettare la richiesta e completare il processo di generazione del certificato.
    
         certreq -accept -user mycert.cer
    
    In questo caso, il certificato **mycert.cer** ricevuto dall'Autorità di certificazione verrà utilizzato per completare la firma del certificato. Non verrà creato alcun file. Il certificato verrà invece archiviato nell'archivio certificati di Windows.

7.  Se l'Autorità di certificazione utilizza i certificati intermedi, sarà necessario installare tali certificati prima di esportare il certificato nei passaggi successivi. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita per Microsoft IIS.
    
    Dopo avere scaricato il certificato, fare clic con il pulsante destro del mouse sul relativo nome in Esplora risorse e selezionare **Installa certificato**. Accettare i valori predefiniti dell'**Importazione guidata certificati** e continuare a selezionare **Avanti** fino al completamento dell'importazione.

8.  Per esportare il certificato dall'archivio certificati, eseguire **certmgr.msc** dalla **schermata Start** o dal **menu Start**. Quando viene visualizzata la finestra **Gestione certificati** espandere la cartella **Personali**, quindi selezionare **Certificati**. Nel campo **Rilasciato a** cercare una voce con il nome di dominio personalizzato per cui è stato richiesto un certificato. Nel campo **Rilasciato da** dovrebbe essere indicata l'Autorità di certificazione utilizzata per questo certificato.
    
    ![Inserimento immagine di Gestione
    certificati](./media/configure-ssl-web-site/waws-certmgr.png)

9.  Fare clic con il pulsante destro del mouse sul certificato, quindi selezionare **Tutte le attività** e infine **Esporta**. In **Esportazione guidata certificati** fare clic su **Avanti**, quindi selezionare **Sì, esporta la chiave privata**. Fare clic su **Avanti**.
    
    ![Esportazione della chiave
    privata](./media/configure-ssl-web-site/waws-certwiz1.png)

10. Selezionare **Scambio informazioni personali - PKCS #12**, **Includi tutti i certificati nel percorso certificazione** ed **Esporta tutte le proprietà estese**. Fare clic su **Avanti**.
    
    ![Inclusione di tutti i certificati e delle proprietà
    estese](./media/configure-ssl-web-site/waws-certwiz2.png)

11. Selezionare **Password**, quindi immettere e confermare la password. Fare clic su **Avanti**.
    
    ![Inserimento di una
    password](./media/configure-ssl-web-site/waws-certwiz3.png)

12. Specificare un percorso e un nome file che includerà il certificato esportato. L'estensione del nome file deve essere **pfx**. Fare clic su **Avanti** per completare il processo.
    
    ![Inserimento di un percorso per il
    file](./media/configure-ssl-web-site/waws-certwiz4.png)

È ora possibile caricare il file PFX esportato nel sito Web di Azure.

### Ottenere un certificato tramite OpenSSL

1.  Generare una chiave privata e una richiesta di firma del certificato utilizzando quanto segue da una riga di comando, una sessione Bash o una sessione terminal:
    
         openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2.  Quando richiesto, immettere le informazioni appropriate, ad esempio:

 		Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []: 
    
    Al termine del processo, dovrebbero essere disponibili due file, **myserver.key** e **server.csr**. Il file **server.csr** contiene la richiesta di firma del certificato.

1.  Inviare la richiesta a un'Autorità di certificazione per ottenere un certificato SSL. Per un elenco di Autorità di certificazione, vedere  l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][2] in Microsoft TechNet Wiki.

2.  Dopo aver ottenuto un certificato da una CA, salvarlo in un file denominato **myserver.crt**. Se la CA ha fornito il certificato in formato testo, incollare il testo nel file **myserver.crt** file. Il contenuto del file dovrà essere simile al seguente se visualizzato in un editor di testo:
    
         -----BEGIN CERTIFICATE-----
         MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
         UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
         c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
         NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
         ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
         ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
         enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
         3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
         xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
         ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
         Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
         AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
         Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
         F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
         7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
         lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
         A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
         -----END CERTIFICATE-----
    
    Salvare il file.

3.  Dalla riga di comando oppure da una sessione Bash o terminal utilizzare il comando seguente per convertire i file **myserver.key** e **myserver.crt** in **myserver.pfx**, che è il formato richiesto da Siti Web di Azure:
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    Quando richiesto, immettere una password per proteggere il file con estensione pfx.
    
	<div  class="dev-callout" markdown="1">

	<b>Nota</b>
	<p>Se la CA utilizza certificati intermedi, è necessario installarli prima di esportare il certificato nel passaggio successivo. In genere questi certificati vengono forniti come download distinto da
	un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita come file PEM (con estensione pem).</p>

	<p>
	Di seguito è illustrato il comando che consente di creare un file con estensione pfx che include i certificati intermedi, contenuti nel file <b>intermediate-cets.pem</b>:</p>
	</div>


		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem





    
Dopo l'esecuzione di questo comando, dovrebbe essere disponibile un file **myserver.pfx** appropriato per l'utilizzo con Siti Web di Azure.

[](bkmk_standardmode)<h2>Configurare la modalità standard</h2>

L'abilitazione di HTTPS per un dominio personalizzato è disponibile solo per la modalità standard dei siti Web di Azure. Per passare alla modalità standard, eseguire la procedura seguente.

> [WACOM.NOTE] Prima di cambiare la modalità di un sito Web, passando
> dalla modalità gratuita alla modalità standard del sito Web, è
> necessario rimuovere i limiti di spesa applicati per la sottoscrizione
> al sito Web. In caso contrario, si rischia che il sito risulti non
> disponibile se si superano i limiti prima del termine del periodo di
> fatturazione. Per ulteriori informazioni sui prezzi per le modalità
> condivisa e standard, vedere [Dettagli prezzi][1].

1.  Accedere al [portale di gestione][5] dal browser.

2.  Nella scheda **Web Sites** fare clic sul nome del sito Web  desiderato.
    
    ![Selezione di un sito
    Web](./media/configure-ssl-web-site/sslwebsite.png)

3.  Fare clic sulla scheda **SCALE**.
    
    ![Scheda
    Scalabilità](./media/configure-ssl-web-site/sslscale.png)

4.  Nella sezione **general** impostare la modalità del sito Web facendo clic su **STANDARD**.
    
    ![Modalità Standard
    selezionata](./media/configure-ssl-web-site/sslreserved.png)

5.  Fare clic su **Save**. Quando richiesto, fare clic su **Yes**.


   
    > [WACOM.NOTE] Se viene visualizzato un messaggio di errore di tipo
    > "Configurazione della scalabilità per il sito Web '<nome
    > sito>' non riuscita", sarà possibile utilizzare il pulsante
    > Dettagli per ottenere ulteriori informazioni. È possibile che
    > venga visualizzato un errore di tipo "Server di istanze riservate
    > disponibili non sufficienti per soddisfare la richiesta". Se viene
    > visualizzato questo errore, contattare il [Supporto per Azure][6].

[](bkmk_configuressl)<h2>Configurare SSL</h2>

Prima di eseguire le procedure incluse in questa sezione, è necessario avere associato un nome di dominio personalizzato al sito Web di Azure. Per ulteriori informazioni, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](/en-us/develop/net/common-tasks/custom-dns-web-site/).

1.  Accedere al [portale di gestione di Azure][5] dal browser.

2.  Nella scheda **Web Sites** fare clic sul nome del sito e quindi selezionare la scheda **CONFIGURE**.
    
    ![Scheda per la
    configurazione](./media/configure-ssl-web-site/sslconfig.png)

3.  Nella sezione **certificates** fare clic su **upload a certificate**
    
    ![Caricamento di un
    certificato](./media/configure-ssl-web-site/ssluploadcert.png)

4.  Nella finestra di dialogo **Upload a certificate** selezionare il file del certificato con estensione pfx creato in precedenza tramite Gestione IIS oppure OpenSSL. Specificare la password, se necessaria, utilizzata per la protezione del file con estensione pfx. Fare infine clic sul **check** per caricare il certificato.
    
    ![Finestra di dialogo per il caricamento di un
    certificato](./media/configure-ssl-web-site/ssluploaddlg.png)

5.  Nella sezione **ssl bindings** della scheda **CONFIGURE** utilizzare gli elenchi a discesa per selezionare il nome di dominio da proteggere con SSL e il certificato da utilizzare. È inoltre possibile stabilire se utilizzare il metodo SSL basato su [Indicazione nome server][7] (SNI, Server Name Indication) o IP.
    
    ![Associazioni SSL](./media/configure-ssl-web-site/sslbindings.png)
    
    * Il metodo SSL basato su IP associa un certificato a un nome di  dominio tramite il mapping dell'indirizzo IP pubblico dedicato del server al nome di dominio. A tale scopo, è necessario che ogni  nome di dominio (contoso.com, fabricam.com e così via) associato al servizio disponga di un indirizzo IP dedicato. Questo è il metodo tradizionale per l'associazione di certificati SSL a un server Web.
    
    * Il metodo SSL basato su SNI è un'estensione di SSL e [Transport Layer Security][8] (TLS), che consente a più domini di condividere lo stesso indirizzo IP, con certificati di protezione distinti per ogni dominio. La maggior parte dei browser moderni, inclusi Internet Explorer, Chrome, Firefox e Opera, supporta SNI, ma è possibile che non sia supportato dai browser precedenti. Per ulteriori informazioni su SNI, vedere l'articolo relativo  all'[Indicazione nome server][7] (SNI, Server Name Indication) su Wikipedia.

6.  Fare clic su **Save** per salvare le modifiche e abilitare SSL.

> [WACOM.NOTE] Se è stato selezionata l'opzione **IP based SSL** e il
> dominio personalizzato è stato configurato utilizzando un record A,
> sarà necessario eseguire i passaggi aggiuntivi seguenti:
> 
> 1.  Dopo la configurazione di un'associazione SSL basata su IP, un
>     indirizzo IP dedicato viene assegnato al sito Web. È possibile
>     trovare tale indirizzo IP nella pagina **Dashboard** del sito Web,
>     nella sezione **quick glance**. Sarà elencato come **Virtual IP
>     Address**:
>     
>     ![Indirizzo IP
>     virtuale](./media/configure-ssl-web-site/staticip.png)
>     
>     Si noti che questo indirizzo IP sarà diverso rispetto
>     all'indirizzo IP virtuale utilizzato in precedenza per la
>     configurazione del record A per il dominio. Se è stato impostato
>     l'utilizzo del metodo SSL basato su SNI o se non è stato impostato
>     l'utilizzo del metodo SSL, per questa voce non verrà elencato
>     alcun indirizzo.
> 
> 2.  Utilizzando gli strumenti forniti dal registrar, modificare il
>     record A per il nome di dominio personalizzato, in modo che faccia
>     riferimento all'indirizzo IP riportato nel passaggio precedente.

A questo punto si dovrebbe essere in grado di visitare il sito Web utilizzando HTTPS per verificare che il certificato sia stato configurato correttamente.

[](bkmk_subjectaltname)<h2>Certificati SubjectAltName (facoltativo)</h2>

È possibile utilizzare OpenSSL per creare una richiesta di certificato che utilizza l'estensione SubjectAltName per supportare più nomi di dominio con un singolo certificato, ma è necessario disporre di un file di configurazione. La procedura seguente illustra in modo dettagliato la creazione di un file di configurazione e l'utilizzo di tale file per richiedere un certificato.

1.  Creare un nuovo file denominato **sancert.cnf** e utilizzare quanto riportato di seguito come contenuto del file:

		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default = 
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default = 
		localityName = Locality Name (eg, city)
		localityName_default = 
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  = 
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----
    
    Si noti la riga che inizia con "subjectAltName". Sostituire i nomi di dominio attualmente elencati con nomi di dominio che si desidera supportare in aggiunta al nome comune. ad esempio:
    
         subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com
    
    Non è necessario modificare il campo commonName_default, poiché verrà richiesto di immettere il nome comune in uno dei passaggi successivi.

2.  Salvare il file **sancert.cnf**.

3.  Generare una chiave privata e una richiesta di firma del certificato utilizzando il file di configurazione sancert.cnf. Da una sessione Bash o terminal utilizzare il comando seguente:
    
         openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

4.  Quando richiesto, immettere le informazioni appropriate, ad esempio:

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
     
    
    Al termine del processo, dovrebbero essere disponibili due file, **myserver.key** e **server.csr**. Il file **server.csr** contiene la richiesta di firma del certificato.

1.  Inviare la richiesta a un'Autorità di certificazione per ottenere un certificato SSL. Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][2] in Microsoft TechNet Wiki.

2.  Dopo aver ottenuto un certificato da una CA, salvarlo in un file denominato **myserver.crt**. Se la CA ha fornito il certificato in formato testo, incollare il testo nel file **myserver.crt** file. Il contenuto del file dovrà essere simile al seguente se visualizzato in un editor di testo:
    
         -----BEGIN CERTIFICATE-----
         MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
         UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
         c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
         NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
         ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
         ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
         enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
         3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
         xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
         ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
         Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
         AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
         Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
         F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
         7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
         lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
         A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
         -----END CERTIFICATE-----
    
    Salvare il file.

3.  Dalla riga di comando oppure da una sessione Bash o terminal utilizzare il comando seguente per convertire i file **myserver.key** e **myserver.crt** in **myserver.pfx**, che è il formato richiesto da Siti Web di Azure:
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    Quando richiesto, immettere una password per proteggere il file con estensione pfx.
    
	<div  class="dev-callout" markdown="1">

	<b>Nota</b>
	<p>Se la CA utilizza certificati intermedi, è necessario installarli prima di esportare il certificato nel passaggio successivo. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita come file PEM (con estensione pem).
	</p>

	<p>Di seguito è illustrato il comando che consente di creare un file con estensione pfx che include i certificati intermedi, contenuti nel file <b>intermediate-cets.pem</b>:</p>

	</div>

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem





    
    Dopo l'esecuzione di questo comando, dovrebbe essere disponibile un file **myserver.pfx** appropriato per l'utilizzo con Siti Web di Azure.

## <a name="bkmk_iismgr"></a>Ottenere un certificato tramite Gestione IIS (facoltativo)

Se si ha familiarità con Gestione IIS, è possibile utilizzarlo per generare un certificato che può essere utilizzato con Siti Web di Azure.

1.  Generare con Gestione IIS una richiesta di firma del certificato da inviare all'Autorità di certificazione. Per ulteriori informazioni sulla generazione di una richiesta di firma del certificato, vedere [Richiedere un certificato del server Internet (IIS 7)][9].

2.  Inviare la richiesta a un'Autorità di certificazione per ottenere un certificato SSL. Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][2] in Microsoft TechNet Wiki.

3.  Completare la richiesta di firma del certificato con il certificato reso disponibile dal fornitore dell'Autorità di certificazione. Per ulteriori informazioni sul completamento della richiesta di firma del certificato, vedere [Installare un certificato del server Internet (IIS 7)][10].

4.  Se la CA utilizza certificati intermedi, è necessario installarli prima di esportare il certificato nel passaggio successivo. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita per Microsoft IIS.
    
    Dopo avere scaricato il certificato, fare clic con il pulsante destro del mouse sul relativo nome in Esplora risorse e selezionare **Installa certificato**. Accettare i valori predefiniti dell'**Importazione guidata certificati** e continuare a selezionare **Avanti** fino al completamento dell'importazione.

5.  Esportare il certificato da Gestione IIS. Per ulteriori informazioni sull'esportazione del certificato, vedere [Esportare un certificato del server (IIS 7)][11]. Il file esportato verrà utilizzato nei passaggi successivi per il caricamento in Azure per l'uso con il sito Web di Azure.
    
	<div  class="dev-callout" markdown="1">

	<b>Nota</b>
	<p>Durante il processo di esportazione, assicurarsi di selezionare l'opzione <b>Sì, esporta la chiave privata</b>.
	La chiave privata verrà inclusa nel certificato esportato.</p>


	</div>

    
	<div  class="dev-callout" markdown="1">

	<b>Nota</b>
	<p>Durante il processo di esportazione, assicurarsi di selezionare l'opzione **Includi tutti i certificati nel percorso certificazione** e l'opzione **Esporta tutte le proprietà estese**. Eventuali certificati
	intermedi verranno inclusi nel certificato esportato.</p>


    </div>


[](bkmk_selfsigned)<h2>Certificati autofirmati (facoltativo)</h2>

In alcuni casi si potrebbe desiderare di ottenere un certificato per fini di test e posticipare l'acquisto di un certificato da un'Autorità di certificazione attendibile fino al passaggio in produzione. I certificati autofirmati possono risultare utili in queste situazioni. Un certificato autofirmato è un certificato creato e firmato dall'utente come se fosse un'Autorità di certificazione. Anche se questo certificato può essere utilizzato per proteggere un sito Web, la maggior parte dei browser restituirà errori in caso di visita al sito, poiché il certificato non è stato firmato da un'Autorità di certificazione attendibile. È inoltre possibile che alcuni browser non consentano la visualizzazione del sito.

Benché sia possibile creare un certificato autofirmato in molti modi, in questo articolo sono disponibili solo informazioni sull'utilizzo di
**makecert** e **OpenSSL**.

### Creare un certificato autofirmato tramite makecert

È possibile creare un certificato di prova da un sistema Windows in cui è installato Visual Studio eseguendo la procedura seguente:

1.  Dal **menu Start** o dalla **schermata Start** cercare il **Prompt dei comandi per gli sviluppatori**. Fare infine clic con il pulsante destro del mouse su **Prompt dei comandi per gli sviluppatori**, quindi scegliere **Esegui come amministratore**.
    
    Se viene visualizzata una finestra di dialogo Controllo dell'account utente, selezionare **Sì** per continuare.

2.  Dal Prompt dei comandi per gli sviluppatori utilizzare il comando seguente per creare un nuovo certificato autofirmato. È necessario sostituire **serverdnsname** con il nome DNS del sito Web specifico.
    
         makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048
    
    Questo comando consente di creare un certificato valido dalla data 01/01/2013 alla data 01/01/2014 e di archiviare la posizione nell'archivio certificati CurrentUser.

3.  Dal **menu Start** o nella **schermata Start** cercare **Windows PowerShell** e avviare tale applicazione.

4.  Dal prompt di Windows PowerShell utilizzare i comandi seguenti per esportare il certificato creato in precedenza:
    
         $mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
         get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd
    
    La password specificata verrà archiviata come stringa sicura in $mypwd, quindi verrà trovato il certificato utilizzando il nome DNS specificato dal parametro **dnsname** e il certificato verrà esportato nel file specificato dal parametro **filepath**. La stringa sicura contenente la password viene utilizzata per proteggere il file esportato.

### Creare un certificato autofirmato tramite OpenSSL

1.  Creare un nuovo documento denominato **serverauth.cnf**, utilizzando quanto riportato di seguito come contenuto del file:
    
         [ req ]
         default_bits           = 2048
         default_keyfile        = privkey.pem
         distinguished_name = req_distinguished_name
         attributes             = req_attributes
         x509_extensions        = v3_ca
        
         [ req_distinguished_name ]
         countryName			= Country Name (2 letter code)
         countryName_min			= 2? countryName_max			= 2? stateOrProvinceName		= State or Province Name (full name)
         localityName			= Locality Name (eg, city)
         0.organizationName		= Organization Name (eg, company)
         organizationalUnitName		= Organizational Unit Name (eg, section)
         commonName			= Common Name (eg, your website's domain name)
         commonName_max			= 64? emailAddress			= Email Address
         emailAddress_max		= 40?? [ req_attributes ]
         challengePassword		= A challenge password
         challengePassword_min		= 4? challengePassword_max		= 20?? [ v3_ca ]
          subjectKeyIdentifier=hash
          authorityKeyIdentifier=keyid:always,issuer:always
          basicConstraints = CA:false
          keyUsage=nonRepudiation, digitalSignature, keyEncipherment
          extendedKeyUsage = serverAuth
    
    Ciò consente di specificare le impostazioni di configurazione necessarie per creare un certificato SSL che possa essere utilizzato da Siti Web di Azure.

2.  Generare un nuovo certificato autofirmato utilizzando quanto riportato di seguito da una riga di comando, una sessione Bash o una sessione terminal:
    
         openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf
    
    Ciò consente di creare un nuovo certificato utilizzando le impostazioni di configurazione specificate nel file **serverauth.cnf**.

3.  Per esportare il certificato in un file con estensione PFX che possa essere caricato in un sito Web di Azure, utilizzare il comando seguente:
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    Quando richiesto, immettere una password per proteggere il file con estensione pfx.
    
    Il file **myserver.pfx** creato da questo comando può essere utilizzato per proteggere il sito Web di Azure per finalità di test.



[1]: https://www.windowsazure.com/en-us/pricing/details/
[2]: http://go.microsoft.com/fwlink/?LinkID=269988
[3]: http://www.openssl.org/
[4]: http://technet.microsoft.com/library/cc725793.aspx
[5]: https://manage.windowsazure.com/
[6]: http://www.windowsazure.com/it-it/support/options/
[7]: http://en.wikipedia.org/wiki/Server_Name_Indication
[8]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[9]: http://technet.microsoft.com/en-us/library/cc732906(WS.10).aspx
[10]: http://technet.microsoft.com/en-us/library/cc771816(WS.10).aspx
[11]: http://technet.microsoft.com/en-us/library/cc731386(WS.10).aspx