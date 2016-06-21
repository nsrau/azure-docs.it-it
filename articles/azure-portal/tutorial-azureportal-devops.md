<properties
	pageTitle="Esercitazione: DevOps con il portale di Azure | Microsoft Azure"
	description="Informazioni sui diversi flussi di lavoro di DevOps nel portale di Azure."
	services="azure-portal"
	documentationCenter=""
	authors="mlearned"
	manager="douge"
	editor="mlearned"/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="06/05/2016"
	ms.author="mlearned"/>

# Esercitazione: DevOps con il portale di Azure

La piattaforma Azure include diversi flussi di lavoro flessibili di DevOps. Questa esercitazione illustra come sfruttare le funzionalità del portale di Azure per sviluppare, testare, distribuire, risolvere i problemi, monitorare e gestire le applicazioni in esecuzione. L'esercitazione si incentra sugli argomenti seguenti:

1.  Creazione di un'app Web e abilitazione della distribuzione continua

2.  Sviluppare e testare un'app

3.  Monitoraggio e risoluzione dei problemi di un'app

4.  Attività di gestione di applicazioni generali

## Creazione di un'app Web e abilitazione della distribuzione continua

Creare un'app Web con il [servizio app di Azure](https://azure.microsoft.com/services/app-service/), che verrà usato nel resto di questa esercitazione. All'inizio si abiliterà la distribuzione continua dal repository di codice sorgente all'ambiente di Azure in esecuzione.

1.  Accedere al portale di Azure.

2.  Scegliere **Servizi app** &gt; **icona Aggiungi** e immettere un nome, scegliere la sottoscrizione e creare un nuovo gruppo di risorse da usare come contenitore per il servizio.

    I gruppi di risorse consentono di gestire diversi aspetti della soluzione, ad esempio, fatturazione, distribuzioni e monitoraggio, come un unico gruppo tramite [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-overview/).

	![Immagine1][image1]


3.  Dopo pochi secondi, verrà creato il servizio app. Dedicare qualche minuto all'esplorazione delle diverse opzioni di menu per il servizio nel portale.

	![Immagine2][image2]

4.  Fare clic sull'URL. Si noti la varietà di scelte disponibili per gli strumenti e i repository. È anche possibile usare i linguaggi e i framework preferiti, inclusi .NET, Java e Ruby.

	![Immagine3][image3]

3.  Con il portale di Azure la distribuzione continua è un processo facile che richiede solo pochi semplici passaggi. Nel portale di Azure scegliere le impostazioni dall'icona del servizio app appena creato.

    ![Immagine4][image4]

    Nel pannello che si apre a destra scorrere fino alla sezione Pubblicazione.

    ![Immagine5][image5]

4.  Configurare poi alcune impostazioni per abilitare la distribuzione continua per l'app. Fare clic su Origine distribuzione e quindi su Scegliere l'origine. Si noti la varietà di opzioni disponibili per le origini del repository.

	![Immagine6][image6]

1.  Per questo esempio scegliere GitHub. In alternativa, è possibile scegliere il repository preferito e configurare le credenziali di autorizzazione.

    ![Immagine7][image7]

2.  Dopo l'autorizzazione per il repository, è possibile scegliere un progetto e il ramo che si vuole distribuire. Sotto sono elencati diversi esempi fittizi.

    ![Immagine8][image8]

3.  Dopo avere scelto il progetto e il ramo, fare clic su OK. Verranno visualizzate le notifiche di una distribuzione.

    ![Immagine9][image9]

4.  Tornare a Github per visualizzare il webhook creato per integrare il repository del controllo del codice sorgente con Azure. Il portale di Azure abilita l'integrazione con Github con pochi semplici passaggi.

    ![Immagine10][image10]

5.  Per eseguire una dimostrazione della distribuzione continua, si aggiunge rapidamente contenuto al repository. Per un semplice esempio, aggiungere un file di testo di esempio a un repository Github. È possibile usare .NET, Ruby, Python o un altro tipo di applicazione con il servizio app. È possibile aggiungere un file di testo, un'applicazione ASP.NET MVC, Java o Ruby al repository scelto.

    ![Immagine11][image11]

