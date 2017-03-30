---
title: Proteggere il dominio personalizzato dell&quot;app tramite HTTPS | Microsoft Docs
description: "Informazioni su come proteggere il nome di dominio personalizzato per l&quot;app nel servizio app di Azure tramite la configurazione di un&quot;associazione di certificato SSL. Verrà anche illustrato come ottenere un certificato SSL con diversi strumenti."
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 613d7932-73aa-4318-867c-1ce1416224dc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: f7a2066f43219e8748b5c5356ff6c81535b7842a
ms.lasthandoff: 03/17/2017


---
# <a name="secure-your-apps-custom-domain-with-https"></a>Proteggere il dominio personalizzato dell'app tramite HTTPS
> [!div class="op_single_selector"]
> * [Acquistare un certificato SSL in Azure](web-sites-purchase-ssl-web-site.md)
> * [Usare un certificato SSL per un dominio personalizzato](web-sites-configure-ssl-certificate.md)
> 
> 

Questo articolo illustra come abilitare HTTP per un'app Web, un back-end dell'app per dispositivi mobili o un'app per le API che usa un nome di dominio personalizzato nel [servizio app di Azure](../app-service/app-service-value-prop-what-is.md) . Viene illustrata l'autenticazione solo server. Per l'autenticazione reciproca, inclusa l'autenticazione del client, vedere [Come configurare l'autenticazione reciproca TLS per il servizio app](app-service-web-configure-tls-mutual-auth.md).

Per proteggere un'app con un nome di dominio personalizzato tramite HTTPS, è necessario aggiungere un certificato per tale nome di dominio specifico. Per impostazione predefinita, Azure protegge il dominio con carattere jolly **\*.azurewebsites.net** con un singolo certificato SSL, di conseguenza i client possono già accedere all'app all'indirizzo **https://*&lt;nomeapp>*.azurewebsites.net**. Se tuttavia si vuole usare un dominio personalizzato, ad esempio**contoso.com**, **www.contoso.com**e**\*.contoso.com**, questo dominio non può essere protetto tramite il certificato predefinito. Inoltre, come tutti i [certificati con caratteri jolly](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), il certificato predefinito non è sicuro quanto un dominio personalizzato e un certificato per tale dominio personalizzato.   

> [!NOTE]
> È possibile ottenere assistenza dagli esperti di Azure in qualsiasi momento nei [forum di Azure](https://azure.microsoft.com/support/forums/). Per un supporto più personalizzato, passare a [Supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e fare clic su **Ottenere supporto**.
> 
> 

<a name="bkmk_domainname"></a>

## <a name="what-you-need"></a>Elementi necessari
Per proteggere il nome di dominio personalizzato tramite HTTPS, è necessario associare un certificato SSL personalizzato al dominio personalizzato in Azure. Prima di associare un certificato personalizzato, è necessario eseguire queste operazioni:

* **Configurare il dominio personalizzato** : il servizio app consente solo di aggiungere un certificato per un nome di dominio già configurato nell'app. Per le istruzioni, vedere [Eseguire il mapping di un nome di dominio personalizzato a un'app Azure](web-sites-custom-domain-name.md). 
* **Passare al piano Basic o a un piano superiore** : i piani di servizio app delle fasce più basse non supportano i certificati SSL personalizzati. Per le istruzioni, vedere [Aumentare le prestazioni di un'app in Azure](web-sites-scale.md). 
* **Ottenere un certificato SSL** : se non si ha un certificato, è necessario ottenerne uno da un' [autorità di certificazione](http://en.wikipedia.org/wiki/Certificate_authority) (CA) attendibile. Il certificato deve soddisfare tutti i requisiti seguenti:
  
  * È firmato da un'autorità di certificazione attendibile e non da server di autorità di certificazione privati.
  * Contiene una chiave privata.
  * È stato creato per lo scambio delle chiavi ed esportato in un file PFX.
  * Usa una crittografia minima a 2048 bit.
  * Il nome soggetto corrisponde al dominio personalizzato da proteggere. Per proteggere più domini con un certificato, è necessario usare un nome con caratteri jolly, ad esempio **\*.contoso.com**, oppure specificare i valori per subjectAltName.
  * È unito a tutti i **[certificati intermedi](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** usati dall'autorità di certificazione. In caso contrario, potrebbero verificarsi problemi di interoperabilità non riproducibili in alcuni client.
    
    > [!NOTE]
    > Il modo più facile per ottenere un certificato SSL che soddisfi tutti i requisiti consiste nell' [acquistarne uno direttamente nel portale di Azure](web-sites-purchase-ssl-web-site.md). Questo articolo illustra come eseguire questa operazione manualmente e quindi associare il certificato al dominio personalizzato nel servizio app di Azure.
    > 
    > **certificati di crittografia a curva ellittica (ECC)** con il servizio app di Azure, ma la creazione di questi certificati non rientra nell'ambito di questo articolo. Per informazioni sulla procedura per creare i certificati ECC, rivolgersi all'autorità di certificazione.
    > 
    > 

<a name="bkmk_getcert"></a>

## <a name="step-1-get-an-ssl-certificate"></a>Passaggio 1. Ottenere un certificato SSL
Poiché le autorità di certificazione forniscono vari tipi di certificati SSL a diversi prezzi, per iniziare è necessario decidere quale tipo di certificato SSL acquistare. Per proteggere un singolo nome di dominio, ad esempio**www.contoso.com**, è sufficiente un certificato di base. Se è necessario proteggere più nomi di dominio, ad esempio **contoso.com** *e* **www.contoso.com** 
*e* **mail.contoso.com**, è possibile ottenere un [certificato con carattere jolly](http://en.wikipedia.org/wiki/Wildcard_certificate) o un certificato con [Nome oggetto alternativo](http://en.wikipedia.org/wiki/SubjectAltName) (`subjectAltName`).

Una volta stabilito qual è il certificato SSL da acquistare, è necessario inviare una richiesta di firma di certificato a un'autorità di certificazione. Quando si riceve il certificato richiesto dall'autorità di certificazione, è necessario generare un file PFX dal certificato. È possibile eseguire questa procedura con lo strumento di propria scelta. Ecco le istruzioni per gli strumenti più comuni:

* [Procedura per Certreq.exe](#bkmk_certreq) : Certreq.exe è un'utilità di Windows per la creazione di richieste di certificato. 
  È inclusa in Windows a partire da Windows XP/Windows Server 2000.
* [Procedura per Gestione IIS](#bkmk_iismgr) : lo strumento ideale, se lo si conosce già.
* [Procedura per OpenSSL](#bkmk_openssl): [strumento open source e multipiattaforma](https://www.openssl.org). Usarlo per ottenere un certificato SSL da qualsiasi piattaforma.
* [Procedura per subjectAltName tramite OpenSSL](#bkmk_subjectaltname): procedura per ottenere i certificati `subjectAltName`.

Per testare la configurazione nel servizio app prima di acquistare un certificato, è possibile generare un [certificato autofirmato](https://en.wikipedia.org/wiki/Self-signed_certificate). Questa esercitazione descrive due modi per generarlo:

* [Certificato autofirmato, procedura per Certreq.exe](#bkmk_sscertreq)
* [Certificato autofirmato, procedura per OpenSSL](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>

### <a name="get-a-certificate-using-certreqexe"></a>Ottenere un certificato tramite Certreq.exe
1. Creare un file, ad esempio **myrequest.txt**, copiare il testo seguente al suo interno e salvarlo in una directory di lavoro. 
   Sostituire il segnaposto `<your-domain>` con il nome di dominio personalizzato dell'app.
   
        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; Required minimum is 2048
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = FALSE
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
   
        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication
   
    Per altre informazioni sulle opzioni nella richiesta di firma del certificato, oltre che sulle altre opzioni disponibili, vedere la [documentazione di riferimento relativa a Certreq](https://technet.microsoft.com/library/dn296456.aspx).
2. In un prompt dei comandi digitare `CD` per passare alla directory di lavoro ed eseguire il comando seguente per creare la richiesta di firma del certificato:
   
        certreq -new myrequest.txt myrequest.csr
   
    **myrequest.csr** è stato creato nella directory di lavoro corrente.
3. Inviare il file **myrequest.csr** a un'autorità di certificazione per ottenere un certificato SSL. Caricare il file o copiare il contenuto da un editor di testo in un modulo Web.
   
    Per un elenco di autorità di certificazione ritenute attendibili da Microsoft, vedere [Microsoft Trusted Root Certificate Program: Participants][cas] (Programma Microsoft Trusted Root Certificate: Partecipanti).
4. Una volta che l'autorità di certificazione ha risposto all'utente con un file di certificato (CER), salvarlo nella directory di lavoro. Eseguire quindi il comando seguente per completare la richiesta di firma di certificato in sospeso.
   
        certreq -accept -user <certificate-name>.cer
   
    Questo comando il certificato completato nell'archivio certificati Windows.
5. Se l'autorità di certificazione usa certificati intermedi, è possibile installarli prima di procedere. In genere questi certificati vengono forniti come download separato da un'autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione per Microsoft IIS.
   
    Dopo avere scaricato i certificati, fare clic con il pulsante destro del mouse su ognuno di essi in Esplora risorse e scegliere  **Installa certificato**. Accettare i valori predefiniti dell'**Importazione guidata certificati** e continuare a selezionare **Avanti** fino al completamento dell'importazione.
6. Per esportare il certificato SSL dall'archivio certificati, premere `Win`+`R` ed eseguire **certmgr.msc** per avviare il gestore di certificati. 
   Selezionare **Personale** > **Certificati**. Nella colonna **Rilasciato a** verrà visualizzata una voce con il nome di dominio personalizzato e l'autorità di certificazione usata per generare il certificato nella colonna **Rilasciato da**.
   
    ![Inserimento immagine di Gestione certificati][certmgr]
7. Fare clic con il pulsante destro del mouse sul certificato e scegliere **Tutte le attività** > **Esporta**. In **Esportazione guidata certificati** fare clic su **Avanti**, quindi selezionare **Sì, Esporta la chiave privata** e fare di nuovo clic su **Avanti**.
   
    ![Esportazione della chiave privata][certwiz1]
8. Selezionare **Scambio informazioni personali - PKCS #12**, **Se possibile, includi tutti i certificati nel percorso certificazione** ed **Esporta tutte le proprietà estese**. Quindi fare clic su **Next**.
   
   ![Inclusione di tutti i certificati e delle proprietà estese][certwiz2]
9. Selezionare **Password**, quindi immettere e confermare la password. Fare clic su **Avanti**.
   
   ![Inserimento di una password][certwiz3]
10. Per il file esportato, specificare un percorso e nome file con l'estensione **pfx**. Fare clic su **Avanti** per completare la procedura.
    
    ![Inserimento di un percorso per il file][certwiz4]

A questo punto si è pronti per caricare il file PFX esportato nel servizio app. Vedere [Passaggio 2. Caricare e associare il certificato SSL personalizzato](#bkmk_configuressl).

<a name="bkmk_iismgr"></a>

### <a name="get-a-certificate-using-the-iis-manager"></a>Ottenere un certificato tramite Gestione IIS
1. In Gestione IIS generare una richiesta di firma del certificato da inviare all'autorità di certificazione. Per altre informazioni sulla generazione di una richiesta di firma del certificato, vedere [Richiedere un certificato del server Internet (IIS 7)][iiscsr].
2. Inviare la richiesta di firma del certificato a un'autorità di certificazione per ottenere un certificato SSL. Per un elenco di autorità di certificazione ritenute attendibili da Microsoft, vedere [Microsoft Trusted Root Certificate Program: Participants][cas] (Programma Microsoft Trusted Root Certificate: Partecipanti).
3. Completare la richiesta di firma del certificato con il certificato inviato dall'autorità di certificazione. Per altre informazioni sul completamento della richiesta di firma del certificato, vedere [Installare un certificato del server Internet (IIS 7)][installcertiis].
4. Se l'autorità di certificazione usa certificati intermedi, è possibile installarli prima di procedere. In genere questi certificati vengono forniti come download separato da un'autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione per Microsoft IIS.
   
    Dopo avere scaricato i certificati, fare clic con il pulsante destro del mouse su ognuno di essi in Esplora risorse e scegliere **Installa certificato**. 
    Accettare i valori predefiniti dell'**Importazione guidata certificati** e continuare a selezionare **Avanti** fino al completamento dell'importazione.
5. Esportare il certificato SSL da Gestione IIS. Per altre informazioni sull'esportazione del certificato, vedere [Esportare un certificato del server (IIS 7)][exportcertiis]. 
   
   > [!IMPORTANT]
   > In **Esportazione guidata certificati** assicurarsi di selezionare **Sì, esporta la chiave privata**  
   > 
   > ![Esportazione della chiave privata][certwiz1]  
   > 
   > e selezionare anche **Scambio informazioni personali - PKCS #12**, **Se possibile, includi tutti i certificati nel percorso certificazione** ed **Esporta tutte le proprietà estese**.
   > 
   > ![Inclusione di tutti i certificati e delle proprietà estese][certwiz2]
   > 
   > 

A questo punto si è pronti per caricare il file PFX esportato nel servizio app. Vedere [Passaggio 2. Caricare e associare il certificato SSL personalizzato](#bkmk_configuressl).

<a name="bkmk_openssl"></a>

### <a name="get-a-certificate-using-openssl"></a>Ottenere un certificato tramite OpenSSL
1. In un terminale della riga di comando passare a una directory di lavoro con il comando `CD` e generare una chiave privata e una richiesta di firma del certificato con il comando seguente:
   
        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048
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
   
    Al termine, nella directory di lavoro saranno presenti due file: **myserver.key** e **server.csr**. 
    Il file **server.csr** contiene la richiesta di firma del certificato, mentre il file **myserver.key** sarà necessario più avanti.
3. Inviare la richiesta di firma del certificato a un'autorità di certificazione per ottenere un certificato SSL. Per un elenco di autorità di certificazione ritenute attendibili da Microsoft, vedere [Microsoft Trusted Root Certificate Program: Participants][cas] (Programma Microsoft Trusted Root Certificate: Partecipanti).
4. Una volta che l'autorità di certificazione invia il certificato richiesto, salvarlo in un file denominato **myserver.crt** nella directory di lavoro. Se l'autorità di certificazione fornisce il file in formato testo, è sufficiente copiare il contenuto in **myserver.crt** in un editor di testo e salvarlo. Il file avrà un aspetto simile al seguente:
   
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
5. Nel terminale della riga di comando eseguire il comando seguente per esportare **myserver.pfx** da **myserver.key** e **myserver.crt**:
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    Quando richiesto, specificare una password per proteggere il file con estensione pfx.
   
   > [!NOTE]
   > Se l'autorità di certificazione usa certificati intermedi, è necessario includerli con il parametro `-certfile`. In genere questi certificati vengono forniti come download separato da un'autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione con l'estensione `.pem` .
   > 
   > L'aspetto del comando `openssl -export` deve essere simile a quello dell'esempio seguente, che crea un file con estensione pfx che include i certificati intermedi dal file **intermediate-cets.pem** :
   > 
   > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`
   > 
   > 

A questo punto si è pronti per caricare il file PFX esportato nel servizio app. Vedere [Passaggio 2. Caricare e associare il certificato SSL personalizzato](#bkmk_configuressl).

<a name="bkmk_subjectaltname"></a>

### <a name="get-a-subjectaltname-certificate-using-openssl"></a>Ottenere un certificato SubjectAltName tramite OpenSSL
1. Creare un file denominato **sancert.cnf**, copiare il testo seguente al suo interno e salvarlo in una directory di lavoro:
   
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
   
    Nella riga che inizia con `subjectAltName`, sostituire il valore con tutti i nomi di dominio da proteggere (oltre a  `commonName`). Ad esempio:
   
        subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com
   
    Gli altri campi non devono essere modificati, incluso `commonName`. Verrà richiesto di specificarli nei passaggi successivi.
2. In un terminale della riga di comando digitare `CD` per passare alla directory di lavoro ed eseguire il comando seguente:
   
        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf
3. Quando richiesto, immettere le informazioni appropriate, Ad esempio:
   
         Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
   
    Al termine, nella directory di lavoro saranno presenti due file: **myserver.key** e **server.csr**. 
    Il file **server.csr** contiene la richiesta di firma del certificato, mentre il file **myserver.key** sarà necessario più avanti.
4. Inviare la richiesta di firma del certificato a un'autorità di certificazione per ottenere un certificato SSL. Per un elenco di autorità di certificazione ritenute attendibili da Microsoft, vedere [Microsoft Trusted Root Certificate Program: Participants][cas] (Programma Microsoft Trusted Root Certificate: Partecipanti).
5. Una volta che l'autorità di certificazione invia il certificato richiesto, salvarlo in un file denominato **myserver.crt**. Se l'autorità di certificazione fornisce il file in formato testo, è sufficiente copiare il contenuto in **myserver.crt** in un editor di testo e salvarlo. Il file dovrebbe avere un aspetto simile al seguente:
   
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
6. Nel terminale della riga di comando eseguire il comando seguente per esportare **myserver.pfx** da **myserver.key** e **myserver.crt**:
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    Quando richiesto, specificare una password per proteggere il file con estensione pfx.
   
   > [!NOTE]
   > Se l'autorità di certificazione usa certificati intermedi, è necessario includerli con il parametro `-certfile`. In genere questi certificati vengono forniti come download separato da un'autorità di certificazione e sono disponibili in vari formati per diversi tipi di server Web. Selezionare la versione con l'estensione `.pem` .
   > 
   > L'aspetto del comando `openssl -export` deve essere simile a quello dell'esempio seguente, che crea un file con estensione pfx che include i certificati intermedi dal file **intermediate-cets.pem** :
   > 
   > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`
   > 
   > 

A questo punto si è pronti per caricare il file PFX esportato nel servizio app. Vedere [Passaggio 2. Caricare e associare il certificato SSL personalizzato](#bkmk_configuressl).

<a name="bkmk_sscertreq"></a>

### <a name="generate-a-self-signed-certificate-using-certreqexe"></a>Generare un certificato autofirmato tramite Certreq.exe
> [!IMPORTANT]
> I certificati autofirmati sono da usare solo a scopo di test. La maggior parte dei browser restituisce errori quando si visita un sito Web protetto tramite un certificato autofirmato. È anche possibile che alcuni browser non consentano la visualizzazione del sito. 
> 
> 

1. Creare un file, ad esempio **mycert.txt**, copiare il testo seguente al suo interno e salvarlo in una directory di lavoro. 
   Sostituire il segnaposto `<your-domain>` con il nome di dominio personalizzato dell'app.
   
        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
        RequestType = Cert            ; Self-signed certificate
        ValidityPeriod = Years
        ValidityPeriodUnits = 1
   
        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication
   
    Il parametro importante è `RequestType = Cert`, che specifica un certificato autofirmato. 
    Per altre informazioni sulle opzioni nella richiesta di firma del certificato, oltre che sulle altre opzioni disponibili, vedere la [documentazione di riferimento relativa a Certreq](https://technet.microsoft.com/library/dn296456.aspx).
2. In un prompt dei comandi digitare `CD` per passare alla directory di lavoro ed eseguire il comando seguente:
   
        certreq -new mycert.txt mycert.crt
   
    Il nuovo certificato autofirmato è ora installato nell'archivio certificati.
3. Per esportare il certificato dall'archivio certificati, premere `Win`+`R` ed eseguire **certmgr.msc** per avviare il gestore di certificati. 
   Selezionare **Personale** > **Certificati**. Nella colonna **Rilasciato a** verrà visualizzata una voce con il nome di dominio personalizzato e l'autorità di certificazione usata per generare il certificato nella colonna **Rilasciato da**.
   
    ![Inserimento immagine di Gestione certificati][certmgr]
4. Fare clic con il pulsante destro del mouse sul certificato e scegliere **Tutte le attività** > **Esporta**. In **Esportazione guidata certificati** fare clic su **Avanti**, quindi selezionare **Sì, Esporta la chiave privata** e fare di nuovo clic su **Avanti**.
   
    ![Esportazione della chiave privata][certwiz1]
5. Selezionare **Scambio informazioni personali - PKCS #12**, **Se possibile, includi tutti i certificati nel percorso certificazione** ed **Esporta tutte le proprietà estese**. Quindi fare clic su **Next**.
   
   ![Inclusione di tutti i certificati e delle proprietà estese][certwiz2]
6. Selezionare **Password**, quindi immettere e confermare la password. Fare clic su **Avanti**.
   
   ![Inserimento di una password][certwiz3]
7. Per il file esportato, specificare un percorso e nome file con l'estensione **pfx**. Fare clic su **Avanti** per completare la procedura.
   
   ![Inserimento di un percorso per il file][certwiz4]

A questo punto si è pronti per caricare il file PFX esportato nel servizio app. Vedere [Passaggio 2. Caricare e associare il certificato SSL personalizzato](#bkmk_configuressl).

<a name="bkmk_ssopenssl"></a>

### <a name="generate-a-self-signed-certificate-using-openssl"></a>Generare un certificato autofirmato tramite OpenSSL
> [!IMPORTANT]
> I certificati autofirmati sono da usare solo a scopo di test. La maggior parte dei browser restituisce errori quando si visita un sito Web protetto tramite un certificato autofirmato. È anche possibile che alcuni browser non consentano la visualizzazione del sito. 
> 
> 

1. Creare un file di testo denominato **serverauth.cnf**, copiare il contenuto seguente al suo interno e quindi salvarlo in una directory di lavoro:
   
        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca
   
        [ req_distinguished_name ]
        countryName            = Country Name (2 letter code)
        countryName_min            = 2
        countryName_max            = 2
        stateOrProvinceName        = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName        = Organization Name (eg, company)
        organizationalUnitName        = Organizational Unit Name (eg, section)
        commonName            = Common Name (eg, your app's domain name)
        commonName_max            = 64
        emailAddress            = Email Address
        emailAddress_max        = 40
   
        [ req_attributes ]
        challengePassword        = A challenge password
        challengePassword_min        = 4
        challengePassword_max        = 20
   
        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth
2. In un terminale della riga di comando digitare `CD` per passare alla directory di lavoro ed eseguire il comando seguente:
   
        openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf
   
    Questo comando crea due file: **myserver.crt**, il certificato autofirmato, e **myserver.key**, la chiave privata, in base alle impostazioni del file **serverauth.cnf**.
3. Eseguire questo comando per esportare il certificato in un file con estensione pfx:
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    Quando richiesto, specificare una password per proteggere il file con estensione pfx.

A questo punto si è pronti per caricare il file PFX esportato nel servizio app. Vedere [Passaggio 2. Caricare e associare il certificato SSL personalizzato](#bkmk_configuressl).

<a name="bkmk_configuressl"></a>

## <a name="step-2-upload-and-bind-the-custom-ssl-certificate"></a>Passaggio 2. Caricare e associare il certificato SSL personalizzato
Prima di procedere, esaminare la sezione [Elementi necessari](#bkmk_domainname) sezione e verificare quando segue:

* è disponibile un dominio personalizzato che esegue il mapping all'app Azure,
* l'app è in esecuzione nel piano **Basic** o superiore ed
* è disponibile un certificato SSL per il dominio personalizzato da un'autorità di certificazione.

1. Accedere al **Portale di Azure[ dal browser.](https://portal.azure.com/)**
2. Fare clic sull'opzione **Servizio app** a sinistra nella pagina.
3. Fare clic sul nome dell'app a cui si desidera assegnare il certificato. 
4. In **Impostazioni** fare clic su **Certificati SSL**
5. Fare clic su **Carica certificato**
6. Selezionare il file PFX esportato nel [Passaggio 1](#bkmk_getcert) e specificare la password creata in precedenza. 
   Fare quindi clic su **Carica** per caricare il certificato. A questo punto, il certificato caricato dovrebbe essere visualizzato nuovamente nel pannello **Certificato SSL** .
7. Nella sezione **Associazioni SSL** fare clic su **Add bindings** (Aggiungi associazioni)
8. Nel pannello **Aggiungi associazione SSL** usare gli elenchi a discesa per selezionare il nome di dominio da proteggere con SSL e il certificato da usare. È possibile anche stabilire se usare il metodo SSL basato su **[Indicazione nome server (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** o su IP.
   
    ![inserimento immagine di associazioni SSL](./media/web-sites-configure-ssl-certificate/sslbindings.png)
   
    > [!NOTE] 
    > Il metodo **SSL basato su IP** associa un certificato a un nome di dominio tramite il mapping dell'indirizzo IP pubblico dedicato del server al nome di dominio. A tale scopo, è necessario che ogni nome di dominio (contoso.com, fabricam.com e così via) associato al servizio disponga di un indirizzo IP dedicato. Questo è il metodo tradizionale per l'associazione di certificati SSL a un server Web.  
    >
    > Il metodo**SSL basato su SNI** è un'estensione di SSL e **[Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS), che consente a più domini di condividere lo stesso indirizzo IP, con certificati di protezione distinti per ogni dominio. La maggior parte dei browser moderni, inclusi Internet Explorer, Chrome, Firefox e Opera, supporta SNI, ma è possibile che non sia supportato dai browser precedenti. Per altre informazioni su SNI, vedere l'articolo relativo all'**[Indicazione nome server](http://en.wikipedia.org/wiki/Server_Name_Indication)** su Wikipedia.
    > 

9. Fare clic su **Aggiungi l'associazione** per salvare le modifiche e abilitare SSL.

## <a name="step-3-change-your-domain-name-mapping-ip-based-ssl-only"></a>Passaggio 3. Modificare il mapping del nome di dominio (solo per il metodo SSL basato su IP)
Se si usano solo associazioni di tipo **SNI SSL** , ignorare questa sezione. Più associazioni **SNI SSL** possono operare sull'indirizzo IP condiviso esistente assegnato all'app. Se, tuttavia, si crea un'associazione di tipo **SSL basato su IP**, il servizio app crea un indirizzo IP dedicato per l'associazione perché il metodo **SSL basato su IP** ne richiede uno. È possibile creare un solo indirizzo IP dedicato, pertanto può essere aggiunta una sola associazione di tipo **SSL basato su IP** .

Questo indirizzo IP dedicato richiede ulteriori configurazioni dell'app, nei casi seguenti:

* È stato [usato un record A per il mapping del dominio personalizzato](web-sites-custom-domain-name.md#a) all'app Azure e si è appena aggiunta un'associazione di tipo **SSL basato su IP**. In questo scenario, è necessario modificare il mapping esistente del record A in modo che punti all'indirizzo IP dedicato. A tale scopo, seguire questa procedura:
  
  1. Dopo la configurazione di un'associazione SSL basata su IP, un indirizzo IP dedicato viene assegnato all'app. È possibile trovare questo indirizzo IP nella pagina **Dominio personalizzato** nelle impostazioni dell'app, proprio sotto la sezione **Nomi host**. Sarà elencato come **Indirizzo IP esterno**
     
      ![Indirizzo IP virtuale](./media/web-sites-configure-ssl-certificate/virtual-ip-address.png)
  2. [Eseguire di nuovo il mapping del record A per il nome di dominio personalizzato al nuovo indirizzo IP](web-sites-custom-domain-name.md#a).
* Sono già presenti una o più associazioni **SNI SSL** nell'app ed è appena stata aggiunta un'associazione di tipo **SSL basato su IP**. 
  Al termine dell'associazione, il nome di dominio *&lt;nomeapp>*.azurewebsites.net punterà al nuovo indirizzo IP. 
  Di conseguenza, qualsiasi [mapping CNAME dal dominio personalizzato](web-sites-custom-domain-name.md#cname) a *&lt;nomeapp >*.azurewebsites.net, inclusi quelli protetti tramite **SNI SSL**, riceve anche il traffico sul nuovo indirizzo, che viene creato solo per l'associazione **SSL basato su IP**. In questo scenario, per inviare il traffico **SNI SSL** all'indirizzo IP condiviso originale è necessario seguire questa procedura:
  
  1. Identificare tutti i [mapping CNAME dei domini personalizzati](web-sites-custom-domain-name.md#cname) all'app che ha un'associazione **SNI SSL** .
  2. Eseguire nuovamente il mapping di ogni record CNAME a **sni.**&lt;nomeapp>.azurewebsites.net anziché a &lt;nomeapp>.azurewebsites.net.

## <a name="step-4-test-https-for-your-custom-domain"></a>Passaggio 4. Testare HTTPS per il dominio personalizzato
A questo punto, non resta che assicurarsi che HTTPS funzioni correttamente per il dominio personalizzato. In diversi browser passare a `https://<your.custom.domain>` per verificare che l'app sia gestita.

* Se l'app restituisce errori di convalida del certificato, è probabile che sia in uso un certificato autofirmato.
* In caso contrario, è possibile che siano stati esclusi i certificati intermedi durante l'esportazione del certificato in formato PFX. Tornare a [Elementi necessari](#bkmk_domainname) per verificare che la richiesta di firma del certificato soddisfi tutti i requisiti del servizio app.

<a name="bkmk_enforce"></a>

## <a name="enforce-https-on-your-app"></a>Imporre HTTPS per l'app
Se si vuole comunque consentire l'accesso HTTP all'app, ignorare questo passaggio. Il servizio app *non* impone HTTPS, di conseguenza i visitatori possono comunque accedere all'app usando HTTP. Per imporre HTTPS per l'app, è possibile definire una regola di riscrittura nel file `web.config` dell'app. Questo file è disponibile per ogni app del servizio app, indipendentemente dal framework del linguaggio dell'app.

> [!NOTE]
> Il reindirizzamento delle richieste è specifico per ogni linguaggio. Per ASP.NET MVC è possibile usare il filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) anziché la regola di riscrittura in `web.config`. Per altre informazioni, vedere [Distribuire un'app ASP.NET MVC 5 sicura in un'app Web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
> 
> 

A tale scopo, seguire questa procedura:

1. Passare alla console di debug Kudu per l'app. L'indirizzo è `https://<appname>.scm.azurewebsites.net/DebugConsole`.
2. Nella console di debug digitare CD per passare a `D:\home\site\wwwroot`.
3. Fare clic sul pulsante a forma di matita per aprire `web.config` .
   
    ![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)
   
    Se si distribuisce l'app con Visual Studio o Git, il servizio app genera automaticamente il file `web.config` appropriato per l'app .NET, PHP, Node.js o Python nella radice dell'applicazione. 
    Se `web.config` non esiste, eseguire `touch web.config` nel prompt dei comandi basato sul Web per crearlo. In alternativa, è possibile crearlo nel progetto locale e ridistribuire il codice.
4. Se è stato necessario creare un file `web.config`, copiare il codice seguente al suo interno e salvarlo. Se è stato aperto un file web.config esistente, è sufficiente copiare l'intero tag `<rule>` nell'elemento `configuration/system.webServer/rewrite/rules` del file `web.config`.
   
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
          <system.webServer>
            <rewrite>
              <rules>
                <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
                <rule name="Force HTTPS" enabled="true">
                  <match url="(.*)" ignoreCase="false" />
                  <conditions>
                    <add input="{HTTPS}" pattern="off" />
                  </conditions>
                  <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
                </rule>
                <!-- END rule TAG FOR HTTPS REDIRECT -->
              </rules>
            </rewrite>
          </system.webServer>
        </configuration>
   
    Questa regola restituisce un errore di tipo HTTP 301 (reindirizzamento permanente) al protocollo HTTPS ogni volta che l'utente richiede una pagina tramite HTTP. Esegue il reindirizzamento da http://contoso.com a https://contoso.com.
   
   > [!IMPORTANT]
   > Se esistono già altri tag `<rule>` nel file `web.config`, posizionare il tag `<rule>` copiato prima degli altri tag `<rule>`.
   > 
   > 
5. Salvare il file nella console di debug Kudu. La modifica sarà effettiva da subito e tutte le richieste verranno reindirizzate ad HTTPS.

Per altre informazioni sul modulo IIS Riscrittura URL, vedere la documentazione [Riscrittura URL](http://www.iis.net/downloads/microsoft/url-rewrite) .

## <a name="more-resources"></a>Altre risorse
* [Centro protezione Microsoft Azure](/support/trust-center/security/)
* [Opzioni di configurazione sbloccate in Siti Web di Azure](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Abilitare la registrazione diagnostica](web-sites-enable-diagnostic-log.md)
* [Configurare app Web nel servizio app di Azure](web-sites-configure.md)
* [Portale di gestione di Azure](https://manage.windowsazure.com)

> [!NOTE]
> Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](https://azure.microsoft.com/try/app-service/), dove è possibile creare un'app iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png



