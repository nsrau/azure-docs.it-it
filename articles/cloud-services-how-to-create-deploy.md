<properties urlDisplayName="How to create and deploy" pageTitle="Come creare e distribuire un servizio Cloud - Azure" metaKeywords="Azure creating cloud service, deleting cloud service" description="Informazioni su come creare e distribuire un servizio cloud usando il metodo Creazione rapida in Azure." metaCanonical="" services="cloud-services" documentationCenter="" title="Come creare e distribuire un servizio Cloud" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="ryanwi" />

# Come creare e distribuire un servizio Cloud

Nel portale di gestione di Azure sono disponibili due modi per creare e distribuire un servizio cloud: **Quick Create** e **Custom Create**.

In questo argomento viene descritto come usare il metodo di creazione rapida di un nuovo servizio cloud e come caricare e distribuire un pacchetto del servizio cloud in Azure tramite l'opzione **Upload**. Quando si usa questo metodo, il portale di gestione di Azure rende disponibili comodi collegamenti per completare tutti i requisiti man mano che si procede. Se si è pronti per distribuire il servizio cloud durante la creazione, è possibile effettuare contemporaneamente entrambe le operazioni usando **Custom Create**.

**Nota** Se si prevede di pubblicare il servizio cloud da Windows Team Foundation Services (TFS), usare Quick Create e quindi configurare la pubblicazione TFS da **Quick Start** o dal dashboard. Per altre informazioni, vedere [Recapito continuo in Azure usando Visual Studio Online][Recapito continuo in Azure usando Visual Studio Online] o la Guida alla pagina **Avvio rapido**.

## Sommario

-   [Concetti][Concetti]
-   [Preparazione dell'app][Preparazione dell'app]
-   [Prima di iniziare][Prima di iniziare]
-   [Procedura: Creare un servizio cloud con Quick Create][Procedura: Creare un servizio cloud con Quick Create]
-   [Procedura: Caricare un certificato per un servizio cloud][Procedura: Caricare un certificato per un servizio cloud]
-   [Procedura: Distribuire un servizio cloud][Procedura: Distribuire un servizio cloud]

## <span id="concepts"></span></a>Concetti

Per distribuire un'applicazione come servizio cloud in Azure, sono necessari tre componenti:

> -   **file di definizione del servizio** Il file di definizione del servizio (.csdef) definisce il modello di servizio, compreso il numero di ruoli.

> -   **file di configurazione del servizio** Il file di configurazione del servizio cloud (.cscfg) specifica le impostazioni di configurazione per il servizio cloud e i singoli ruoli, incluso il numero di istanze del ruolo.

> -   **pacchetto del servizio** Il pacchetto di servizio (.cspkg) contiene il codice dell'applicazione e il file di definizione del servizio.

## <span id="prepare"></span></a>Preparazione dell'app

Per poter distribuire un servizio cloud, è necessario creare il pacchetto di servizio cloud (.cspkg) dal codice dell'applicazione e un file di configurazione del servizio cloud (.cscfg). Ogni pacchetto di servizio cloud contiene le configurazioni e i file dell'applicazione. Le impostazioni di configurazione sono incluse nel file di configurazione del servizio.

Azure SDK offre strumenti per la preparazione dei file necessari alla distribuzione. È possibile installare l'SDK dalla pagina di [download][download] di Azure, nel linguaggio in cui si preferisce sviluppare il codice dell'applicazione.

Per poter esportare un pacchetto di servizio, è necessario configurare tre funzionalità del servizio cloud:

-   Se si desidera distribuire un servizio cloud che usa SSL (Secure Sockets Layer) per la crittografia dei dati, configurare l'applicazione per SSL. Per altre informazioni, vedere [Come configurare un certificato SSL su un endpoint HTTPS][Come configurare un certificato SSL su un endpoint HTTPS].

-   Se si desidera configurare connessioni Desktop remoto a istanze del ruolo, configurare i ruoli per Desktop remoto. Per altre informazioni sulla preparazione del file di definizione del servizio di accesso remoto, vedere [Impostare una connessione Desktop remoto per un ruolo in Azure][Impostare una connessione Desktop remoto per un ruolo in Azure].

