<properties
   pageTitle="Testare le prestazioni dell'app Web di Azure | Microsoft Azure"
   description="Eseguire test delle prestazioni dell'app Web di Azure per valutare come l'app gestisce il carico utente. Misurare i tempi di risposta e individuare gli errori che potrebbero indicare un problema."
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/11/2015"
   ms.author="estfan; manasma"/>

# Testare le prestazioni dell'app Web di Azure sotto carico

Verificare le prestazioni dell'app Web prima di implementarla nell'ambiente di produzione o di distribuire aggiornamenti. In questo modo, è possibile valutare se l'app è pronta per il rilascio ed essere sicuri che l'app sia in grado di gestire il traffico nei periodi di picco o in occasione del push di marketing successivo.

Durante l'anteprima pubblica, è possibile testare gratuitamente le prestazioni dell'app nel portale di anteprima di Azure. I test simulano il carico utente nell'app in un periodo di tempo specificato e misurano la risposta dell'app. Ad esempio, i test possono mostrare la velocità di risposta dell'app a un numero di utenti specifico o il numero di richieste non riuscite, che può indicare che l'app presenta alcuni problemi.

![Individuare problemi di prestazioni nell'app Web][TestOverview]

## Prima di iniziare

*	È necessario creare una [sottoscrizione di Azure][AzureSubscription], se non è già stato fatto. Informazioni su come [aprire gratuitamente un account di Azure][AzureFreeTrial].

*	È necessario avere un account [Visual Studio Online (VSO)][WhatIsVSO] per conservare la cronologia dei test delle prestazioni. È possibile creare il nuovo account durante la configurazione del test delle prestazioni o usare un account esistente, se si è il proprietario dell'account. [Operazioni che si possono eseguire con un account Visual Studio Online](#VSOAccount)

*	Distribuire l'app per il test in un ambiente non di produzione. Consentire all'app di usare un piano di servizio app diverso dal piano usato in produzione. In questo modo, il test non avrà alcun impatto sui clienti esistenti, né rallenterà l'app nell'ambiente di produzione.

## Configurare ed eseguire il test delle prestazioni

0.	Accedere al [portale di anteprima di Azure][AzurePortal]. Per usare un account Visual Studio online di cui si è proprietari, eseguire l'accesso come proprietario dell'account.

0.	Passare all'app Web.

	![Passare a Esplora tutto, fare clic su App Web e selezionare l'app Web testata][WebApp]

0.	Passare a **Test delle prestazioni**.

	![Passare a Strumenti e fare clic su Test delle prestazioni][ExpandedTools]
 
0.	È necessario collegare un account [Visual Studio Online (VSO)][WhatIsVSO] per conservare la cronologia dei test delle prestazioni.

	Se si ha già un account VSO, selezionarlo. In caso contrario, creare un nuovo account.

	![Selezionare un account VSO esistente o crearne uno nuovo][ExistingNewVSOAccount]

0.	Creare il test delle prestazioni. Impostare i dettagli ed eseguire il test. È possibile osservare i risultati in tempo reale durante l'esecuzione del test.

	Si prenda ad esempio un'app che ha distribuito buoni sconto durante i saldi dell'anno precedente. L'evento ha avuto una durata di 15 minuti con un carico di picco pari a 100 clienti simultanei. Quest'anno si vuole raddoppiare il numero dei clienti. E si vuole anche migliorare la soddisfazione dei clienti riducendo il tempo di caricamento della pagina da 5 secondi a 2 secondi. Verrà quindi eseguito il test delle prestazioni dell'app aggiornata con 250 utenti per 15 minuti.

	Verrà simulato il carico sull'app tramite la generazione di utenti virtuali (clienti) che visitano il sito Web contemporaneamente. In questo modo, sarà possibile determinare il numero di richieste non riuscite e di quelle che rispondono lentamente.

	![Creare, configurare ed eseguire il test delle prestazioni][NewTest]

	 *	L'URL predefinito dell'app Web viene aggiunto automaticamente. È possibile modificare l'URL per testare altre pagine (solo richieste HTTP GET).

	 *	Per simulare le condizioni locali e ridurre la latenza, selezionare la località più vicina agli utenti per generare il carico.

	Ecco il test in corso. Durante il primo minuto, la pagina viene caricata più lentamente di quanto previsto.

	![Test delle prestazioni in corso con dati in tempo reale][TestRunning]

	Al termine del test, si può osservare che nei minuti successivi al primo, il caricamento della pagina risulta molto più veloce. Questo permette di individuare da dove è opportuno partire per la risoluzione del problema.

	![Test delle prestazioni completato con i risultati, incluse le richieste non riuscite][TestDone]
	
I commenti e suggerimenti degli utenti sono molto apprezzati. Per domande o problemi, scrivere all'indirizzo <vsoloadtest@microsoft.com>.

##	Domande e risposte

####D: È previsto un limite di durata per l'esecuzione di un test? 

R: Sì. È possibile eseguire test per un massimo di un'ora nel portale di anteprima di Azure.

####D: Quanti sono i minuti disponibili per l'esecuzione dei test delle prestazioni? 

R: Dopo l'anteprima pubblica, l'utente ottiene 20.000 minuti utente virtuale (VUM) gratuiti ogni mese con l'account Visual Studio Online. Un minuto utente virtuale (VUM) corrisponde al numero di utenti virtuali moltiplicato per il numero di minuti nel test. Se i minuti gratuiti non sono sufficienti, è possibile acquistare altro tempo e pagare solo per i minuti effettivamente usati.

####D: Dove posso controllare il numero di minuti utente virtuale usati fino a questo momento?

R: È possibile visualizzare questo valore nel portale di anteprima di Azure.

![Passare all'account VSO][VSOAccount]

![Verifica dei minuti utente virtuale usati][CheckTestTime]

<a name="VSOAccount"></a>
####D: Quali altre operazioni si possono eseguire con un account Visual Studio Online?

R: Per individuare il nuovo account, passare a ```https://{accountname}.visualstudio.com```. È possibile condividere codice, compilare, monitorare lo stato del lavoro e distribuire software, tutto nel cloud e con qualsiasi strumento o linguaggio. Per altre informazioni su come le funzionalità e i servizi di [Visual Studio Online][WhatIsVSO] semplificano la collaborazione con il team e la distribuzione continua.

<!--Image references-->
[WebApp]: ./media/app-service-web-app-performance-test/azure-np-web-apps.png
[TestOverview]: ./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png
[ExpandedTools]: ./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png
[ExistingNewVSOAccount]: ./media/app-service-web-app-performance-test/azure-np-no-vso-account.png
[NewTest]: ./media/app-service-web-app-performance-test/azure-np-new-performance-test.png
[TestRunning]: ./media/app-service-web-app-performance-test/azure-np-running-perf-test.png
[TestDone]: ./media/app-service-web-app-performance-test/azure-np-perf-test-done.png
[VSOAccount]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts.png
[CheckTestTime]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png

<!--Reference links -->
[AzurePortal]: https://portal.azure.com
[AzureSubscription]: https://account.windowsazure.com/subscriptions
[AzureFreeTrial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[WhatIsVSO]: https://www.visualstudio.com/products/what-is-visual-studio-online-vs

<!---HONumber=Oct15_HO3-->