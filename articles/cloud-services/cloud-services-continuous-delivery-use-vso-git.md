---
title: Recapito continuo con Git e Visual Studio Team Services in Azure | Documentazione Microsoft
description: "Informazioni sulla configurazione dei progetti team di Visual Studio Team Services per usare Git per la compilazione e la distribuzione automatiche alla funzionalità App Web nel Servizio app di Azure o nei servizi cloud."
services: cloud-services
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: 4b3297ef-0de6-4d5f-925c-fcdacc3085ac
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/06/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 91e061820e8edc81bca395dc78d3e1e840b21438


---
# <a name="continuous-delivery-to-azure-using-visual-studio-team-services-and-git"></a>Recapito continuo in Azure tramite Visual Studio Team Services e Git
È possibile usare i progetti team di Visual Studio Team Services per ospitare un repository Git per il codice sorgente e compilarlo e distribuirlo automaticamente nelle app Web o nei servizi cloud di Azure quando si effettua il push di un commit nel repository.

È necessario che siano installati Visual Studio 2013 e Azure SDK. Se non si dispone ancora di Visual Studio 2013, scaricarlo scegliendo il collegamento **Inizia gratuitamente** all'indirizzo [www.visualstudio.com](http://www.visualstudio.com). Installare Azure SDK da [questa pagina](http://go.microsoft.com/fwlink/?LinkId=239540).

> [!NOTE]
> Per completare l'esercitazione, è necessario un account di Visual Studio Team Services: è possibile [aprire un account di Visual Studio Team Services gratuitamente](http://go.microsoft.com/fwlink/p/?LinkId=512979).
> 
> 

Per configurare un servizio cloud da compilare e distribuire automaticamente in Azure tramite Visual Studio Team Services, seguire questa procedura.

## <a name="1-create-a-git-repository"></a>1: Creare un repository Git
1. Se non si ha già un account Visual Studio Team Services, è possibile ottenerne uno [qui](http://go.microsoft.com/fwlink/?LinkId=397665). Quando si crea il progetto team, scegliere Git come sistema di controllo del codice sorgente. Seguire le istruzioni per collegare Visual Studio al progetto team.
2. In **Team Explorer** fare clic sul collegamento **Clona questo repository**.
   
    ![][3]
3. Specificare il percorso della copia locale e quindi scegliere il pulsante **Clona** .

## <a name="2-create-a-project-and-commit-it-to-the-repository"></a>2: Creare un progetto ed eseguirne il commit nel repository
1. Nella sezione **Soluzioni** di **Team Explorer** scegliere il collegamento **Nuovo** per creare un nuovo progetto nel repository locale.
   
    ![][4]
2. È possibile distribuire un’app Web o un servizio cloud (applicazione Azure) seguendo i passaggi di questa procedura dettagliata. Creare un nuovo progetto servizio cloud di Azure o un nuovo progetto ASP.NET MVC. Assicurarsi che il progetto sia per .NET Framework 4 o versione successiva. Se si crea un progetto di servizio cloud, aggiungere un ruolo Web ASP.NET MVC e un ruolo di lavoro.
   Per creare un'app Web, scegliere il modello di progetto **Applicazione Web ASP.NET** e quindi **MVC**. Per altre informazioni, vedere [Creare un'app Web ASP.NET in Servizio app di Azure](../app-service-web/web-sites-dotnet-get-started.md) .
3. Aprire il menu di scelta rapida relativo alla soluzione e scegliere **Commit**.
   
    ![][7]
4. Se è la prima volta che si usa Git in Visual Studio Team Services, sarà necessario fornire alcune informazioni per identificarsi in Git. Nell'area **Modifiche in sospeso** di **Team Explorer** immettere il proprio nome utente e indirizzo di posta elettronica. Immettere un commento per il commit e quindi scegliere il pulsante **Commit** .
   
    ![][8]
5. Notare le opzioni per includere o escludere modifiche specifiche quando si esegue l'archiviazione. Se le modifiche desiderate sono escluse, scegliere **Includi tutto**.
6. È stato eseguito il commit delle modifiche nella copia locale del repository. A questo punto, sincronizzare le modifiche con il server scegliendo il collegamento **Sincronizza** .

## <a name="3-connect-the-project-to-azure"></a>3: Collegare il progetto ad Azure
1. Ora si dispone di un repository Git in Visual Studio Team Services contenente codice sorgente ed è possibile connettere il repository ad Azure.  Nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885) selezionare il servizio cloud o l'app Web oppure crearne uno nuovo selezionando l'icona + in basso a sinistra e scegliendo **Servizio cloud** o **App Web** e quindi **Creazione rapida**.
   
    ![][9]
2. Per i servizi cloud, scegliere il collegamento **Imposta pubblicazione con Visual Studio Team Services** . Per le app Web scegliere il collegamento **Imposta distribuzione dal controllo del codice sorgente** .
   
    ![][10]
