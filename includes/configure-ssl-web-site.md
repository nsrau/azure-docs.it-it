#Abilitare HTTPS per un sito Web di Azure

> [WACOM.NOTE]
> Acquista velocità - usa il NUOVO [percorso guidato](http://support.microsoft.com/kb/2990804)di Azure!  Grazie al percorso guidato, è facilissimo associare un nome di dominio personalizzato E proteggere le comunicazioni (SSL) con i Servizi cloud di Azure o Siti Web di Azure.

È possibile proteggere la comunicazione tra il sito Web e il browser con HTTPS, che usa la crittografia SSL (Secure Socket Layer). Si tratta del metodo più usato per la protezione dei dati inviati in Internet e assicura ai visitatori che le transazioni con il sito sono sicure. Questo articolo illustra come abilitare HTTPS per un sito Web di Azure. 

<a href="bkmk_azurewebsites"></a><h2>HTTPS per il dominio \*.azurewebsites.net</h2>

Se non si prevede di usare un nome di dominio personalizzato, ma si prevede di usare il dominio *.azurewebsites.net assegnato al sito Web da Azure (ad esempio, contoso.azurewebsites.net), HTTP è già abilitato nel sito con un certificato fornito di Microsoft. È possibile usare **https://mywebsite.azurewebsites.net** per accedere al sito, tuttavia \*.azurewebsites.net è un dominio con caratteri jolly. Come [tutti i domini con caratteri jolly](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), non è sicuro quanto usare un dominio personalizzato con il proprio certificato. 

In questo documento sono inoltre disponibili informazioni dettagliate sull'abilitazione di HTTPS per nomi di dominio personalizzati, ad esempio **contoso.com**, **www.contoso.com** o **\*.contoso.com**

<a href="bkmk_domainname"></a><h2>Abilitare SSL per il dominio personalizzato</h2>

Per abilitare HTTPS per un dominio personalizzato, ad esempio **contoso.com**, è necessario registrare un nome di dominio personalizzato presso un registrar. Per altre informazioni su come configurare il nome di dominio di un sito Web di Azure, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](/it-it/develop/net/common-tasks/custom-dns-web-site/). Dopo la registrazione di un nome di dominio personalizzato e la configurazione del sito Web per la risposta al nome personalizzato, è necessario richiedere un certificato SSL per il dominio. 

> [WACOM.NOTE] Per abilitare HTTPS per nomi di dominio personalizzati, è necessario configurare i siti Web per la modalità dei piani di hosting Web **standard**. Ciò potrebbe comportare costi aggiuntivi, se la modalità attualmente in uso è gratuita o condivisa. Per altre informazioni sui prezzi per le modalità condivisa e **standard**, vedere [Dettagli prezzi][]. 

Dopo aver creato un dominio personalizzato valido, l'abilitazione di HTTPS per il sito Web prevede i passaggi seguenti:

1. [Ottenere un certificato SSL](#bkmk_getcert)
1. [Configurare la modalità standard](#bkmk_standardmode)
1. [Configurare SSL](#bkmk_configuressl)
1. [Applicare HTTPS nel sito Web di Azure](#bkmk_enforce)

<a href="bkmk_getcert"></a><h2>Ottenere un certificato SSL</h2>

.Prima di richiedere un certificato SSL, è necessario innanzitutto determinare i nomi di dominio da proteggere tramite il certificato, in modo da stabilire il tipo di certificato che è necessario ottenere. Se si desidera proteggere solo un singolo nome di dominio, ad esempio **contoso.com** o **www.contoso.com**, sarà sufficiente un certificato di base. Per proteggere più nomi di dominio, ad esempio **contoso.com**, **www.contoso.com** e **mail.contoso.com**, è possibile ottenere un [certificato con caratteri jolly](http://en.wikipedia.org/wiki/Wildcard_certificate) o un certificato con [Nome soggetto alternativo (SAN, Subject Alternate Name,](http://en.wikipedia.org/wiki/SubjectAltName) subjectAltName).

È necessario che i certificati SSL usati con Siti Web di Azure siano firmati da un'[Autorità di certificazione](http://en.wikipedia.org/wiki/Certificate_authority) (CA). Se non se ne dispone già, è necessario ottenerne uno da una società che emette certificati SSL. Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][cas] in Microsoft TechNet Wiki.

Il certificato deve soddisfare i requisiti seguenti per i certificati SSL in Azure:

* Il certificato deve includere una chiave privata.
* Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
* Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al sito Web. Se questo certificato deve essere usato da più domini, sarà necessario usare un valore carattere jolly o specificare valori di tipo subjectAltName, come illustrato in precedenza.
* È necessario che per il certificato venga usata una crittografia di almeno 2048 bit.
* I certificati emessi da server di Autorità di certificazione private non sono supportati da Siti Web di Azure.

Per ottenere un certificato SSL da usare con Siti Web di Azure, è necessario inviare una richiesta di firma del certificato (CSR) a un'autorità di certificazione e quindi generare un file con estensione pfx dal certificato ricevuto. A tale scopo, è possibile usare uno strumento a propria scelta. Di seguito sono riportati alcuni dei modi più comuni per ottenere un certificato:

- [Ottenere un certificato tramite Certreq.exe](#bkmk_certreq)
- [Ottenere un certificato tramite Gestione IIS](#bkmk_iismgr)
- [Ottenere un certificato tramite OpenSSL](#bkmk_openssl)
- [Ottenere un certificato SubjectAltName tramite OpenSSL](#bkmk_subjectaltname)
- [Generare certificati autofirmati (solo per i test)](#bkmk_selfsigned) 

> [WACOM.NOTE] Quando si esegue questa procedura, verrà richiesto di immettere un **nome comune** (CN, Common Name), ad esempio "www.contoso.com". Per i certificati con caratteri jolly, questo valore dovrebbe essere \*.nomedominio (ad esempio, \*.contoso.com). Se è necessario supporto sia per un nome con caratteri jolly quale *.contoso.com che per un nome di dominio radice quale contoso.com, è possibile usare un certificato subjectAltName con caratteri jolly.

> [WACOM.NOTE] I certificati di crittografia a curva ellittica (ECC) sono supportati in Siti Web di Azure, ma sono relativamente nuovi e per la procedura esatta per la richiesta di firma del certificato è opportuno rivolgersi alla propria Autorità di certificazione.

Potrebbe anche essere necessario ottenere **[certificati intermedi](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)**, noti anche come certificati della catena, se sono usati dalla CA. L'utilizzo di certificati intermedi è considerato più sicuro rispetto ai "certificati non in catena", quindi questi certificati sono usati spesso dalle Autorità di certificazione. I certificati intermedi vengono spesso forniti come download separato dal sito Web dell'Autorità di certificazione. Le procedure illustrate in questo articolo includono informazioni dettagliate per assicurarsi che eventuali certificati intermedi vengano uniti al certificato caricato nel sito Web di Azure. 

<a href="bkmk_certreq"></a>
###Ottenere un certificato tramite Certreq.exe (solo Windows)

Certreq.exe è un'utilità Windows per la creazione di richieste di certificato. È stata inclusa nelle installazioni di base di Windows da Windows XP/Windows Server 2000 in poi, quindi dovrebbe essere disponibile nei sistemi Windows recenti. Per ottenere un certificato SSL usando certreq.exe, eseguire la procedura seguente.

1. Aprire il **Blocco note** e creare un nuovo documento contenente gli elementi seguenti. Sostituire **mysite.com** nella riga Subject con il nome di dominio personalizzato per il sito. Ad esempio, Subject = "CN=www.contoso.com".

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

	Per altre informazioni sulle opzioni indicate, oltre che sulle altre opzioni disponibili, vedere la [documentazione di riferimento relativa a Certreq](http://technet.microsoft.com/library/cc725793.aspx).

2. Salvare il file di testo come **myrequest.txt**.

3. Nella **schermata Start** o nel **menu Start** eseguire **cmd.exe**.

4. Al prompt dei comandi usare il comando seguente per creare il file di richiesta del certificato:

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	Specificare il percorso del file **myrequest.txt** creato nel passaggio 1 e il percorso da usare durante la creazione del file **myrequest.csr**.

5. Inviare il file **myrequest.csr** a un'Autorità di certificazione per ottenere un certificato SSL. Potrebbe essere necessario caricare il file oppure aprirlo nel Blocco note e incollarne i contenuti direttamente in un modulo Web.

	Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][cas] in Microsoft TechNet Wiki.

6. Dopo che l'Autorità di certificazione ha fornito un file di certificato (con estensione CER), salvare il file nel computer usato per generare la richiesta, quindi usare il comando seguente per accettare la richiesta e completare il processo di generazione del certificato.

		certreq -accept -user mycert.cer

	In questo caso, il certificato **mycert.cer** ricevuto dall'Autorità di certificazione verrà usato per completare la firma del certificato. Non verrà creato alcun file. Il certificato verrà invece archiviato nell'archivio certificati di Windows.

6. Se la CA usa certificati intermedi, è necessario installarli prima di esportare il certificato nel passaggio successivo. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita per Microsoft IIS.

	Dopo avere scaricato il certificato, fare clic con il pulsante destro del mouse sul relativo nome in Esplora risorse e scegliere **Installa certificato**. Accettare i valori predefiniti dell'**Importazione guidata certificati** e continuare a selezionare **Avanti** fino al completamento dell'importazione.

7. Per esportare il certificato dall'archivio certificati, eseguire **certmgr.msc** dalla **schermata Start** o dal **menu Start**. Quando viene visualizzata la finestra **Gestione certificati** espandere la cartella **Personali**, quindi selezionare **Certificati**. Nel campo **Rilasciato a** cercare una voce con il nome di dominio personalizzato per cui è stato richiesto un certificato. Nel campo **Rilasciato da** dovrebbe essere indicata l'Autorità di certificazione usata per questo certificato.

	![insert image of cert manager here][certmgr]

9. Fare clic con il pulsante destro del mouse sul certificato, quindi scegliere **Tutte le attività** e infine **Esporta**. In **Esportazione guidata certificati** fare clic su **Avanti**, quindi selezionare **Sì, esporta la chiave privata**. Fare clic su **Avanti**.

	![Export the private key][certwiz1]

10. Selezionare **Scambio informazioni personali - PKCS #12**, **Includi tutti i certificati nel percorso certificazione** ed **Esporta tutte le proprietà estese**. Fare clic su **Avanti**.

	![include all certs and extended properties][certwiz2]

11. Selezionare **Password** e quindi immettere e confermare la password. Fare clic su **Avanti**.

	![specify a password][certwiz3]

12. Specificare un percorso e un nome file che includerà il certificato esportato. L'estensione del nome file deve essere **pfx**. Fare clic su **Avanti** per completare il processo.

	![provide a file path][certwiz4]

È ora possibile caricare il file PFX esportato nel sito Web di Azure.

<a href="bkmk_openssl"></a>
###Ottenere un certificato tramite OpenSSL

1. Generare una chiave privata e una richiesta di firma del certificato usando quanto segue da una riga di comando, una sessione Bash o una sessione terminal:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Quando richiesto, immettere le informazioni appropriate, ad esempio:

 		Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []: 

	Al termine del processo dovrebbero essere disponibili due file, **myserver.key** e **server.csr**. Il file **server.csr** contiene la richiesta di firma del certificato.

3. Inviare la richiesta a un'Autorità di certificazione per ottenere un certificato SSL. Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][cas] in Microsoft TechNet Wiki.

4. Dopo aver ottenuto un certificato da una CA, salvarlo in un file denominato **myserver.crt**. Se la CA ha fornito il certificato in formato testo, incollare il testo nel file **myserver.crt** file. Il contenuto del file dovrà essere simile al seguente se visualizzato in un editor di testo:

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

5. Dalla riga di comando oppure da una sessione Bash o terminal usare il comando seguente per convertire i file **myserver.key** e **myserver.crt** in **myserver.pfx**, che è il formato richiesto da Siti Web di Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando richiesto, immettere una password per proteggere il file con estensione pfx.

	<div class="dev-callout"> 
	<b>Nota</b>
	<p>Se la CA usa certificati intermedi, è necessario installarli prima di esportare il certificato nel passaggio successivo. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita come file PEM (con estensione pem).</p>
	<p>Di seguito è illustrato il comando che consente di creare un file con estensione pfx che include i certificati intermedi, contenuti nel file <b>intermediate-cets.pem</b>:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	Dopo l'esecuzione di questo comando, dovrebbe essere disponibile un file **myserver.pfx** appropriato per l'uso con Siti Web di Azure.

###<a name="bkmk_iismgr"></a>Ottenere un certificato tramite Gestione IIS

Se si ha familiarità con Gestione IIS, è possibile usarlo per generare un certificato che può essere usato con Siti Web di Azure.

1. Generare con Gestione IIS una richiesta di firma del certificato da inviare all'Autorità di certificazione. Per altre informazioni sulla generazione di una richiesta di firma del certificato, vedere [Richiedere un certificato del server Internet (IIS 7)][iiscsr].

2. Inviare la richiesta a un'Autorità di certificazione per ottenere un certificato SSL. Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][cas] in Microsoft TechNet Wiki.

3. Completare la richiesta di firma del certificato con il certificato reso disponibile dal fornitore dell'Autorità di certificazione. Per altre informazioni sul completamento della richiesta di firma del certificato, vedere [Installare un certificato del server Internet (IIS 7)][installcertiis].

4. Se la CA usa certificati intermedi, è necessario installarli prima di esportare il certificato nel passaggio successivo. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita per Microsoft IIS.

	Dopo avere scaricato il certificato, fare clic con il pulsante destro del mouse sul relativo nome in Esplora risorse e scegliere **Installa certificato**. Accettare i valori predefiniti dell'**Importazione guidata certificati** e continuare a selezionare **Avanti** fino al completamento dell'importazione.

4. Esportare il certificato da Gestione IIS. Per altre informazioni sull'esportazione del certificato, vedere [Esportare un certificato del server (IIS 7)][exportcertiis]. Il file esportato verrà usato nei passaggi successivi per il caricamento in Azure per l'uso con il sito Web di Azure.

	<div class="dev-callout"> 
	<b>Nota</b>
	<p>Durante il processo di esportazione, assicurarsi di selezionare l'opzione <strong>Sì, esporta la chiave privata</strong>. La chiave privata verrà inclusa nel certificato esportato.</p>
	</div>

	<div class="dev-callout"> 
	<b>Nota</b>
	<p>Durante il processo di esportazione, assicurarsi di selezionare l'opzione <strong>Includi tutti i certificati nel percorso certificazione</strong> e l'opzione <strong>Esporta tutte le proprietà estese</strong>. Eventuali certificati intermedi verranno inclusi nel certificato esportato.</p>
	</div>


###<a href="bkmk_subjectaltname"></a>Ottenere un certificato SubjectAltName tramite OpenSSL

È possibile usare OpenSSL per creare una richiesta di certificato che usa l'estensione SubjectAltName per supportare più nomi di dominio con un singolo certificato, ma è necessario disporre di un file di configurazione. La procedura seguente illustra in modo dettagliato la creazione di un file di configurazione e l'utilizzo di tale file per richiedere un certificato.

1. Creare un nuovo file denominato __sancert.cnf__ e usare quanto riportato di seguito come contenuto del file:
 
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

2. Salvare il file __sancert.cnf__.

1. Generare una chiave privata e una richiesta di firma del certificato usando il file di configurazione sancert.cnf. Da una sessione Bash o terminal usare il comando seguente:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Quando richiesto, immettere le informazioni appropriate, ad esempio:

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
 

	Al termine del processo dovrebbero essere disponibili due file, **myserver.key** e **server.csr**. Il file **server.csr** contiene la richiesta di firma del certificato.

3. Inviare la richiesta a un'Autorità di certificazione per ottenere un certificato SSL. Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][cas] in Microsoft TechNet Wiki.

4. Dopo aver ottenuto un certificato da una CA, salvarlo in un file denominato **myserver.crt**. Se la CA ha fornito il certificato in formato testo, incollare il testo nel file **myserver.crt** file. Il contenuto del file dovrà essere simile al seguente se visualizzato in un editor di testo:

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

5. Dalla riga di comando oppure da una sessione Bash o terminal usare il comando seguente per convertire i file **myserver.key** e **myserver.crt** in **myserver.pfx**, che è il formato richiesto da Siti Web di Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando richiesto, immettere una password per proteggere il file con estensione pfx.

	<div class="dev-callout"> 
	<b>Nota</b>
	<p>Se la CA usa certificati intermedi, è necessario installarli prima di esportare il certificato nel passaggio successivo. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita come file PEM (con estensione pem).</p>
	<p>Di seguito è illustrato il comando che consente di creare un file con estensione pfx che include i certificati intermedi, contenuti nel file <b>intermediate-cets.pem</b>:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	Dopo l'esecuzione di questo comando, dovrebbe essere disponibile un file **myserver.pfx** appropriato per l'uso con Siti Web di Azure.


###<a href="bkmk_selfsigned"></a>Generare certificati autofirmati (solo per i test)

In alcuni casi si potrebbe desiderare di ottenere un certificato per fini di test e posticipare l'acquisto di un certificato da un'Autorità di certificazione attendibile fino al passaggio in produzione. I certificati autofirmati possono risultare utili in queste situazioni. Un certificato autofirmato è un certificato creato e firmato dall'utente come se fosse un'Autorità di certificazione. Anche se questo certificato può essere usato per proteggere un sito Web, la maggior parte dei browser restituirà errori in caso di visita al sito, poiché il certificato non è stato firmato da un'Autorità di certificazione attendibile. È inoltre possibile che alcuni browser non consentano la visualizzazione del sito.

- [Generare un certificato autofirmato tramite makecert](#bkmk_ssmakecert)
- [Generare un certificato autofirmato tramite OpenSSL](#bkmk_ssopenssl)

<a href="bkmk_ssmakecert"></a>
#### Generare un certificato autofirmato tramite makecert ####

È possibile creare un certificato di prova da un sistema Windows in cui è installato Visual Studio eseguendo la procedura seguente:

1. Dal **menu Start** o dalla **schermata Start**, cercare **Prompt dei comandi per gli sviluppatori**. Fare infine clic con il pulsante destro del mouse su **Prompt dei comandi per gli sviluppatori** e scegliere **Esegui come amministratore**.

	Se viene visualizzata una finestra di dialogo Controllo dell'account utente, selezionare **Sì** per continuare.

2. Dal Prompt dei comandi per gli sviluppatori usare il comando seguente per creare un nuovo certificato autofirmato. È necessario sostituire **serverdnsname** con il nome DNS del sito Web specifico.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Questo comando consente di creare un certificato valido dalla data 01/01/2013 alla data 01/01/2014 e di archiviare la posizione nell'archivio certificati CurrentUser.

3. Dal **menu Start** o nella **schermata Start** cercare **Windows PowerShell** e avviare l'applicazione.

4. Dal prompt di Windows PowerShell usare i comandi seguenti per esportare il certificato creato in precedenza:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	La password specificata verrà archiviata come stringa sicura in $mypwd, quindi verrà trovato il certificato usando il nome DNS specificato dal parametro **dnsname** e il certificato verrà esportato nel file specificato dal parametro **filepath**. La stringa sicura contenente la password viene usata per proteggere il file esportato..

<a href="bkmk_ssopenssl"></a>
####Generare un certificato autofirmato tramite OpenSSL ####

1. Creare un nuovo documento denominato **serverauth.cnf**, usando quanto riportato di seguito come contenuto del file:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your website's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

	Ciò consente di specificare le impostazioni di configurazione necessarie per creare un certificato SSL che possa essere usato da Siti Web di Azure.

2. Generare un nuovo certificato autofirmato usando quanto riportato di seguito da una riga di comando, una sessione Bash o una sessione terminal:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Ciò consente di creare un nuovo certificato usando le impostazioni di configurazione specificate nel file **serverauth.cnf**.

3. Per esportare il certificato in un file con estensione PFX che possa essere caricato in un sito Web di Azure, usare il comando seguente:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando richiesto, immettere una password per proteggere il file con estensione pfx.

	Il file **myserver.pfx** creato da questo comando può essere usato per proteggere il sito Web di Azure per finalità di test.

<a href="bkmk_standardmode"></a><h2>Configurare la modalità standard</h2>

L'abilitazione di HTTPS per un dominio personalizzato è disponibile colo per la modalità dei piani di hosting Web **standard** di Siti Web di Azure. Per passare alla modalità **standard**, eseguire la procedura seguente.

> [WACOM.NOTE] Prima di cambiare la modalità di un sito Web, passando dalla modalità gratuita alla modalità **standard** del sito Web, è necessario rimuovere i limiti di spesa applicati per la sottoscrizione al sito Web. In caso contrario, si rischia che il sito risulti non disponibile se si superano i limiti prima del termine del periodo di fatturazione. Per altre informazioni sui prezzi per le modalità condivisa e **standard**, vedere [Dettagli prezzi][].

1. Nel browser, aprire il [portale di gestione][].

2. Nella scheda **Siti Web** fare clic sul nome del sito Web desiderato.

	![selecting a web site][sito Web]

3. Fare clic sulla scheda **SCALA**.

	![The scale tab][scala]

4. Nella sezione **generale** impostare la modalità del sito Web facendo clic su **STANDARD**.

	![standard mode selected][standard]

5. Fare clic su **Salva**. Quando richiesto fare clic su **Sì**.

	> [WACOM.NOTE] Se viene visualizzato un messaggio di errore di tipo "Configurazione della scalabilità per il sito Web '&lt;nome sito Web&gt;' non riuscita", è possibile usare il pulsante Dettagli per ottenere altre informazioni. È possibile che venga visualizzato un errore di tipo "Server di istanze riservate disponibili non sufficienti per soddisfare la richiesta". Se viene visualizzato questo errore, contattare il [Supporto per Azure](http://www.windowsazure.com/it-it/support/options/).


##<a href="bkmk_configuressl"></a>Configurare SSL

Prima di eseguire le procedure incluse in questa sezione, è necessario avere associato un nome di dominio personalizzato al sito Web di Azure. Per altre informazioni, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure.][customdomain].

1. Nel browser aprire il [portale di gestione di Azure][].

2. Nella scheda **Siti Web** fare clic sul nome del sito e quindi selezionare la scheda **CONFIGURA**.

	![the configure tab][configure]

3. Nella sezione **certificati** fare clic su**Carica un certificato**

	![upload a certificate][uploadcert]

4. Nella finestra di dialogo **Carica un certificato** selezionare il file del certificato con estensione pfx creato in precedenza tramite Gestione IIS oppure OpenSSL. Specificare la password, se necessaria, usata per la protezione del file con estensione pfx. Fare infine clic sul **segno di spunta** per caricare il certificato.

	![upload certificate dialog][uploadcertdlg]

5. Nella sezione **Associazioni SSL** della scheda **CONFIGURA** usare gli elenchi a discesa per selezionare il nome di dominio da proteggere con SSL e il certificato da usare. È inoltre possibile stabilire se usare il metodo SSL basato su [Indicazione nome server][sni] (SNI) o IP.

	![ssl bindings][sslbindings]
	
	* Il metodo SSL basato su IP associa un certificato a un nome di dominio tramite il mapping dell'indirizzo IP pubblico dedicato del server al nome di dominio. A tale scopo, è necessario che ogni nome di dominio (contoso.com, fabricam.com e così via) associato al servizio disponga di un indirizzo IP dedicato. Questo è il metodo tradizionale per l'associazione di certificati SSL a un server Web.

	* Il metodo SSL basato su SNI è un'estensione di SSL e [Transport Layer Security][tls] (TLS) che consente a più domini di condividere lo stesso indirizzo IP, con certificati di protezione distinti per ogni dominio. La maggior parte dei browser moderni, inclusi Internet Explorer, Chrome, Firefox e Opera, supporta SNI, ma è possibile che non sia supportato dai browser precedenti. Per altre informazioni su SNI, vedere l'articolo relativo all'[Indicazione nome server][(SNI, Server Name Indication)] su Wikipedia.

6. Fare clic su **Salva** per salvare le modifiche e abilitare SSL.

> [WACOM.NOTE] Se è stato selezionata l'opzione **SSL basato su IP** e il dominio personalizzato è stato configurato usando un record A, sarà necessario eseguire i passaggi aggiuntivi seguenti:
>
> 1. Dopo la configurazione di un'associazione SSL basata su IP, un indirizzo IP dedicato viene assegnato al sito Web. È possibile trovare tale indirizzo IP nella pagina **Dashboard** del sito Web, nella sezione **riepilogo rapido**. Sarà elencato come **Indirizzo IP virtuale**:
>    
>     ![Virtual IP address](./media/configure-ssl-web-site/staticip.png)
>    
>  Si noti che questo indirizzo IP sarà diverso rispetto all'indirizzo IP virtuale usato in precedenza per la configurazione del record A per il dominio. Se è stato impostato l'utilizzo del metodo SSL basato su SNI o se non è stato impostato l'utilizzo del metodo SSL, per questa voce non verrà elencato alcun indirizzo.
>
> 2. Usando gli strumenti forniti dal registrar, modificare il record A per il nome di dominio personalizzato, in modo che faccia riferimento all'indirizzo IP riportato nel passaggio precedente.


A questo punto si dovrebbe essere in grado di visitare il sito Web usando "HTTPS://" anziché "HTTP://" per verificare che il certificato sia stato configurato correttamente.

##<a href="bkmk_enforce"></a>Applicare HTTPS nel sito Web di Azure

Siti Web di Azure *non* applica HTTPS. I visitatori possono comunque accedere usando HTTP, con il rischio di compromettere la sicurezza del sito Web. Per applicare HTTPS per il sito Web, è possibile usare il modulo **Riscrittura URL**. Il modulo Riscrittura URL è incluso nei siti Web di Azure e consente di definire le regole applicate alle richieste in ingresso prima che queste vengano passate all'applicazione. **Può essere usato per le applicazioni scritte in qualsiasi linguaggio di programmazione supportato dai siti Web di Azure.** 

> [WACOM.NOTE] Le applicazioni .NET MVC devono usare il filtro [RequireHttps](http://msdn.microsoft.com/it-it/library/system.web.mvc.requirehttpsattribute.aspx) invece di Riscrittura URL. Per altre informazioni sull'uso di RequireHttps, vedere [Distribuzione di un'app ASP.NET MVC 5 sicura in un sito Web di Azure](/it-it/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).
> 
> Per informazioni sul reindirizzamento delle richieste a livello di codice usando altri linguaggi e framework di programmazione, consultare la documentazione relativa a queste tecnologie.

Le regole di Riscrittura URL sono definite in un file **web.config** archiviato nella radice dell'applicazione. Il seguente esempio contiene una regole di Riscrittura URL di base che forza l'uso di HTTPS per tutto il traffico in ingresso.

<a name="example"></a>**Esempio Web.Config di Riscrittura URL**

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <rewrite>
	      <rules>
	        <rule name="Force HTTPS" enabled="true">
	          <match url="(.*)" ignoreCase="false" />
	          <conditions>
	            <add input="{HTTPS}" pattern="off" />
	          </conditions>
	          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
	        </rule>
	      </rules>
	    </rewrite>
	  </system.webServer>
	</configuration>

Il funzionamento di questa regola prevede la restituzione di un codice di stato HTTP 301 (reindirizzamento permanente) quando l'utente richiede una pagina mediante HTTP. Il codice 301 reindirizza la richiesta allo stesso URL richiesto dal visitatore, ma sostituisce la parte HTTP della richiesta con HTTPS. Ad esempio, HTTP://contoso.com viene reindirizzato a HTTPS://contoso.com.

> [WACOM.NOTE] Se l'applicazione è scritta in **Node.js**, **PHP**, **Python Django** o **Java**, è probabile che non includa un file web.config. **Node.js**, **Python Django** e **Java** usano comunque il file web.config quando sono ospitati nei siti Web di Azure perché Azure crea automaticamente il file durante distribuzione, anche se l'utente non lo visualizza. Se si include un file nell'applicazione, questo sostituisce quello generato automaticamente da Azure.

###.NET

Per le applicazioni .NET, modificare il file web.config per l'applicazione e aggiungere la sezione **&lt;rewrite>** dall'[esempio](#example) alla sezione **&lt;system.WebServer>**.

Se il file web.config include già una sezione **&lt;rewrite>**, aggiungere **&lt;rule>** dall'[esempio](#example) come prima voce nella sezione **&lt;rules>**.

###PHP

Per le applicazioni PHP, salvare semplicemente l'[esempio](#example) come file web.config nella radice dell'applicazione, quindi ridistribuire l'applicazione nel sito Web di Azure.

###Node.js, Python Django e Java

.Un file web.config viene creato automaticamente per le app Node.js, Python Django e Java se non è già disponibile, ma esiste solo sul server perché viene creato durante la distribuzione. Il file generato automaticamente contiene le impostazioni che indicano ad Azure come ospitare l'applicazione.

Per recuperare e modificare il file autogenerato dal sito Web, usare i seguenti passaggi.

1. Scaricare il file tramite FTP (vedere la pagina relativa a [caricamento/scaricamento dei file tramite FTP e raccolta dei log di diagnostica](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)).

2. Aggiungerlo alla radice dell'applicazione.

3. Aggiungere le regole di riscrittura usando le seguenti informazioni.

	* **Node.js e Python Django**

		Il file web.config generato per le applicazioni Node.js e Python Django ha già una sezione **&lt;rewrite>** contenente le voci **&lt;rule>** necessarie per il corretto funzionamento del sito. Per forzare l'uso di HTTPS nel sito, aggiungere **&lt;rule>** dall'esempio come prima voce nella sezione **&lt;rules>**. In questo modo viene forzato HTTPS, mentre le altre regole restano intatte.

	* **Java**
	
		Il file web.config per le applicazioni Java con Apache Tomcat non contiene una sezione **&lt;rewrite>**, quindi è necessario aggiungere la sezione **&lt;rewrite>** dell'esempio alla sezione **&lt;system.webServer>**.

4. Ridistribuire il progetto (incluso il file web.config aggiornato) in Azure

Dopo aver distribuito un file web.config con una regola di riscrittura per forzare HTTPS, la regola viene applicata immediatamente e tutte le richieste vengono reindirizzate a HTTPS.

 Per altre informazioni sul modulo IIS Riscrittura URL, vedere la documentazione di [Riscrittura URL](http://www.iis.net/downloads/microsoft/url-rewrite). 

## Altre risorse ##
- [Centro protezione Microsoft Azure](/it-it/support/trust-center/security/)
- [Opzioni di configurazione sbloccate in Siti Web di Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Abilitare la registrazione diagnostica](/it-it/documentation/articles/web-sites-enable-diagnostic-log/)
- [Come configurare i siti Web](/it-it/documentation/articles/web-sites-configure/)
- [Portale di gestione di Azure](https://manage.windowsazure.com)

[customdomain]: /it-it/develop/net/common-tasks/custom-dns-web-site/
[iiscsr]: http://technet.microsoft.com/it-it/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/it-it/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/it-it/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: https://www.windowsazure.com/it-it/pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png
