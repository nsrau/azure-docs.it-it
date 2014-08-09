<properties  linkid="manage-services-how-to-create-and-deploy-a-cloud-service" urlDisplayName="How to create and deploy" pageTitle="How to create and deploy a cloud service - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Learn how to create and deploy a cloud service using the Quick Create method in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="ryanwi" solutions="" manager="" editor="" />

# Come creare e distribuire un servizio Cloud

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

Nel portale di gestione di Azure sono disponibili due modi per creare e distribuire un servizio cloud: **Quick Create** e **Custom Create**.

In questo argomento viene descritto come utilizzare il metodo di creazione rapida di un nuovo servizio cloud e come caricare e distribuire un pacchetto del servizio cloud in Azure tramite l'opzione
**Upload**. Quando si utilizza questo metodo, il portale di gestione di
Azure rende disponibili comodi collegamenti per completare tutti i requisiti man mano che si procede. Se si è pronti per distribuire il servizio cloud durante la creazione, è possibile effettuare contemporaneamente entrambe le operazioni utilizzando **Custom Create**.

**Nota** Se si prevede di pubblicare il servizio cloud da Windows Team
Foundation Services (TFS), utilizzare Quick Create e quindi configurare la pubblicazione TFS da **Quick Start** o dal dashboard. Per ulteriori informazioni, vedere l'anteprima di [Distribuzione continua per i servizi cloud in Azure][1] o la Guida alla pagina **Quick Start**.

## Sommario

