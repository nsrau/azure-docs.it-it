<properties linkid="dev-nodejs-enablesslworker" urlDisplayName="Enable SSL worker role" pageTitle="Configure SSL for a cloud service (Node.js) worker role" metaKeywords="Node.js Azure SSL, Node.js Azure, SSL worker role" description="" metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Configuring SSL for a Node.js Application in an Azure Worker Role" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Configurazione di SSL per un'applicazione Node.js in un ruolo di lavoro di Azure

La crittografia SSL (Secure Socket Layer) è il metodo più diffuso per proteggere i dati inviati tramite Internet. Questa attività comune illustra come specificare un endpoint HTTPS per un'applicazione Node.js ospitata in un ruolo di lavoro in un Servizio cloud di Azure.


   <b>Nota</b>
   <p>I passaggi illustrati in questo articolo riguardano esclusivamente le applicazioni Node ospitate come Servizio cloud di Azure in un ruolo di lavoro.</p>

Questa attività include i passaggi seguenti:

-   [Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud][Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud]
-   [Passaggio 2: Ottenere un certificato SSL][Passaggio 2: Ottenere un certificato SSL]
-   [Passaggio 3: Modificare l'applicazione in modo che utilizzi il certificato SSL][Passaggio 3: Modificare l'applicazione in modo che utilizzi il certificato SSL]
-   [Passaggio 4: Modificare il file di definizione del servizio][Passaggio 4: Modificare il file di definizione del servizio]
-   [Passaggio 5: Connettersi all'istanza del ruolo usando HTTPS][Passaggio 5: Connettersi all'istanza del ruolo usando HTTPS]

## <a name="step1"> </a>Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud

È possibile creare un semplice servizio Node.js di tipo "hello world" usando Azure PowerShell e attenendosi alla procedura seguente:

1.  Dal **menu Start** o dalla **schermata Start** cercare **Azure PowerShell**. Fare infine clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

    ![Icona di Azure PowerShell][Icona di Azure PowerShell]

2.  Creare un nuovo servizio usando il cmdlet **New-AzureServiceProject**.

    ![][0]

3.  Aggiungere un ruolo di lavoro al servizio usando il cmdlet **Add-AzureNodeWorkerRole**:

    ![][1]

4.  Pubblicare il servizio nel cloud usando il cmdlet **Publish-AzureServiceProject**:

    ![][2]

    <div class="dev-callout">
<strong>Nota</strong>
<p>Se le impostazioni per la sottoscrizione di Azure non sono state importate in precedenza, verr&agrave; visualizzato un errore quando si tenta di eseguire la pubblicazione. Per informazioni sul download e sull'importazione delle impostazioni di pubblicazione per la sottoscrizione, vedere <a href="https://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings">Come usare Azure PowerShell per Node.js</a></p>
</div>

Il valore **URL sito Web creato** restituito dal cmdlet **Publish-AzureServiceProject** include il nome di dominio completo per l'applicazione ospitata. Sarà necessario ottenere un certificato SSL per tale nome di dominio completo specifico e distribuirlo in Azure.

## <a name="step2"> </a>Passaggio 2: Ottenere un certificato SSL

Per configurare SSL per un'applicazione, è necessario innanzitutto ottenere un certificato SSL che sia stato firmato da un'Autorità di certificazione (CA), ovvero un ente di terze parti attendibile che rilascia certificati per questo scopo. Se non se ne dispone già, è necessario ottenerne uno da un rivenditore di certificati SSL.

Il certificato deve soddisfare i requisiti seguenti per i certificati SSL in Azure:

-   Il certificato deve includere una chiave privata.
-   Il certificato deve essere creato per lo scambio di chiave (file con estensione **pfx**).
-   Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere
    al servizio cloud. Non è possibile ottenere un certificato SSL per il dominio
    cloudapp.net. Il nome del soggetto del certificato deve pertanto corrispondere
    al nome di dominio personalizzato usato per accedere all'applicazione. Ad esempio, **sitoprotetto.cloudapp.net**.
-   Per il certificato deve essere utilizzata una crittografia di almeno 2048 bit.

Il file con estensione **pfx** contenente il certificato verrà aggiunto al progetto di servizio e verrà distribuito in Azure nei passaggi seguenti.

## <a name="step3"> </a>Passaggio 3: Modificare l'applicazione in modo che utilizzi il certificato SSL

Quando un'applicazione Node.js viene distribuita in un ruolo di lavoro, il certificato del server e la connessione SSL vengono gestiti da Node.exe. Per gestire il traffico SSL, è necessario usare il modulo 'https' invece del modulo 'http'. Eseguire la procedura seguente per aggiungere il certificato SSL al progetto e quindi modificare l'applicazione in modo che utilizzi il certificato.

