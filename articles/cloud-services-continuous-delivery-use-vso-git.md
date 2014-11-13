<properties urlDisplayName="Publishing with Visual Studio Online" pageTitle="Recapito continuo con Visual Studio Online in Azure" metaKeywords="" description="Informazioni sulla configurazione dei progetti team di Visual Studio Online per poter compilare ed eseguire automaticamente la distribuzione nei siti Web o nei servizi cloud di Azure." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Recapito continuo in Azure tramite Visual Studio Online e Git" authors="ghogen" solutions="" manager="douge" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="ghogen" />

# Recapito continuo in Azure tramite Visual Studio Online e Git

È possibile usare i progetti team di Visual Studio Online per ospitare un repository Git per il codice sorgente e compilarlo e distribuirlo automaticamente nei siti Web o nei servizi cloud di Azure quando si effettua il push di un commit nel repository.

È necessario che siano installati Visual Studio 2013 e Azure SDK. Se non si dispone ancora di Visual Studio 2013, scaricarlo scegliendo il collegamento **Inizia gratuitamente** all'indirizzo [www.visualstudio.com][www.visualstudio.com]. Installare Azure SDK da [questa pagina][questa pagina].

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Per completare l'esercitazione, &egrave; necessario un account di Visual Studio Online:</h5>
<p>&Egrave; possibile <a href="http://go.microsoft.com/fwlink/p/?LinkId=512979">aprire un account di Visual Studio Online gratuitamente</a>.</p>
</div>

Per configurare un servizio cloud in modo da compilare e distribuire automaticamente in Azure mediante Visual Studio Online, eseguire la procedura seguente:

-   [Passaggio 1: Creare un repository Git.][Passaggio 1: Creare un repository Git.]

-   [Passaggio 2: Creazione e push di un progetto nel repository Git.][Passaggio 2: Creazione e push di un progetto nel repository Git.]

-   [Passaggio 3: Connessione del progetto ad Azure.][Passaggio 3: Connessione del progetto ad Azure.]

-   [Passaggio 4: Modifiche e attivazione di una ricompilazione e una ridistribuzione.][Passaggio 4: Modifiche e attivazione di una ricompilazione e una ridistribuzione.]

-   [Passaggio 5: Ridistribuzione di una compilazione precedente (facoltativa)][Passaggio 5: Ridistribuzione di una compilazione precedente (facoltativa)]

-   [Passaggio 6: Modifica della distribuzione di produzione][Passaggio 6: Modifica della distribuzione di produzione]

-   [Passaggio 7: Distribuzione da un branch di lavoro][Passaggio 7: Distribuzione da un branch di lavoro]

## <a name="step1"></a><span class="short-header">Passaggio 1: Creare un repository Git.</span>Passaggio 1: Creare un repository Git

1.  Se non si ha ancora un account di Visual Studio Online, seguire le istruzioni riportate [qui][qui]. Quando si crea il progetto team, scegliere Git come sistema di controllo del codice sorgente. Seguire le istruzioni per collegare Visual Studio al progetto team.

2.  In Team Explorer fare clic sul collegamento .
    ![][0]

3.  Specificare il percorso della copia locale e scegliere il pulsante **Clona**.

## <a name="step2"> </a><span class="short-header">Creazione e commit di un progetto nel repository.</span>Passaggio 2: Creazione e commit di un progetto nel repository

1.  Nella sezione Soluzioni di Team Explorer scegliere il collegamento Nuovo per creare un nuovo progetto nel repository locale.
    ![][1]

2.  È possibile distribuire un sito Web o un servizio cloud (applicazione Azure) seguendo i passaggi di questa procedura dettagliata.
    Creare un nuovo progetto di servizio cloud di Microsoft Azure
    o un nuovo progetto MVC ASP.NET. Assicurarsi che la destinazione del progetto sia .NET Framework 4 o 4.5 e, se si sta creando un progetto di servizio cloud, aggiungere un ruolo Web MVC ASP.NET e un ruolo di lavoro.
    Se si vuole creare un sito Web, scegliere il modello di progetto di applicazione Web ASP.NET e quindi scegliere MVC. Vedere [Introduzione ad Azure e ASP.NET][Introduzione ad Azure e ASP.NET].

3.  Aprire il menu di scelta rapida relativo alla soluzione e scegliere **Commit**.
    ![][2]

