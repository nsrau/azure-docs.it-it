<properties linkid="dev-net-common-tasks-publishing-with-vso" urlDisplayName="Publishing with Visual Studio Online" pageTitle="Continuous delivery with Visual Studio Online in Azure" metaKeywords="" description="Learn how to configure your Visual Studio Online team projects to automatically build and deploy to Azure websites or cloud services." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Continuous delivery to Azure using Visual Studio Online" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Recapito continuo in Azure mediante Visual Studio Online

È possibile configurare i progetti del proprio team di Visual Studio Online in modo da compilare ed eseguire automaticamente la distribuzione sui siti Web o sui servizi cloud di Azure. Per informazioni su come configurare un sistema di compilazione e distribuzione continua mediante un Team Foundation Server *locale*, vedere [Recapito continuo per Servizi cloud in Azure][Recapito continuo per Servizi cloud in Azure].

In questa esercitazione si presuppone che l'utente abbia installato Visual Studio 2013 e Azure SDK. Se non si dispone ancora di Visual Studio 2013, scaricarlo scegliendo il collegamento **Inizia gratuitamente** all'indirizzo [www.visualstudio.com][www.visualstudio.com]. Installare Azure SDK da [questa pagina][questa pagina].

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Per completare l'esercitazione, &egrave; necessario un account di Visual Studio Online.</h5>
<p>&Egrave; possibile aprire gratuitamente un account di Visual Studio Online nella pagina <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">Connettersi a Visual Studio Online</a>.</p>
</div>

Per configurare un servizio cloud in modo da compilare e distribuire automaticamente in Azure mediante Visual Studio Online, eseguire la procedura seguente:

-   [Passaggio 1: Creare un progetto team][Passaggio 1: Creare un progetto team]

-   [Passaggio 2: Archiviare un progetto nel controllo del codice sorgente][Passaggio 2: Archiviare un progetto nel controllo del codice sorgente]

-   [Passaggio 3: Collegare il progetto ad Azure][Passaggio 3: Collegare il progetto ad Azure]

-   [Passaggio 4: Modificare e attivare una ricompilazione e una ridistribuzione][Passaggio 4: Modificare e attivare una ricompilazione e una ridistribuzione]

-   [Passaggio 5: Ridistribuire una compilazione precedente (facoltativo)][Passaggio 5: Ridistribuire una compilazione precedente (facoltativo)]

-   [Passaggio 6: Modificare la distribuzione di produzione (solo servizi cloud)][Passaggio 6: Modificare la distribuzione di produzione (solo servizi cloud)]

-   [Passaggio 7: Eseguire unit test (facoltativo)][Passaggio 7: Eseguire unit test (facoltativo)]

## <a name="step1"></a><span class="short-header">Creare un progetto team</span>Passaggio 1: Creare un progetto team

Seguire le istruzioni riportate [qui][qui] per creare il progetto team e collegarlo a Visual Studio. Questa procedura dettagliata presuppone l'uso di TFVC (Team Foundation Version Control) come soluzione per il controllo dell'origine. Se per il controllo della versione si desidera usare Git, vedere la pagina relativa alla [versione Git di questa procedura dettagliata][versione Git di questa procedura dettagliata].

## <a name="step2"> </a><span class="short-header">Archiviare un progetto nel controllo del codice sorgente.</span>Passaggio 2: Archiviare un progetto nel controllo del codice sorgente

1.  In Visual Studio aprire la soluzione che si desidera distribuire oppure crearne una nuova.
    È possibile distribuire un sito Web o un servizio cloud (applicazione di Azure) seguendo i passaggi di questa procedura dettagliata.
    Se si desidera creare una nuova soluzione, creare un nuovo progetto di servizio cloud di Azure
    o un nuovo progetto ASP.NET MVC. Assicurarsi che la destinazione del progetto sia .NET Framework 4 o 4.5 e, se si sta creando un progetto di servizio cloud, aggiungere un ruolo Web ASP.NET MVC e un ruolo di lavoro, quindi scegliere l'applicazione Internet per il ruolo Web. Quando richiesto, scegliere **Internet Application**.
    Se si desidera creare un sito Web, scegliere il modello di progetto Applicazione Web ASP.NET, quindi scegliere MVC. Vedere [Introduzione ad Azure e ASP.NET][Introduzione ad Azure e ASP.NET].

