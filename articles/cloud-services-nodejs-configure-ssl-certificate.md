<properties urlDisplayName="Enable SSL" pageTitle="Configurare SSL per un servizio cloud (Node.js) - Azure" metaKeywords="Node.js Azure SSL, Node.js Azure HTTPS" description="Informazioni su come specificare un endpoint HTTPS per un ruolo Web Node.js e come caricare un certificato SSL al fine di proteggere l'applicazione." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Configurazione di SSL per un'applicazione Node.js in un ruolo Web di Azure" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="wpickett" />

# Configurazione di SSL per un'applicazione Node.js in un ruolo Web di Azure

La crittografia SSL (Secure Socket Layer) è il metodo più diffuso per
proteggere i dati inviati tramite Internet. In questa attività comune viene illustrato come
specificare un endpoint HTTPS per un'applicazione Node.js ospitata come servizio cloud di Azure in un ruolo Web e come caricare
un certificato SSL al fine di proteggere l'applicazione.

<div class="dev-callout">

Nota
Le procedure descritte in questo articolo si applicano solo alle applicazioni nodo ospitate come servizio cloud di Azure in un ruolo Web. Per informazioni su Siti Web, vedere [Configurazione di un certificato SSL per un sito Web di Azure][Configurazione di un certificato SSL per un sito Web di Azure].

</div>

Questa attività include i passaggi seguenti:

-   [Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud][Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud]
-   [Passaggio 2: Ottenere un certificato SSL][Passaggio 2: Ottenere un certificato SSL]
-   [Passaggio 3: Importare il certificato SSL][Passaggio 3: Importare il certificato SSL]
-   [Passaggio 4: Modificare i file di definizione e configurazione del servizio][Passaggio 4: Modificare i file di definizione e configurazione del servizio]
-   [Passaggio 5: Connettersi all'istanza del ruolo usando HTTPS][Passaggio 5: Connettersi all'istanza del ruolo usando HTTPS]

## <a name="step1"> </a>Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud

Quando si distribuisce un'applicazione Node.js in un ruolo Web di Azure, il
certificato del server e la connessione SSL vengono gestiti da Internet
Information Services (IIS), in modo che sia possibile scrivere
il servizio Node.js come se fosse un servizio HTTP. È possibile creare un semplice servizio Node.js di tipo "hello
world" usando Azure PowerShell e attenendosi alla procedura seguente:

1.  Dal **menu Start** o dalla **schermata Start** cercare **Azure PowerShell**. Fare infine clic con il pulsante destro del mouse su **Azure PowerShell** e scegliere **Esegui come amministratore**.

    ![Icona di Azure PowerShell][Icona di Azure PowerShell]