4.  Se è la prima volta che si usa Git in Visual Studio Online, sarà necessario fornire alcune informazioni per identificarsi in Git. Nell'area **Modifiche in sospeso** di Team Explorer digitare il proprio nome utente e indirizzo di posta elettronica. Digitare un commento per il commit e scegliere **Commit**.
    ![][3]

5.  Si notino le opzioni per includere o escludere modifiche specifiche quando si esegue l'archiviazione. Se le modifiche desiderate sono escluse, scegliere **Includi tutto**.

6.  È stato eseguito il commit delle modifiche nella copia locale del repository. A questo punto, sincronizzare le modifiche con il server. Scegliere il collegamento **Sincronizza**.

## <a name="step3"> </a><span class="short-header">Connessione del progetto ad Azure.</span>Passaggio3: Connessione del progetto ad Azure

1.  Ora si dispone di un repository Git in Visual Studio Online contenente codice sorgente ed è possibile connettere il repository ad Azure. Nel [portale di Azure][portale di Azure] selezionare il servizio cloud o il sito Web oppure crearne uno nuovo selezionando l'icona + in basso a sinistra e scegliendo **Servizio cloud** o **Sito Web**, quindi **Creazione rapida**.<br.>
    ![][4]

2.  Scegliere il collegamento **Imposta pubblicazione con Visual Studio Online**. Per i siti Web scegliere il collegamento **Imposta distribuzione dal controllo del codice sorgente**.
    ![][5]

3.  Nella procedura guidata digitare il nome del proprio account di Visual Studio Online nella casella di testo e fare clic sul collegamento **Autorizza ora**. È possibile che venga richiesto di effettuare l'accesso.
    ![][6]

4.  Nella finestra di dialogo popup OAuth scegliere **Accetta** per autorizzare Azure a configurare il progetto team in Visual Studio Online.
    ![][7]

5.  Dopo aver concesso l'autorizzazione verrà visualizzato un elenco a discesa contenente un elenco dei progetti team di Visual Studio Online. Selezionare il nome del progetto team creato nei passaggi precedenti e fare clic sul pulsante di segno di spunta della procedura guidata.
    ![][8]

Quando si effettuerà di nuovo il push di un commit al repository, Visual Studio Online compilerà e distribuirà il progetto in Azure.

## <a name="step4"> </a><span class="short-header">Attivazione di una ricompilazione</span>Passaggio 4: Attivazione di una ricompilazione e ridistribuzione del progetto

1.  In Visual Studio aprire un file e modificarlo. Ad esempio, modificare il file \_Layout.cshtml nella cartella Views\\Shared di un ruolo Web MVC.
    ![][9]

2.  Modificare il testo del piè di pagina per il sito e salvare il file.
    ![][10]

3.  In Esplora soluzioni aprire il menu di scelta rapida per il nodo della soluzione, il nodo del progetto o il file modificato e scegliere **Commit**.

4.  Digitare un commento e scegliere **Commit**.
    ![][3]

5.  Scegliere il collegamento **Sincronizza**.
    ![][11]

6.  Scegliere il collegamento **Push** per effettuare il push del commit al repository in Visual Studio Online. È anche possibile usare il pulsante **Sincronizza** per copiare i commit nel repository. La differenza sta nel fatto che **Sincronizza** effettua anche il pull delle ultime modifiche dal repository.
    ![][12]

7.  Scegliere il pulsante Home per tornare alla pagina iniziale di Team Explorer.
    ![][13]

8.  Scegliere **Compilazioni** per visualizzare le compilazioni in corso.
    ![][14]
    In Team Explorer è possibile osservare che è stata attivata una compilazione per i dati archiviati.
    ![][15]

9.  Fare doppio clic sul nome della compilazione in corso per visualizzare un log dettagliato con l'avanzare della compilazione.