-   Se si desidera configurare il monitoraggio dettagliato per il servizio cloud, abilitare la Diagnostica Azure per il servizio cloud. *Monitoraggio minimo* (livello di monitoraggio predefinito) ricorre a contatori delle prestazioni raccolti dai sistemi operativi host per istanze del ruolo (macchine virtuali). "Monitoraggio dettagliato\* raccoglie metriche supplementari in base ai dati delle prestazioni all'interno delle istanze del ruolo per consentire un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione. Per scoprire come abilitare la Diagnostica Azure, vedere [Abilitazione della diagnostica in Azure][Abilitazione della diagnostica in Azure].

-   Per creare un servizio cloud con le distribuzioni dei ruoli Web o dei ruoli di lavoro è necessario creare il pacchetto del servizio. Per informazioni sui file relativi al pacchetto vedere [Impostare un servizio cloud per Azure][Impostare un servizio cloud per Azure]. Per creare il file del pacchetto vedere [Pacchetto applicazione Azure][Pacchetto applicazione Azure]. Se si usa Visual Studio per sviluppare l'applicazione, vedere [Pubblicazione di un servizio cloud con gli strumenti di Azure][Pubblicazione di un servizio cloud con gli strumenti di Azure].

## <span id="begin"></span></a>Prima di iniziare

-   Se Azure SDK non è stato installato, fare clic su **Install Azure SDK** per aprire la pagina di [download][download] di Azure e quindi scaricare l'SDK nel linguaggio in cui si preferisce sviluppare il codice. (È possibile eseguire questa operazione in seguito).

-   Se un'istanza del ruolo lo richiede, creare i certificati. I servizi cloud richiedono un file con estensione pfx con una chiave privata. È possibile caricare i certificati in Azure nel corso della creazione e della distribuzione del servizio cloud. Per informazioni sui certificati, vedere [Gestione certificati][Gestione certificati].

-   Se si prevede di distribuire il servizio cloud a un gruppo di affinità, creare il gruppo di affinità. Per distribuire il servizio cloud e altri servizi di Azure alla stessa posizione all'interno di una regione, è possibile usare un gruppo di affinità. È possibile creare il gruppo di affinità nell'area **Reti** del portale di gestione, alla pagina **Gruppi di affinità**. Per altre informazioni, vedere [Creare un gruppo di affinità nel portale di gestione][Creare un gruppo di affinità nel portale di gestione].

## <span id="quick"></span></a>Procedura: Creare un servizio cloud con Quick Create

1.  Nel [portale di gestione][portale di gestione] fare clic su **Nuovo**\>**Calcolo**\>**Servizio cloud**\>**Creazione rapida**.

    ![CloudServices\_QuickCreate][CloudServices\_QuickCreate]

2.  Nel campo **URL**, immettere un nome di sottodominio da usare nell'URL pubblico per accedere al servizio cloud nelle distribuzioni di produzione. Il formato dell'URL per le distribuzioni di produzione è: http://*myURL*.cloudapp.net.

3.  In **Regione o gruppo di affinità** selezionare l'area geografica o il gruppo di affinità a cui distribuire il servizio cloud. Per distribuire il servizio cloud alla stessa posizione di altri servizi di Azure all'interno di una regione, selezionare un gruppo di affinità.

4.  Fare clic su **Create Cloud Service**.

    ![CloudServices\_Region][CloudServices\_Region]

    È possibile monitorare lo stato di elaborazione nell'area dei messaggi, nella parte inferiore della finestra.

    Viene visualizzata l'area **Cloud Services**, con il nuovo servizio cloud. Lo stato Created indica che la creazione di servizi cloud è stata completata correttamente.

    ![CloudServices\_CloudServicesPage][CloudServices\_CloudServicesPage]

## <span id="uploadcertificate"></span></a>Procedura: Caricare un certificato per un servizio cloud

1.  Nel [portale di gestione][portale di gestione] fare clic su **Servizi cloud**, quindi sul nome del servizio cloud e infine su **Configura**.

    ![CloudServices\_QuickCreate][1]

2.  Fare clic su **Carica un certificato** o su **Carica**.

3.  Nel campo **File** usare **Sfoglia** per selezionare il certificato (file con estensione pfx).

4.  Nel campo **Password** immettere la chiave privata del certificato.

5.  Fare clic su **OK** (segno di spunta).

    ![CloudServices\_AddaCertificate][CloudServices\_AddaCertificate]

    È possibile seguire l'avanzamento del caricamento nell'area dei messaggi, illustrata di seguito. Terminato il caricamento, il certificato viene aggiunto alla tabella. Nell'area dei messaggi fare clic su OK per chiudere il messaggio.

    ![CloudServices\_CertificateProgress][CloudServices\_CertificateProgress]