[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

1.  Creare un nuovo progetto di servizio usando il cmdlet **New-AzureServiceProject**.

    ![][0]

2.  Aggiungere un ruolo Web al servizio usando il cmdlet **Add-AzureNodeWebRole**:

    ![][1]

3.  Pubblicare il servizio nel cloud usando il cmdlet **Publish-AzureServiceProject**:

    ![][2]

    <div class="dev-callout">

    **Nota**
    Se le impostazioni per la sottoscrizione di Azure non sono state importate in precedenza, verrà visualizzato un errore quando si tenta di eseguire la pubblicazione. Per informazioni sul download e sull'importazione delle impostazioni di pubblicazione per la sottoscrizione, vedere [Come usare Azure PowerShell per Node.js][Come usare Azure PowerShell per Node.js]

    </div>

Il valore **URL sito Web creato** restituito dal cmdlet **Publish-AzureServiceProject** include il nome di dominio completo per l'applicazione ospitata. Sarà necessario ottenere un certificato SSL per tale nome di dominio completo specifico e distribuirlo in Azure.

## <a name="step2"> </a>Passaggio 2: Ottenere un certificato SSL

Per configurare SSL per un'applicazione, è necessario innanzitutto ottenere un certificato
SSL che sia stato firmato da un'Autorità di certificazione (CA), ovvero un
ente di terze parti attendibile che rilascia certificati per questo scopo. Se non se
ne dispone già, è necessario ottenerne uno da un rivenditore
di certificati SSL.

Il certificato deve soddisfare i requisiti seguenti per i certificati
SSL in Azure:

-   Il certificato deve includere una chiave privata.
-   Il certificato deve essere creato per lo scambio di chiave (file con estensione pfx).
-   Il nome del soggetto del certificato deve corrispondere al dominio usato per accedere
    al servizio cloud. Non è possibile ottenere un certificato SSL per il dominio
    cloudapp.net. Il nome del soggetto del certificato deve pertanto corrispondere
    al nome di dominio personalizzato usato per accedere all'applicazione. Ad esempio, **sitoprotetto.cloudapp.net**.
-   Per il certificato deve essere usata una crittografia di almeno 2048 bit.

## <a name="step3"> </a>Passaggio 3: Importare il certificato SSL

Dopo aver creato il certificato, è necessario installarlo nell'archivio certificati del computer di sviluppo. Questo certificato verrà recuperato e caricato in Azure come parte del pacchetto di distribuzione dell'applicazione in base alle modifiche della configurazione apportate in un passaggio successivo.

<div class="dev-callout">

**Nota**
I passaggi descritti in questa sezione si basano sulla versione di Windows 8 dell'Importazione guidata certificati. Se si usa una versione precedente di Windows, i passaggi elencati potrebbero non corrispondere all'ordine visualizzato nella procedura guidata. In questo caso, leggere con attenzione questa sezione prima di usare l'Importazione guidata certificati in modo da comprendere le operazioni generali da eseguire.

</div>

Per importare il certificato SSL, eseguire la procedura seguente:

1.  In Esplora risorse passare alla directory in cui si trova il file **pfx** contenente il certificato e quindi fare doppio clic sul certificato. Verrà avviata l'Importazione guidata certificati.

    ![Importazione guidata dei certificati][Importazione guidata dei certificati]

2.  Nella sezione **Percorso archivio** selezionare **Utente corrente** e quindi fare clic su **Avanti**. Il certificato verrà installato nell'archivio certificati dell'account utente.

3.  Continuare con la procedura guidata accettando le impostazioni predefinite fino ad arrivare alla schermata **Protezione della chiave privata**. Immettere la password, se impostata, per il certificato. Selezionare anche **Contrassegna questa chiave come esportabile**. Infine, fare clic su **Next**.

    ![protezione chiave privata][protezione chiave privata]

4.  Continuare la procedura guidata accettando le impostazioni predefinite, fino al completamento dell'installazione del certificato.

Ora è necessario modificare la definizione del servizio in modo che faccia riferimento al certificato
installato.

## <a name="step4"> </a>Passaggio 4: Modificare i file di definizione e configurazione del servizio

L'applicazione deve essere configurata per fare riferimento al certificato ed è necessario
aggiungere un endpoint HTTPS. Di conseguenza, è necessario aggiornare i file di definizione e
configurazione del servizio.

1.  Nella directory del servizio aprire il file di definizione del servizio
    (ServiceDefinition.csdef), aggiungere una sezione **Certificates** all'interno della sezione **WebRole** e includere le informazioni seguenti sul
    certificato:

        <WebRole name="WebRole1" vmsize="ExtraSmall">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    storeLocation="LocalMachine" storeName="My" />
            </Certificates>
        ...
        </WebRole>

    Nella sezione **Certificates** è definito il nome del certificato,
    il relativo percorso e il nome dell'archivio in cui si trova. Poiché il certificato è stato installato nell'archivio certificati dell'utente, viene usato il valore "My". È inoltre possibile usare altri percorsi per l'archivio certificati. Per altre informazioni, vedere [Come
    associare un certificato a un servizio][Come
    associare un certificato a un servizio].

2.  Nel file di definizione del servizio aggiornare l'elemento **InputEndpoint** all'interno della sezione **Endpoints** per abilitare HTTPS:

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    Tutte le modifiche necessarie al file di definizione del servizio sono state
    completate ma è ora necessario aggiungere le informazioni
    del certificato al file di configurazione del servizio.

3.  Nei file di configurazione del servizio
    (**ServiceConfiguration.Cloud.cscfg** e
    **ServiceConfiguration.Local.cscfg**) aggiungere il certificato alla
    sezione **Certificates** vuota all'interno della sezione **Role**,
    sostituendo il valore di identificazione personale illustrato di seguito con quello del
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
connettersi a questa usando HTTPS.

1.  Nel portale di gestione selezionare il servizio cloud e quindi fare clic su **Dashboard**.

2.  Scorrere verso il basso e fare clic sul collegamento visualizzato come **Site URL**:

    ![URL sito][URL sito]

    <div class="dev-callout">

    **Nota**
    Se nell'URL del sito visualizzato nel portale non è specificato HTTPS, sarà necessario immettere manualmente l'URL nel browser usando HTTPS invece di HTTP.

    </div>

3.  Verrà aperta una nuova finestra del browser, in cui sarà visualizzato il sito Web desiderato.

    Nel browser verrà visualizzata un'icona a forma di lucchetto, per indicare che la connessione
    in uso è di tipo HTTPS. Ciò indica anche che l'applicazione
    è stata configurata correttamente per SSL.

    ![][3]

## Risorse aggiuntive

[Come associare un certificato a un servizio][Come
associare un certificato a un servizio]

[Configurazione di SSL per un'applicazione Node.js in un ruolo di lavoro di Azure][Configurazione di SSL per un'applicazione Node.js in un ruolo di lavoro di Azure]

[Come configurare un certificato SSL su un endpoint HTTPS][Come configurare un certificato SSL su un endpoint HTTPS]

  [Configurazione di un certificato SSL per un sito Web di Azure]: ../web-sites-configure-ssl-certificate/
  [Passaggio 1: Creare un servizio Node.js e pubblicarlo sul cloud]: #step1
  [Passaggio 2: Ottenere un certificato SSL]: #step2
  [Passaggio 3: Importare il certificato SSL]: #step3
  [Passaggio 4: Modificare i file di definizione e configurazione del servizio]: #step4
  [Passaggio 5: Connettersi all'istanza del ruolo usando HTTPS]: #step5
  [Icona di Azure PowerShell]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
  [0]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png
  [1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
  [Come usare Azure PowerShell per Node.js]: https://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [Importazione guidata dei certificati]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
  [protezione chiave privata]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
  [URL sito]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
  [Configurazione di SSL per un'applicazione Node.js in un ruolo di lavoro di Azure]: /it-it/develop/nodejs/common-tasks/enable-ssl-worker-role/
  [Come configurare un certificato SSL su un endpoint HTTPS]: http://msdn.microsoft.com/it-it/library/windowsazure/ff795779.aspx
