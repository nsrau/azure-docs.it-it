<properties
	pageTitle="Come creare e distribuire un servizio Cloud | Microsoft Azure"
	description="Informazioni su come creare e distribuire un servizio cloud usando il metodo Creazione rapida in Azure."
	services="cloud-services"
	documentationCenter=""
	authors="Thraka"
	manager="timlt"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="adegeo"/>




# Come creare e distribuire un servizio Cloud

> [AZURE.SELECTOR]
- [Azure Portal](cloud-services-how-to-create-deploy.md)
- [Azure Preview Portal](cloud-services-how-to-create-deploy-portal.md)

Nel portale di Azure sono disponibili due modi per creare e distribuire un servizio cloud: **Creazione rapida** e **Creazione personalizzata**.

In questo argomento viene descritto come usare il metodo di creazione rapida di un nuovo servizio cloud e come caricare e distribuire un pacchetto del servizio cloud in Azure tramite l'opzione **Carica**. Quando si usa questo metodo, il portale di Azure rende disponibili comodi collegamenti per completare tutti i requisiti man mano che si procede. Se si è pronti per distribuire il servizio cloud durante la creazione, è possibile effettuare contemporaneamente entrambe le operazioni usando **Creazione personalizzata**.

> [AZURE.NOTE]Se si prevede di pubblicare il servizio cloud da Visual Studio Team Services (VSTS), usare Creazione rapida, quindi configurare la pubblicazione VSTS da **Avvio rapido** o dal dashboard. Per ulteriori informazioni, vedere [Recapito continuo in Azure mediante Visual Studio Team Services][TFSTutorialForCloudService] o la Guida alla pagina **Avvio rapido**.

## Concetti
Per distribuire un'applicazione come servizio cloud in Azure, sono necessari tre componenti:

- **Definizione del servizio** Il file di definizione del servizio cloud (con estensione csdef) definisce il modello di servizio, compreso il numero di ruoli.

- **Configurazione del servizio** l file di configurazione del servizio cloud (con estensione cscfg) specifica le impostazioni di configurazione per il servizio cloud e i singoli ruoli, incluso il numero di istanze del ruolo.

- **Pacchetto del servizio** Il pacchetto del servizio (con estensione cspkg) contiene il codice dell'applicazione, le configurazioni e il file di definizione del servizio.
  
Per altre informazioni in proposito e su come creare un pacchetto, fare clic [qui](cloud-services-model-and-package.md).

