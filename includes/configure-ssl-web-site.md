
È possibile proteggere la comunicazione tra l'app Web e il browser con HTTPS, che usa la crittografia SSL (Secure Socket Layer). Si tratta del metodo più usato per la protezione dei dati inviati in Internet e assicura ai visitatori che le transazioni con l'app sono sicure. Questo articolo illustra come configurare HTTPS per un'app Web nel servizio app di Azure. Questo articolo non comprende l'autenticazione del certificato client. Per informazioni, vedere [Come configurare l’autenticazione reciproca TLS per le applicazioni Web](../articles/app-service-web/app-service-web-configure-tls-mutual-auth.md).

> [AZURE.NOTE]
> Acquista velocità: utilizza il NUOVO [percorso guidato](http://support.microsoft.com/kb/2990804)di Azure È facilissimo associare un nome di dominio personalizzato E proteggere le comunicazioni (SSL) con i servizi cloud o il [servizio app](http://go.microsoft.com/fwlink/?LinkId=529714) di Azure.

##<a name="bkmk_azurewebsites"></a>HTTPS per il dominio *.azurewebsites.net

Se non si prevede di usare un nome di dominio personalizzato, ma si prevede di usare il dominio *.azurewebsites.net assegnato all'app Web da Azure (ad esempio, contoso.azurewebsites.net), HTTPS è già abilitato nel sito con un certificato fornito da Microsoft. È possibile usare ****https://mywebsite.azurewebsites.net** per accedere all'app, tuttavia *.azurewebsites.net è un dominio con caratteri jolly. Come tutti i [domini con caratteri jolly](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), non è sicuro quanto un dominio personalizzato con il proprio certificato.

Questo documento fornisce anche informazioni dettagliate sull'abilitazione di HTTPS per domini personalizzati, ad esempio **contoso.com**, **www.contoso.com** o ***.contoso.com**.

##<a name="bkmk_domainname"></a>Abilitare SSL per il dominio personalizzato

Per abilitare HTTPS per un dominio personalizzato, ad esempio **contoso.com**, è necessario prima registrare un nome di dominio personalizzato presso un registrar. Per altre informazioni su come configurare il nome di dominio di un'app Web, vedere [Configurazione di un nome di dominio personalizzato per un sito Web di Azure](/it-IT/develop/net/common-tasks/custom-dns-web-site/). Dopo la registrazione di un nome di dominio personalizzato e la configurazione dell'app Web per la risposta al nome personalizzato, è necessario richiedere un certificato SSL per il dominio.

> [AZURE.NOTE]Per abilitare HTTPS per i nomi di dominio personalizzati, è necessario configurare l'app Web per la modalità **Standard**. Ciò potrebbe comportare costi aggiuntivi, se la modalità attualmente in uso è gratuita o condivisa. Per altre informazioni sui prezzi per la modalità condivisa e **Standard**, vedere [Dettagli prezzi][pricing].

##<a name="bkmk_getcert"></a>Ottenere un certificato SSL

Prima di richiedere un certificato SSL, è necessario prima di tutto determinare i nomi di dominio da proteggere tramite il certificato, in modo da stabilire il tipo di certificato che è necessario ottenere. Se si vuole proteggere solo un singolo nome di dominio, ad esempio **contoso.com** o **www.contoso.com**, sarà sufficiente un certificato di base. Se è necessario proteggere più nomi di dominio, ad esempio **contoso.com**, **www.contoso.com** e **mail.contoso.com**, è possibile ottenere un [certificato con carattere jolly](http://en.wikipedia.org/wiki/Wildcard_certificate) o un certificato con [Nome oggetto alternativo](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName).

È necessario che i certificati SSL usati con App Web siano firmati da un'[Autorità di certificazione](http://en.wikipedia.org/wiki/Certificate_authority) (CA). Se non se ne dispone già, è necessario ottenerne uno da una società che emette certificati SSL. Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][cas] in Microsoft TechNet Wiki.

Il certificato deve soddisfare i requisiti seguenti per i certificati SSL in Azure:

* Il certificato deve includere una chiave privata.
* Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
* Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere all'app Web. Se questo certificato deve essere usato da più domini, sarà necessario usare un valore carattere jolly o specificare valori di tipo subjectAltName, come illustrato in precedenza.
* È necessario che per il certificato venga usata una crittografia di almeno 2048 bit.
* I certificati emessi da server di Autorità di certificazione private non sono supportati dal servizio app di Azure.

Per ottenere un certificato SSL da usare con il servizio app di Azure, è necessario inviare una richiesta di firma del certificato (CSR) a un'autorità di certificazione e quindi generare un file con estensione pfx dal certificato ricevuto. A tale scopo, è possibile usare uno strumento a propria scelta. Di seguito sono riportati alcuni dei modi più comuni per ottenere un certificato:

- [Ottenere un certificato tramite Certreq.exe](#bkmk_certreq)
- [Ottenere un certificato tramite Gestione IIS](#bkmk_iismgr)
- [Ottenere un certificato tramite OpenSSL](#bkmk_openssl)
- [Ottenere un certificato SubjectAltName tramite OpenSSL](#bkmk_subjectaltname)
- [Generare certificati autofirmati (solo per scopi di test)](#bkmk_selfsigned) 

> [AZURE.NOTE]Nel corso della procedura viene richiesto di immettere un **Nome comune**, ad esempio `www.contoso.com`. Per i certificati con caratteri jolly, questo valore dovrebbe essere *.nomedominio (ad esempio, *.contoso.com). Se è necessario il supporto sia per un nome con caratteri jolly quale *.contoso.com che per un nome di dominio radice quale contoso.com, è possibile usare un certificato subjectAltName con caratteri jolly.
>
> I certificati di crittografia a curva ellittica (ECC) sono supportati nel servizio app di Azure, ma sono relativamente nuovi e per la procedura da seguire per la richiesta di firma del certificato è opportuno rivolgersi alla propria Autorità di certificazione.

Potrebbe essere necessario, inoltre, ottenere **[certificati intermedi](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)**, noti anche come certificati della catena, se sono usati dall'Autorità di certificazione. L'uso di certificati intermedi è considerato più sicuro rispetto ai "certificati non in catena", quindi questi certificati sono usati spesso dalle Autorità di certificazione. I certificati intermedi vengono spesso forniti come download separato dal sito Web dell'Autorità di certificazione. Le procedure illustrate in questo articolo includono i passaggi da eseguire per assicurarsi che eventuali certificati intermedi vengano uniti al certificato caricato nelle app Web.

<a name="bkmk_certreq"></a>
###Ottenere un certificato tramite Certreq.exe (solo Windows)

Certreq.exe è un'utilità Windows per la creazione di richieste di certificato. È stata inclusa nelle installazioni di base di Windows da Windows XP/Windows Server 2000 in poi, quindi dovrebbe essere disponibile nei sistemi Windows recenti. Per ottenere un certificato SSL usando certreq.exe, eseguire la procedura seguente.

1. Aprire **Blocco note** e creare un nuovo documento contenente gli elementi seguenti. Sostituire **mysite.com** nella riga Subject con il nome di dominio personalizzato per l'app Web. Ad esempio, Subject = "CN=www.contoso.com".

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	Per altre informazioni sulle opzioni indicate, oltre che sulle altre opzioni disponibili, vedere la [documentazione di riferimento relativa a Certreq](http://technet.microsoft.com/library/cc725793.aspx).

2. Salvare il file di testo come **myrequest.txt**.

3. Nella **schermata iniziale** o nel **menu Start** eseguire **cmd.exe**.

4. Nel prompt dei comandi usare il comando seguente per creare il file di richiesta del certificato:

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	Specificare il percorso del file **myrequest.txt** creato nel passaggio 1 e il percorso da usare durante la creazione del file **myrequest.csr**.

5. Inviare il file **myrequest.csr** a un'Autorità di certificazione per ottenere un certificato SSL. Potrebbe essere necessario caricare il file oppure aprirlo in Blocco note e incollarne i contenuti direttamente in un modulo Web.

	Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][cas] in Microsoft TechNet Wiki.

6. Dopo che l'Autorità di certificazione ha fornito un file di certificato (con estensione CER), salvare il file nel computer usato per generare la richiesta, quindi usare il comando seguente per accettare la richiesta e completare il processo di generazione del certificato.

		certreq -accept -user mycert.cer

	In questo caso, il certificato **mycert.cer** ricevuto dall'Autorità di certificazione verrà usato per completare la firma del certificato. Non verrà creato alcun file. Il certificato verrà invece archiviato nell'archivio certificati di Windows.

6. Se l'Autorità di certificazione usa i certificati intermedi, sarà necessario installare tali certificati prima di esportare il certificato nei passaggi successivi. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita per Microsoft IIS.

	Dopo avere scaricato il certificato, fare clic con il pulsante destro del mouse sul relativo nome in Esplora risorse e selezionare **Installa certificato**. Accettare i valori predefiniti dell'**Importazione guidata certificati** e continuare a selezionare **Avanti** fino al completamento dell'importazione.

7. Per esportare il certificato dall'archivio certificati, eseguire **certmgr.msc** dalla **schermata Start** o dal **menu Start**. Quando viene visualizzata la finestra **Gestione certificati** espandere la cartella **Personali**, quindi selezionare **Certificati**. Nel campo **Rilasciato a** cercare una voce con il nome di dominio personalizzato per cui è stato richiesto un certificato. Nel campo **Rilasciato da** dovrebbe essere indicata l'Autorità di certificazione usata per questo certificato.

	![Inserimento immagine di Gestione certificati][certmgr]

9. Fare clic con il pulsante destro del mouse sul certificato, quindi selezionare **Tutte le attività** e infine **Esporta**. In **Esportazione guidata certificati** fare clic su **Avanti**, quindi selezionare **Sì, esporta la chiave privata**. Fare clic su **Avanti**.

	![Esportazione della chiave privata][certwiz1]

10. Selezionare **Scambio informazioni personali - PKCS #12**, **Includi tutti i certificati nel percorso certificazione** ed **Esporta tutte le proprietà estese**. Fare clic su **Avanti**.

	![Inclusione di tutti i certificati e delle proprietà estese][certwiz2]

11. Selezionare **Password**, quindi immettere e confermare la password. Fare clic su **Avanti**.

	![Inserimento di una password][certwiz3]

12. Specificare un percorso e un nome file che includerà il certificato esportato. L'estensione del nome file deve essere **pfx**. Fare clic su **Avanti** per completare il processo.

	![Inserimento di un percorso per il file][certwiz4]

È ora possibile caricare il file PFX esportato nell'app Web di Azure.

<a name="bkmk_openssl"></a>
###Ottenere un certificato tramite OpenSSL

1. Generare una chiave privata e una richiesta di firma del certificato usando quanto segue da una riga di comando, una sessione Bash o una sessione terminal:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Quando richiesto, immettere le informazioni appropriate, Ad esempio:

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

5. Dalla riga di comando oppure da una sessione Bash o terminal, usare il comando seguente per convertire **myserver.key** e **myserver.crt** in **myserver.pfx**, che è il formato richiesto dal servizio app di Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando richiesto, immettere una password per proteggere il file con estensione pfx.

	> [AZURE.NOTE]Se la CA usa certificati intermedi, è necessario installarli prima di esportare il certificato nel passaggio successivo. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita come file PEM (con estensione pem).
	> 
	> Di seguito è illustrato il comando che consente di creare un file con estensione pfx che include i certificati intermedi, contenuti nel file **intermediate-cets.pem**:
	>
	>
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	Dopo l'esecuzione di questo comando, dovrebbe essere disponibile un file **myserver.pfx** appropriato per l'uso con il servizio app di Azure.

###<a name="bkmk_iismgr"></a>Ottenere un certificato tramite Gestione IIS

Se si ha familiarità con Gestione IIS, è possibile usarlo per generare un certificato che può essere usato con il servizio app di Azure.

1. In Gestione IIS generare una richiesta di firma del certificato da inviare all'Autorità di certificazione. Per altre informazioni sulla generazione di una richiesta di firma del certificato, vedere [Richiedere un certificato del server Internet (IIS 7)][iiscsr].

2. Inviare la richiesta a un'Autorità di certificazione per ottenere un certificato SSL. Per un elenco di Autorità di certificazione, vedere l'articolo relativo al [programma SSL Root Certificate per Windows 8 e Windows Phone (CA membri)][cas] in Microsoft TechNet Wiki.

3. Completare la richiesta di firma del certificato con il certificato reso disponibile dal fornitore dell'Autorità di certificazione. Per altre informazioni sul completamento della richiesta di firma del certificato, vedere [Installare un certificato del server Internet (IIS 7)][installcertiis].

4. Se la CA usa certificati intermedi, è necessario installarli prima di esportare il certificato nel passaggio successivo. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita per Microsoft IIS.

	Dopo avere scaricato il certificato, fare clic con il pulsante destro del mouse sul relativo nome in Esplora risorse e selezionare **Installa certificato**. Accettare i valori predefiniti dell'**Importazione guidata certificati** e continuare a selezionare **Avanti** fino al completamento dell'importazione.

4. Esportare il certificato da Gestione IIS. Per altre informazioni sull'esportazione del certificato, vedere [Esportare un certificato del server (IIS 7)][exportcertiis]. Il file esportato verrà usato nei passaggi successivi per il caricamento in Azure per l'uso con l'app Web.

	> [AZURE.NOTE]Durante il processo di esportazione, accertarsi di aver selezionato l'opzione <strong>Sì, esporta la chiave privata</strong>. La chiave privata verrà inclusa nel certificato esportato.

	> [AZURE.NOTE]Durate il processo di esportazione, accertarsi di aver selezionato le opzioni **Includi tutti i certificati nel percorso di certificazione** ed **Esporta tutte le proprietà estese**. Eventuali certificati intermedi verranno inclusi nel certificato esportato.


###<a name="bkmk_subjectaltname"></a>Ottenere un certificato SubjectAltName tramite OpenSSL

È possibile usare OpenSSL per creare una richiesta di certificato che usa l'estensione SubjectAltName per supportare più nomi di dominio con un singolo certificato, ma è necessario disporre di un file di configurazione. La procedura seguente illustra in modo dettagliato la creazione di un file di configurazione e l'uso di tale file per richiedere un certificato.

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

	Si noti la riga che inizia con "subjectAltName". Sostituire i nomi di dominio attualmente elencati con nomi di dominio che si desidera supportare in aggiunta al nome comune. Ad esempio:

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	Non è necessario modificare il campo commonName\_default, poiché verrà richiesto di immettere il nome comune in uno dei passaggi successivi.

2. Salvare il file __sancert.cnf__.

1. Generare una chiave privata e una richiesta di firma del certificato usando il file di configurazione sancert.cnf. Da una sessione Bash o terminal usare il comando seguente:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Quando richiesto, immettere le informazioni appropriate, Ad esempio:

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

5. Dalla riga di comando oppure da una sessione Bash o terminal, usare il comando seguente per convertire **myserver.key** e **myserver.crt** in **myserver.pfx**, che è il formato richiesto dal servizio app di Azure:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando richiesto, immettere una password per proteggere il file con estensione pfx.

	> [AZURE.NOTE]Se la CA usa certificati intermedi, è necessario installarli prima di esportare il certificato nel passaggio successivo. In genere questi certificati vengono forniti come download distinto da un'Autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione fornita come file PEM (con estensione pem).
	> 
	> Di seguito è illustrato il comando che consente di creare un file con estensione pfx che include i certificati intermedi, contenuti nel file **intermediate-cets.pem**:
	>
	> 
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	Dopo l'esecuzione di questo comando, dovrebbe essere disponibile un file **myserver.pfx** appropriato per l'uso con il servizio app di Azure.


###<a name="bkmk_selfsigned"></a>Generare un certificato autofirmato (solo per scopi di test)

In alcuni casi si potrebbe desiderare di ottenere un certificato per fini di test e posticipare l'acquisto di un certificato da un'Autorità di certificazione attendibile fino al passaggio in produzione. I certificati autofirmati possono risultare utili in queste situazioni. Un certificato autofirmato è un certificato creato e firmato dall'utente come se fosse un'Autorità di certificazione. Anche se questo certificato può essere usato per proteggere un'app Web, la maggior parte dei browser restituirà errori in caso di visita al sito perché il certificato non è stato firmato da un'Autorità di certificazione attendibile. È inoltre possibile che alcuni browser non consentano la visualizzazione del sito.

- [Generare un certificato autofirmato tramite makecert](#bkmk_ssmakecert)
- [Generare un certificato autofirmato tramite OpenSSL](#bkmk_ssopenssl)

<a name="bkmk_ssmakecert"></a>
#### Generare un certificato autofirmato tramite makecert ####

È possibile creare un certificato di prova da un sistema Windows in cui è installato Visual Studio eseguendo la procedura seguente:

1. Dal **menu Start** o dalla **schermata Start** cercare il **Prompt dei comandi per gli sviluppatori**. Fare infine clic con il pulsante destro del mouse su **Prompt dei comandi per gli sviluppatori**, quindi scegliere **Esegui come amministratore**.

	Se viene visualizzata una finestra di dialogo Controllo dell'account utente, selezionare **Sì** per continuare.

2. Dal Prompt dei comandi per gli sviluppatori usare il comando seguente per creare un nuovo certificato autofirmato. È necessario sostituire **serverdnsname** con il nome DNS dell'app Web specifica.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Questo comando consente di creare un certificato valido dalla data 01/01/2013 alla data 01/01/2014 e di archiviare la posizione nell'archivio certificati CurrentUser.

3. Dal **menu Start** o nella **schermata Start** cercare **Windows PowerShell** e avviare tale applicazione.

4. Dal prompt di Windows PowerShell usare i comandi seguenti per esportare il certificato creato in precedenza:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	La password specificata verrà archiviata come stringa sicura in $mypwd, quindi verrà trovato il certificato usando il nome DNS specificato dal parametro **dnsname** e il certificato verrà esportato nel file specificato dal parametro **filepath**. La stringa sicura contenente la password viene usata per proteggere il file esportato..

<a name="bkmk_ssopenssl"></a>
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
        commonName			= Common Name (eg, your app's domain name)
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

	Ciò consente di specificare le impostazioni di configurazione necessarie per creare un certificato SSL che possa essere usato dal servizio app di Azure.

2. Generare un nuovo certificato autofirmato usando quanto riportato di seguito da una riga di comando, una sessione Bash o una sessione terminal:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Ciò consente di creare un nuovo certificato usando le impostazioni di configurazione specificate nel file **serverauth.cnf**.

3. Per esportare il certificato in un file con estensione PFX che possa essere caricato in un'app Web, usare il comando seguente:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Quando richiesto, immettere una password per proteggere il file con estensione pfx.

	Il file **myserver.pfx** creato da questo comando può essere usato per proteggere l'app Web per finalità di test.

<a name="bkmk_standardmode"></a><h2>Configurare la modalità standard</h2>

L'abilitazione di HTTPS per un dominio personalizzato è disponibile solo per la modalità **Standard** dell'app Web. Per passare alla modalità **Standard**, eseguire la procedura seguente.

> [AZURE.NOTE]Prima di cambiare la modalità di un'app Web da **Gratuito** a **Standard**, è necessario rimuovere i limiti di spesa applicati per la sottoscrizione all'app Web o si rischia che il sito risulti non disponibile se si limitano i limiti prima del termine del periodo di fatturazione. Per altre informazioni sui prezzi per le modalità condivisa e **Standard**, vedere [Dettagli prezzi][pricing].

1.	Accedere al [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) dal browser.
2.	Fare clic sull'opzione **Sfoglia** a sinistra nella pagina.
3.	Fare clic sul pannello **App Web**.
4.	Fare clic sul nome dell'app Web.
5.	Nella pagina **Informazioni di base**, fare clic su **Impostazioni**.
6.	Fare clic su **Scala**.![Scheda Scalabilità][scale]
7.	Nella sezione **Scala** impostare la modalità del piano di servizio app facendo clic su **Seleziona**. ![Livello di prezzo][sslreserved]

	> [AZURE.NOTE]Se viene visualizzato un messaggio di errore di tipo "Configurazione della scalabilità per l'app Web '&lt;nome app&gt' non riuscita", è possibile usare il pulsante Dettagli per ottenere altre informazioni. È possibile che venga visualizzato un errore di tipo "Server di istanze riservate disponibili non sufficienti per soddisfare la richiesta". Se viene visualizzato questo errore, contattare il [Supporto per Azure](/support/options/).


##<a name="bkmk_configuressl"></a>Configurare SSL

Prima di eseguire la procedura inclusa in questa sezione, è necessario avere associato un nome di dominio personalizzato all'app Web. Per altre informazioni, vedere [Configurazione di un nome di dominio personalizzato per un'app Web][customdomain].

1.	Accedere al [portale di gestione di Azure](https://portal.azure.com) dal browser.
2.	Fare clic sull'opzione **Sfoglia** a sinistra nella pagina.
3.	Fare clic sul pannello **App Web**.
4.	Fare clic sul nome dell'app Web.
5.	Nella pagina **Informazioni di base**, fare clic su **Impostazioni**.	
6.	Fare clic su **Domini personalizzati ed SSL**. 
	![Scheda di configurazione][sslconfig]
7.	Nella sezione **certificati** fare clic su **Carica**
8.	Nella finestra di dialogo **Carica un certificato** selezionare il file del certificato con estensione pfx creato in precedenza tramite Gestione IIS oppure OpenSSL. Specificare la password, se necessaria, usata per la protezione del file con estensione pfx. Fare infine clic su **Salva** per caricare il certificato. 
	![Caricamento SSL][ssluploadcert]
9. Nella sezione **Associazioni SSL** della scheda **Impostazioni SSL**, usare gli elenchi a discesa per selezionare il nome di dominio da proteggere con SSL e il certificato da usare. È inoltre possibile stabilire se usare il metodo SSL basato su [Indicazione nome server][sni] (SNI, Server Name Indication) o IP.

	![Associazioni SSL][sslbindings]
	
	* Il metodo SSL basato su IP associa un certificato a un nome di dominio tramite il mapping dell'indirizzo IP pubblico dedicato del server al nome di dominio. A tale scopo, è necessario che ogni nome di dominio (contoso.com, fabricam.com e così via) associato al servizio disponga di un indirizzo IP dedicato. Questo è il metodo tradizionale per l'associazione di certificati SSL a un server Web.

	* Il metodo SSL basato su SNI è un'estensione di SSL e [Transport Layer Security][tls] (TLS), che consente a più domini di condividere lo stesso indirizzo IP, con certificati di protezione distinti per ogni dominio. La maggior parte dei browser moderni, inclusi Internet Explorer, Chrome, Firefox e Opera, supporta SNI, ma è possibile che non sia supportato dai browser precedenti. Per altre informazioni su SNI, vedere l'articolo relativo all'[Indicazione nome server][sni] (SNI, Server Name Indication) su Wikipedia.

10. Fare clic su **Save** per salvare le modifiche e abilitare SSL.

> [AZURE.NOTE]Se è stata selezionata l'opzione **SSL basato su IP** e il dominio personalizzato è stato configurato usando un record A, sarà necessario eseguire i passaggi aggiuntivi seguenti:
>
> 1. Dopo la configurazione di un'associazione SSL basata su IP, un indirizzo IP dedicato viene assegnato all'app Web. È possibile trovare tale indirizzo IP nella pagina **Dashboard** dell'app Web, nella sezione **Riepilogo rapido**. Sarà elencato come **Virtual IP Address**:
>    
>     ![Indirizzo IP virtuale](./media/configure-ssl-web-site/staticip.png)
>    
>     Si noti che questo indirizzo IP sarà diverso rispetto all'indirizzo IP virtuale usato in precedenza per la configurazione del record A per il dominio. Se è stato impostato l'uso del metodo SSL basato su SNI o se non è stato impostato l'uso del metodo SSL, per questa voce non verrà elencato alcun indirizzo.
>
> 2. Usando gli strumenti forniti dal registrar, modificare il record A per il nome di dominio personalizzato, in modo che faccia riferimento all'indirizzo IP riportato nel passaggio precedente.


A questo punto dovrebbe essere possibile passare all'app Web usando `HTTPS://` anziché `HTTP://` per verificare che il certificato sia stato configurato correttamente.

##<a name="bkmk_enforce"></a>Applicare HTTPS all'app Web

Il servizio app di Azure *non* applica HTTPS. I visitatori possono comunque accedere all'app Web usando HTTP, con il rischio di compromettere la sicurezza dell'app Web. Per applicare HTTPS per l'app Web, è possibile usare il modulo **Riscrittura URL**. Il modulo Riscrittura URL è incluso nel servizio app di Azure e consente di definire le regole applicate alle richieste in ingresso prima che queste vengano passate all'applicazione. **Può essere usato per le applicazioni scritte in qualsiasi linguaggio di programmazione supportato da Azure.**

> [AZURE.NOTE]Le applicazioni .NET MVC devono usare il filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) anziché Riscrittura URL. Per altre informazioni sull'uso di RequireHttps, vedere [Distribuire un'app ASP.NET MVC 5 sicura in un'app Web](../article/app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
> 
> Per informazioni sul reindirizzamento delle richieste a livello di codice usando altri linguaggi e framework di programmazione, consultare la documentazione relativa a queste tecnologie.

Le regole di Riscrittura URL sono definite in un file **web.config** archiviato nella radice dell'applicazione. Il seguente esempio contiene una regole di Riscrittura URL di base che forza l'uso di HTTPS per tutto il traffico in ingresso.

<a name="example"></a>**Esempio di Riscrittura URL Web.Config**

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

Il funzionamento di questa regola prevede la restituzione di un codice di stato HTTP 301 (reindirizzamento permanente) quando l'utente richiede una pagina mediante HTTP. Il codice 301 reindirizza la richiesta allo stesso URL richiesto dal visitatore, ma sostituisce la parte HTTP della richiesta con HTTPS. Ad esempio, HTTP://contoso.com verrà reindirizzato a HTTPS://contoso.com.

> [AZURE.NOTE]Se l'applicazione è scritta in **Node.js**, **PHP**, **Python Django** o **Java**, è probabile che non includa un file web.config. Tuttavia, **Node.js**, **Python Django** e **Java** usano tutti il file web.config quando sono ospitati nel servizio app di Azure. Azure crea automaticamente il file durante distribuzione, anche se l'utente non lo visualizza. Se si include un file nell'applicazione, questo sostituisce quello generato automaticamente da Azure.

###.NET

Per le applicazioni .NET, modificare il file web.config per l'applicazione e aggiungere la sezione **&lt;rewrite>** dall'[esempio](#example) alla sezione **&lt;system.WebServer>**.

Se il file web.config include già una sezione **&lt;rewrite>**, aggiungere **&lt;rule>** dall'[esempio](#example) come prima voce nella sezione **&lt;rules>**.

###PHP

Per le applicazioni PHP, salvare semplicemente l'[esempio](#example) come file web.config nella radice dell'applicazione, quindi ridistribuire l'applicazione nell'app Web.

###Node.js, Python Django e Java

Un file web.config viene creato automaticamente per le app Node.js, Python Django e Java se non è già disponibile, ma esiste solo sul server perché viene creato durante la distribuzione. Il file generato automaticamente contiene le impostazioni che indicano ad Azure come ospitare l'applicazione.

Per recuperare e modificare il file autogenerato dall'app Web, usare i seguenti passaggi.

1. Scaricare il file tramite FTP (vedere la pagina relativa al [caricamento/download dei file tramite FTP e alla raccolta dei log di diagnostica](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)).

2. Aggiungerlo alla radice dell'applicazione.

3. Aggiungere le regole di riscrittura usando le seguenti informazioni.

	* **Node.js e Python Django**

		Il file web.config generato per le applicazioni Node.js e Python Django conterrà già una sezione **&lt;rewrite>**, con le voci **&lt;rule>** richieste per il funzionamento corretto del sito. Per forzare il sito a usare HTTPS, aggiungere **&lt;rule>** dall'esempio come prima voce nella sezione **&lt;rules>**. In questo modo viene forzato HTTPS, mentre le altre regole restano intatte.

	* **Java**
	
		Il file web.config per le applicazioni Java che usano Apache Tomcat non contiene una sezione **&lt;rewrite>**, per cui è necessario aggiungere la sezione **&lt;rewrite>** dell'esempio alla sezione **&lt;system.webServer>**.

4. Ridistribuire il progetto (incluso il file web.config aggiornato) in Azure

Dopo aver distribuito un file web.config con una regola di riscrittura per forzare HTTPS, la regola viene applicata immediatamente e tutte le richieste vengono reindirizzate a HTTPS.

Per altre informazioni sul modulo IIS Riscrittura URL, vedere la documentazione [Riscrittura URL](http://www.iis.net/downloads/microsoft/url-rewrite).

## Altre risorse ##
- [Centro protezione Microsoft Azure](/support/trust-center/security/)
- [Opzioni di configurazione sbloccate in Siti Web di Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Abilitare la registrazione diagnostica](../article/app-service-web/web-sites-enable-diagnostic-log.md)
- [Come configurare i siti Web](../article/app-service-web/web-sites-configure.md)
- [Portale di gestione di Azure](https://manage.windowsazure.com)

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una guida relativa al passaggio dal portale precedente al nuovo portale, vedere [Informazioni di riferimento per l'esplorazione del portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[customdomain]: ../articles/app-service-web/web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: /pricing/details/
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

<!---HONumber=Sept15_HO3-->