10. Quando la compilazione è in corso, osservare la definizione di compilazione creata quando la procedura guidata è stata usata per eseguire il collegamento ad Azure. Aprire il menu di scelta rapida per la definizione di compilazione e scegliere **Modifica definizione di compilazione**.
    ![][16]
    Nella scheda **Trigger** si vedrà che, per impostazione predefinita, la definizione della compilazione è impostata su compilazione per ogni archiviazione. Per un servizio cloud, Visual Studio Online compila e distribuisce automaticamente il branch master nell'ambiente di gestione temporanea. È comunque necessario eseguire un'operazione manuale per distribuire il branch master nel sito attivo. Per un sito Web che non include un ambiente di gestione temporanea, il branch master viene distribuito direttamente nel sito attivo.
    ![][17]
    Nella scheda **Processo** è possibile vedere che l'ambiente di distribuzione è impostato sul nome del servizio cloud o del sito Web.
    ![][18]
    Specificare i valori per le proprietà se si vuole usare valori diversi da quelli predefiniti. Le proprietà per la pubblicazione in Azure si trovano nella sezione Distribuzione e potrebbe anche essere necessario impostare i parametri MSBuild. Ad esempio, in un progetto di servizio cloud, per specificare una configurazione del servizio diversa da "Cloud", impostare i parametri MSbuild su /p:TargetProfile=*ProfiloPersonale* dove *ProfiloPersonale* corrisponde a un file di configurazione del servizio con un nome simile a ServiceConfiguration.*ProfiloPersonale*.cscfg.
    La tabella seguente illustra le proprietà disponibili nella sezione Distribuzione:

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
    > Se è false, i certificati SSL devono essere firmati da un'autorità radice.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Consenti aggiornamento
    >
    > </td>
    > <td>
    > Consente l'aggiornamento di una distribuzione esistente anziché crearne una nuova. Conserva l'indirizzo IP.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Non eliminare
    >
    > </td>
    > <td>
    > Se è true, una distribuzione non correlata esistente non viene sovrascritta (l'aggiornamento è consentito).
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Percorso impostazioni di distribuzione
    >
    > </td>
    > <td>
    > Percorso del file con estensione pubxml di un sito Web, relativo alla cartella radice del repository. Viene ignorato per i servizi cloud.
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Ambiente di distribuzione SharePoint
    >
    > </td>
    > <td>
    > Analoga al nome del servizio
    >
    > </td>
    > </tr>
    > <tr>
    > <td>
    > Ambiente di distribuzione Windows Azure
    >
    > </td>
    > <td>
    > Nome del sito Web o del servizio cloud
    >
    > </td>
    > </tr>
    > </table>

11. A questo punto la compilazione sarà stata completata correttamente.
    ![][19]

12. Facendo doppio clic sul nome della compilazione, in Visual Studio verrà visualizzato un **Riepilogo compilazione** che include eventuali risultati del test restituiti dai progetti unit test associati.
    ![][20]

13. Nel [portale di Azure][portale di Azure] è possibile visualizzare la distribuzione associata nella scheda Deployments quando si seleziona l'ambiente di gestione temporanea.
    ![][21]

14. Passare all'URL del proprio sito. Per un sito Web, scegliere semplicemente il pulsante **Sfoglia** nel portale. Per un servizio cloud, scegliere l'URL nella sezione **Riepilogo rapido** della pagina **Dashboard** in cui è visualizzato l'ambiente di gestione temporanea. Le distribuzioni derivanti dall'integrazione continua per i servizi cloud vengono pubblicate nell'ambiente di gestione temporanea per impostazione predefinita. È possibile modificare questa impostazione configurando la proprietà Ambiente del servizio cloud alternativo su Produzione. Ecco dove si trova l'URL del sito nella pagina del dashboard del servizio cloud:
    ![][22]
    Il sito in esecuzione verrà aperto in una nuova scheda del browser.
    ![][23]

15. Se si apportano altre modifiche al progetto, si attiveranno ulteriori compilazioni e si accumuleranno più distribuzioni. La più recente è contrassegnata come Attiva.
    ![][24]

## <a name="step5"> </a><span class="short-header">Ridistribuzione di una compilazione precedente</span>Passaggio 5: Ridistribuzione di una compilazione precedente

Questo passaggio è facoltativo. Nel portale di gestione selezionare una distribuzione precedente e fare clic su **Ridistribuisci** per riportare il sito a un'archiviazione precedente. Si noti che verrà attivata una nuova compilazione in TFS e verrà creata una nuova voce nella cronologia della distribuzione.
![][25]

## <a name="step6"> </a><span class="short-header">Modifica della distribuzione di produzione</span>Passaggio 6: Modifica della distribuzione di produzione

Quando si è pronti, è possibile alzare di livello l'ambiente di gestione temporanea all'ambiente di produzione scegliendo **Scambia** nel portale di gestione. L'ambiente di gestione temporanea appena distribuito verrà promosso alla produzione e il precedente ambiente di produzione (se presente) diventerà un ambiente di gestione temporanea. La distribuzione attiva potrebbe differire per gli ambienti di produzione e di gestione temporanea, ma la cronologia di distribuzione delle recenti compilazioni è la stessa indipendentemente dall'ambiente.
![][26]

## <a name="step7"> </a><span class="short-header">Distribuzione da un branch di lavoro</span>Passaggio 6: Distribuzione da un branch di lavoro.

Quando si usa Git, in genere si apportano modifiche in un branch di lavoro che vengono integrate nel branch master quando lo sviluppo è terminato. Durante la fase di sviluppo di un progetto, è consigliabile compilare e distribuire il branch di lavoro in Azure.

1.  In Team Explorer scegliere il pulsante **Home** e quindi **Branch**.
    ![][27]

2.  Scegliere il collegamento **Nuovo branch**.
    ![][28]

3.  Digitare il nome del branch, ad esempio "working", e scegliere **Crea branch**. Verrà creato un nuovo branch locale.
    ![][29]

4.  Pubblicare il branch. Scegliere il nome del branch in **Branch non pubblicati** e scegliere **Pubblica**.
    ![][30]

5.  Per impostazione predefinita, solo le modifiche al branch master attivano una compilazione continua. Per configurare una compilazione continua per un branch di lavoro, scegliere la pagina Compilazioni in Team Explorer e scegliere **Modifica definizione di compilazione**.

6.  Aprire la scheda **Impostazioni di origine**. In **Branch monitorati per le compilazioni a integrazione continua e in corso** scegliere **Fare clic qui per aggiungere una nuova riga**.
    ![][31]

7.  Specificare il branch creato, ad esempio refs/heads/working.
    ![][32]

8.  Modificare il codice, aprire il menu di scelta rapida per il file modificato e scegliere **Commit**.
    ![][3]

9.  Scegliere il collegamento **Commit non sincronizzati** e scegliere il pulsante **Sincronizza** o il collegamento **Push** per copiare le modifiche nella copia del branch di lavoro in Visual Studio Online.
    ![][11]

10. Passare alla visualizzazione **Compilazioni** e cercare la compilazione appena attivata per il branch di lavoro.

Per altre informazioni, vedere [Visual Studio Online][Visual Studio Online]. Per altri suggerimenti su come usare Git con Visual Studio Online, vedere [Condividere il codice in Git][Condividere il codice in Git] e per informazioni sull'uso di un repository Git non gestito da Visual Studio Online per pubblicare in Azure, vedere [Pubblicazione da controllo del codice sorgente in Siti Web di Azure][Pubblicazione da controllo del codice sorgente in Siti Web di Azure].

  [www.visualstudio.com]: http://www.visualstudio.com
  [questa pagina]: http://go.microsoft.com/fwlink/?LinkId=239540
  [Passaggio 1: Creare un repository Git.]: #step1
  [Passaggio 2: Creazione e push di un progetto nel repository Git.]: #step2
  [Passaggio 3: Connessione del progetto ad Azure.]: #step3
  [Passaggio 4: Modifiche e attivazione di una ricompilazione e una ridistribuzione.]: #step4
  [Passaggio 5: Ridistribuzione di una compilazione precedente (facoltativa)]: #step5
  [Passaggio 6: Modifica della distribuzione di produzione]: #step6
  [Passaggio 7: Distribuzione da un branch di lavoro]: #step7
  [qui]: http://go.microsoft.com/fwlink/?LinkId=397665
  [0]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
  [1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
  [Introduzione ad Azure e ASP.NET]: http://www.windowsazure.com/it-it/documentation/articles/web-sites-dotnet-get-started/
  [2]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
  [3]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
  [portale di Azure]: http://manage.windowsazure.com
  [4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
  [5]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
  [6]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
  [7]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
  [8]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
  [9]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
  [10]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
  [11]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
  [12]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
  [13]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
  [14]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
  [15]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
  [16]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
  [17]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
  [18]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
  [19]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
  [20]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
  [21]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
  [22]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
  [23]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
  [24]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
  [25]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
  [26]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
  [27]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
  [28]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
  [29]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
  [30]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
  [31]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
  [32]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
  [Visual Studio Online]: http://go.microsoft.com/fwlink/?LinkId=253861
  [Condividere il codice in Git]: http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx
  [Pubblicazione da controllo del codice sorgente in Siti Web di Azure]: http://www.windowsazure.com/it-it/documentation/articles/web-sites-publish-source-control
