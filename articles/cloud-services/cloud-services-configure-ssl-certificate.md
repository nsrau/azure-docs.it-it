<properties 
	pageTitle="Configurare SSL per un servizio cloud - Azure" 
	description="Informazioni su come specificare un endpoint HTTPS per un ruolo Web e come caricare un certificato SSL al fine di proteggere l'applicazione." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/13/2015" 
	ms.author="adegeo"/>




# Configurazione di SSL per un'applicazione in Azure

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

La crittografia SSL (Secure Socket Layer) è il metodo più diffuso per proteggere i dati inviati tramite Internet. In questa attività comune viene illustrato come specificare un endpoint HTTPS per un ruolo Web e come caricare un certificato SSL al fine di proteggere l'applicazione.

> [AZURE.NOTE]Le procedure descritte in questa attività si applicano a Servizi cloud di Azure. Per Siti Web, vedere [Configurazione di un certificato SSL per un sito Web di Azure](../web-sites-configure-ssl-certificate.md).

In questa attività viene utilizzata una distribuzione di produzione. In fondo a questo argomento vengono fornite informazioni sull'utilizzo di una distribuzione di gestione temporanea.

## Passaggio 1: Ottenere un certificato SSL

Per configurare SSL per un'applicazione, è necessario innanzitutto ottenere un certificato SSL che sia stato firmato da un'Autorità di certificazione (CA), ovvero un ente di terze parti attendibile che rilascia certificati per questo scopo. Se non se ne dispone già, è necessario ottenerne uno da un rivenditore di certificati SSL.

Il certificato deve soddisfare i requisiti seguenti per i certificati SSL in Azure:

-   Il certificato deve includere una chiave privata.
-   Il certificato deve essere stato creato per lo scambio di chiave, esportabile in un file con estensione pfx (Personal Information Exchange).
-   Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere al servizio cloud. Non è possibile ottenere un certificato SSL da un'Autorità di certificazione (CA) per il dominio cloudapp.net. È necessario acquistare un nome di dominio personalizzato da utilizzare per accedere al servizio. Quando si richiede un certificato da una CA, il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per accedere all'applicazione. Se, ad esempio, il nome di dominio personalizzato è **contoso.com**, si richiede un certificato dalla CA per ***.contoso.com** o **www.contoso.com**.
-   Per il certificato deve essere usata una crittografia di almeno 2048 bit.

Per eseguire delle prove, è possibile creare e utilizzare un certificato autofirmato. Un certificato autofirmato non è autenticato tramite una CA e può usare il dominio cloudapp.net come URL del sito Web. Nell'attività seguente, ad esempio, viene usato un certificato autofirmato in cui il nome comune è **sslexample.cloudapp.net**. Per informazioni dettagliate sulla creazione di un certificato autofirmato con Gestione IIS, vedere [Come creare un certificato per un ruolo][].

A questo punto, è necessario includere le informazioni sul certificato nei file di definizione e configurazione del servizio.

## Passaggio 2: Modificare i file di definizione e configurazione del servizio

L'applicazione deve essere configurata per utilizzare il certificato ed è necessario aggiungere un endpoint HTTPS. Di conseguenza, è necessario aggiornare i file di definizione e configurazione del servizio.

1.  Nell'ambiente di sviluppo, aprire il file di definizione del servizio (CSDEF), aggiungere una sezione **Certificates** all'interno della sezione **WebRole** e includere le seguenti informazioni relative al certificato:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
							 storeLocation="LocalMachine" 
                    		 storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    Nella sezione**Certificates** è definito il nome del certificato, il relativo percorso e il nome dell'archivio in cui è situato. Si è scelto di archiviare il certificato nell'archivio della CA (Autorità di certificazione) ma sono disponibili anche altre opzioni. Per altre informazioni, vedere [Come associare un certificato a un servizio][].

2.  Nel file di definizione del servizio aggiungere un elemento **InputEndpoint** all'interno della sezione **Endpoints** per abilitare HTTPS:

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  Nel file di definizione del servizio aggiungere un elemento **Binding** all'interno della sezione **Sites**. Viene aggiunto un binding HTTPS per il mapping dell'endpoint al sito:

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

    Tutte le modifiche necessarie al file di definizione del servizio sono state completate ma è ora necessario aggiungere le informazioni del certificato al file di configurazione del servizio.

4.  Nel file di configurazione del servizio (CSCFG), ServiceConfiguration.Cloud.cscfg, aggiungere una sezione **Certificates** all'interno della sezione **Role**, sostituendo il valore di identificazione personale illustrato di seguito con quello del certificato:

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

