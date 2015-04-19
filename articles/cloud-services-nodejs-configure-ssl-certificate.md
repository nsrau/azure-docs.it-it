<properties 
	pageTitle="Configurare SSL per un servizio cloud (Node.js) - Azure" 
	description="Informazioni su come specificare un endpoint HTTPS per un ruolo Web Node.js e come caricare un certificato SSL al fine di proteggere l'applicazione." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>




# Configurazione di SSL per un'applicazione Node.js in un ruolo Web di Azure

La crittografia Secure Socket Layer (SSL) è il metodo più diffuso per
proteggere i dati inviati tramite Internet. Questa attività comune illustra come
specificare un endpoint HTTPS per un'applicazione Node.js ospitata come Servizio Cloud di Azure in un ruolo di Web e come caricare un
certificato SSL per proteggere l'applicazione.

> [AZURE.NOTE] Le procedure descritte in questo articolo si applicano solo alle applicazioni nodo ospitate come Servizio cloud di Azure in un ruolo Web. Per informazioni su Siti Web, vedere l'argomento relativo alla [configurazione di un certificato SSL per un sito Web di Azure](web-sites-configure-ssl-certificate.md).

Questa attività include i seguenti passaggi:

-   [Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud]
-   [Passaggio 2: Ottenere un certificato SSL]
-   [Passaggio 3: Importare il certificato SSL]
-   [Passaggio 4: Modificare i file di definizione e configurazione del servizio]
-   [Passaggio 5: Connettersi all'istanza del ruolo usando HTTPS]

## <a name="step1"> </a>Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud

Quando un'applicazione Node.js viene distribuita in un ruolo web di Azure, il
certificato del server e la connessione SSL vengono gestiti da Internet
Information Services (IIS), in modo che sia possibile scrivere il servizio Node.js
come se fosse un servizio http. È possibile creare un semplice servizio Node.js  'hello
world' usando Azure PowerShell seguendo questa procedura:

1. Nel menu **Start** o nella schermata **Start** cercare **Azure PowerShell**. Fare infine clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

	![Azure PowerShell icon][powershell-menu]

[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Creare un nuovo progetto di servizio usando il cmdlet **New-AzureServiceProject**. 

	![][1]

3.  Aggiungere un ruolo Web al servizio usando il cmdlet **Add-AzureNodeWebRole**:

    ![][2]

4.  Pubblicare il servizio nel cloud usando il cmdlet **Publish-AzureServiceProject**:

    ![][3]

	> [AZURE.NOTE] Se le impostazioni per la sottoscrizione Azure non sono state importate in precedenza, verrà visualizzato un errore quando si tenta di eseguire la pubblicazione. Per informazioni sul download e sull'importazione delle impostazioni di pubblicazione per la sottoscrizione, vedere l'argomento relativo all'[uso di Azure PowerShell per Node.js](https://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)

Il valore **URL sito Web creato** restituito dal cmdlet **Publish-AzureServiceProject** contiene il nome di dominio completo per l'applicazione ospitata. Sarà necessario ottenere un certificato SSL per tale nome di dominio completo specifico e distribuirlo in Azure.

## <a name="step2"> </a>Passaggio 2: Ottenere un certificato SSL

Per configurare SSL per un'applicazione, è necessario innanzitutto ottenere un certificato SSL
che sia stato firmato da un'Autorità di certificazione (CA, Certificate Authority), ovvero un
ente di terze parti attendibile che rilascia certificati per questo scopo. Se non
si dispone ancora di un certificato, è necessario ottenerne uno da una società che
emette certificati SSL.

Il certificato deve soddisfare i seguenti requisiti per i
certificati SSL in Azure:

-   Il certificato deve includere una chiave privata.
-   Il certificato deve essere creato per lo scambio di chiave (file con estensione pfx).
-   Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere
    al servizio cloud. Non è possibile ottenere un certificato SSL per
    il dominio cloudapp.net, pertanto il nome del soggetto del certificato deve corrispondere
    al nome di dominio personalizzato usato per accedere all'applicazione. Ad esempio, __mysecuresite.cloudapp.net__.
-   Per il certificato deve essere usata una crittografia di almeno 2048 bit.

## <a name="step3"> </a>Passaggio 3: Importare il certificato SSL

Dopo aver creato il certificato, è necessario installarlo nell'archivio certificati del computer di sviluppo. Questo certificato verrà recuperato e caricato in Azure come parte del pacchetto di distribuzione dell'applicazione in base alle modifiche della configurazione apportate in un passaggio successivo.

> [AZURE.NOTE] I passaggi descritti in questa sezione si basano sulla versione di Windows 8 dell'Importazione guidata certificati. Se si usa una versione precedente di Windows, i passaggi elencati potrebbero non corrispondere all'ordine visualizzato nella procedura guidata. In questo caso, leggere con attenzione questa sezione prima di usare l'Importazione guidata certificati in modo da comprendere le operazioni generali da eseguire.

Per importare il certificato SSL, seguire questa procedura:

1.   In Esplora risorse passare alla directory in cui si trova il file con estensione **pfx** contenente il certificato e quindi fare doppio clic sul certificato. Verrà avviata l'Importazione guidata certificati.
	
	![certificate wizard][cert-wizard]

2.   Nella sezione **Percorso archivio** selezionare **Utente corrente** e quindi fare clic su **Avanti**. Il certificato verrà installato nell'archivio certificati dell'account utente.

3.   Continuare la procedura guidata accettando le impostazioni predefinite, fino ad arrivare alla schermata **Protezione della chiave privata**. Immettere la password, se impostata, per il certificato. Selezionare anche **Contrassegna questa chiave come esportabile**. Infine, fare clic su **Avanti**.

	![private key protection][key-protection]

4. Continuare la procedura guidata accettando le impostazioni predefinite, fino al completamento dell'installazione del certificato.

Ora è necessario modificare la definizione del servizio in modo che faccia riferimento al certificato
installato.

## <a name="step4"> </a>Passaggio 4: Modificare i file di definizione e configurazione del servizio

L'applicazione deve essere configurata per fare riferimento al certificato ed è necessario
aggiungere un endpoint HTTPS. Di conseguenza, la definizione e la configurazione del servizio
devono essere aggiornate.

1.  Nella directory del servizio aprire il file di definizione del servizio
    (ServiceDefinition.csdef), aggiungere una sezione **Certificates** nella sezione **WebRole** e includere le seguenti informazioni sul
    certificato:

        <WebRole name="WebRole1" vmsize="ExtraSmall">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    storeLocation="LocalMachine" storeName="My" />
            </Certificates>
        ...
        </WebRole>

    La sezione **Certificates** definisce il nome del certificato,
    il relativo percorso e il nome dell'archivio in cui si trova. Poiché il certificato è stato installato nell'archivio certificati dell'utente, viene usato il valore "My". È inoltre possibile usare altri percorsi per l'archivio certificati. Per altre informazioni, vedere [Come 
    associare un certificato a un servizio].

2.  Nel file di definizione del servizio aggiornare l'elemento http **InputEndpoint** nella sezione **Endpoints** per abilitare HTTPS:

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    Tutte le modifiche necessarie apportate al file di definizione del servizio sono state
    completate, tuttavia è ancora necessario aggiungere le informazioni del certificato al
    file di configurazione del servizio.

3.  Nei file di configurazione del servizio
    (**ServiceConfiguration.Cloud.cscfg** e 
    **ServiceConfiguration.Local.cscfg**), aggiungere il certificato alla
    sezione **Certificates** vuota nella sezione **Role**,
    sostituendo il valore dell'identificazione personale di esempio indicato di seguito con quello del
    certificato:

        <Role name="WebRole1">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

4.  Aggiornare la configurazione del servizio nel cloud, pubblicando
    di nuovo il servizio. Al prompt di Azure PowerShell
    digitare **Publish-AzureServiceProject** dalla directory del servizio.

	Come parte del processo di pubblicazione, il certificato a cui viene fatto riferimento verrà copiato dall'archivio certificati locale e incluso nel pacchetto di distribuzione.

## <a name="step5"> </a>Passaggio 5: Connettersi all'istanza del ruolo usando HTTPS

Ora che la distribuzione è in esecuzione in Azure, è possibile
connettersi a questa con HTTPS.

1.  Nel portale di gestione selezionare il servizio cloud, quindi fare clic su **Dashboard**.

2. Scorrere verso il basso e fare clic sul collegamento visualizzato come **URL del sito**:

    ![the site url][site-url]

	> [AZURE.IMPORTANT] Se nell'URL del sito visualizzato nel portale non è specificato HTTPS, sarà necessario immettere manualmente l'URL nel browser usando HTTPS invece di HTTP.

3.  Verrà aperta una nuova finestra del browser, in cui sarà visualizzato il sito Web desiderato.

    Nel browser sarà visualizzata un'icona a forma di lucchetto, per indicare che la connessione in uso
    è di tipo HTTPS. L'icona indica inoltre che l'applicazione
    è stata configurata correttamente per SSL.

    ![][8]

## Risorse aggiuntive

[Come associare un certificato a un servizio]

[Configurazione di SSL per un'applicazione Node.js in un ruolo di lavoro di Azure]

[Come configurare un certificato SSL su un endpoint HTTPS]

  [Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud]: #step1
  [Passaggio 2: Ottenere un certificato SSL]: #step2
  [Passaggio 3: Importare il certificato SSL]: #step3
  [Passaggio 4: Modificare i file di definizione e configurazione del servizio]: #step4
  [Passaggio 5: Connettersi all'istanza del ruolo usando HTTPS]: #step5
  [**Azure PowerShell**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
  [Portale di gestione di Azure]: http://manage.windowsazure.com
  
  
  [Come associare un certificato a un servizio]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
  [Come configurare un certificato SSL su un endpoint HTTPS]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
  [cert-wizard]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
  [key-protection]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
  [Configurazione di SSL per un'applicazione Node.js in un ruolo di lavoro di Azure]: /it-it/develop/nodejs/common-tasks/enable-ssl-worker-role/

<!--HONumber=45--> 
