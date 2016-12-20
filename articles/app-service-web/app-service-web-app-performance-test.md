---
title: Testare le prestazioni dell&quot;app Web di Azure | Documentazione Microsoft
description: Eseguire test delle prestazioni dell&quot;app Web di Azure per valutare come l&quot;app gestisce il carico utente. Misurare i tempi di risposta e individuare gli errori che potrebbero indicare un problema.
services: app-service\web
documentationcenter: 
author: ecfan
manager: douge
editor: jimbe
ms.assetid: e4858f40-038d-4039-a3d4-beee7960ac7d
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2016
ms.author: estfan; manasma; ahomer
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 71f0a6e8d63e8f9ebb9307cce051010649a31a53


---
# <a name="performance-test-your-azure-web-app-under-load"></a>Testare le prestazioni dell'app Web di Azure sotto carico
Verificare le prestazioni dell'app Web prima di implementarla nell'ambiente di produzione o di distribuire aggiornamenti. In questo modo, è possibile valutare se l'app è pronta per il rilascio ed essere sicuri che l'app sia in grado di gestire il traffico nei periodi di picco o in occasione del push di marketing successivo.

Durante l'anteprima pubblica, è possibile testare gratuitamente le prestazioni dell'app nel portale di Azure.
I test simulano il carico utente nell'app in un periodo di tempo specificato e misurano la risposta dell'app. Ad esempio, i test possono mostrare la velocità di risposta dell'app a un numero di utenti specifico o il numero di richieste non riuscite, che può indicare che l'app presenta alcuni problemi.      

