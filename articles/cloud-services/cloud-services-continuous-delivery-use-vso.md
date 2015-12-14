<properties
	pageTitle="Recapito continuo con Visual Studio Team Services in Azure"
	description="Informazioni sulla configurazione dei progetti team di Visual Studio Team Services per la compilazione e la distribuzione automatiche alla funzionalità App Web nel Servizio app di Azure o nei servizi cloud."
	services="cloud-services"
	documentationCenter=".net"
	authors="kempb"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="kempb"/>

# Recapito continuo in Azure tramite Visual Studio Team Services

È possibile configurare i progetti del proprio team di Visual Studio Team Services in modo da compilare ed eseguire automaticamente la distribuzione nelle app Web o nei servizi cloud di Azure. Per informazioni su come configurare un sistema di compilazione e distribuzione continua con un Team Foundation Server *locale*, vedere [Recapito continuo per Servizi cloud in Azure](cloud-services-dotnet-continuous-delivery.md).

In questa esercitazione si presuppone che l'utente abbia installato Visual Studio 2013 e Azure SDK. Se non si dispone ancora di Visual Studio 2013, scaricarlo scegliendo il collegamento **Inizia gratuitamente** all'indirizzo [www.visualstudio.com](http://www.visualstudio.com). Installare Azure SDK da [questa pagina](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account di Visual Studio Online:
> È possibile [aprire un account di Visual Studio Online gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Per configurare un servizio cloud da compilare e distribuire automaticamente in Azure tramite Visual Studio Team Services, seguire questa procedura.

## Passaggio 1: Creare un progetto team

Seguire le istruzioni [qui](http://go.microsoft.com/fwlink/?LinkId=512980) per creare il progetto team e collegarlo a Visual Studio. In questa procedura dettagliata si presume che si usi Controllo della versione di Team Foundation (TFVC) come soluzione di controllo del codice sorgente. Per usare Git per il controllo della versione, vedere [la versione per Git di questa procedura dettagliata](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## Passaggio 2: Archiviare un progetto nel controllo del codice sorgente

1. In Visual Studio, aprire la soluzione che si desidera distribuire o crearne una nuova.
È possibile distribuire un’app Web o un servizio cloud (applicazione Azure) seguendo i passaggi di questa procedura dettagliata.
Se si desidera creare una nuova soluzione, creare un nuovo progetto di servizio cloud di Azure o un nuovo progetto ASP.NET MVC. Assicurarsi che la destinazione del progetto sia .NET Framework 4 o 4.5 e, se si sta creando un progetto di servizio cloud, aggiungere un ruolo Web ASP.NET MVC e un ruolo di lavoro, quindi scegliere l'applicazione Internet per il ruolo Web.
Quando richiesto, scegliere **Applicazione Internet**.
Per creare un’app Web, scegliere il modello di progetto Applicazione Web ASP.NET e quindi scegliere MVC. Vedere [Creare un'app Web ASP.NET in Azure App Service](../web-sites-dotnet-get-started.md).

	> [AZURE.NOTE]Al momento, Visual Studio Team Services supporta solo le distribuzioni CI di applicazioni Web di Visual Studio. I progetti di sito Web sono esterni all'ambito.

1. Aprire il menu di scelta rapida relativo alla soluzione e selezionare **Aggiungi soluzione al controllo del codice sorgente**.

	![][5]

1. Accettare o modificare le impostazioni predefinite e quindi scegliere **OK**. Dopo il completamento del processo, in **Esplora soluzioni** vengono visualizzate le icone del controllo codice sorgente.

	![][6]

1. Aprire il menu di scelta rapida relativo alla soluzione e scegliere **Archivia**.

	![][7]

1. Nell'area **Modifiche in sospeso** di **Team Explorer** digitare un commento per l'archiviazione e scegliere il pulsante **Archivia**.

	![][8]

	Notare le opzioni per includere o escludere modifiche specifiche quando si esegue l'archiviazione. Se le modifiche desiderate sono escluse, scegliere il collegamento **Includi tutto**.

	![][9]

## Passaggio 3: Connettere il progetto ad Azure

1. A questo punto, dopo aver creato un progetto team VSTS contenente il codice sorgente, è possibile connettere il progetto team ad Azure. Nel [portale di Azure classico](http://manage.windowsazure.com) selezionare il servizio cloud o l'app Web, oppure crearne uno nuovo selezionando l'icona **+** in basso a sinistra e scegliendo **Servizio cloud** o **App Web** e quindi **Creazione rapida**. Scegliere il collegamento **Imposta pubblicazione con Visual Studio Team Services**.

	![][10]

1. Nella procedura guidata digitare il nome del proprio account di Visual Studio Team Services nella casella di testo e fare clic sul collegamento **Autorizza ora**. È possibile che venga richiesto di effettuare l'accesso.

	![][11]

1. Nella finestra di dialogo popup della **richiesta di connessione** scegliere il pulsante per **autorizzare** Azure a configurare il progetto team in VSTS.

	![][12]

1. Dopo aver concesso l'autorizzazione verrà visualizzato un elenco a discesa contenente un elenco dei progetti team di Visual Studio Team Services. Selezionare il nome del progetto team creato nei passaggi precedenti e quindi fare clic sul pulsante con il segno di spunta della procedura guidata.

	![][13]

1. Dopo aver collegato il progetto saranno visualizzate alcune istruzioni per l'archiviazione delle modifiche nel progetto team di Visual Studio Team Services. Alla successiva archiviazione, Visual Studio Team Services compilerà e distribuirà il progetto in Azure. Provare subito facendo clic sul collegamento per **archiviare da Visual Studio** quindi sul collegamento per l'**avvio di Visual Studio** (o sul pulsante equivalente di **Visual Studio** in basso nella schermata del portale).

	![][14]

## Passaggio 4: Attivare una ricompilazione e ridistribuire il progetto

1. In **Team Explorer** di Visual Studio fare clic sul collegamento **Esplora controllo codice sorgente**.

	![][15]

1. Passare al file della soluzione e aprirlo.

	![][16]

1. In **Esplora soluzioni** aprire un file e modificarlo. Ad esempio, modificare il file `_Layout.cshtml` nella cartella Views\\Shared in un ruolo Web MVC.

	![][17]

1. Modificare il logo del sito e premere **CTRL+S** per salvare il file.

	![][18]

1. In **Team Explorer** scegliere il collegamento **Modifiche in sospeso**.

	![][19]

1. Immettere un commento e quindi scegliere il pulsante **Archivia**.

	![][20]

1. Scegliere il pulsante **Home** per tornare alla pagina iniziale di **Team Explorer**.

	![][21]

1. Scegliere il collegamento **Compilazioni** per visualizzare le compilazioni in corso.

	![][22]

	**Team Explorer** mostra che è stata attivata una compilazione per l'archiviazione.

	![][23]

1. Fare doppio clic sul nome della compilazione in corso per visualizzare un log dettagliato con l'avanzare della compilazione.

	![][24]

1. Quando la compilazione è in corso, osservare la definizione di compilazione creata quando TFS è stato collegato ad Azure mediante la procedura guidata. Aprire il menu di scelta rapida relativo alla definizione di compilazione e scegliere **Modifica definizione di compilazione**.

	![][25]

	Nella scheda **Trigger** si osserva che, per impostazione predefinita, la definizione di compilazione è configurata per la compilazione a ogni archiviazione.

	![][26]

	Nella scheda **Processo** si osserva che l'ambiente di distribuzione è configurato con il nome del proprio servizio cloud o app Web. Se si lavora con le app Web, le proprietà visualizzate saranno diverse da quelle presenti nella sezione seguente.

	![][27]

1. Specificare i valori per le proprietà se si desidera che siano diversi da quelli predefiniti. Le proprietà per la pubblicazione in Azure sono nella sezione **Distribuzione**.

	La tabella seguente illustra le proprietà disponibili nella sezione **Distribuzione**:

	|Proprietà|Valore predefinito|
	|---|---|
	|Consenti certificati non attendibili|Se è false, i certificati SSL devono essere firmati da un'autorità radice.|
	|Consenti aggiornamento|Consente l'aggiornamento di una distribuzione esistente anziché crearne una nuova. Conserva l'indirizzo IP.|
	|Non eliminare|Se è true, una distribuzione non correlata esistente non viene sovrascritta (l'aggiornamento è consentito).|
	|Percorso impostazioni di distribuzione|Percorso del file con estensione pubxml di un’app Web, relativo alla cartella radice del repository. Viene ignorato per i servizi cloud.|
	|Ambiente di distribuzione SharePoint|Analogo al nome del servizio.|
	|Ambiente di distribuzione Azure|Nome dell'app Web o del servizio cloud.|

1. Se si usano più configurazioni di servizio (file .cscfg), è possibile specificare la configurazione del servizio desiderata nell'impostazione **Compila, Avanzate, Argomenti MSBuild**. Ad esempio, per usare ServiceConfiguration.Test.cscfg impostare l'opzione della riga degli argomenti di MSBuild `/p:TargetProfile=Test`.

	![][38]

	A questo punto la compilazione sarà stata completata correttamente.

	![][28]

1. Facendo doppio clic sul nome della compilazione, Visual Studio visualizza un **Riepilogo compilazione** che include eventuali risultati del test restituiti dai progetti unit test associati.

	![][29]

1. Nel [portale di Azure classico](http://manage.windowsazure.com) è possibile visualizzare la distribuzione associata nella scheda **Distribuzioni** quando si seleziona l'ambiente di gestione temporanea.

	![][30]

1.	Passare all'URL del proprio sito. Per un'app Web è sufficiente fare clic sul pulsante **Browse** della barra dei comandi. Per un servizio cloud, scegliere l'URL nella sezione **Quick Glance** della pagina **Dashboard** in cui è visualizzato l'ambiente di gestione temporanea per un servizio cloud. Le distribuzioni derivanti dall'integrazione continua per i servizi cloud vengono pubblicate nell'ambiente di gestione temporanea per impostazione predefinita. È possibile modificare questa impostazione configurando la proprietà **Ambiente del servizio cloud alternativo** su **Produzione**. La schermata seguente mostra dove si trova l'URL del sito nella pagina dashboard del servizio cloud.

	![][31]

	Il sito in esecuzione verrà aperto in una nuova scheda del browser.

	![][32]

	Per i servizi cloud, se si apportano altre modifiche al progetto, si attiveranno altre compilazioni e si accumuleranno più distribuzioni. L'ultima è contrassegnata come Attiva.

	![][33]

## Passaggio 5: Ridistribuire una compilazione precedente

Questo passaggio si applica ai servizi cloud ed è facoltativo. Nel portale di Azure classico selezionare una distribuzione precedente e fare clic sul pulsante **Ridistribuisci** per riportare il sito a un'archiviazione precedente. Si noti che verrà attivata una nuova compilazione in TFS e verrà creata una nuova voce nella cronologia della distribuzione.

![][34]

## Passaggio 6: Modificare la distribuzione di produzione.

Questo passaggio si applica solo ai servizi cloud, non alle app Web. Quando si è pronti, è possibile promuovere l'ambiente di gestione temporanea all'ambiente di produzione scegliendo il pulsante **Scambia** nel portale di Azure classico. L'ambiente di gestione temporanea appena distribuito verrà promosso alla produzione e il precedente ambiente di produzione (se presente) diventerà un ambiente di gestione temporanea. La distribuzione attiva potrebbe differire per gli ambienti di produzione e di gestione temporanea, ma la cronologia di distribuzione delle compilazioni recenti è la stessa indipendentemente dall'ambiente.

![][35]

## Passaggio 7: Eseguire unit test

Questo passaggio si applica solo alle app Web, non ai servizi cloud. Per inserire un controllo qualità nella distribuzione, è possibile eseguire alcuni unit test e, se non riescono, è possibile arrestare la distribuzione.

1.  In Visual Studio aggiungere un progetto di unit test.

	![][39]

1.  Aggiungere i riferimenti al progetto da testare.

	![][40]

1.  Aggiungere alcuni unit test. Per iniziare, provare un test fittizio che verrà sempre superato.

		```
		using System;
		using Microsoft.VisualStudio.TestTools.UnitTesting;

		namespace UnitTestProject1
		{
		    [TestClass]
		    public class UnitTest1
		    {
		        [TestMethod]
		        [ExpectedException(typeof(NotImplementedException))]
		        public void TestMethod1()
		        {
		            throw new NotImplementedException();
		        }
		    }
		}
		```

1.  Modificare la definizione di compilazione, scegliere la scheda **Processo** ed espandere il nodo **Test**.

1.  Impostare **Errore compilazione se test non superato** su True. Questo significa che la distribuzione non verrà eseguita se non dopo il superamento dei test.

	![][41]

1.  Inserire in coda una nuova compilazione.

	![][42]

	![][43]

1. Mentre la compilazione è in corso, controllarne l'avanzamento.

	![][44]

	![][45]

1. Al termine della compilazione, controllare i risultati del test.

	![][46]

	![][47]

1.  Provare a creare un test che non verrà superato. Aggiungere un nuovo test copiando il primo, rinominarlo e impostare come commento la riga di codice che indica che NotImplementedException è un'eccezione prevista.

		```
		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}
		```

1. Archiviare la modifica per inserire in coda una nuova compilazione.

	![][48]

1. Visualizzare i risultati del test per vedere i dettagli sull'errore.

	![][49]

	![][50]

## Passaggi successivi
Per altre informazioni sull'esecuzione di unit test in Visual Studio Team Services, vedere [Eseguire test nella compilazione](http://go.microsoft.com/fwlink/p/?LinkId=510474). Se si usa Git, vedere [Condividere il codice in Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [Distribuzione continua tramite GIT in Azure App Service](../web-sites-publish-source-control.md). Per altre informazioni su Visual Studio Team Services, vedere [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG

<!---HONumber=AcomDC_1203_2015-->