Ora che i file di definizione e configurazione del servizio sono stati aggiornati, creare il pacchetto della distribuzione per il caricamento in Azure. Se si utilizza **cspack**, assicurarsi di non utilizzare il flag **/generateConfigurationFile**, poiché questo sovrascriverebbe le informazioni del certificato appena inserite.

## Passaggio 3: Caricare il pacchetto di distribuzione e il certificato

Il pacchetto di distribuzione è stato aggiornato per utilizzare il certificato ed è stato aggiunto un endpoint HTTPS. Ora è possibile caricare il pacchetto e il certificato in Azure tramite il portale di gestione.

1. Accedere al [portale di gestione di Azure][]. 
2. Fare clic su **New**, quindi su **Cloud Service** e infine su **Custom Create**.
3. Nella finestra di dialogo **Create a cloud service** immettere i valori per URL, area/gruppo di affinità e sottoscrizione. Assicurarsi che l'opzione **Deploy a cloud service package now** sia selezionata, quindi scegliere **Next**.
3. Nella finestra di dialogo **Publish your cloud service** immettere le informazioni relative al servizio cloud, selezionare **Production** per l'ambiente, quindi assicurarsi che l'opzione **Add certificates now** sia selezionata. Se sono presenti ruoli contenenti una singola istanza, assicurarsi che l'opzione **Deploy even if one or more roles contain a single instance** sia selezionata. 

    ![Pubblicare il servizio cloud][0]

4.  Fare clic sue **Next**.
5.  Nella finestra di dialogo **Add Certificate** immettere il percorso del file PFX del certificato SSL, la password per il certificato, quindi fare clic su **attach certificate**.  

    ![Add certificate][1]

6.  Assicurarsi che il certificato sia elencato nella sezione **Attached Certificates**.

    ![Attached certificates][4]

7.  Fare clic su **Complete** per creare il servizio cloud. Quando la distribuzione ha raggiunto lo stato **Ready**, è possibile procedere con i passaggi successivi.

## Passaggio 4: Connettersi all'istanza del ruolo usando HTTPS

Ora che la distribuzione è in esecuzione in Azure, è possibile connettersi a questa usando HTTPS.

1.  Nel portale di gestione selezionare la distribuzione, quindi fare clic sul collegamento in **Site URL**.

    ![Determinare l'URL del sito][2]

2.  Nel Web browser modificare il collegamento per utilizzare **https** invece di **http**, quindi accedere alla pagina.

    **Nota**: se si usa un certificato autofirmato, quando si passa a un endpoint HTTPS con il certificato autofirmato associato, nel browser verrà visualizzato un errore del certificato. L'utilizzo di un certificato firmato da un'Autorità di certificazione attendibile eliminerà il problema. Nel frattempo l'errore può essere ignorato. Un'altra opzione consiste nell'aggiungere il certificato autofirmato nell'archivio certificati dell'Autorità di certificazione attendibile dell'utente.

    ![Sito Web SSL di esempio][3]

Se si desidera utilizzare SSL per una distribuzione di gestione temporanea anziché di produzione, è necessario innanzitutto determinare l'URL usato per la distribuzione di gestione temporanea. Distribuire il servizio cloud per l'ambiente di gestione temporanea senza includere un certificato né alcuna delle relative informazioni. Una volta distribuito il servizio, è possibile determinare l'URL basato su GUID, che viene visualizzato nel campo **Site URL** del portale di gestione. Creare un certificato con il nome comune uguale all'URL basato su GUID (ad esempio **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**), utilizzare il portale di gestione per aggiungere il certificato al servizio cloud preconfigurato, aggiungere le informazioni del certificato ai file CSDEF e CSCFG, ricreare il pacchetto dell'applicazione, quindi aggiornare la distribuzione di gestione temporanea per utilizzare il nuovo pacchetto e il nuovo file CSCFG.

## Risorse aggiuntive

* [Come associare un certificato a un servizio][]

* [Come configurare un certificato SSL su un endpoint HTTPS][]

[Come creare un certificato per un ruolo]: http://msdn.microsoft.com/library/azure/gg432987.aspx
[Come associare un certificato a un servizio]: http://msdn.microsoft.com/library/azure/gg465718.aspx
[portale di gestione di Azure]: http://manage.windowsazure.com
[0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
[1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
[2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
[3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
[4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png
[Come configurare un certificato SSL su un endpoint HTTPS]: http://msdn.microsoft.com/library/azure/ff795779.aspx

<!--HONumber=54--> 