![Individuare problemi di prestazioni nell'app Web](./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png)

## <a name="before-you-start"></a>Prima di iniziare
* È necessario creare una [sottoscrizione di Azure](https://account.windowsazure.com/subscriptions), se non è già stato fatto. Informazioni su come [aprire gratuitamente un account di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* È necessario avere un account [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) per conservare la cronologia dei test delle prestazioni. Verrà creato automaticamente un account appropriato quando si configura il test delle prestazioni. Oppure è possibile creare un nuovo account o usare un account già esistente, se si è il proprietario dell'account. 
* Distribuire l'app per il test in un ambiente non di produzione. 
  Consentire all'app di usare un piano di servizio app diverso dal piano usato in produzione. 
  In questo modo, il test non avrà alcun impatto sui clienti esistenti, né rallenterà l'app nell'ambiente di produzione. 

## <a name="set-up-and-run-your-performance-test"></a>Configurare ed eseguire il test delle prestazioni
1. Accedere al [portale di Azure](https://portal.azure.com). 
   Per usare un account Visual Studio Team Services di cui si è proprietari, eseguire l'accesso come proprietario dell'account.
2. Passare all'app Web.
   
   ![Passare a Esplora tutto, fare clic su App Web e selezionare l'app Web testata](./media/app-service-web-app-performance-test/azure-np-web-apps.png)
3. Passare a **Test delle prestazioni**.
   
   ![Passare a Strumenti e fare clic su Test delle prestazioni](./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png)
4. Ora è necessario collegare un account [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) per conservare la cronologia dei test delle prestazioni.
   
    Se si ha già un account di Team Services, selezionarlo. In caso contrario, creare un nuovo account.
   
    ![Selezionare un account di Team Services esistente o crearne uno nuovo](./media/app-service-web-app-performance-test/azure-np-no-vso-account.png)
5. Creare il test delle prestazioni. Impostare i dettagli ed eseguire il test. 

È possibile osservare i risultati in tempo reale durante l'esecuzione del test.

Si prenda ad esempio un'app che ha distribuito buoni sconto durante i saldi dell'anno precedente. L'evento ha avuto una durata di 15 minuti con un carico di picco pari a 100 clienti simultanei. Quest'anno si vuole raddoppiare il numero dei clienti. E si vuole anche migliorare la soddisfazione dei clienti riducendo il tempo di caricamento della pagina da 5 secondi a 2 secondi. Verrà quindi eseguito il test delle prestazioni dell'app aggiornata con 250 utenti per 15 minuti.

Verrà simulato il carico sull'app tramite la generazione di utenti virtuali (clienti) che visitano il sito Web contemporaneamente. In questo modo, sarà possibile determinare il numero di richieste non riuscite e di quelle che rispondono lentamente.

  ![Creare, configurare ed eseguire il test delle prestazioni](./media/app-service-web-app-performance-test/azure-np-new-performance-test.png)

* L'URL predefinito dell'app Web viene aggiunto automaticamente. 
  È possibile modificare l'URL per testare altre pagine (solo richieste HTTP GET).
* Per simulare le condizioni locali e ridurre la latenza, selezionare la località più vicina agli utenti per generare il carico.
  
  Ecco il test in corso. Durante il primo minuto, la pagina viene caricata più lentamente di quanto previsto.
  
  ![Test delle prestazioni in corso con dati in tempo reale](./media/app-service-web-app-performance-test/azure-np-running-perf-test.png)
  
  Al termine del test, si può osservare che nei minuti successivi al primo, il caricamento della pagina risulta molto più veloce. Questo permette di individuare da dove è opportuno partire per la risoluzione del problema.
  
  ![Test delle prestazioni completato con i risultati, incluse le richieste non riuscite](./media/app-service-web-app-performance-test/azure-np-perf-test-done.png)

## <a name="test-multiple-urls"></a>Testare più URL
È possibile eseguire test delle prestazioni con più URL che rappresentano uno scenario utente end-to-end caricando un file di test Web di Visual Studio. Di seguito sono riportate alcune procedure per la creazione dei file di test Web in Visual Studio:

* [Acquisire il traffico con Fiddler ed esportarlo come file di test Web di Visual Studio](http://docs.telerik.com/fiddler/Save-And-Load-Traffic/Tasks/VSWebTest)
* [Creare un file di test di carico in Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)

Per caricare ed eseguire un file di test Web di Visual Studio:

1. Seguire la procedura precedente per aprire il pannello **Nuovo test delle prestazioni** .
   In questo pannello scegliere l'opzione CONFIGURE TEST USING (Configura test con) per aprire il pannello **Configure test using** (Configura test con).  
   
    ![Apertura del pannello di configurazione del test di carico](./media/app-service-web-app-performance-test/multiple-01-authoring-blade.png)
2. Verificare che come tipo di test sia impostato **Test Web di Visual Studio** e selezionare il file di archiviazione HTTP.
    Usare l'icona "cartella" per aprire la finestra di selezione dei file.
   
    ![Caricamento di un file di test Web di Visual Studio con più URL](./media/app-service-web-app-performance-test/multiple-01-authoring-blade2.png)
   
    Dopo che il file è stato caricato, viene visualizzato un elenco di URL da testare nella sezione dei dettagli degli URL.
3. Specificare il carico dell'utente e la durata del test, quindi scegliere **Esegui test**.
   
    ![Selezione del carico dell'utente e della durata](./media/app-service-web-app-performance-test/multiple-01-authoring-blade3.png)
   
    Al termine del test i risultati vengono visualizzati in due riquadri. Il riquadro a sinistra visualizza le informazioni sulle prestazioni in una serie di grafici.
   
    ![Riquadro dei risultati delle prestazioni](./media/app-service-web-app-performance-test/multiple-01a-results.png)
   
    Il riquadro a destra visualizza un elenco di richieste non riuscite, con il tipo di errore e il numero di volte che si è verificato.
   
    ![Riquadro degli errori di richiesta](./media/app-service-web-app-performance-test/multiple-01b-results.png)
4. Eseguire nuovamente il test scegliendo l'icona **Riesegui** nella parte superiore del riquadro a destra.
   
    ![Riesecuzione del test](./media/app-service-web-app-performance-test/multiple-rerun-test.png)

## <a name="q-a"></a>Domande e risposte
#### <a name="q-is-there-a-limit-on-how-long-i-can-run-a-test"></a>D: È previsto un limite di durata per l'esecuzione di un test?
**R**: Sì. È possibile eseguire test al massimo per un'ora nel portale di Azure.

#### <a name="q-how-much-time-do-i-get-to-run-performance-tests"></a>D: Quanti sono i minuti disponibili per l'esecuzione dei test delle prestazioni?
**R**: Dopo l'anteprima pubblica, l'utente riceve 20.000 minuti utente virtuale (VUM) gratuiti ogni mese con l'account Visual Studio Team Services. Un minuto utente virtuale (VUM) corrisponde al numero di utenti virtuali moltiplicato per il numero di minuti nel test. Se i minuti gratuiti non sono sufficienti, è possibile acquistare altro tempo e pagare solo per i minuti effettivamente usati.

#### <a name="q-where-can-i-check-how-many-vums-ive-used-so-far"></a>D: Dove posso controllare il numero di minuti utente virtuale usati fino a questo momento?
**R**: È possibile visualizzare questo valore nel portale di Azure.

![Accedere al proprio account di Team Services](./media/app-service-web-app-performance-test/azure-np-vso-accounts.png)

![Verifica dei minuti utente virtuale usati](./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png)

#### <a name="q-what-is-the-default-option-and-are-my-existing-tests-impacted"></a>D: Qual è l'opzione predefinita e quali sono i test interessati?
**R**: L'opzione predefinita per i test di carico delle prestazioni è un test manuale,  lo stesso usato prima che l'opzione per il test di più URL venisse aggiunta al portale.
I test esistenti continuano a usare l'URL configurato e funzioneranno come in precedenza.

#### <a name="q-what-features-not-supported-in-the-visual-studio-web-test-file"></a>D: Quali funzionalità non sono supportate nel file di test Web di Visual Studio?
**R**: Al momento non sono supportati i plug-in di test Web, le origini dati e le regole di estrazione. È necessario modificare il file di test Web per rimuovere questi elementi. Ci auguriamo di aggiungere il supporto per queste funzionalità negli aggiornamenti futuri.

#### <a name="q-does-it-support-any-other-web-test-file-formats"></a>D: Sono supportati altri formati di file di test Web?
**R**: Per il momento sono supportati solo i file di test Web di Visual Studio.
Se è necessario il supporto per altri formati di file, contattare Microsoft Inviare un messaggio di posta elettronica all'indirizzo [vsoloadtest@microsoft.com](mailto:vsoloadtest@microsoft.com).

#### <a name="q-what-else-can-i-do-with-a-visual-studio-team-services-account"></a>D: Cos'altro può fare con un account di Visual Studio Team Services?
**R**: Per trovare il nuovo account, passare a ```https://{accountname}.visualstudio.com```. È possibile condividere codice, compilare, monitorare lo stato del lavoro e distribuire software, tutto nel cloud e con qualsiasi strumento o linguaggio. Per altre informazioni su come le funzionalità e i servizi di [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) semplificano la collaborazione con il team e la distribuzione continua.

## <a name="see-also"></a>Vedere anche
* [Eseguire i test delle prestazioni cloud semplici](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-simple-cloud-load-test)
* [Eseguire i test delle prestazioni di Apache Jmeter](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-jmeter-test)
* [Registrare e riprodurre i test di carico basati sul cloud](https://www.visualstudio.com/docs/test/performance-testing/getting-started/record-and-replay-cloud-load-tests)
* [Test delle prestazioni dell'applicazione nel cloud](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)




<!--HONumber=Nov16_HO3-->