## Preparare l'app
Per poter distribuire un servizio cloud, è necessario creare il pacchetto di servizio cloud (.cspkg) dal codice dell'applicazione e un file di configurazione del servizio cloud (.cscfg). Azure SDK offre strumenti per la preparazione dei file necessari alla distribuzione. È possibile installare l'SDK dalla pagina di [download](http://azure.microsoft.com/downloads/) di Azure, nel linguaggio in cui si preferisce sviluppare il codice dell'applicazione.

Per poter esportare un pacchetto di servizio, è necessario configurare tre funzionalità del servizio cloud:

- Se si desidera distribuire un servizio cloud che usa SSL (Secure Sockets Layer) per la crittografia dei dati, configurare l'applicazione per SSL. Per altre informazioni, vedere [Come configurare un certificato SSL su un endpoint HTTPS](http://msdn.microsoft.com/library/azure/ff795779.aspx).

- Se si desidera configurare connessioni Desktop remoto a istanze del ruolo, configurare i ruoli per Desktop remoto. Per altre informazioni sulla preparazione del file di definizione del servizio di accesso remoto, vedere [Impostare una connessione Desktop remoto per un ruolo in Azure](http://msdn.microsoft.com/library/hh124107.aspx).

- Se si desidera configurare il monitoraggio dettagliato per il servizio cloud, abilitare la Diagnostica Azure per il servizio cloud. *Monitoraggio minimo* (livello di monitoraggio predefinito) ricorre a contatori delle prestazioni raccolti dai sistemi operativi host per istanze del ruolo (macchine virtuali). "Monitoraggio dettagliato* raccoglie metriche supplementari in base ai dati delle prestazioni all'interno delle istanze del ruolo per consentire un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione. Per scoprire come abilitare la Diagnostica Azure, vedere [Abilitazione della diagnostica in Azure](cloud-services-dotnet-diagnostics.md).

- Per creare un servizio cloud con le distribuzioni dei ruoli Web o dei ruoli di lavoro è necessario creare il pacchetto del servizio. Per informazioni sui file relativi al pacchetto vedere [Impostare un servizio cloud per Azure](http://msdn.microsoft.com/library/hh124108.aspx). Per creare il file del pacchetto vedere [Pacchetto di applicazione Azure](http://msdn.microsoft.com/library/hh403979.aspx). Se si usa Visual Studio per sviluppare l'applicazione, vedere [Pubblicazione di un servizio cloud con gli strumenti di Azure](http://msdn.microsoft.com/library/ff683672.aspx).

## Prima di iniziare

- Se Azure SDK non è stato installato, fare clic su **Install Azure SDK** per aprire la pagina di [download](http://azure.microsoft.com/downloads/) di Azure e quindi scaricare l'SDK nel linguaggio in cui si preferisce sviluppare il codice. (È possibile eseguire questa operazione in seguito).

- Se un'istanza del ruolo lo richiede, creare i certificati. I servizi cloud richiedono un file con estensione pfx con una chiave privata. È possibile caricare i certificati in Azure nel corso della creazione e della distribuzione del servizio cloud. Per informazioni sui certificati, vedere [Gestione certificati](http://msdn.microsoft.com/library/gg981929.aspx).

- Se si prevede di distribuire il servizio cloud a un gruppo di affinità, creare il gruppo di affinità. Per distribuire il servizio cloud e altri servizi di Azure alla stessa posizione all'interno di un'area, è possibile usare un gruppo di affinità. È possibile creare il gruppo di affinità nell'area **Reti** del portale di gestione, alla pagina **Gruppi di affinità**. Per altre informazioni, vedere [Creare un gruppo di affinità nel portale di gestione](http://msdn.microsoft.com/library/jj156209.aspx).


## Procedura: Creare un servizio cloud con Creazione rapida

1. Nel [portale di gestione](http://manage.windowsazure.com/) fare clic su **Nuovo**>**Calcolo**>**Servizio cloud**>**Creazione rapida**.

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. Nel campo **URL**, immettere un nome di sottodominio da usare nell'URL pubblico per accedere al servizio cloud nelle distribuzioni di produzione. Il formato dell'URL per le distribuzioni di produzione è: http://*myURL*.cloudapp.net.

3. In **Regione o gruppo di affinità** selezionare l'area geografica o il gruppo di affinità a cui distribuire il servizio cloud. Per distribuire il servizio cloud alla stessa posizione di altri servizi di Azure all'interno di un'area, selezionare un gruppo di affinità.

4. Fare clic su **Crea servizio cloud**.

	![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

	È possibile monitorare lo stato di elaborazione nell'area dei messaggi, nella parte inferiore della finestra.

	Viene visualizzata l'area **Servizi cloud**, con il nuovo servizio cloud. Lo stato Creato indica che la creazione di servizi cloud è stata completata correttamente.

	![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


## Procedura: Caricare un certificato per un servizio cloud

1. Nel [portale di gestione](http://manage.windowsazure.com/) fare clic su **Servizi cloud**, quindi sul nome del servizio cloud e infine su **Configura**.

	![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. Fare clic su **Carica un certificato** o su **Carica**.

3. Nel campo **File** usare **Sfoglia** per selezionare il certificato (file con estensione pfx).

4. Nel campo **Password** immettere la chiave privata del certificato.

5. Fare clic su **OK** (segno di spunta).

	![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

	È possibile seguire l'avanzamento del caricamento nell'area dei messaggi, illustrata di seguito. Terminato il caricamento, il certificato viene aggiunto alla tabella. Nell'area dei messaggi fare clic su OK per chiudere il messaggio.

	![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## Procedura: Distribuire un servizio cloud

1. Nel [portale di gestione](http://manage.windowsazure.com/) fare clic su **Servizi cloud**, quindi sul nome del servizio cloud e infine su **Dashboard**.

	Viene visualizzato il dashboard nell'ambiente di produzione. A questo punto è possibile selezionare Gestione temporanea per distribuire l'applicazione nell'ambiente di gestione temporanea. Per altre informazioni, vedere [Gestire le distribuzioni in Azure](http://msdn.microsoft.com/library/gg433027.aspx).


2. Fare clic su **Carica una nuova distribuzione di produzione** oppure su **Carica**.

3. Nel campo **Etichetta distribuzione** immettere un nome per la nuova distribuzione, ad esempio, MyCloudServicev4.

3. In **Package** usare **Browse** per selezionare il file del pacchetto di servizio (.cspkg) da usare.

4. In **Configuration** usare **Browse** per selezionare il file di configurazione del servizio (.cscfg) da usare.

5. Se il servizio cloud includerà ruoli con una sola istanza, selezionare la casella di controllo **Deploy even if one or more roles contain a single instance** per abilitare la distribuzione.

    Se ogni ruolo ha almeno due istanze, Azure può garantire l'accesso al servizio cloud solo al 99,95% in fase di manutenzione e aggiornamento del servizio. Se necessario, è possibile aggiungere istanze del ruolo aggiuntive alla pagina **Scale** dopo aver distribuito il servizio cloud. Per altre informazioni, vedere [Contratti di servizio](http://azure.microsoft.com/support/legal/sla/).

6. Fare clic su **OK** (segno di spunta) per iniziare la distribuzione del servizio cloud.

	![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

	È possibile monitorare lo stato della distribuzione nell'area dei messaggi. Fare clic su OK per nascondere il messaggio.

	![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## Verificare che la distribuzione sia stata completata correttamente

1. Fare clic su **Dashboard**.

	Lo stato del servizio dovrebbe ora essere **In esecuzione**.

2. In **quick glance** fare clic sull'URL del sito per aprire il servizio cloud in un Web browser.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796&clcid=0x409
 
## Passaggi successivi

* [Configurazione generale del servizio cloud](cloud-services-how-to-configure.md).
* Configurare un [nome di dominio personalizzato](cloud-services-custom-domain-name.md).
* [Gestire il servizio cloud](cloud-services-how-to-manage.md).
* Configurare i [certificati ssl](cloud-services-configure-ssl-certificate.md).

<!---HONumber=Nov15_HO4-->