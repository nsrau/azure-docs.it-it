<properties 
	pageTitle="Recapito continuo con Visual Studio Online in Azure" 
	description="Informazioni sulla configurazione dei progetti team di Visual Studio Online per poter compilare ed eseguire automaticamente la distribuzione nella funzione App Web in Azure App Service o nei servizi cloud." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="kempb"/>




# Recapito continuo in Azure tramite Visual Studio Online e Git

È possibile usare i progetti team di Visual Studio Online per ospitare un repository Git per il codice sorgente e compilarlo e distribuirlo automaticamente nelle app Web o nei servizi cloud di Azure quando si effettua il push di un commit nel repository.

È necessario che siano installati Visual Studio 2013 e Azure SDK. Se non si dispone ancora di Visual Studio 2013, scaricarlo scegliendo il collegamento **Inizia gratuitamente** all'indirizzo [www.visualstudio.com](http://www.visualstudio.com). Installare Azure SDK da [questa pagina](http://go.microsoft.com/fwlink/?LinkId=239540).


> [AZURE.NOTE]Per completare l'esercitazione, è necessario un account di Visual Studio Online: è possibile [aprire un account di Visual Studio Online gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).

Per configurare un servizio cloud in modo da compilare e distribuire automaticamente in Azure mediante Visual Studio Online, eseguire la procedura seguente:

-   [Passaggio 1: Creare un repository Git.][]

-   [Passaggio 2: Creare un progetto ed eseguirne il push nel repository Git.][]

-   [Passaggio 3: Connettere il progetto ad Azure.][]

-   [Passaggio 4: Modificare e attivare una ricompilazione e una ridistribuzione.][]

-   [Passaggio 5: Ridistribuire una compilazione precedente (facoltativo).][]

-   [Passaggio 6: Modificare la distribuzione di produzione.][]

-	[Passaggio 7: Distribuire da un branch di lavoro.][]

## <a name="step1"></a>Passaggio 1: Creare un repository Git