6.  Dopo avere eseguito il commit delle modifiche nel repository, nell'area delle notifiche del portale verrà indicato l'avvio di una nuova distribuzione. Fare clic su Sincronizza se dopo il commit nel repository le modifiche non vengono visualizzate rapidamente.

    ![Immagine12][image12]

7.  A questo punto, se si prova a caricare la pagina del servizio app, è possibile che venga visualizzato un errore 403. In questo esempio si verifica perché non è stato configurato un documento predefinito tipico per la pagina, ad esempio un file index.htm o default.html. È possibile risolvere rapidamente il problema con gli strumenti del portale di Azure. Nel portale di Azure scegliere Impostazioni &gt; Impostazioni applicazione.

	 ![Immagine13][image13]

8.  Viene aperto un pannello per le impostazioni dell'applicazione. Immettere il nome della pagina "SamplePage.html" e fare clic su Salva. Dedicare qualche minuto all'esplorazione delle altre impostazioni.

	![Immagine14][image14]

9.  È possibile aggiornare l'URL del browser per assicurarsi di visualizzare le modifiche previste. In questo caso, un semplice testo popolerà la pagina. Ogni altra modifica al repository comporterà una nuova distribuzione automatica.

    ![Immagine15][image15]

    Abilitare la distribuzione continua con il portale di Azure è un'esperienza semplice. È anche possibile creare pipeline di rilascio più complesse e usare diverse altre tecniche con il controllo del codice sorgente esistente e sistemi di integrazione continua per la distribuzione in Azure, ad esempio sfruttando sistemi automatizzati di gestione della compilazione e del rilascio.

## Sviluppare e testare un'app

Apportare ora alcune modifiche alla base codici e distribuirle rapidamente. Si configurerà anche un test delle prestazioni per l'app Web.

1.  Nel portale di Azure scegliere Servizi app nel riquadro di spostamento e trovare il servizio app.

    ![Immagine16][image16]

2.  Fare clic su Strumenti.

    ![Immagine17][image17]

3.  Si noti la categoria Sviluppo sotto Strumenti, in cui sono disponibili diversi utili strumenti che consentono di usare le app senza uscire dal portale di Azure. Fare clic su Console.

    ![Immagine18][image18]

4.  Nella finestra Console è possibile eseguire comandi attivi per l'app. Digitare il comando dir e premere INVIO. Si noti che i comandi che richiedono privilegi elevati non funzionano.

    ![Immagine19][image19]

5.  Tornare alla categoria Sviluppo e scegliere Visual Studio Online. Nota: Visual Studio Online si chiama ora Visual Studio Team Services.

    ![Immagine20][image20]

6.  Attivare l'esperienza di modifica nel browser per l'app.

    ![Immagine21][image21]

7.  Viene installata un'estensione Web per l'app. Le estensioni aggiungono funzionalità alle app in Azure velocemente e facilmente. Nello screenshot seguente sono visibili alcuni degli altri tipi di estensione disponibili.

    ![Immagine22][image22]

8.  Una volta installata l'estensione Visual Studio Online, fare clic su Vai.

    ![Immagine23][image23]

9.  Si apre una scheda del browser in cui viene direttamente visualizzato un IDE di sviluppo. Si noti che nell'immagine seguente viene usato Chrome.

    ![Immagine24][image24]

10. È possibile eseguire diverse attività, ad esempio modificare file, aggiungere file e cartelle e scaricare contenuto dal sito attivo. Apportare una rapida modifica al file SamplePage.html.

    ![Immagine25][image25]

11. Dopo alcuni istanti, le modifiche vengono automaticamente salvate. Se si torna alla pagina, è possibile visualizzarle. Tenere presente che modifiche di questo tipo non sono per lo più adatte agli ambienti di produzione. Gli strumenti consentono tuttavia di apportare molto facilmente modifiche rapide agli ambienti di sviluppo e di test.

    ![Immagine26][image26]

    ![Immagine27][image27]

12. Tornare al pannello Strumenti e nella categoria Sviluppo fare clic su Test delle prestazioni.

    ![Immagine28][image28]