2.  Aprire il menu di scelta rapida relativo alla soluzione e selezionare **Aggiungi soluzione al controllo del codice sorgente**.
    ![][0]

3.  Accettare o modificare le impostazioni predefinite, quindi scegliere **OK**. Al completamento del processo, in Esplora soluzioni vengono visualizzate le icone del controllo codice sorgente.
    ![][1]

4.  Aprire il menu di scelta rapida relativo alla soluzione e scegliere **Archivia**.
    ![][2]

5.  Nell'area Modifiche in sospeso di Team Explorer, digitare un commento per l'archiviazione e scegliere il pulsante **Archivia**.
    ![][3]

 Si notino le opzioni per includere o escludere modifiche specifiche quando si esegue l'archiviazione; se le modifiche desiderate sono escluse, scegliere il collegamento **Includi tutto**.
![][4]

## <a name="step3"> </a><span class="short-header">Collegare il progetto ad Azure.</span>Passaggio 3: Collegare il progetto ad Azure

1.  A questo punto, dopo aver creato un progetto team VSO contenente il codice sorgente, è possibile collegare il progetto team ad Azure. Nel [portale di Azure][portale di Azure], selezionare il servizio cloud o il sito Web oppure crearne uno nuovo selezionando l'icona + in basso a sinistra e scegliendo **Servizio cloud** o **Sito Web**, quindi selezionare **Creazione rapida**. Scegliere il collegamento **Imposta pubblicazione da Visual Studio Online**.
    ![][5]

2.  Nella procedura guidata, digitare il nome del proprio account di Visual Studio Online nella casella di testo e fare clic sul collegamento **Autorizza ora**. È possibile che venga richiesto di effettuare l'accesso.
    ![][6]

3.  Nella finestra di dialogo popup OAuth scegliere **Accetta** per autorizzare Azure a configurare il progetto team in VSO.
    ![][7]

4.  Dopo aver concesso l'autorizzazione verrà visualizzato un elenco a discesa contenente un elenco dei progetti team di Visual Studio Online. Selezionare il nome del progetto team creato nei passaggi precedenti e fare clic sul pulsante di segno di spunta della procedura guidata.
    ![][8]

5.  Quando il progetto sarà stato collegato, saranno visualizzate alcune istruzioni per l'archiviazione delle modifiche nel progetto team di Visual Studio Online. Alla successiva archiviazione, Visual Studio Online compilerà e distribuirà il progetto in Azure. Effettuare subito una prova facendo clic sul collegamento **Archivia da Visual Studio** quindi sul collegamento **Launch Visual Studio** (o sul pulsante equivalente di **Visual Studio** in basso nella schermata del portale).
    ![][9]

## <a name="step4"> </a><span class="short-header">Attivare una ricompilazione</span>Passaggio 4: Attivare una ricompilazione e ridistribuzione del progetto

1.  In Team Explorer di Visual Studio fare clic sul collegamento **Esplora controllo codice sorgente**.
    ![][10]

2.  Passare al file della soluzione e aprirlo.
    ![][11]

3.  In Esplora soluzioni aprire un file e apportarvi delle modifiche. Ad esempio, modificare il file \_Layout.cshtml nella cartella Views\\Shared di un ruolo Web MVC.
    ![][12]

4.  Modificare il logo del sito e premere CTRL+S per salvare il file.
    ![][13]

5.  In Team Explorer scegliere il collegamento **Modifiche in sospeso**.
    ![][14]

6.  Digitare un commento e scegliere il pulsante **Archivia**.
    ![][15]

7.  Scegliere il pulsante Home per tornare alla pagina iniziale di Team Explorer.
    ![][16]

8.  Scegliere il collegamento **Compilazioni** per visualizzare le compilazioni in corso.
    ![][17]
    In Team Explorer è possibile osservare che è stata attivata una compilazione per la propria archiviazione.
    ![][18]

9.  Fare doppio clic sul nome della compilazione in corso per visualizzare un log dettagliato con l'avanzare della compilazione.
    ![][19]