3. Nella procedura guidata digitare il nome del proprio account di Visual Studio Team Services nella casella di testo e fare clic sul collegamento **Autorizza ora** . È possibile che venga richiesto di effettuare l'accesso.
   
    ![][11]
4. Nella finestra di dialogo popup **Connection Request** (Richiesta di connessione) scegliere **Accetta** per autorizzare Azure a configurare il progetto team in Visual Studio Team Services.
   
    ![][12]
5. Dopo aver concesso l'autorizzazione verrà visualizzato un elenco a discesa contenente un elenco dei progetti team di Visual Studio Team Services.  Selezionare il nome del progetto team creato nei passaggi precedenti e fare clic sul pulsante con il segno di spunta della procedura guidata.
   
    ![][13]
   
    Quando si effettuerà di nuovo il push di un commit al repository, Visual Studio Team Services compilerà e distribuirà il progetto in Azure.

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4: Attivare una ricompilazione e ridistribuire il progetto
1. In Visual Studio aprire un file e modificarlo. Modificare ad esempio il file `_Layout.cshtml` nella cartella Views\\Shared in un ruolo Web MVC.
   
    ![][17]
2. Modificare il testo del piè di pagina per il sito e salvare il file.
   
    ![][18]
3. In **Esplora soluzioni** aprire il menu di scelta rapida relativo al nodo della soluzione, al nodo del progetto o al file modificato e quindi scegliere **Commit**.
4. Digitare un commento e scegliere **Commit**.
   
    ![][20]
5. Scegliere il collegamento **Sincronizza** .
   
    ![][38]
6. Scegliere il collegamento **Push** per effettuare il push del commit al repository in Visual Studio Team Services. È anche possibile usare il pulsante **Sincronizza** per copiare i commit nel repository. La differenza sta nel fatto che **Sincronizza** effettua anche il pull delle ultime modifiche dal repository.
   
    ![][39]
7. Scegliere il pulsante **Home** per tornare alla pagina iniziale di **Team Explorer**.
   
    ![][21]
8. Scegliere **Compilazioni** per visualizzare le compilazioni in corso.
   
    ![][22]
   
    **Team Explorer** mostra che è stata attivata una compilazione per l'archiviazione.
   
    ![][23]
9. Fare doppio clic sul nome della compilazione in corso per visualizzare un log dettagliato con l'avanzare della compilazione.
10. Quando la compilazione è in corso, osservare la definizione di compilazione creata quando la procedura guidata è stata usata per eseguire il collegamento ad Azure.  Aprire il menu di scelta rapida relativo alla definizione di compilazione e scegliere **Modifica definizione di compilazione**.
    
    ![][25]
11. Nella scheda **Trigger** è possibile osservare che, per impostazione predefinita, la definizione di compilazione è impostata per la compilazione a ogni archiviazione. Per un servizio cloud, Visual Studio Team Services compila e distribuisce automaticamente il branch master nell'ambiente di gestione temporanea. È comunque necessario eseguire un'operazione manuale per distribuire il branch master nel sito attivo. Per un'app Web senza un ambiente di gestione temporanea, il branch master viene distribuito direttamente nel sito attivo.
    
    ![][26]
12. Nella scheda **Processo** è possibile osservare che l'ambiente di distribuzione è configurato con il nome del proprio servizio cloud o dell'app Web.
    
     ![][27]
