---
title: Recapito continuo con Visual Studio Team Services in Azure | Documentazione Microsoft
description: "Informazioni sulla configurazione dei progetti team di Visual Studio Team Services per la compilazione e la distribuzione automatiche alla funzionalità App Web nel Servizio app di Azure o nei servizi cloud."
services: cloud-services
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: 797f67ad-e4d4-4063-ae91-41cbdf154191
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/06/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 171c3f05b0eacfd8b1ede0b807f284ce6c0555b4


---
# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>Recapito continuo in Azure tramite Visual Studio Team Services
È possibile configurare i progetti del proprio team di Visual Studio Team Services in modo da compilare ed eseguire automaticamente la distribuzione nelle app Web o nei servizi cloud di Azure.  Per informazioni su come configurare un sistema di compilazione e distribuzione continua con un Team Foundation Server *locale* , vedere [Recapito continuo per Servizi cloud in Azure](cloud-services-dotnet-continuous-delivery.md).

In questa esercitazione si presuppone che l'utente abbia installato Visual Studio 2013 e Azure SDK. Se non si dispone ancora di Visual Studio 2013, scaricarlo scegliendo il collegamento **Inizia gratuitamente** all'indirizzo [www.visualstudio.com](http://www.visualstudio.com). Installare Azure SDK da [questa pagina](http://go.microsoft.com/fwlink/?LinkId=239540).

> [!NOTE]
> Per completare l'esercitazione, è necessario un account di Visual Studio Team Services: è possibile [aprire un account di Visual Studio Team Services gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).
> 
> 

Per configurare un servizio cloud da compilare e distribuire automaticamente in Azure tramite Visual Studio Team Services, seguire questa procedura.

## <a name="1-create-a-team-project"></a>1: Creare un progetto team
Seguire le istruzioni [qui](http://go.microsoft.com/fwlink/?LinkId=512980) per creare il progetto team e collegarlo a Visual Studio. In questa procedura dettagliata si presume che si usi Controllo della versione di Team Foundation (TFVC) come soluzione di controllo del codice sorgente. Per usare Git per il controllo della versione, vedere [la versione per Git di questa procedura dettagliata](http://go.microsoft.com/fwlink/p/?LinkId=397358).

## <a name="2-check-in-a-project-to-source-control"></a>2: Archiviare un progetto nel controllo del codice sorgente
1. In Visual Studio, aprire la soluzione che si desidera distribuire o crearne una nuova.
   È possibile distribuire un’app Web o un servizio cloud (applicazione Azure) seguendo i passaggi di questa procedura dettagliata.
   Se si desidera creare una nuova soluzione, creare un nuovo progetto di servizio cloud di Azure o un nuovo progetto ASP.NET MVC. Assicurarsi che la destinazione del progetto sia .NET Framework 4 o 4.5 e, se si sta creando un progetto di servizio cloud, aggiungere un ruolo Web ASP.NET MVC e un ruolo di lavoro, quindi scegliere l'applicazione Internet per il ruolo Web. Quando richiesto, scegliere **Applicazione Internet**.
   Per creare un’app Web, scegliere il modello di progetto Applicazione Web ASP.NET e quindi scegliere MVC. Vedere [Creare un'app Web ASP.NET in Azure App Service](../app-service-web/web-sites-dotnet-get-started.md).
   
   > [!NOTE]
   > Al momento, Visual Studio Team Services supporta solo le distribuzioni CI di applicazioni Web di Visual Studio. I progetti di sito Web sono esterni all'ambito.
   > 
   > 
2. Aprire il menu di scelta rapida relativo alla soluzione e selezionare **Aggiungi soluzione al controllo del codice sorgente**.
   
    ![][5]
3. Accettare o modificare le impostazioni predefinite e quindi scegliere **OK** . Dopo il completamento del processo, in **Esplora soluzioni**vengono visualizzate le icone del controllo codice sorgente.
   
    ![][6]
4. Aprire il menu di scelta rapida relativo alla soluzione e scegliere **Archivia**.
   
    ![][7]
5. Nell'area **Modifiche in sospeso** di **Team Explorer** digitare un commento per l'archiviazione e scegliere il pulsante **Archivia**.
   
    ![][8]
   
    Notare le opzioni per includere o escludere modifiche specifiche quando si esegue l'archiviazione. Se le modifiche desiderate sono escluse, scegliere il collegamento **Includi tutto** .
   
    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3: Collegare il progetto ad Azure
1. A questo punto, dopo avere creato un progetto team VS Team Services contenente il codice sorgente, è possibile connettere il progetto team ad Azure.  Nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885) selezionare il servizio cloud o l'app Web oppure crearne uno nuovo selezionando l'icona **+** in basso a sinistra e scegliendo **Servizio cloud** o **App Web** e quindi **Creazione rapida**. Scegliere il collegamento **Imposta pubblicazione con Visual Studio Team Services** .
   
    ![][10]
2. Nella procedura guidata digitare il nome del proprio account di Visual Studio Team Services nella casella di testo e fare clic sul collegamento **Autorizza ora** . È possibile che venga richiesto di effettuare l'accesso.
   
    ![][11]
3. Nella finestra di dialogo popup **Connection Request** (Richiesta di connessione) scegliere il pulsante **Accetta** per autorizzare Azure a configurare il progetto team in VS Team Services.
   
    ![][12]
4. Dopo aver concesso l'autorizzazione verrà visualizzato un elenco a discesa contenente un elenco dei progetti team di Visual Studio Team Services. Selezionare il nome del progetto team creato nei passaggi precedenti e quindi fare clic sul pulsante con il segno di spunta della procedura guidata.
   
    ![][13]
5. Dopo aver collegato il progetto saranno visualizzate alcune istruzioni per l'archiviazione delle modifiche nel progetto team di Visual Studio Team Services.  Alla successiva archiviazione, Visual Studio Team Services compilerà e distribuirà il progetto in Azure.  Provare subito facendo clic sul collegamento **Archivia da Visual Studio**, quindi sul collegamento **Avviare Visual Studio** o sul pulsante equivalente **Visual Studio** nella parte inferiore della schermata del portale.
   
    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: Attivare una ricompilazione e ridistribuire il progetto
1. In **Team Explorer** di Visual Studio fare clic sul collegamento **Esplora controllo codice sorgente**.
   
    ![][15]
2. Passare al file della soluzione e aprirlo.
   
    ![][16]
3. In **Esplora soluzioni**aprire un file e modificarlo. Modificare ad esempio il file `_Layout.cshtml` nella cartella Views\\Shared in un ruolo Web MVC.
   
    ![][17]
4. Modificare il logo del sito e premere **CTRL+S** per salvare il file.
   
    ![][18]
5. In **Team Explorer** scegliere il collegamento **Modifiche in sospeso**.
   
    ![][19]
6. Immettere un commento e quindi scegliere il pulsante **Archivia** .
   
    ![][20]
7. Scegliere il pulsante **Home** per tornare alla pagina iniziale di **Team Explorer**.
   
    ![][21]
8. Scegliere il collegamento **Compilazioni** per visualizzare le compilazioni in corso.
   
    ![][22]
   
    **Team Explorer** mostra che è stata attivata una compilazione per l'archiviazione.
   
    ![][23]
9. Fare doppio clic sul nome della compilazione in corso per visualizzare un log dettagliato con l'avanzare della compilazione.
   
    ![][24]
10. Quando la compilazione è in corso, osservare la definizione di compilazione creata quando TFS è stato collegato ad Azure mediante la procedura guidata.  Aprire il menu di scelta rapida relativo alla definizione di compilazione e scegliere **Modifica definizione di compilazione**.
    
     ![][25]
    
     Nella scheda **Trigger** si osserva che, per impostazione predefinita, la definizione di compilazione è configurata per la compilazione a ogni archiviazione.
    
     ![][26]
    
     Nella scheda **Processo** si osserva che l'ambiente di distribuzione è configurato con il nome del proprio servizio cloud o app Web. Se si lavora con le app Web, le proprietà visualizzate saranno diverse da quelle presenti nella sezione seguente.
    
     ![][27]
11. Specificare i valori per le proprietà se si desidera che siano diversi da quelli predefiniti. Le proprietà per la pubblicazione in Azure sono nella sezione **Distribuzione** .
    
     La tabella seguente illustra le proprietà disponibili nella sezione **Distribuzione** :
    
    | Proprietà | Valore predefinito |
    | --- | --- |
    | Consenti certificati non attendibili |Se è false, i certificati SSL devono essere firmati da un'autorità radice. |
    | Consenti aggiornamento |Consente l'aggiornamento di una distribuzione esistente anziché crearne una nuova. Conserva l'indirizzo IP. |
    | Non eliminare |Se è true, una distribuzione non correlata esistente non viene sovrascritta (l'aggiornamento è consentito). |
    | Percorso impostazioni di distribuzione |Percorso del file con estensione pubxml di un’app Web, relativo alla cartella radice del repository. Viene ignorato per i servizi cloud. |
    | Ambiente di distribuzione SharePoint |Analogo al nome del servizio. |
    | Ambiente di distribuzione Azure |Nome dell'app Web o del servizio cloud. |
12. Se si usano più configurazioni di servizio (file .cscfg), è possibile specificare la configurazione del servizio desiderata nell'impostazione **Compila, Avanzate, Argomenti MSBuild** . Ad esempio, per usare ServiceConfiguration.Test.cscfg impostare l'opzione della riga degli argomenti di MSBuild `/p:TargetProfile=Test`.
    
     ![][38]
    
     A questo punto la compilazione sarà stata completata correttamente.
    
     ![][28]
13. Facendo doppio clic sul nome della compilazione, Visual Studio visualizza un **Riepilogo compilazione**che include eventuali risultati del test restituiti dai progetti unit test associati.
    
     ![][29]
14. Nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885)è possibile visualizzare la distribuzione associata nella scheda **Distribuzioni** quando si seleziona l'ambiente di gestione temporanea.
    
     ![][30]
15. Passare all'URL del proprio sito. Per un'app Web è sufficiente fare clic sul pulsante **Browse** della barra dei comandi. Per un servizio cloud, scegliere l'URL nella sezione **Riepilogo rapido** della pagina **Dashboard** in cui è visualizzato l'ambiente di staging per un servizio cloud. Le distribuzioni derivanti dall'integrazione continua per i servizi cloud vengono pubblicate nell'ambiente di gestione temporanea per impostazione predefinita. È possibile modificare questa impostazione configurando la proprietà **Ambiente del servizio cloud alternativo** su **Produzione**. La schermata seguente mostra dove si trova l'URL del sito nella pagina dashboard del servizio cloud.
    
    ![][31]
    
    Il sito in esecuzione verrà aperto in una nuova scheda del browser.
    
    ![][32]
    
    Per i servizi cloud, se si apportano altre modifiche al progetto, si attiveranno altre compilazioni e si accumuleranno più distribuzioni. L'ultima è contrassegnata come Attiva.
    
    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: Ridistribuire una compilazione precedente
Questo passaggio si applica ai servizi cloud ed è facoltativo. Nel portale di Azure classico selezionare una distribuzione precedente e fare clic sul pulsante **Ridistribuisci** per riportare il sito a un'archiviazione precedente.  Si noti che verrà attivata una nuova compilazione in TFS e verrà creata una nuova voce nella cronologia della distribuzione.

![][34]

## <a name="6-change-the-production-deployment"></a>6: Modificare la distribuzione di produzione
Questo passaggio si applica solo ai servizi cloud, non alle app Web. Quando si è pronti, è possibile promuovere l'ambiente di gestione temporanea all'ambiente di produzione scegliendo il pulsante **Scambia** nel portale di Azure classico. L'ambiente di gestione temporanea appena distribuito verrà promosso alla produzione e il precedente ambiente di produzione (se presente) diventerà un ambiente di gestione temporanea. La distribuzione attiva potrebbe differire per gli ambienti di produzione e di gestione temporanea, ma la cronologia di distribuzione delle compilazioni recenti è la stessa indipendentemente dall'ambiente.

![][35]

## <a name="7-run-unit-tests"></a>7: Eseguire unit test
Questo passaggio si applica solo alle app Web, non ai servizi cloud. Per inserire un controllo qualità nella distribuzione, è possibile eseguire alcuni unit test e, se non riescono, è possibile arrestare la distribuzione.

1. In Visual Studio aggiungere un progetto di unit test.
   
   ![][39]
2. Aggiungere i riferimenti al progetto da testare.
   
   ![][40]
3. Aggiungere alcuni unit test. Per iniziare, provare un test fittizio che verrà sempre superato.
   
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
4. Modificare la definizione di compilazione, scegliere la scheda **Processo** ed espandere il nodo **Test**.
5. Impostare **Errore compilazione se test non superato** su True. Questo significa che la distribuzione non verrà eseguita se non dopo il superamento dei test.
   
   ![][41]
6. Inserire in coda una nuova compilazione.
   
   ![][42]
   
   ![][43]
7. Mentre la compilazione è in corso, controllarne l'avanzamento.
   
    ![][44]
   
    ![][45]
8. Al termine della compilazione, controllare i risultati del test.
   
    ![][46]
   
    ![][47]
9. Provare a creare un test che non verrà superato. Aggiungere un nuovo test copiando il primo, rinominarlo e impostare come commento la riga di codice che indica che NotImplementedException è un'eccezione prevista.
   
       ```
       [TestMethod]
       //[ExpectedException(typeof(NotImplementedException))]
       public void TestMethod2()
       {
           throw new NotImplementedException();
       }
       ```
10. Archiviare la modifica per inserire in coda una nuova compilazione.
    
     ![][48]
11. Visualizzare i risultati del test per vedere i dettagli sull'errore.
    
     ![][49]
    
     ![][50]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'esecuzione di test delle unità in Visual Studio Team Services, vedere [Eseguire test delle unità nella compilazione](http://go.microsoft.com/fwlink/p/?LinkId=510474). Se si usa Git, vedere [Condividere il codice in Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e [Distribuzione continua nel servizio app di Azure](../app-service-web/app-service-continuous-deployment.md).  Per altre informazioni su Visual Studio Team Services, vedere [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

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



<!--HONumber=Nov16_HO3-->


