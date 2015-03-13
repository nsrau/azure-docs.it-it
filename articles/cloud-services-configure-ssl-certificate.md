<properties 
	pageTitle="Configurare SSL per un servizio cloud - Azure" 
	description="Informazioni su come specificare un endpoint HTTPS per un ruolo Web e come caricare un certificato SSL al fine di proteggere l'applicazione." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor="mollybos"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="adegeo"/>




# Configurazione di SSL per un'applicazione in Azure

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

La crittografia SSL (Secure Socket Layer) è il metodo più diffuso per proteggere i dati inviati tramite Internet. In questa attività comune viene illustrato come specificare un endpoint HTTPS per un ruolo Web e come caricare un certificato SSL al fine di proteggere l'applicazione.

> [AZURE.NOTE] Le procedure in questa attività si applicano a Servizi cloud di Azure; per Siti Web, vedere l'argomento relativo alla [configurazione di un certificato SSL per un sito Web di Azure](../articles/web-sites-configure-ssl-certificate/).

Questa attività include i seguenti passaggi:

-   [Passaggio 1: Ottenere un certificato SSL][]
-   [Passaggio 2: Modificare i file di definizione e configurazione del servizio][]
-   [Passaggio 3: Caricare il pacchetto di distribuzione e il certificato][]
-   [Passaggio 4: Connettersi all'istanza del ruolo usando HTTPS][]

In questa attività viene usata una distribuzione di produzione. In fondo a questo argomento vengono fornite informazioni sull'uso di una distribuzione di gestione temporanea.

<h2><a name="step1"> </a>Passaggio 1: Ottenere un certificato SSL</h2>

Per configurare SSL per un'applicazione, è necessario innanzitutto ottenere un certificato SSL che sia stato firmato da un'Autorità di certificazione (CA, Certificate Authority), ovvero un ente di terze parti attendibile che rilascia certificati per questo scopo. Se non se ne dispone già, è necessario ottenerne uno da un rivenditore di certificati SSL.

Il certificato deve soddisfare i seguenti requisiti per i certificati SSL in Azure:

-   Il certificato deve includere una chiave privata.
-   Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
-   Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al servizio cloud. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione per il dominio cloudapp.net. È necessario acquistare un nome di dominio personalizzato da usare per accedere al servizio. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per accedere all'applicazione. Se, ad esempio, il nome di dominio personalizzato è **contoso.com**, si richiede un certificato dalla CA per ***.contoso.com** o **www.contoso.com**.
-   Per il certificato deve essere usata una crittografia di almeno 2048 bit.

Per eseguire delle prove, è possibile creare e usare un certificato autofirmato. Un certificato autofirmato non è autenticato tramite una CA e può usare il dominio cloudapp.net come URL del sito Web. La seguente attività, ad esempio, usa un certificato autofirmato in cui il nome comune è **sslexample.cloudapp.net**. Per informazioni dettagliate sulla creazione di un certificato autofirmato mediante Gestione IIS, vedere l'argomento relativo allla [creazione di un certificato per un ruolo][].

A questo punto, è necessario includere le informazioni sul certificato nei file di definizione e configurazione del servizio.

<h2><a name="step2"> </a>Passaggio 2: Modificare i file di definizione e configurazione del servizio</h2>

L'applicazione deve essere configurata per usare il certificato ed è necessario aggiungere un endpoint HTTPS. Di conseguenza, è necessario aggiornare i file di definizione e configurazione del servizio.

1.  Nell'ambiente di sviluppo aprire il file di definizione del servizio
    (CSDEF), aggiungere una sezione **Certificates** nella sezione **WebRole**
    e includere le seguenti informazioni relative al
    certificato:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
							 storeLocation="LocalMachine" 
                    		 storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    Nella sezione **Certificates** sono definiti il nome del certificato, il relativo percorso e il nome dell'archivio in cui è situato. Si è scelto di archiviare il certificato nell'archivio della CA ma sono disponibili anche altre opzioni. Per altre informazioni, vedere [Come associare un certificato a un servizio][].

2.  Nel file di definizione del servizio aggiungere un elemento **InputEndpoint**
    nella sezione **Endpoints** per abilitare HTTPS:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  Nel file di definizione del servizio aggiungere un elemento **Binding** nella sezione
    **Sites**. Viene aggiunto un binding HTTPS per il mapping
    dell'endpoint al sito:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    Tutte le modifiche necessarie apportate al file di definizione del servizio sono state
    completate, tuttavia è ancora necessario aggiungere le informazioni del certificato al
    file di configurazione del servizio.

4.  Nel file di configurazione del servizio (CSCFG), ServiceConfiguration.Cloud.cscfg, aggiungere una sezione **Certificates**
    nella sezione **Role**, sostituendo il valore dell'identificazione personale di esempio
    indicato di seguito con quello del certificato:

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

Nell'esempio precedente viene usato **sha1** come algoritmo di identificazione personale. Specificare il valore appropriato per l'algoritmo di identificazione personale del certificato in uso.

Dopo aver caricato i file di definizione e configurazione e del servizio,
includere in un pacchetto la distribuzione per caricarla in Azure. Se
si sta usando **cspack**, assicurarsi di non usare il contrassegno
**/generateConfigurationFile** in quanto sovrascriverà le
informazioni del certificato appena inserite.

<h2><a name="step3"> </a>Passaggio 3: Caricare il pacchetto di distribuzione e il certificato</h2>

Il pacchetto di distribuzione è stato aggiornato per usare il certificato ed è stato
aggiunto un endpoint HTTPS. A questo punto, è possibile caricare il pacchetto e
il certificato in Azure mediante il portale di gestione.

1. Accedere al [portale di gestione di Azure][]. 
2. Fare clic su **Nuovo**, selezionare **Servizio cloud** quindi **Creazione personalizzata**.
3. Nella finestra di dialogo **Crea un servizio cloud** immettere i valori per URL, area/gruppo di affinità e sottoscrizione. Verificare che l'opzione **Distribuire un pacchetto di servizio cloud** sia selezionata e fare clic sul pulsante **Avanti**.
3. Nella finestra di dialogo **Pubblica il servizio cloud** immettere le informazioni relative al servizio cloud, selezionare **Produzione** per l'ambiente, quindi assicurarsi che l'opzione **Aggiungi certificati** sia selezionata. Se sono presenti ruoli contenenti una singola istanza, assicurarsi che l'opzione **Distribuire anche se uno o più ruoli contengono una singola istanza** sia selezionata. 

    ![Publish your cloud service][0]

4.  Fare clic sul pulsante **Avanti**.
5.  Nella finestra di dialogo **Aggiungi certificato** immettere il percorso del file con estensione pfx
    del certificato SSL, la password del certificato e fare clic su
    **collega certificato**.

    ![Add certificate][1]

6.  Verificare che il certificato sia elencato nella sezione **Certificati collegati**.

    ![Attached certificates][4]

7.  Fare clic sul pulsante **Completa** per creare il servizio cloud. Quando la distribuzione ha raggiunto lo stato **Pronto**, è possibile procedere con i passaggi successivi.

<h2><a name="step4"> </a>Passaggio 4: Connettersi all'istanza del ruolo usando HTTPS</h2>

Ora che la distribuzione è in esecuzione in Azure, è possibile
connettersi a questa con HTTPS.

1.  Nel portale di gestione selezionare la distribuzione, quindi fare clic sul collegamento in **URL sito**.

    ![Determine site URL][2]

2.  Nel Web browser modificare il collegamento per usare **https** invece di **http**, quindi accedere alla pagina.

    **Nota:** se si sta usando un certificato autofirmato,
    passando a un endpoint HTTPS associato al certificato,
    è possibile che nel browser venga visualizzato un errore del certificato. L'uso di un
    certificato firmato da un'Autorità di certificazione attendibile eliminerà il problema. Nel frattempo l'errore l'errore può essere ignorato. Un'altra opzione consiste nell'aggiungere il certificato autofirmato nell'archivio certificati dell'Autorità di certificazione attendibile dell'utente.

    ![SSL example web site][3]

Se si desidera usare SSL per una distribuzione di gestione temporanea anziché di produzione, è necessario innanzitutto determinare l'URL usato per la distribuzione di gestione temporanea. Distribuire il servizio cloud per l'ambiente di gestione temporanea senza includere un certificato né alcuna delle relative informazioni. Una volta distribuito il servizio, è possibile determinare l'URL basato su GUID, elencato nel campo **URL sito** del portale di gestione. Creare un certificato con il nome comune uguale all'URL basato su GUID (ad esempio **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**), usare il portale di gestione per aggiungere il certificato al servizio cloud preconfigurato, aggiungere le informazioni del certificato ai file CSDEF e CSCFG, ricreare il pacchetto dell'applicazione, quindi aggiornare la distribuzione di gestione temporanea per usare il nuovo pacchetto e il nuovo file CSCFG.

<h2><a name="additional_resources"> </a>Risorse aggiuntive</h2>

* [Come associare un certificato a un servizio][]

* [Come configurare un certificato SSL su un endpoint HTTPS][]

  [Passaggio 1: Ottenere un certificato SSL]: #step1
  [Passaggio 2: Modificare i file di definizione e configurazione del servizio]: #step2
  [Passaggio 3: Caricare il pacchetto di distribuzione e il certificato]: #step3
  [Passaggio 4: Connettersi all'istanza del ruolo usando HTTPS]: #step4
  [creazione di un certificato per un ruolo]: http://msdn.microsoft.com/library/windowsazure/gg432987.aspx
  [Come associare un certificato a un servizio]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-dotnet-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-dotnet-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-dotnet-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificateComplete.png  
  [Come configurare un certificato SSL su un endpoint HTTPS]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx

<!--HONumber=45--> 