10. Quando la compilazione è in corso, osservare la definizione di compilazione creata quando TFS è stato collegato ad Azure mediante la procedura guidata. Aprire il menu di scelta rapida relativo alla definizione della compilazione e scegliere **Modifica definizione di compilazione**.
    ![][20]
    Nella scheda **Trigger** è possibile osservare che, per impostazione predefinita, la definizione di compilazione è configurata per la compilazione a ogni archiviazione.
    ![][21]
    Nella scheda **Processo** è possibile notare che l'ambiente di distribuzione è impostato sul nome del servizio cloud o del sito Web. Se si lavora con i siti Web, le proprietà visualizzate saranno diverse da quelle presenti nella sezione riportata di seguito.
    ![][22]
    Specificare i valori per le proprietà se si desidera che siano diversi da quelli predefiniti. Le proprietà per la pubblicazione di Azure sono disponibili nella sezione relativa alla distribuzione.
    La tabella seguente mostra le proprietà disponibili in tale sezione:

    <table>

    <tr>
    <td>
    **Proprietà**

    </td>
    <td>
    **Valore predefinito**

    </td>
    </tr>
    </p>
    > <tr>
    > <td>
    > Consenti certificati non attendibili
    >
    > </td>
    > <td>
    > Se impostata su False, i certificati SSL devono essere firmati da un'autorità radice.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Consenti aggiornamento
    >
    > </td>
    > <td>
    > Consente di aggiornare una distribuzione esistente anziché crearne una nuova. L'indirizzo IP viene conservato.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Non eliminare
    >
    > </td>
    > <td>
    > Se impostata su True, non sovrascrivere una distribuzione esistente non correlata (l'aggiornamento è consentito).
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Percorso impostazioni di distribuzione
    >
    > </td>
    > <td>
    > Il percorso del file .pubxml per un sito Web, relativo alla cartella radice del repository. Ignorato per i servizi cloud.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Ambiente di distribuzione SharePoint
    >
    > </td>
    > <td>
    > Proprietà analoga al nome del servizio.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Ambiente di distribuzione Windows Azure
    >
    > </td>
    > <td>
    > Nome del sito Web o del servizio cloud.
    >
    > </td>
    > </tr>
    > </table>

Se si usano più configurazioni del servizio (file .cscfg), è possibile specificare quella desiderata nell'impostazione **Compila, Avanzate, Argomenti MSBuild**. Ad esempio, per usare ServiceConfiguration.Test.cscfg, impostare l'opzione degli argomenti MSBuild /p:TargetProfile=Test.
![][23]

1.  A questo punto la compilazione sarà stata completata correttamente.
    ![][24]

2.  Facendo doppio clic sul nome della compilazione, in Visual Studio verrà visualizzato un **Riepilogo compilazione** che include eventuali risultati del test restituiti dai progetti unit test associati.
    ![][25]

3.  Nel [portale di Azure][portale di Azure] è possibile visualizzare la distribuzione associata nella scheda Distribuzioni quando si seleziona l'ambiente di gestione temporanea.
    ![][26]

4.  Passare all'URL del proprio sito. Per un sito Web è sufficiente fare clic sul pulsante Sfoglia della barra dei comandi. Per un servizio cloud, scegliere l'URL nella sezione **Riepilogo rapido** della pagina **Dashboard** in cui è visualizzato l'ambiente di gestione temporanea per un servizio cloud. Le distribuzioni derivanti dall'integrazione continua per i servizi cloud vengono pubblicate nell'ambiente di gestione temporanea per impostazione predefinita. È possibile modificare questa impostazione configurando la proprietà Ambiente del servizio cloud alternativo su Produzione. In questa schermata è possibile osservare dove si trova l'URL del sito nella pagina dashboard del servizio cloud:
    ![][27]
    Il sito in esecuzione verrà aperto in una nuova scheda del browser.
    ![][28]

5.  Per i servizi cloud, se si apportano altre modifiche al progetto, si attiveranno ulteriori compilazioni e si accumuleranno più distribuzioni. L'ultima è contrassegnata come attiva.
    ![][29]

## <a name="step5"> </a><span class="short-header">Ridistribuire una compilazione precedente</span>Passaggio 5: Ridistribuire una compilazione precedente

Questo passaggio si applica ai servizi cloud ed è facoltativo. Nel portale di gestione selezionare una distribuzione precedente e fare clic sul pulsante **Ridistribuisci** per riportare il sito a un'archiviazione precedente. SI noti che verrà attivata una nuova compilazione in TFS e verrà creata una nuova voce nella cronologia di distribuzione.
![][30]

## <a name="step6"> </a><span class="short-header">Modificare la distribuzione di produzione</span>Passaggio 6: Modificare la distribuzione di produzione

Questo passaggio si applica solo ai servizi cloud, non ai siti Web. Quando si è pronti, è possibile promuovere l'ambiente di gestione temporanea all'ambiente di produzione scegliendo il pulsante Scambia nel portale di gestione. L'ambiente di gestione temporanea appena distribuito verrà promosso alla produzione e il precedente ambiente di produzione (se presente) diventerà un ambiente di gestione temporanea. La distribuzione attiva potrebbe differire per gli ambienti di produzione e di gestione temporanea, ma la cronologia di distribuzione delle recenti compilazioni è la stessa indipendentemente dall'ambiente.
![][31]

## <a name="step7"> </a><span class="short-header">Eseguire unit test</span>Passaggio 7: Eseguire unit test

Per integrare un controllo di qualità nelle distribuzioni attive o temporanee, è possibile eseguire alcuni unit test. Se tali test hanno esito negativo, è possibile interrompere la distribuzione.

1.  In Visual Studio aggiungere un progetto unit test.
    ![][32]

2.  Aggiungere i riferimenti ai progetti che si desidera sottoporre a test.
    ![][33]

3.  Aggiungere alcuni unit test. Per iniziare, creare un test di prova che verrà sempre superato.

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

4.  Modificare la definizione della compilazione, scegliere la scheda Processo ed espandere il nodo Test.

5.  Impostare **Errore compilazione se test non superato** su True. In questo modo, la distribuzione verrà eseguita solo se il test sarà superato.
    ![][34]

6.  Accodare una nuova compilazione.
    ![][35]
    ![][36]

7.  Durante l'esecuzione della compilazione, verificarne l'avanzamento.
    ![][37]
    ![][38]

8.  Al termine della compilazione, controllare i risultati del test.
    ![][39]
    ![][40]

9.  Provare a creare un test che avrà esito negativo. Per aggiungere un nuovo test, copiare il primo, rinominarlo e commentare la riga di codice che genera l'eccezione NotImplementedException.

        [TestMethod]
        [ExpectedException(typeof(NotImplementedException))]
        public void TestMethod2()
        {
            //throw new NotImplementedException();
        }

10. Archiviare la modifica per accodare una nuova compilazione.
    ![][41]

11. Visualizzare i risultati del test per osservare i dettagli dell'esito negativo.
    ![][42]
    ![][43]

Per altre informazioni sugli unit test in Visual Studio Online, vedere [Eseguire test nel processo di compilazione][Eseguire test nel processo di compilazione].

Per altre informazioni, vedere [Visual Studio Online][Visual Studio Online]. Se si utilizza Git, vedere [Condivisione del codice in Git][Condivisione del codice in Git] e [Pubblicazione da controllo del codice sorgente in Siti Web di Azure][Pubblicazione da controllo del codice sorgente in Siti Web di Azure].

  [Recapito continuo per Servizi cloud in Azure]: ../cloud-services-dotnet-continuous-delivery
  [www.visualstudio.com]: http://www.visualstudio.com
  [questa pagina]: http://go.microsoft.com/fwlink/?LinkId=239540
  [Passaggio 1: Creare un progetto team]: #step1
  [Passaggio 2: Archiviare un progetto nel controllo del codice sorgente]: #step2
  [Passaggio 3: Collegare il progetto ad Azure]: #step3
  [Passaggio 4: Modificare e attivare una ricompilazione e una ridistribuzione]: #step4
  [qui]: http://go.microsoft.com/fwlink/?LinkId=512980
  [versione Git di questa procedura dettagliata]: http://go.microsoft.com/fwlink/p/?LinkId=397358
  [Introduzione ad Azure e ASP.NET]: http://www.windowsazure.com/it-it/documentation/articles/web-sites-dotnet-get-started/
  [0]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
  [1]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
  [2]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
  [3]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
  [4]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
  [portale di Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
  [6]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
  [7]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
  [8]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
  [9]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
  [10]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
  [11]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
  [12]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [13]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
  [14]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
  [15]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
  [16]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
  [19]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [28]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [29]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [30]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [31]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [32]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
  [33]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
  [34]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
  [35]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
  [36]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
  [37]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
  [38]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
  [39]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
  [40]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
  [41]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
  [42]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
  [43]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG
  [Eseguire test nel processo di compilazione]: http://go.microsoft.com/fwlink/p/?LinkId=510474
  [Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Condivisione del codice in Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Pubblicazione da controllo del codice sorgente in Siti Web di Azure]: http://www.windowsazure.com/it-it/documentation/articles/web-sites-publish-source-control
