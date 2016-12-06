---
title: "Monitorare la disponibilità e la velocità di risposta dei siti Web | Microsoft Docs"
description: Configurare i test Web in Application Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente.
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: dea21a59b189d1d3d474cbc5e67f64df485a1981
ms.openlocfilehash: 334d7391368509385dfc6c18ae1353d27faf7600


---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Monitorare la disponibilità e la velocità di risposta dei siti Web
Dopo aver distribuito l'app Web o il sito Web in qualsiasi server, è possibile configurare test Web per monitorarne la disponibilità e la velocità di risposta. [Azure Application Insights](app-insights-overview.md) invia richieste Web all'applicazione a intervalli regolari da diversi punti in tutto il mondo. Invia avvisi all'utente nel caso in cui l'applicazione risponda lentamente o non risponda affatto.

![Esempio di test Web](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

È possibile configurare test Web per qualsiasi endpoint HTTPS accessibile dalla rete Internet pubblica. Non è necessario aggiungere altro al sito Web che si sta testando. Non deve necessariamente trattarsi del proprio sito: è possibile testare un servizio API REST da cui si dipende.

Sono disponibili due tipi di test Web:

* [Test di ping URL](#create): un semplice test che può essere creato nel portale di Azure.
* [Test Web in più passaggi](#multi-step-web-tests): viene creato in Visual Studio Ultimate o Visual Studio Enterprise e caricato nel portale.

È possibile creare fino a 10 test Web per ogni risorsa dell'applicazione.

## <a name="a-namecreatea1-create-a-resource-for-your-test-reports"></a><a name="create"></a>1. Creare una risorsa per i report di test
Ignorare questo passaggio se è già stata [configurata una risorsa di Application Insights][start] per questa applicazione e si vuole visualizzare i report di disponibilità nella stessa posizione.

Accedere a [Microsoft Azure](http://azure.com), passare al [portale di Azure](https://portal.azure.com) e creare una risorsa di Application Insights.

![New > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Fare clic su **Tutte le risorse** per aprire il pannello Panoramica per la nuova risorsa.

## <a name="a-namesetupa2-create-a-url-ping-test"></a><a name="setup"></a>2. Creare un test di ping URL
Nella risorsa di Application Insights cercare il riquadro Disponibilità. Fare clic per aprire il pannello dei test Web per l'applicazione e aggiungere un test Web.

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/13-availability.png)

* **URL** deve essere visibile dalla rete Internet pubblica. Può includere una stringa di query&#151;pertanto è possibile, ad esempio, esercitarsi nell'uso del database. Se l'URL comporta un reindirizzamento, l'operazione viene effettuata fino a un numero massimo di 10 reindirizzamenti.
* **Analizza richieste dipendenti**: immagini, script, file di stile e altre risorse della pagina sono richiesti nell'ambito del test. Il tempo di risposta registrato include questi tempi. Il test avrà esito negativo se non è possibile scaricare tutte queste risorse entro il timeout definito per l'intero test.
* **Abilita nuovi tentativi**: quando il test ha esito negativo, viene eseguito un nuovo tentativo dopo un breve intervallo. Un errore viene segnalato solo se tre tentativi successivi non riescono. I test successivi vengono quindi eseguiti in base alla frequenza di test normale. I nuovi tentativi saranno temporaneamente sospesi fino al completamento successivo. Questa regola viene applicata in modo indipendente in ogni località di test. Questa è un'impostazione consigliata. In media, circa l'80% degli errori non si ripresenta al nuovo tentativo.
* **Frequenza test**: impostare la frequenza di esecuzione del test da ogni località di test. Con una frequenza di cinque minuti e cinque località di test, il sito verrà testato in media ogni minuto.
* **Località di test** : sono le posizioni da cui i server inviano richieste Web all'URL indicato. Sceglierne più di una, per poter distinguere i problemi del sito Web dai problemi di rete. È possibile selezionare fino a 16 località.
* **Criteri di successo**:

    **Timeout test**: ridurre questo valore per ricevere avvisi in merito alle risposte lente. Il test viene conteggiato come non riuscito se le risposte dal sito non sono state ricevute entro questo periodo. Se è stata selezionata l'opzione **Analizza richieste dipendenti**, è necessario che tutti gli script, i file di stile, le immagini e le altre risorse dipendenti siano stati ricevuti entro questo periodo.

    **Risposta HTTP**: codice di stato restituito che viene conteggiato come operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web.

    **Il contenuto corrisponde a**: stringa, ad esempio "Benvenuto", di cui viene verificata la presenza in tutte le risposte. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. È importante ricordare che, se il contenuto cambia, potrebbe essere necessario aggiornare la stringa.
* **Avvisi** vengono inviati se si verificano errori in tre posizioni in cinque minuti. Un errore in una posizione può indicare un errore di rete e non un problema con il sito. È comunque possibile modificare la soglia in modo da aumentare la sensibilità del test e modificare i destinatari a cui inviare i messaggi di posta elettronica.

    È possibile configurare un [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) che verrà chiamato quando viene generato un avviso. Si noti però che attualmente i parametri di query non vengono passati come proprietà.

### <a name="test-more-urls"></a>Testare più URL
Aggiungere altri test. Ad esempio, oltre a testare la home page, è possibile verificare che il database sia in esecuzione testando l'URL per una ricerca.

## <a name="a-namemonitora3-see-your-web-test-results"></a><a name="monitor"></a>3. Visualizzare i risultati del test Web
I risultati verranno visualizzati dopo 1-2 minuti nel pannello Test Web.

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Fare clic su qualsiasi barra nel grafico di riepilogo per ottenere una visualizzazione più dettagliata di tale periodo di tempo.

Questi grafici combinano i risultati per tutti i test Web di questa applicazione.

## <a name="a-namefailuresaif-you-see-failures"></a><a name="failures"></a>In caso di errori
Fare clic su un punto rosso.

![Click a red dot](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

In alternativa, scorrere verso il basso e fare clic su un test con risultato inferiore al 100%.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

Verranno visualizzati i risultati del test.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/16-1test.png)

Il test viene eseguito da diverse posizioni&#151;selezionarne una in cui i risultati siano inferiori al 100%.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)

Scorrere verso il basso fino a **Test non superati** e selezionare un risultato.

Fare clic sul risultato per valutarlo nel portale e visualizzare il motivo dell'errore.

![Webtest run result](./media/app-insights-monitor-web-app-availability/18-availDetails.png)

In alternativa, scaricare il file dei risultati ed esaminarlo in Visual Studio.

*Ha un aspetto corretto ma è segnalato come errore?*  Controllare tutte le immagini, gli script, i fogli di stile e qualsiasi altro file caricato dalla pagina. In caso di errore in uno di essi, il test verrà segnalato come non superato, anche se la pagina HTML principale viene caricata correttamente.

### <a name="open-the-server-request-and-exceptions"></a>Aprire il report della richieste e delle eccezioni del server

Dalle proprietà dettagliate di un determinato test, è possibile aprire il report sul lato server relativo alla richiesta e ad altri eventi, ad esempio le eccezioni.

![Webtest run result](./media/app-insights-monitor-web-app-availability/web-test-linked-to-server-telemetry.png)

Se non vengono visualizzati elementi correlati, il problema può essere dovuto al fatto che è in corso il [campionamento](app-insights-sampling.md).

## <a name="multi-step-web-tests"></a>Test Web in più passaggi
È possibile monitorare uno scenario che comporta una sequenza di URL. Ad esempio, se si monitora un sito Web di vendita, si potrebbe testare il corretto funzionamento dell'aggiunta di articoli al carrelli acquisti.

> [!NOTE] 
> È prevista una tariffa per i test Web in più passaggi. Vedere lo [schema dei prezzi](http://azure.microsoft.com/pricing/details/application-insights/).
> 

Per creare un test in più passaggi, registrare lo scenario con Visual Studio, quindi caricare la registrazione in Application Insights. Application Insights riprodurrà lo scenario a intervalli e verificherà le risposte.

Si noti che non è possibile usare funzioni codificate nei test: i passaggi dello scenario devono essere contenuti sotto forma di script nel file con estensione webtest.

#### <a name="1-record-a-scenario"></a>1. Registrare uno scenario
Usare Visual Studio Enterprise o Ultimate per registrare una sessione Web.

1. Creare un progetto di test delle prestazioni Web.

    ![In Visual Studio creare un progetto dal modello di test di carico e prestazioni Web.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)
2. Aprire il file con estensione webtest e iniziare la registrazione.

    ![Aprire il file con estensione webtest e fare clic su Registra.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Eseguire le azioni utente che si vuole simulare nel test: aprire il sito Web, aggiungere un prodotto al carrello e così via. Quindi, arrestare il test.

    ![Il registratore esecuzioni dei test Web viene eseguito in Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Non creare uno scenario lungo, in quanto è presente un limite di 100 passaggi e 2 minuti.
4. Modificare il test per:

   * Aggiungere convalide per verificare i codici di testo e di risposta ricevuti.
   * Rimuovere tutte le interazioni superflue. È inoltre possibile rimuovere le richieste dipendenti per le immagini o per Active Directory o i siti di rilevamento.

     Tenere presente che è possibile modificare solo lo script del test ma non è possibile aggiungere codice personalizzato o chiamare altri test web. Non inserire cicli nel test. È possibile utilizzare i plug-in del test web standard.
5. Eseguire il test in Visual Studio per verificarne il corretto funzionamento.

    Il programma di esecuzione del test Web apre un browser Web e ripete le azioni registrate. Verificare che funzioni come previsto.

    ![In Visual Studio, aprire il file con estensione webtest e fare clic su Esegui.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Caricare il test Web in Application Insights
1. Nel portale Application Insights, creare un nuovo test Web.

    ![Nel pannello Test Web, scegliere Aggiungi.](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. Selezionare un test in più passaggi e caricare il file con estensione webtest.

    ![Selezionare Test Web in più passaggi.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Impostare le posizioni di test, la frequenza e i parametri di avviso allo stesso modo dei test ping.

Visualizzare i risultati del test e gli eventuali errori nello stesso modo dei test con singolo URL.

Un motivo frequente di errore è l'eccessiva durata del test. L'esecuzione non deve superare i due minuti.

Non dimenticare che, perché il test abbia esito positivo, tutte le risorse di una pagina devono essere caricate correttamente, inclusi script, fogli di stile, immagini e così via.

Si noti che il test Web deve essere interamente contenuto nel file con estensione webtest: non è possibile usare funzioni codificate nel test.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Inserimento di plug-in relativi a tempo e numeri casuali nel test in più passaggi
Si supponga di voler testare uno strumento che riceva dati dipendenti dal tempo, come ad esempio valori di scorte da un feed esterno. Quando si registra il test Web, è necessario usare tempi specifici impostandoli come parametri del test, StartTime e EndTime.

![Un test Web con parametri.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Quando si esegue il test, si vuole che EndTime sia sempre l'ora corrente e StartTime 15 minuti fa.

I plug-in del test Web consentono di impostare questi parametri.

1. Aggiungere un plug-in del test Web per ciascun valore di parametro desiderato. Nella barra degli strumenti del test Web scegliere **Aggiungi plug-in test Web**.

    ![Scegliere Aggiungi plug-in test Web e selezionare un tipo.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    In questo esempio vengono usate due istanze di Plug-in data e ora, una per "15 minuti fa" e l'altra per "ora".
2. Aprire le proprietà di ciascun plug-in. Assegnare un nome al plug-in e impostarlo in modo che usi l'ora corrente. Per uno di essi, impostare Aggiungi minuti = -15.

    ![Set name, Use Current Time e Add Minutes.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. Nei parametri del test Web, usare {{nome plug-in}} per fare riferimento a un nome di plug-in.

    ![Nel parametro di test usare {{nome plug-in}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Caricare quindi il test nel portale. Userà i valori dinamici ogni volta che verrà eseguito.

## <a name="dealing-with-sign-in"></a>Gestione degli accessi
Se gli utenti accedono all'app, è possibile simulare l'accesso in vari modi per testare le pagine usate per l'accesso. L'approccio da preferire dipende dal tipo di sicurezza fornito dall'app.

In tutti i casi è consigliabile creare un account nell'applicazione solo a scopo di test. Se possibile, limitare le autorizzazioni dell'account di test in modo che i test Web non possano influire in alcun modo sugli utenti reali.

### <a name="simple-username-and-password"></a>Nome utente e password semplici
Registrare un test Web nel modo consueto. Eliminare prima di tutto i cookie.

### <a name="saml-authentication"></a>SAML Authentication
usare il plug-in SAML disponibile per i test Web.

### <a name="client-secret"></a>Segreto client
Se l'app ha un percorso di accesso che prevede un segreto client, usare tale percorso. Un servizio che offre l'accesso con segreto client è ad esempio Azure Active Directory (AAD). In AAD, il segreto client è la chiave dell'app.

Ecco un test Web di esempio di un'app Web di Azure che usa una chiave dell'app:

![Esempio di segreto client](./media/app-insights-monitor-web-app-availability/110.png)

1. Ottenere il token da AAD usando il segreto client (AppKey).
2. Estrarre il token di connessione dalla risposta.
3. Chiamare l'API usando il token di connessione nell'intestazione dell'autorizzazione.

Verificare che il test Web sia effettivamente un client, ovvero che abbia una propria app in AAD, e usare i relativi valori ClientId e AppKey. Anche il servizio sottoposto a test ha una propria app in AAD, il cui URI ID app è riportato nel campo "resource" del test Web.

### <a name="open-authentication"></a>Autenticazione aperta
Un esempio di autenticazione aperta è l'accesso con il proprio account Microsoft o Google. Molte app che usano OAuth offrono l'alternativa del segreto client ed è quindi consigliabile ricercare prima di tutto tale possibilità.

Se il test deve eseguire l'accesso con OAuth, procedere come indicato di seguito:

* Usare uno strumento come Fiddler per esaminare il traffico tra il Web browser, il sito di autenticazione e l'app.
* Eseguire due o più accessi usando computer o browser diversi oppure a distanza di tempo, per lasciar scadere i token.
* Mettendo a confronto le diverse sessioni, identificare il token restituito dal sito di autenticazione e poi passato al server applicazioni dopo l'accesso.
* Registrare un test Web usando Visual Studio.
* Impostare i parametri dei token quando questi vengono restituiti dall'autenticatore e usarli per l'esecuzione di query nel sito.
  Visual Studio prova a impostare i parametri del test, ma non imposta correttamente i parametri dei token.

## <a name="a-nameedita-edit-or-disable-a-test"></a><a name="edit"></a> Modificare o disabilitare un test
Aprire un singolo test per modificarlo o disabilitarlo.

![Modificare o disabilitare un test Web](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Disabilitare i test Web può essere utile durante l'esecuzione di operazioni di manutenzione sul servizio.

## <a name="performance-tests"></a>Test delle prestazioni
È possibile eseguire un test di carico nel sito Web. Analogamente al test di disponibilità, è possibile inviare semplici richieste o richieste in più passaggi da diversi punti in tutto il mondo. A differenza dei test di disponibilità, vengono inviate molte richieste, simulando più utenti simultanei.

Dal pannello Panoramica aprire **Impostazioni**, **Test delle prestazioni**. Quando si crea un test, si è invitati a connettersi o a creare un account di Visual Studio Team Services.

Al termine del test verranno visualizzati i tempi di risposta e le percentuali di successo.

## <a name="automation"></a>Automazione
* [Usare script di PowerShell per configurare automaticamente un test Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/) .
* Configurare un [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) che verrà chiamato quando viene generato un avviso.

## <a name="questions-problems"></a>Domande? Problemi?
* *È possibile chiamare codice da un test Web?*

    No. I passaggi del test devono essere nel file con estensione webtest. Inoltre non è possibile chiamare altri test web o utilizzare cicli. Esistono diversi plug-in che potrebbero risultare utili.
* *HTTPS è supportato?*

    Sono supportati TLS 1.1 e TLS 1.2.
* *Esiste una differenza tra "test Web" e "test di disponibilità"?*

    I due termini vengono usati in modo intercambiabile.
* *È possibile usare test di disponibilità nel server interno protetto da un firewall?*

    Configurare il firewall per consentire richieste dagli [indirizzi IP degli agenti di test Web](app-insights-ip-addresses.md).
* *Non è possibile caricare un test Web in più passi*

    È previsto un limite di dimensioni pari a 300 KB.

    I cicli non sono supportati.

    I riferimenti ad altri test Web non sono supportati.

    Le origini dati non sono supportate.
* *Il test in più passi non viene completato*

    È previsto un limite di 100 richieste per ogni test.

    Il test viene arrestato se la durata dell'esecuzione è superiore a due minuti.
* *È possibile eseguire un test con certificati client?*

    Questa funzionalità non è supportata.

## <a name="a-namevideoavideo"></a><a name="video"></a>Video
> [!VIDEO https://channel9.msdn.com/Series/Application-Insights-on-Azure-Preview-Portal/Monitoring-Availability-with-Application-Insights/player]
>
>

## <a name="a-namenextanext-steps"></a><a name="next"></a>Passaggi successivi
[Ricerca nei registri di diagnostica][diagnostic]

[Risoluzione dei problemi][qna]

[Indirizzi IP degli agenti di test Web](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md



<!--HONumber=Nov16_HO3-->