13. È necessario impostare un account di Team Services. Per altri dettagli, vedere [Create a Team Services Account](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (Creare un account di Team Services).

14. Fare clic su Nuovo per creare un test delle prestazioni.

    ![Immagine29][image29]

    Configurare i diversi valori e fare clic su Esegui test nella parte inferiore della finestra di dialogo per avviare un test delle prestazioni.

    ![Immagine30][image30]

	![Immagine31][image31]

1.  Una volta avviata l'esecuzione del test, è possibile monitorare lo stato.

    ![Immagine32][image32]

    Una volta terminato il test, fare clic sul risultati per visualizzare altri dettagli.

    ![Immagine33][image33]

2.  In questo esempio è stata creata una rapida esecuzione dei test, quindi i dati da analizzare sono limitati, ma è possibile notare diverse metriche, oltre a eseguire di nuovo il test da questa visualizzazione. Il portale di Azure facilita la creazione, l'esecuzione e l'analisi dei test prestazioni Web. Gli screenshot seguenti visualizzano i dati sulle prestazioni.

    ![Immagine34][image34]

    ![Immagine35][image35]

    ![Immagine36][image36]

## Monitoraggio e risoluzione dei problemi di un'app

Azure offre diverse funzionalità per il monitoraggio e la risoluzione dei problemi delle applicazioni in esecuzione.

1.  Nel portale di Azure per l'app Web scegliere Strumenti.

    ![Immagine37][image37]

2.  Nella categoria Risoluzione dei problemi sono disponibili diverse scelte per l'uso di strumenti per risolvere potenziali problemi con un'app in esecuzione. È possibile, ad esempio, monitorare il traffico HTTP in tempo reale, abilitare la riparazione automatica, visualizzare log e altro ancora.

    ![Immagine38][image38]

3.  Scegliere Metriche del sito per ottenere velocemente una visualizzazione di alcuni codici HTTP.

    ![Immagine39][image39]

4.  Scegliere Diagnostica distribuita come servizio. Scegliere il tipo di applicazione, quindi scegliere Esegui.

    ![Immagine40][image40]

    Inizia una raccolta.

    ![Immagine41][image41]

5.  È possibile scegliere il log appropriato per diagnosticare i potenziali problemi. È necessario abilitare la registrazione per visualizzare tutte le opzioni dei dati disponibili, ad esempio HTTP Logs (Log HTTP).

    ![Immagine42][image42]

    Facendo clic sul file di immagine della memoria, è possibile scaricare e analizzare un report analisi DebugDiag per trovare i potenziali problemi.

    ![Immagine43][image43]

6.  Per visualizzare altri dati, è necessario abilitare altre opzioni di registrazione. Nel portale di Azure andare all'app Web e scegliere Impostazioni.

    ![Immagine44][image44]

7.  Scorrere verso il basso fino alla categoria Funzionalità e scegliere Log di diagnostica.

  	 ![Immagine45][image45]

8.  Si notino le diverse opzioni per la registrazione. Attivare Registrazione server Web e fare clic su Salva.

    ![Immagine46][image46]

9.  Tornare all'area degli strumenti per l'app, scegliere Diagnostica distribuita come servizio e fare clic su Esegui per eseguire di nuovo la raccolta dati.

    ![Immagine47][image47]

10. Con l'impostazione per la registrazione HTTP abilitata, vengono visualizzati i dati raccolti per HTTP Logs (Log HTTP).

    ![Immagine48][image48]

11. Fare clic sul log del file HTML, per generare un report avanzato basato sul browser per altre indagini.

    ![Immagine49][image49]

12. Tornare alla sezione Strumenti nel portale di Azure per l'app. Scorrere fino alla sezione Strumenti e scegliere Esplora processi.

    ![Immagine50][image50]

13. Scegliendo Esplora processi, è possibile visualizzare i dettagli sui processi in esecuzione. Come si può notare dalle immagini seguenti, dal portale di Azure è anche possibile eseguire il drill-down dei processi e terminarli.

    ![Immagine51][image51]

    ![Immagine52][image52]

14. Tornare al pannello Impostazioni a sinistra. Fare clic su Nuova richiesta di supporto.

    ![Immagine53][image53]

15. Nel pannello a destra è possibile compilare i dettagli sui problemi, immettere le informazioni sul contatto e anche caricare i dati di diagnostica. Il portale di Azure consente di collaborare con il supporto Microsoft in modo molto facile.

    ![Immagine54][image54]

    ![Immagine55][image55]

    Il portale di Azure consente di offrire esperienze avanzate e familiari di uso degli strumenti per monitorare e risolvere i problemi delle applicazioni in esecuzione. È anche possibile agire rapidamente eseguendo attività come il riciclo dei processi, l'abilitazione e la disabilitazione di diverse raccolte dati e l'interazione con il supporto dei professionisti Microsoft.

## Gestione di applicazioni generale

Quando si gestiscono applicazioni, spesso è necessario eseguire un'ampia gamma di attività, ad esempio configurare strategie di backup, implementare e gestire provider di identità e configurare il controllo degli accessi in base al ruolo. Come con le altre esperienze DevOps, la piattaforma Azure integra queste attività direttamente nel portale.

1.  Per assicurarsi di proteggere l'app Web dalla perdita dei dati, è necessario configurare i backup. Andare all'area Impostazioni per l'app Web.

    ![Immagine56][image56]

2.  Nel pannello a destra scorrere verso il basso fino alla categoria Funzionalità.

	 ![Immagine57][image57]

1.  Scegliere Backup. Si apre un pannello a destra.

    ![Immagine58][image58]

2.  Fare clic su Configura, scegliere un account di archiviazione dal pannello a destra.

    ![Immagine59][image59]

3.  Ora creare e scegliere un contenitore di archiviazione in cui salvare i backup. Fare clic su Crea nella parte inferiore del pannello, quindi selezionare il contenitore.

    ![Immagine60][image60]

4.  Una volta scelto il contenitore, è possibile configurare le pianificazioni, oltre ai backup per i database. Per questo scenario, fare clic sull'icona Salva.

     ![Immagine61][image61]

5.  Dopo avere salvato, scorrere di nuovo fino al pannello a sinistra per Backup. Fare clic su Esegui backup ora per eseguire il backup dell'applicazione.

     ![Immagine62][image62]

6.  Dopo alcuni istanti, viene creato un backup. Si noti l'opzione Ripristina ora nello screenshot seguente.

     ![Immagine63][image63]

7.  Fare clic su Ripristina ora ed esaminare le opzioni nel pannello a destra. È possibile scegliere un backup appropriato e ripristinare facilmente uno stato precedente, se necessario. Il portale di Azure ha consentito di abilitare facilmente una semplice strategia di ripristino di emergenza per l'app.

     ![Immagine64][image64]

8.  Tornare al pannello Impostazioni a sinistra e in Funzionalità scegliere Autenticazione/Autorizzazione.

     ![Immagine65][image65]

9.  Nel pannello a destra scegliere Autenticazione servizio app. Si noti la varietà di opzioni che è possibile configurare con noti provider.

     ![Immagine66][image66]

10. Scegliere il provider preferito e notare le opzioni per l'ambito. È possibile specificare un ID app e una chiave privata app e abilitare rapidamente l'autenticazione Facebook per l'app. Il portale di Azure consente l'autenticazione come soluzione chiavi in mano per le app.

     ![Immagine67][image67]

11. Tornare al pannello Impostazioni e scegliere Utenti nella categoria Gestione risorse.

     ![Immagine68][image68]

12. Nel pannello a destra esaminare le diverse opzioni per l'aggiunta di ruoli e utenti. Il portale di Azure consente di controllare facilmente il controllo degli accessi in base al ruolo per l'applicazione.

     ![Immagine69][image69]


## Riepilogo

Questa esercitazione ha illustrato alcune potenzialità della piattaforma Azure abilitando rapidamente la distribuzione continua per un'app Web, eseguendo diverse attività di sviluppo e test, monitorando e risolvendo i problemi di un'app attiva e infine gestendo strategie chiave, ad esempio il ripristino di emergenza, l'identità e il controllo degli accessi in base al ruolo. La piattaforma Azure consente un'esperienza integrata per questi flussi di lavoro di DevOps ed è possibile lavorare in modo efficiente rimanendo nell'ambito dell'attività in questione.


## Passaggi successivi 

* Azure Resource Manager è importante per l'abilitazione di DevOps nella piattaforma Azure. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../resource-group-overview.md).

* Per altre informazioni sulla distribuzione del servizio app di Azure, vedere [Distribuire l'app nel servizio app di Azure](../app-service-web/web-sites-deploy.md)


[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png

<!---HONumber=AcomDC_0615_2016-->