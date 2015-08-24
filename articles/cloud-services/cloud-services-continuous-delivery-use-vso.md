<properties
	pageTitle="Recapito continuo con Visual Studio Online in Azure"
	description="Informazioni sulla configurazione dei progetti team di Visual Studio Online per poter compilare ed eseguire automaticamente la distribuzione nella funzione App Web in Azure App Service o nei servizi cloud."
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
	ms.date="08/07/2015" 
	ms.author="kempb"/>


# Recapito continuo in Azure con Visual Studio Online

  È possibile configurare i progetti del proprio team di Visual Studio Online in modo da compilare ed eseguire automaticamente la distribuzione nelle app Web o nei servizi cloud di Azure. Per informazioni su come configurare un sistema di compilazione e distribuzione continua con un Team Foundation Server *locale*, vedere [Recapito continuo per Servizi cloud in Azure](cloud-services-dotnet-continuous-delivery.md).

In questa esercitazione si presuppone che l'utente abbia installato Visual Studio 2013 e Azure SDK. Se non si dispone ancora di Visual Studio 2013, scaricarlo scegliendo il collegamento **Inizia gratuitamente** all'indirizzo [www.visualstudio.com](http://www.visualstudio.com). Installare Azure SDK da [questa pagina](http://go.microsoft.com/fwlink/?LinkId=239540).

> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account di Visual Studio Online:
> È possibile [aprire un account di Visual Studio Online gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Per configurare un servizio cloud in modo da compilare e distribuire automaticamente in Azure mediante Visual Studio Online, eseguire la procedura seguente:

-   [Passaggio 1: Creare un progetto team.][]

-   [Passaggio 2: Archiviare un progetto nel controllo del codice sorgente.][]

-   [Passaggio 3: Connettere il progetto ad Azure.][]

-   [Passaggio 4: Modificare e attivare una ricompilazione e una ridistribuzione.][]

-   [Passaggio 5: Ridistribuire una compilazione precedente (facoltativo).][]

-   [Passaggio 6: Modificare la distribuzione di produzione (solo servizi cloud).][]

-	[Passaggio 7: Eseguire unit test (facoltativo)][]

## <a name="step1"></a>Passaggio 1: Creare un progetto team

Seguire le istruzioni [qui](http://go.microsoft.com/fwlink/?LinkId=512980) per creare il progetto team e collegarlo a Visual Studio. In questa procedura dettagliata si presume che si usi Controllo della versione di Team Foundation (TFVC) come soluzione di controllo del codice sorgente. Per usare Git per il controllo della versione, vedere [la versione per Git di questa procedura dettagliata](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="step2"> </a>Passaggio 2: Archiviare un progetto nel controllo del codice sorgente

1. In Visual Studio, aprire la soluzione che si desidera distribuire o crearne una nuova.
È possibile distribuire un’app Web o un servizio cloud (applicazione Azure) seguendo i passaggi di questa procedura dettagliata.
Se si desidera creare una nuova soluzione, creare un nuovo progetto di servizio cloud di Azure o un nuovo progetto ASP.NET MVC. Assicurarsi che la destinazione del progetto sia .NET Framework 4 o 4.5 e, se si sta creando un progetto di servizio cloud, aggiungere un ruolo Web ASP.NET MVC e un ruolo di lavoro, quindi scegliere l'applicazione Internet per il ruolo Web.
Quando richiesto, scegliere **Applicazione Internet**.
Per creare un’app Web, scegliere il modello di progetto Applicazione Web ASP.NET e quindi scegliere MVC. Vedere [Creare un'app Web ASP.NET in Azure App Service](../web-sites-dotnet-get-started.md).

> [AZURE.NOTE]Al momento, Visual Studio Online supporta solo le distribuzioni CI di applicazioni Web di Visual Studio. I progetti di sito Web sono esterni all'ambito.


2. Aprire il menu di scelta rapida relativo alla soluzione e selezionare **Aggiungi soluzione al controllo del codice sorgente**.<br/>
![][5]

3. Accettare o modificare le impostazioni predefinite e quindi scegliere **OK**. Al completamento del processo, in Esplora soluzioni vengono visualizzate le icone del controllo codice sorgente.<br/>
![][6]

4. Aprire il menu di scelta rapida relativo alla soluzione e scegliere **Archivia**.<br/>
![][7]

5. Nell'area Modifiche in sospeso di Team Explorer digitare un commento per l'archiviazione e scegliere il pulsante **Archivia**.<br/>
![][8]

<br/>
Si notino le opzioni per includere o escludere modifiche specifiche quando si esegue l'archiviazione. Se le modifiche desiderate sono escluse, scegliere il collegamento **Includi tutto**.<br/>
![][9]

## <a name="step3"> </a>Passaggio 3: Connettere il progetto ad Azure

1. A questo punto, dopo aver creato un progetto team VSO contenente il codice sorgente, è possibile connettere il progetto team ad Azure. Nel [portale di Azure](http://manage.windowsazure.com), selezionare il servizio cloud o l’app Web oppure crearne uno nuovo selezionando l'icona + in basso a sinistra e scegliendo **Cloud Service** o **Web App**, quindi selezionare **Quick Create**. Scegliere il collegamento **Imposta pubblicazione con Visual Studio Online**.<br/>
![][10]

2. Nella procedura guidata, digitare il nome del proprio account di Visual Studio Online nella casella di testo e fare clic sul collegamento **Authorize Now**. È possibile che venga richiesto di eseguire l'accesso.<br/>
![][11]

3. Nella finestra di dialogo popup OAuth scegliere **Accetta** per autorizzare Azure a configurare il progetto team in Visual Studio Online.<br/>
![][12]

4. Dopo aver concesso l'autorizzazione verrà visualizzato un elenco a discesa contenente un elenco dei progetti team di Visual Studio Online. Selezionare il nome del progetto team creato nei passaggi precedenti e fare clic sul pulsante con il segno di spunta della procedura guidata.<br/>
![][13]

5. Quando il progetto sarà stato collegato, saranno visualizzate alcune istruzioni per l'archiviazione delle modifiche nel progetto team di Visual Studio Online. Alla successiva archiviazione, Visual Studio Online compilerà e distribuirà il progetto in Azure. Effettuare subito una prova facendo clic sul collegamento **Archivia da Visual Studio** quindi sul collegamento **Avviare Visual Studio** (o sul pulsante equivalente di **Visual Studio** in basso nella schermata del portale).<br/>
![][14]

## <a name="step4"> </a>Passaggio 4: Attivare una ricompilazione e ridistribuire il progetto

1. In Team Explorer di Visual Studio fare clic sul collegamento **Esplora controllo codice sorgente**.<br/>
![][15]

2. Passare al file della soluzione e aprirlo.<br/>
![][16]

3. In Esplora soluzioni aprire un e apportarvi delle modifiche. Ad esempio, modificare il file _Layout.cshtml nella cartella Views\Shared di un ruolo Web MVC.<br/>
![][17]

4. Modificare il logo del sito e premere CTRL+S per salvare il file.<br/>
![][18]

5. In Team Explorer scegliere il collegamento **Modifiche in sospeso**.<br/>
![][19]

6. Digitare un commento e scegliere il pulsante **Archivia**.<br/>
![][20]

7. Scegliere il pulsante Home per tornare alla pagina iniziale di Team Explorer.<br/>
![][21]

8. Scegliere il collegamento **Compilazioni** per visualizzare le compilazioni in corso.<br/>
![][22]
<br/>
In Team Explorer è possibile osservare che è stata attivata una compilazione per la propria archiviazione..<br/>
![][23]

9. Fare doppio clic sul nome della compilazione in corso per visualizzare un log dettagliato con l'avanzare della compilazione.<br/>
![][24]

10. Quando la compilazione è in corso, osservare la definizione di compilazione creata quando TFS è stato collegato ad Azure mediante la procedura guidata. Aprire il menu di scelta rapida relativo alla definizione di compilazione e scegliere **Modifica definizione di compilazione**.<br/>
![][25]
<br/>
Nella scheda **Trigger** è possibile osservare che, per impostazione predefinita, la definizione di compilazione è configurata per la compilazione a ogni archiviazione.<br/>
![][26]
<br/>
Nella scheda **Processo** è possibile osservare che l'ambiente di distribuzione è configurato con il nome del proprio servizio cloud o app Web. Se si lavora con le app Web, le proprietà visualizzate saranno diverse da quelle presenti nella sezione riportata di seguito.<br/>
![][27]
<br/>
Specificare i valori per le proprietà se si desidera che siano diversi da quelli predefiniti. Le proprietà per la pubblicazione in Azure sono nella sezione Distribuzione.
La tabella seguente illustra le proprietà disponibili nella sezione Distribuzione:
	<table>
<tr><td><b>Proprietà</b></td><td><b>Valore predefinito</b></td></tr>
><tr><td>Consenti certificati non attendibili</td><td>Se è false, i certificati SSL devono essere firmati da un'autorità radice.</td></tr>
<tr><td>Consenti aggiornamento</td><td>Consente l'aggiornamento di una distribuzione esistente anziché crearne una nuova. Conserva l'indirizzo IP.</td></tr>
><tr><td>Non eliminare</td><td>Se è true, una distribuzione non correlata esistente non viene sovrascritta (l'aggiornamento è consentito).</td></tr>
<tr><td>Percorso impostazioni di distribuzione</td><td>Percorso del file con estensione pubxml di un’app Web, relativo alla cartella radice del repository. Viene ignorato per i servizi cloud.</td></tr>
<tr><td>Ambiente di distribuzione SharePoint</td><td>Analoga al nome del servizio</td></tr>
<tr><td>Ambiente di distribuzione Azure</td><td>Nome dell’app Web o del servizio cloud</td></tr>
</table>
<br/>

Se si usano più configurazioni di servizio (file .cscfg), è possibile specificare la configurazione del servizio desiderata nell'impostazione **Compila, Avanzate, Argomenti MSBuild**. Ad esempio, per usare ServiceConfiguration.Test.cscfg, impostare l'opzione della riga degli argomenti di MSBuild /p:TargetProfile=Test.<br/>
![][38]

11. A questo punto la compilazione sarà stata completata correttamente.<br/>
![][28]

12. Facendo doppio clic sul nome della compilazione, in Visual Studio verrà visualizzato un **Riepilogo compilazione** che include eventuali risultati del test restituiti dai progetti unit test associati.<br/>
![][29]

13. Nel [portale di Azure](http://manage.windowsazure.com) è possibile visualizzare la distribuzione associata nella scheda Distribuzioni quando si seleziona l'ambiente di gestione temporanea.<br/>
![][30]

14.	Passare all'URL del proprio sito. Per un’app Web basta fare clic sul pulsante Browse della barra dei comandi. Per un servizio cloud, scegliere l'URL nella sezione **Quick Glance** della pagina **Dashboard** in cui è visualizzato l'ambiente di gestione temporanea per un servizio cloud. Le distribuzioni derivanti dall'integrazione continua per i servizi cloud vengono pubblicate nell'ambiente di gestione temporanea per impostazione predefinita. È possibile modificare questa impostazione configurando la proprietà Ambiente del servizio cloud alternativo su Produzione. La schermata mostra dove si trova l'URL del sito nella pagina del dashboard del servizio cloud: <br/>
![][31]
<br/>
Il sito in esecuzione verrà aperto in una nuova scheda del browser.<br/>
![][32]

15.	Per i servizi cloud, se si apportano altre modifiche al progetto, si attiveranno altre compilazioni e si accumuleranno più distribuzioni. L'ultima è contrassegnata come Attiva.<br/>
![][33]

## <a name="step5"> </a>Passaggio 5: Ridistribuire una compilazione precedente

Questo passaggio si applica ai servizi cloud ed è facoltativo. Nel portale di gestione selezionare una distribuzione precedente e fare clic sul pulsante **Ridistribuisci** per riportare il sito a un'archiviazione precedente. Si noti che verrà attivata una nuova compilazione in TFS e verrà creata una nuova voce nella cronologia della distribuzione.<br/>
![][34]

## <a name="step6"> </a>Passaggio 6: Modificare la distribuzione di produzione

Questo passaggio si applica solo ai servizi cloud, non alle app Web. Quando si è pronti, è possibile promuovere l'ambiente di gestione temporanea all'ambiente di produzione scegliendo il pulsante Swap nel portale di gestione. L'ambiente di gestione temporanea appena distribuito verrà promosso alla produzione e il precedente ambiente di produzione (se presente) diventerà un ambiente di gestione temporanea. La distribuzione attiva potrebbe differire per gli ambienti di produzione e di gestione temporanea, ma la cronologia di distribuzione delle recenti compilazioni è la stessa indipendentemente dall'ambiente.<br/>
![][35]

## <a name="step7"> </a>Passaggio 7: Eseguire unit test

Questo passaggio si applica solo alle app Web, non ai servizi cloud. Per inserire un controllo qualità nella distribuzione, è possibile eseguire alcuni unit test e, se non riescono, è possibile arrestare la distribuzione.

1.  In Visual Studio aggiungere un progetto di unit test.<br/>
![][39]

2.  Aggiungere i riferimenti al progetto da testare.<br/>
![][40]

3.  Aggiungere alcuni unit test. Per iniziare, provare un test fittizio che verrà sempre superato.

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

4.  Modificare la definizione di compilazione, scegliere la scheda Processo ed espandere il nodo Test.


5.  Impostare **Errore compilazione se test non superato** su True. Questo significa che la distribuzione non verrà eseguita se non dopo il superamento dei test.<br/>
![][41]

6.  Inserire in coda una nuova compilazione.<br/>
![][42]
<br/>
![][43]

7. Mentre la compilazione è in corso, controllarne l'avanzamento.<br/>
![][44]
<br/>
![][45]

7. Al termine della compilazione, controllare i risultati del test.<br/>
![][46]
<br/>
![][47]

8.  Provare a creare un test che non verrà superato. Aggiungere un nuovo test copiando il primo, rinominarlo e impostare come commento la riga di codice che indica che NotImplementedException è un'eccezione prevista.

		[TestMethod]
		//[ExpectedException(typeof(NotImplementedException))]
		public void TestMethod2()
		{
		    throw new NotImplementedException();
		}

9. Archiviare la modifica per inserire in coda una nuova compilazione.<br/>
![][48]

10. Visualizzare i risultati del test per vedere i dettagli sull'errore.<br/>
![][49]
<br/>
![][50]

Per altre informazioni sull'esecuzione di unit test in Visual Studio Online, vedere [Eseguire test nella compilazione](http://go.microsoft.com/fwlink/p/?LinkId=510474).

Per altre informazioni, vedere [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Se si usa Git, vedere [Condividere il codice in Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [Distribuzione continua tramite GIT in Azure App Service](../web-sites-publish-source-control.md).

[Passaggio 1: Creare un progetto team.]: #step1
[Passaggio 2: Archiviare un progetto nel controllo del codice sorgente.]: #step2
[Passaggio 3: Connettere il progetto ad Azure.]: #step3
[Passaggio 4: Modificare e attivare una ricompilazione e una ridistribuzione.]: #step4
[Passaggio 5: Ridistribuire una compilazione precedente (facoltativo).]: #step5
[Passaggio 6: Modificare la distribuzione di produzione (solo servizi cloud).]: #step6
[Passaggio 7: Eseguire unit test (facoltativo)]: #step7
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

<!---HONumber=August15_HO7-->