13. Specificare i valori per le proprietà se si desidera che siano diversi da quelli predefiniti. Le proprietà per la pubblicazione in Azure si trovano nella sezione **Distribuzione** e potrebbe anche essere necessario impostare i parametri MSBuild. In un progetto di servizio cloud, ad esempio, per specificare una configurazione del servizio diversa da "Cloud", impostare i parametri MSbuild su `/p:TargetProfile=[YourProfile]`, dove *[YourProfile]* corrisponde a un file di configurazione del servizio con un nome simile a ServiceConfiguration.*ProfiloPersonale*.cscfg.
    
     La tabella seguente illustra le proprietà disponibili nella sezione **Distribuzione** :
    
    | Proprietà | Valore predefinito |
    | --- | --- |
    | Consenti certificati non attendibili |Se è false, i certificati SSL devono essere firmati da un'autorità radice. |
    | Consenti aggiornamento |Consente l'aggiornamento di una distribuzione esistente anziché crearne una nuova. Conserva l'indirizzo IP. |
    | Non eliminare |Se è true, una distribuzione non correlata esistente non viene sovrascritta (l'aggiornamento è consentito). |
    | Percorso impostazioni di distribuzione |Percorso del file con estensione pubxml di un’app Web, relativo alla cartella radice del repository. Viene ignorato per i servizi cloud. |
    | Ambiente di distribuzione SharePoint |Analogo al nome del servizio. |
    | Ambiente di distribuzione Azure |Nome dell'app Web o del servizio cloud. |
14. A questo punto la compilazione sarà stata completata correttamente.
    
     ![][28]
15. Facendo doppio clic sul nome della compilazione, Visual Studio visualizza un **Riepilogo compilazione**che include eventuali risultati del test restituiti dai progetti unit test associati.
    
     ![][29]
16. Nel [portale di Azure classico](http://go.microsoft.com/fwlink/?LinkID=213885)è possibile visualizzare la distribuzione associata nella scheda **Distribuzioni** quando si seleziona l'ambiente di gestione temporanea.
    
     ![][30]
17. Passare all'URL del proprio sito. Per un'app Web, scegliere semplicemente il pulsante **Esplora** nel portale. Per un servizio cloud, scegliere l'URL nella sezione **Riepilogo rapido** della pagina **Dashboard** in cui è visualizzato l'ambiente di gestione temporanea.
    
    Le distribuzioni derivanti dall'integrazione continua per i servizi cloud vengono pubblicate nell'ambiente di gestione temporanea per impostazione predefinita. È possibile modificare questa impostazione configurando la proprietà **Ambiente del servizio cloud alternativo** su **Produzione**. Ecco dove si trova l'URL del sito nella pagina del dashboard del servizio cloud.
    
    ![][31]
    
    Il sito in esecuzione verrà aperto in una nuova scheda del browser.
    
    ![][32]
18. Se si apportano altre modifiche al progetto, si attiveranno altre compilazioni e si accumuleranno più distribuzioni. L'ultima è contrassegnata come Attiva.
    
    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5: Ridistribuire una compilazione precedente
Questo passaggio è facoltativo. Nel portale di Azure classico, selezionare una distribuzione precedente e fare clic sul pulsante **Ridistribuisci** per riportare il sito a un'archiviazione precedente. Si noti che verrà attivata una nuova compilazione in TFS e verrà creata una nuova voce nella cronologia della distribuzione.

![][34]

## <a name="6-change-the-production-deployment"></a>6: Modificare la distribuzione di produzione
Quando si è pronti, è possibile alzare di livello l'ambiente di gestione temporanea all'ambiente di produzione scegliendo **Scambia** nel portale di Azure classico. L'ambiente di gestione temporanea appena distribuito verrà promosso alla produzione e il precedente ambiente di produzione (se presente) diventerà un ambiente di gestione temporanea. La distribuzione attiva potrebbe differire per gli ambienti di produzione e di gestione temporanea, ma la cronologia di distribuzione delle compilazioni recenti è la stessa indipendentemente dall'ambiente.

![][35]

## <a name="7-deploy-from-a-working-branch"></a>7: Eseguire la distribuzione da un ramo di lavoro.
Quando si usa Git, in genere si apportano modifiche in un branch di lavoro che vengono integrate nel branch master quando lo sviluppo è terminato. Durante la fase di sviluppo di un progetto, è consigliabile compilare e distribuire il branch di lavoro in Azure.

1. In **Team Explorer** scegliere il pulsante **Home** e quindi il pulsante **Rami**.
   
    ![][40]
2. Scegliere il collegamento **Nuovo branch** .
   
    ![][41]
3. Digitare il nome del branch, ad esempio "working", e scegliere **Crea branch**. Verrà creato un nuovo branch locale.
   
    ![][42]
4. Pubblicare il branch. Scegliere il nome del ramo in **Rami non pubblicati** e scegliere **Pubblica**.
   
    ![][44]
5. Per impostazione predefinita, solo le modifiche al branch master attivano una compilazione continua. Per configurare una compilazione continua per un ramo di lavoro, scegliere la pagina **Compilazioni** in **Team Explorer** e quindi **Modifica definizione di compilazione**.
6. Aprire la scheda **Impostazioni di origine** . In **Rami monitorati per le compilazioni a integrazione continua e in corso** scegliere **Fare clic qui per aggiungere una nuova riga**.
   
    ![][47]
7. Specificare il branch creato, ad esempio refs/heads/working.
   
    ![][48]
8. Modificare il codice, aprire il menu di scelta rapida per il file modificato e scegliere **Commit**.
   
    ![][43]
9. Scegliere il collegamento **Commit non sincronizzati** e quindi il pulsante **Sincronizzazione** o il collegamento **Push** per copiare le modifiche nella copia del ramo di lavoro in Visual Studio Team Services.
   
   ![][45]
10. Passare alla visualizzazione **Compilazioni** e cercare la compilazione appena attivata per il branch di lavoro.

## <a name="next-steps"></a>Passaggi successivi
Per altri suggerimenti sull'uso di Git con Visual Studio Team Services, vedere l'articolo su come [sviluppare e condividere il codice con Git e Visual Studio](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx). Per informazioni sull'uso di un repository Git non gestito da Visual Studio Team Services per la pubblicazione in Azure, vedere [Distribuzione continua nel servizio app di Azure](../app-service-web/app-service-continuous-deployment.md). Per altre informazioni su Visual Studio Team Services, vedere [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861).

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



<!--HONumber=Dec16_HO2-->