* [Concetti](#concepts)
* [Preparazione dell'app](#prepare)
* [Prima di iniziare](#begin)
* [Procedura: Creare un servizio cloud con Quick Create](#quick)
* [Procedura: Caricare un certificato per un servizio
  cloud](#uploadcertificate)
* [Procedura: Distribuire un servizio cloud](#deploy)

<h2><a  id="concepts" ></a>Concetti</h2>


Per distribuire un'applicazione come servizio cloud in Azure, sono necessari tre componenti:

> * **file di definizione del servizio** Il file di definizione del
>   servizio (.csdef) definisce il modello di servizio, compreso il
>   numero di ruoli.

> * **file di configurazione del servizio** Il file di configurazione
>   del servizio cloud (.cscfg) specifica le impostazioni di
>   configurazione per il servizio cloud e i singoli ruoli, incluso il
>   numero di istanze del ruolo.

> * **pacchetto del servizio** Il pacchetto di servizio (.cspkg)
>   contiene il codice dell'applicazione e il file di definizione del
>   servizio.

<h2><a  id="prepare" ></a>Preparazione dell'app</h2>


Per poter distribuire un servizio cloud, è necessario creare il pacchetto di servizio cloud (.cspkg) dal codice dell'applicazione e un file di configurazione del servizio cloud (.cscfg). Ogni pacchetto di servizio cloud contiene le configurazioni e i file dell'applicazione. Le impostazioni di configurazione sono incluse nel file di configurazione del servizio.

Azure SDK offre strumenti per la preparazione dei file necessari alla distribuzione. È possibile installare l'SDK dalla pagina di
[download][2] di Azure, nel linguaggio in cui si preferisce sviluppare
il codice dell'applicazione.

Chi non ha familiarità con i servizi cloud può scaricare un pacchetto di servizio cloud (.cspkg) e il file di configurazione del servizio
(.cscfg) di esempio da [Esempi di codice di Azure][3].

Per poter esportare un pacchetto di servizio, è necessario configurare tre funzionalità del servizio cloud:

* Se si desidera distribuire un servizio cloud che utilizza SSL (Secure
  Sockets Layer) per la crittografia dei dati, configurare
  l'applicazione per SSL. Per ulteriori informazioni, vedere [Come
  configurare un certificato SSL su un endpoint HTTPS][4].

* Se si desidera configurare connessioni Desktop remoto a istanze del
  ruolo, configurare i ruoli per Desktop remoto. Per ulteriori
  informazioni sulla preparazione del file di definizione del servizio
  di accesso remoto, vedere [Informazioni generali sulla configurazione
  di una connessione Desktop remoto per un ruolo][5].

* Se si desidera configurare il monitoraggio dettagliato per il servizio
  cloud, abilitare la Diagnostica Azure per il servizio cloud.
  *Monitoraggio minimo* (livello di monitoraggio predefinito) ricorre a
  contatori delle prestazioni raccolti dai sistemi operativi host per
  istanze del ruolo (macchine virtuali). "Monitoraggio dettagliato\*
  raccoglie metriche supplementari in base ai dati delle prestazioni
  all'interno delle istanze del ruolo per consentire un'analisi più
  accurata dei problemi che si verificano durante l'elaborazione
  dell'applicazione. Per scoprire come abilitare la Diagnostica Azure,
  vedere [Abilitazione della diagnostica in Azure][6].

<h2><a  id="begin" ></a>Prima di iniziare</h2>


* Se Azure SDK non è stato installato, fare clic su **Install Azure
  SDK** per aprire la pagina di [download][2] di Azure e quindi
  scaricare l'SDK nel linguaggio in cui si preferisce sviluppare il
  codice. (È possibile eseguire questa operazione in seguito).

* Se un'istanza del ruolo lo richiede, creare i certificati. I servizi
  cloud richiedono un file con estensione pfx con una chiave privata. È
  possibile caricare i certificati in Azure nel corso della creazione e
  della distribuzione del servizio cloud. Per ulteriori informazioni
  sulla creazione dei certificati, vedere [Come configurare un
  certificato SSL su un endpoint HTTPS][4].

* Se si prevede di distribuire il servizio cloud a un gruppo di
  affinità, creare il gruppo di affinità. Per distribuire il servizio
  cloud e altri servizi di Azure alla stessa posizione all'interno di
  una regione, è possibile utilizzare un gruppo di affinità. È possibile
  creare il gruppo di affinità nell'area **Reti** del portale di
  gestione, alla pagina **Gruppi di affinità**. Per ulteriori
  informazioni, vedere la Guida alla pagina **Gruppi di affinità**.

<h2><a  id="quick" ></a>Procedura: Creare un servizio cloud con Quick Create</h2>


1.  Nel [portale di gestione][7] fare clic su **New**, su **Cloud
    Service** quindi fare clic su **Quick Create**.
    
    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2.  Nel campo **URL**, immettere un nome di sottodominio da utilizzare
    nell'URL pubblico per accedere al servizio cloud nelle
    distribuzioni di produzione. Il formato dell'URL per le
    distribuzioni di produzione è: http://*myURL*.cloudapp.net.

3.  In **Region/Affinity Group** selezionare l'area geografica o il
    gruppo di affinità a cui distribuire il servizio cloud. Per
    distribuire il servizio cloud alla stessa posizione di altri servizi
    di Azure all'interno di una regione, selezionare un gruppo di
    affinità.
    
    > [WACOM.NOTE] Per creare un gruppo di affinità, aprire l'area
    > **Networks** del portale di gestione, fare clic su **Affinity
    > Groups** quindi fare clic su **Create a new affinity group** o
    > **Create**. È possibile utilizzare i gruppi di affinità creati in
    > precedenza nel portale di gestione di Azure. Ed è possibile creare
    > e gestire gruppi di affinità utilizzando l'API Gestione servizi
    > di Azure. Per ulteriori informazioni, vedere [Operazioni sui
    > gruppi di affinità][8].

4.  Fare clic su **Create Cloud Service**.
    
    È possibile monitorare lo stato di elaborazione nell'area dei
    messaggi, nella parte inferiore della finestra.
    
    Viene visualizzata l'area **Cloud Services**, con il nuovo servizio
    cloud. Lo stato Created indica che la creazione di servizi cloud è
    stata completata correttamente.
    
    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)
    
    Se per uno o più ruoli nel servizio cloud è richiesto un certificato
    per la crittografia dei dati con SSL (Secure Sockets Layer) che non
    è stato caricato in Azure, è necessario caricare il certificato
    prima di distribuire il servizio cloud. Dopo aver caricato un
    certificato, tutte le applicazioni Windows in esecuzione nelle
    istanze del ruolo possono accedere al certificato.

<h2><a  id="uploadcertificate" ></a>Procedura: Caricare un certificato per un servizio cloud</h2>


1.  Nel [portale di gestione][7] fare clic su **Cloud Services**. Quindi
    fare clic sul nome del servizio cloud per aprire il dashboard.
    
    ![CloudServices_EmptyDashboard](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)

2.  Fare clic su **Certificates** per aprire la pagina dei certificati,
    illustrata di seguito.****
    
    ![CloudServices_CertificatesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CertificatesPage.png)

3.  Fare clic su **Add new certificate** o su **Upload** <strong>Viene
    visualizzata la finestra </strong>Add a Certificate.
    
    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

4.  Nel campo **Certificate file** utilizzare **Browse** per selezionare
    il certificato (file con estensione pfx).

5.  Nel campo **Password** immettere la chiave privata del certificato.

6.  Fare clic su OK (segno di spunta).
    
    È possibile seguire l'avanzamento del caricamento nell'area dei
    messaggi, illustrata di seguito. Terminato il caricamento, il
    certificato viene aggiunto alla tabella. Nell'area dei messaggi,
    fare clic sulla freccia giù per chiudere il messaggio, oppure fare
    clic su X per rimuoverlo.
    
    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)
    
    È possibile distribuire il servizio cloud dal dashboard o da **Quick
    Start**.

<h2><a  id="deploy" ></a>Procedura: Distribuire un servizio cloud</h2>


1.  Nel [portale di gestione][7] fare clic su **Cloud Services**. Quindi
    fare clic sul nome del servizio cloud per aprire il dashboard.

2.  Fare clic su **Quick Start** (l'icona a sinistra del **dashboard**)
    per aprire la pagina di avvio rapido, illustrata di seguito.**** (È
    inoltre possibile distribuire il servizio cloud tramite **Upload**
    sul dashboard).
    
    ![CloudServices_QuickStartPage](./media/cloud-services-how-to-create-deploy/CloudServices_QuickStartPage.png)

3.  Se Azure SDK non è stato installato, fare clic su **Install Azure
    SDK** per aprire la pagina di [download][2] di Azure e quindi
    scaricare l'SDK nel linguaggio in cui si preferisce sviluppare il
    codice.
    
    Nella pagina di download è inoltre possibile installare librerie
    client e codice sorgente per sviluppare applicazioni Web in Node.js,
    Java, PHP e altri linguaggi che è possibile distribuire come servizi
    cloud di Azure scalabili.
    
    > [WACOM.NOTE] Per servizi cloud creati in precedenza (noti come
    > *servizi ospitati*), sarà necessario assicurarsi che i sistemi
    > operativi guest nelle macchine virtuali (istanze del ruolo) siano
    > compatibili con la versione di Azure SDK installata. Per ulteriori
    > informazioni, vedere le [Note sulla versione di Azure SDK][9].

4.  Fare clic su **New Production Deployment** o su **New Staging
    Deployment**.
    
    Se si desidera eseguire il test del servizio cloud in Azure prima
    della distribuzione alla produzione, è possibile distribuirlo alla
    gestione temporanea. Nell'ambiente di gestione temporanea
    l'identificatore univoco globale (GUID) identifica il servizio
    cloud negli URL (*GUID*.cloudapp.net). Nell'ambiente di produzione
    viene utilizzato il prefisso DNS descrittivo assegnato (ad esempio,
    *myservice*.cloudapp.net). Quando si è pronti per innalzare di
    livello il servizio cloud preconfigurato alla produzione, utilizzare
    **Swap** per reindirizzare le richieste client.
    
    Quando si seleziona un ambiente di distribuzione, viene visualizzata
    la finestra **Upload a Package**.
    
    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

5.  Nel campo **Deployment name** immettere un nome per la nuova
    distribuzione, ad esempio, MyCloudServicev1.

6.  In **Package** utilizzare **Browse** per selezionare il file del
    pacchetto di servizio (.cspkg) da utilizzare.

7.  In **Configuration** utilizzare **Browse** per selezionare il file
    di configurazione del servizio (.cscfg) da utilizzare.

8.  Se il servizio cloud includerà ruoli con una sola istanza,
    selezionare la casella di controllo **Deploy even if one or more
    roles contain a single instance** per abilitare la distribuzione.

Se ogni ruolo ha almeno due istanze, Azure può garantire l'accesso al servizio cloud solo al 99,95% in fase di manutenzione e aggiornamento del servizio. Se necessario, è possibile aggiungere istanze del ruolo aggiuntive alla pagina **Scale** dopo aver distribuito il servizio cloud. Per ulteriori informazioni, vedere [Contratti di servizio][10].

1.  Fare clic su OK (segno di spunta) per iniziare la distribuzione del
    servizio cloud.
    
    È possibile monitorare lo stato della distribuzione nell'area dei
    messaggi. Fare clic sulla freccia giù per nascondere il messaggio.
    
    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

### Per verificare che la distribuzione sia stata completata correttamente

1.  Fare clic su **Dashboard**.

2.  In **quick glance** fare clic sull'URL del sito per aprire il
    servizio cloud in un Web browser.
^

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)



[1]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
[2]: http://www.windowsazure.com/it-it/develop/downloads/
[3]: http://code.msdn.microsoft.com/windowsazure/
[4]: http://msdn.microsoft.com/it-it/library/windowsazure/ff795779.aspx
[5]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433010.aspx
[6]: http://www.windowsazure.com/it-it/develop/net/common-tasks/diagnostics/
[7]: http://manage.windowsazure.com/
[8]: http://msdn.microsoft.com/it-it/library/windowsazure/ee460798.aspx
[9]: http://msdn.microsoft.com/it-it/library/windowsazure/hh552718.aspx
[10]: http://www.windowsazure.com/it-it/support/legal/sla/