1. Se non si ha ancora un account di Visual Studio Online, seguire le istruzioni riportate [qui](http://go.microsoft.com/fwlink/?LinkId=397665). Quando si crea il progetto team, scegliere Git come sistema di controllo del codice sorgente. Seguire le istruzioni per collegare Visual Studio al progetto team.

2. In Team Explorer fare clic sul collegamento **Eseguire la clonazione di questo repository**. ![][3]

3. Specificare il percorso della copia locale e scegliere il pulsante **Clona**.
 
## <a name="step2"> </a>Passaggio 2: Creare un progetto ed eseguirne il commit nel repository

1. Nella sezione Soluzioni di Team Explorer scegliere il collegamento Nuovo per creare un nuovo progetto nel repository locale.<br/> ![][4]

2. È possibile distribuire un’app Web o un servizio cloud (applicazione Azure) seguendo i passaggi di questa procedura dettagliata. Creare un nuovo progetto servizio cloud di Azure o un nuovo progetto ASP.NET MVC. Assicurarsi che la destinazione del progetto sia .NET Framework 4 o 4.5 e, se si sta creando un progetto di servizio cloud, aggiungere un ruolo Web ASP.NET MVC e un ruolo di lavoro. Per creare un’app Web, scegliere il modello di progetto Applicazione Web ASP.NET e quindi scegliere MVC. Vedere [Creare un'app Web ASP.NET in Azure App Service](../web-sites-dotnet-get-started.md).

3. Aprire il menu di scelta rapida relativo alla soluzione e scegliere **Commit**.<br/> ![][7]

4. Se è la prima volta che si usa Git in Visual Studio Online, sarà necessario fornire alcune informazioni per identificarsi in Git. Nell'area **Modifiche in sospeso** di Team Explorer digitare il proprio nome utente e indirizzo di posta elettronica. Digitare un commento per il commit e scegliere **Commit**.<br/> ![][8]

5. Notare le opzioni per includere o escludere modifiche specifiche quando si esegue l'archiviazione. Se le modifiche desiderate sono escluse, scegliere **Includi tutto**.<br/>

6. È stato eseguito il commit delle modifiche nella copia locale del repository. A questo punto, sincronizzare le modifiche con il server. Scegliere il collegamento **Sincronizza**.

## <a name="step3"> </a>Passaggio 3: Connettere il progetto ad Azure

1. Ora si dispone di un repository Git in Visual Studio Online contenente codice sorgente ed è possibile connettere il repository ad Azure. Nel [portale di Azure](http://manage.windowsazure.com), selezionare il servizio cloud o l’app Web oppure crearne uno nuovo selezionando l'icona + in basso a sinistra e scegliendo **Cloud Service** o **Web App**, quindi selezionare **Quick Create**.<br.> ![][9]

3. Scegliere il collegamento **Imposta pubblicazione con Visual Studio Online**. Per le app Web scegliere il collegamento **Imposta distribuzione dal controllo del codice sorgente**.<br/> ![][10]

2. Nella procedura guidata digitare il nome del proprio account di Visual Studio Online nella casella di testo e fare clic sul collegamento **Autorizza ora**. È possibile che venga richiesto di eseguire l'accesso.<br/> ![][11]

3. Nella finestra di dialogo popup OAuth scegliere **Accetta** per autorizzare Azure a configurare il progetto team in Visual Studio Online.<br/> ![][12]

4. Dopo aver concesso l'autorizzazione verrà visualizzato un elenco a discesa contenente un elenco dei progetti team di Visual Studio Online. Selezionare il nome del progetto team creato nei passaggi precedenti e fare clic sul pulsante con il segno di spunta della procedura guidata.<br/> ![][13]

Quando si effettuerà di nuovo il push di un commit al repository, Visual Studio Online compilerà e distribuirà il progetto in Azure.<br/>


## <a name="step4"> </a>Passaggio 4: Attivare una ricompilazione e ridistribuire il progetto

1. In Visual Studio aprire un file e modificarlo. Ad esempio, modificare il file _Layout.cshtml nella cartella Views\\Shared di un ruolo Web MVC. <br/> ![][17] 
2. Modificare il testo del piè di pagina per il sito e salvare il file.<br/> ![][18]

3. In Esplora soluzioni aprire il menu di scelta rapida per il nodo della soluzione, il nodo del progetto o il file modificato e scegliere **Commit**.<br/>

4. Digitare un commento e scegliere **Commit**.<br/> ![][20]

5. Scegliere il collegamento **Sincronizza**.<br/> ![][38]

6. Scegliere il collegamento **Push** per effettuare il push del commit al repository in Visual Studio Online. È anche possibile usare il pulsante **Sincronizza** per copiare i commit nel repository. La differenza sta nel fatto che **Sincronizza ** effettua anche il pull delle ultime modifiche dal repository.<br/> ![][39]

7. Scegliere il pulsante Home per tornare alla pagina iniziale di Team Explorer.<br/> ![][21]

8. Scegliere **Compilazioni** per visualizzare le compilazioni in corso.<br/> ![][22] <br/> Team Explorer mostra che è stata attivata una compilazione per la propria archiviazione.<br/> ![][23]

9. Fare doppio clic sul nome della compilazione in corso per visualizzare un log dettagliato con l'avanzare della compilazione.<br/>

10. Quando la compilazione è in corso, osservare la definizione di compilazione creata quando la procedura guidata è stata usata per eseguire il collegamento ad Azure. Aprire il menu di scelta rapida relativo alla definizione di compilazione e scegliere **Modifica definizione di compilazione**.<br/> ![][25] <br/> Nella scheda **Trigger** è possibile osservare che, per impostazione predefinita, la definizione di compilazione è configurata per la compilazione a ogni archiviazione. Per un servizio cloud, Visual Studio Online compila e distribuisce automaticamente il branch master nell'ambiente di gestione temporanea. È comunque necessario eseguire un'operazione manuale per distribuire il branch master nel sito attivo. Per un’app Web senza un ambiente di gestione temporanea, il branch master viene distribuito direttamente nel sito attivo.<br/> ![][26] <br/>Nella scheda **Processo** è possibile osservare che l'ambiente di distribuzione è configurato con il nome del proprio servizio cloud o app Web.<br/> ![][27] <br/> Specificare i valori per le proprietà se si desidera che siano diversi da quelli predefiniti. Le proprietà per la pubblicazione in Azure si trovano nella sezione Distribuzione e potrebbe anche essere necessario impostare i parametri MSBuild. Ad esempio, in un progetto di servizio cloud, per specificare una configurazione del servizio diversa da "Cloud", impostare i parametri MSbuild su /p:TargetProfile=*ProfiloPersonale* dove *ProfiloPersonale* corrisponde a un file di configurazione del servizio con un nome simile a ServiceConfiguration.*ProfiloPersonale*.cscfg. La tabella seguente illustra le proprietà disponibili nella sezione Distribuzione:
	<table>
<tr><td><b>Proprietà</b></td><td><b>Valore predefinito</b></td></tr>
><tr><td>Consenti certificati non attendibili</td><td>Se è false, i certificati SSL devono essere firmati da un'autorità radice.</td></tr>
<tr><td>Consenti aggiornamento</td><td>Consente l'aggiornamento di una distribuzione esistente anziché crearne una nuova. Conserva l'indirizzo IP.</td></tr><tr><td>Non eliminare</td><td>Se è true, una distribuzione non correlata esistente non viene sovrascritta (l'aggiornamento è consentito).</td></tr>
<tr><td>Percorso impostazioni di distribuzione</td><td>Percorso del file con estensione pubxml di un’app Web, relativo alla cartella radice del repository. Viene ignorato per i servizi cloud.</td></tr>
<tr><td>Ambiente di distribuzione SharePoint</td><td>Analoga al nome del servizio</td></tr>
<tr><td>Ambiente di distribuzione Azure</td><td>Nome dell’app Web o del servizio cloud</td></tr>
</table> <br/>

11. A questo punto la compilazione sarà stata completata correttamente.<br/> ![][28]

12. Facendo doppio clic sul nome della compilazione, in Visual Studio verrà visualizzato un **Riepilogo compilazione** che include eventuali risultati del test restituiti dai progetti unit test associati.<br/> ![][29]

13. Nel [portale di Azure](http://manage.windowsazure.com) è possibile visualizzare la distribuzione associata nella scheda Distribuzioni quando si seleziona l'ambiente di gestione temporanea.<br/> ![][30]

14.	Passare all'URL del proprio sito. Per un’app Web, scegliere semplicemente il pulsante **Sfoglia** nel portale. Per un servizio cloud, scegliere l'URL nella sezione **Riepilogo rapido** della pagina **Dashboard** in cui è visualizzato l'ambiente di gestione temporanea. Le distribuzioni derivanti dall'integrazione continua per i servizi cloud vengono pubblicate nell'ambiente di gestione temporanea per impostazione predefinita. È possibile modificare questa impostazione configurando la proprietà Ambiente del servizio cloud alternativo su Produzione. Ecco dove si trova l'URL del sito nella pagina del dashboard del servizio cloud: <br/> ![][31] <br/> Il sito in esecuzione verrà aperto in una nuova scheda del browser.<br/> ![][32]

15.	Se si apportano altre modifiche al progetto, si attiveranno altre compilazioni e si accumuleranno più distribuzioni. La più recente è contrassegnata come Attiva.<br/> ![][33]

## <a name="step5"> </a>Passaggio 5: Ridistribuire una compilazione precedente

Questo passaggio è facoltativo. Nel portale di gestione selezionare una distribuzione precedente e fare clic su **Ridistribuisci** per riportare il sito a un'archiviazione precedente. Si noti che verrà attivata una nuova compilazione in TFS e verrà creata una nuova voce nella cronologia della distribuzione.<br/> ![][34]

## <a name="step6"> </a>Passaggio 6: Modificare la distribuzione di produzione

 Quando si è pronti, è possibile alzare di livello l'ambiente di gestione temporanea all'ambiente di produzione scegliendo **Scambia** nel portale di gestione. L'ambiente di gestione temporanea appena distribuito verrà promosso alla produzione e il precedente ambiente di produzione (se presente) diventerà un ambiente di gestione temporanea. La distribuzione attiva potrebbe differire per gli ambienti di produzione e di gestione temporanea, ma la cronologia di distribuzione delle compilazioni recenti è la stessa indipendentemente dall'ambiente.<br/> ![][35]

## <a name="step7"> </a>Passaggio 6: Distribuire da un branch di lavoro.

Quando si usa Git, in genere si apportano modifiche in un branch di lavoro che vengono integrate nel branch master quando lo sviluppo è terminato. Durante la fase di sviluppo di un progetto, è consigliabile compilare e distribuire il branch di lavoro in Azure.

1. In Team Explorer scegliere il pulsante **Home** e quindi **Branch**.<br/> ![][40]

2. Scegliere il collegamento **Nuovo branch**.<br/> ![][41]

3. Digitare il nome del branch, ad esempio "working", e scegliere **Crea branch**. Verrà creato un nuovo branch locale.<br/> ![][42]

4. Pubblicare il branch. Scegliere il nome del branch in **Branch non pubblicati** e scegliere **Pubblica**.<br/> ![][44]

6. Per impostazione predefinita, solo le modifiche al branch master attivano una compilazione continua. Per configurare una compilazione continua per un branch di lavoro, scegliere la pagina Compilazioni in Team Explorer e scegliere **Modifica definizione di compilazione**.

7. Aprire la scheda **Impostazioni di origine**. In **Branch monitorati** per le compilazioni a integrazione continua e in corso scegliere **Fare clic qui per aggiungere una nuova riga**.<br/> ![][47]

8. Specificare il branch creato, ad esempio refs/heads/working. ![][48]

9. Modificare il codice, aprire il menu di scelta rapida per il file modificato e scegliere **Commit**.<br/> ![][43]

10. Scegliere il collegamento **Commit non sincronizzati** e scegliere il pulsante **Sincronizza** o il collegamento **Push** per copiare le modifiche nella copia del branch di lavoro in Visual Studio Online. ![][45]

11. Passare alla visualizzazione **Compilazioni** e cercare la compilazione appena attivata per il branch di lavoro.

Per altre informazioni, vedere [Visual Studio Online](http://go.microsoft.com/fwlink/?LinkId=253861). Per altri suggerimenti su come usare Git con Visual Studio Online, vedere [Condividere il codice in Git](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx) e per informazioni sull'uso di un repository Git non gestito da Visual Studio Online per pubblicare in Azure, vedere [Distribuzione continua tramite GIT in Azure App Service](../web-sites-publish-source-control.md).

[Passaggio 1: Creare un repository Git.]: #step1
[Passaggio 2: Creare un progetto ed eseguirne il push nel repository Git.]: #step2
[Passaggio 3: Connettere il progetto ad Azure.]: #step3
[Passaggio 4: Modificare e attivare una ricompilazione e una ridistribuzione.]: #step4
[Passaggio 5: Ridistribuire una compilazione precedente (facoltativo).]: #step5
[Passaggio 6: Modificare la distribuzione di produzione.]: #step6
[Passaggio 7: Distribuire da un branch di lavoro.]: #step7
[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateTeamProjectInGit.PNG
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png
[3]: ./media/cloud-services-continuous-delivery-use-vso-git/CloneThisRepository.PNG
[4]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateNewSolutionInClonedRepo.PNG
[7]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitMenuItem.PNG
[8]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[9]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateCloudService.PNG
[10]: ./media/cloud-services-continuous-delivery-use-vso-git/SetUpPublishingWithVSO.PNG
[11]: ./media/cloud-services-continuous-delivery-use-vso-git/AuthorizeConnection.PNG
[12]: ./media/cloud-services-continuous-delivery-use-vso-git/ConnectionRequest.PNG
[13]: ./media/cloud-services-continuous-delivery-use-vso-git/ChooseARepo3.PNG
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso-git/MakeACodeChange.PNG
[20]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[21]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerHome.png
[22]: ./media/cloud-services-continuous-delivery-use-vso-git/TeamExplorerBuilds.PNG
[23]: ./media/cloud-services-continuous-delivery-use-vso-git/BuildInQueue.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso-git/ProcessTab.PNG
[28]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso-git/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateANewAccount.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso-git/PushCurrentBranch.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso-git/BranchesInTeamExplorer.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso-git/NewBranch.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso-git/CreateBranch.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso-git/CommitAChange2.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso-git/PublishBranch.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso-git/SyncChanges2.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso-git/SourceSettingsPage.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso-git/IncludeWorkingBranch.PNG
 

<!---HONumber=July15_HO3-->