1.  Salvare il file con estensione **pfx** fornito dall'Autorità di certificazione (CA, Certificate Authority) nella directory contenente l'applicazione. Ad esempio, **c:\\node\\securesite\\workerrole1** è la directory che include l'applicazione utilizzata in questo articolo.

2.  Aprire il file **c:\\node\\securesite\\workerrole1\\server.js** usando Notepad.exe e quindi sostituire i contenuti del file con il codice seguente:

        var https = require('https');
        var fs = require('fs');

        var options = {
            pfx: fs.readFileSync('certificate.pfx'),
            passphrase: "password"
        };
        var port = process.env.PORT || 8000;
        https.createServer(options, function (req, res) {
            res.writeHead(200, { 'Content-Type': 'text/plain' });
            res.end('Hello World\n');
        }).listen(port);

    <div class="dev-callout">
<strong>Nota</strong>
<p>&Egrave; necessario sostituire 'certificate.pfx' con il nome del file di certificato e &quot;password&quot; con l'eventuale password corrispondente.</p>
</div>

3.  Salvare il file **server.js**.

Al termine delle modifiche al file **server.js**, l'applicazione rimarrà in attesa di comunicazioni sulla porta 443, ovvero la porta standard per le comunicazioni SSL, dopo la distribuzione in Azure. Il file con estensione **pfx** verrà usato per implementare le comunicazioni SSL su tale trasporto.

## <a name="step4"> </a>Passaggio 4: Modificare il file di definizione del servizio

Poiché l'applicazione è ora in attesa sulla porta 443, è necessario modificare anche la definizione del servizio, in modo da consentire le comunicazioni su tale porta.

1.  Nella directory del servizio aprire il file di definizione del servizio     (**ServiceDefinition.csdef**) e aggiornare l'elemento http **InputEndpoint** nella sezione **Endpoints** in modo da permettere le comunicazioni sulla porta 443:

        <WorkerRole name="WorkerRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpIn" protocol="tcp" 
                    port="443" />
            </Endpoints>
        ...
        </WorkerRole>

    Al termine di questa modifica, salvare il file **ServiceDefinition.csdef**.

2.  Aggiornare la configurazione modificata nel cloud, pubblicando    di nuovo il servizio. Al prompt di Azure PowerShell
    digitare **Publish-AzureServiceProject** dalla directory del servizio.

## <a name="step5"> </a>Passaggio 5: Connettersi all'istanza del ruolo usando HTTPS

Ora che la distribuzione è in esecuzione in Azure, è possibile connettersi a questa usando HTTPS.

1.  Nel portale di gestione selezionare il servizio cloud e quindi fare clic su **Dashboard**.

2.  Scorrere verso il basso e fare clic sul collegamento visualizzato come **Site URL**:

    ![URL sito][URL sito]

    <div class="dev-callout">
<strong>Nota</strong>
<p>Se nell'URL del sito visualizzato nel portale non &egrave; specificato HTTPS, sar&agrave; necessario immettere manualmente l'URL nel browser usando HTTPS invece di HTTP.</p>
</div>

3.  Verrà aperta una nuova finestra del browser, in cui sarà visualizzato il sito Web desiderato.

    Nel browser verrà visualizzata un'icona a forma di lucchetto, per indicare che la connessione    in uso è di tipo HTTPS. Ciò indica anche che l'applicazione    è stata configurata correttamente per SSL.

    ![][3]

## Risorse aggiuntive

[Come associare un certificato a un servizio][Come associare un certificato a un servizio]

[Configurazione di SSL per un'applicazione Node.js in un ruolo Web di Azure][Configurazione di SSL per un'applicazione Node.js in un ruolo Web di Azure]

[Come configurare un certificato SSL su un endpoint HTTPS][Come configurare un certificato SSL su un endpoint HTTPS]

  [Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud]: #step1
  [Passaggio 2: Ottenere un certificato SSL]: #step2
  [Passaggio 3: Modificare l'applicazione in modo che utilizzi il certificato SSL]: #step3
  [Passaggio 4: Modificare il file di definizione del servizio]: #step4
  [Passaggio 5: Connettersi all'istanza del ruolo usando HTTPS]: #step5
  [Icona di Azure PowerShell]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/azure-powershell-start.png
  [0]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-01.png
  [1]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-02-worker.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-03-worker.png
  [URL sito]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/site-url.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certficate-worker-role/enable-ssl-08.png
  [Come associare un certificato a un servizio]: http://msdn.microsoft.com/it-it/library/windowsazure/gg465718.aspx
  [Configurazione di SSL per un'applicazione Node.js in un ruolo Web di Azure]: /it-it/develop/nodejs/common-tasks/enable-ssl/
  [Come configurare un certificato SSL su un endpoint HTTPS]: http://msdn.microsoft.com/it-it/library/windowsazure/ff795779.aspx