## <span id="deploy"></span></a>Procedura: Distribuire un servizio cloud

1.  Nel [portale di gestione][portale di gestione] fare clic su **Servizi cloud**, quindi sul nome del servizio cloud e infine su **Dashboard**.

    Viene visualizzato il dashboard nell'ambiente di produzione. A questo punto è possibile selezionare Gestione temporanea per distribuire l'applicazione nell'ambiente di gestione temporanea. Per altre informazioni, vedere [Gestire le distribuzioni in Azure][Gestire le distribuzioni in Azure].

2.  Fare clic su **Carica una nuova distribuzione di produzione** oppure su **Carica**.

3.  Nel campo **Etichetta distribuzione** immettere un nome per la nuova distribuzione, ad esempio, MyCloudServicev4.

4.  In **Package** usare **Browse** per selezionare il file del pacchetto di servizio (.cspkg) da usare.

5.  In **Configuration** usare **Browse** per selezionare il file di configurazione del servizio (.cscfg) da usare.

6.  Se il servizio cloud includerà ruoli con una sola istanza, selezionare la casella di controllo **Distribuisci anche se uno o più ruoli contengono una singola istanza** per abilitare la distribuzione.

Se ogni ruolo ha almeno due istanze, Azure può garantire l'accesso al servizio cloud solo al 99,95% in fase di manutenzione e aggiornamento del servizio. Se necessario, è possibile aggiungere istanze del ruolo aggiuntive alla pagina **Scale** dopo aver distribuito il servizio cloud. Per altre informazioni, vedere [Contratti di servizio][Contratti di servizio].

1.  Fare clic su **OK** (segno di spunta) per iniziare la distribuzione del servizio cloud.

    ![CloudServices\_UploadaPackage][CloudServices\_UploadaPackage]

    È possibile monitorare lo stato della distribuzione nell'area dei messaggi. Fare clic su OK per nascondere il messaggio.

    ![CloudServices\_UploadProgress][CloudServices\_UploadProgress]

### Per verificare che la distribuzione sia stata completata correttamente

1.  Fare clic su **Dashboard**.

    Lo stato del servizio dovrebbe ora essere **In esecuzione**.

2.  In **riepilogo rapido** fare clic sull'URL del sito per aprire il servizio cloud in un Web browser.

<!-- -->

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)

  [Recapito continuo in Azure usando Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
  [Concetti]: #concepts
  [Preparazione dell'app]: #prepare
  [Prima di iniziare]: #begin
  [Procedura: Creare un servizio cloud con Quick Create]: #quick
  [Procedura: Caricare un certificato per un servizio cloud]: #uploadcertificate
  [Procedura: Distribuire un servizio cloud]: #deploy
  [download]: http://www.windowsazure.com/it-it/develop/downloads/
  [Come configurare un certificato SSL su un endpoint HTTPS]: http://msdn.microsoft.com/it-it/library/windowsazure/ff795779.aspx
  [Impostare una connessione Desktop remoto per un ruolo in Azure]: http://msdn.microsoft.com/it-it/library/hh124107.aspx
  [Abilitazione della diagnostica in Azure]: http://www.windowsazure.com/it-it/develop/net/common-tasks/diagnostics/
  [Impostare un servizio cloud per Azure]: http://msdn.microsoft.com/it-it/library/hh124108.aspx
  [Pacchetto applicazione Azure]: http://msdn.microsoft.com/it-it/library/hh403979.aspx
  [Pubblicazione di un servizio cloud con gli strumenti di Azure]: http://msdn.microsoft.com/it-it/library/ff683672.aspx
  [Gestione certificati]: http://msdn.microsoft.com/it-it/library/gg981929.aspx
  [Creare un gruppo di affinità nel portale di gestione]: http://msdn.microsoft.com/it-it/library/jj156209.aspx
  [portale di gestione]: http://manage.windowsazure.com/
  [CloudServices\_QuickCreate]: ./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png
  [CloudServices\_Region]: ./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png
  [CloudServices\_CloudServicesPage]: ./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png
  [1]: ./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png
  [CloudServices\_AddaCertificate]: ./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png
  [CloudServices\_CertificateProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png
  [Gestire le distribuzioni in Azure]: http://msdn.microsoft.com/it-it/library/gg433027.aspx
  [Contratti di servizio]: http://www.windowsazure.com/it-it/support/legal/sla/
  [CloudServices\_UploadaPackage]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png
  [CloudServices\_UploadProgress]: ./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png
