---
title: Monitorare la disponibilità e la velocità di risposta dei siti Web | Microsoft Docs
description: Configurare i test Web in Application Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/05/2018
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 480edbb508b875d53d972e9ac93fd4d119c7e54a
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119663"
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Monitorare la disponibilità e la velocità di risposta dei siti Web
Dopo aver distribuito l'app Web o il sito Web in qualsiasi server, è possibile configurare alcuni test per monitorarne la disponibilità e la velocità di risposta. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) invia richieste Web all'applicazione a intervalli regolari da diversi punti in tutto il mondo. Invia avvisi all'utente nel caso in cui l'applicazione risponda lentamente o non risponda affatto.

È possibile configurare test di disponibilità per qualsiasi endpoint HTTP o HTTPS accessibile dalla rete Internet pubblica. Non è necessario aggiungere altro al sito Web che si sta testando. Non deve necessariamente trattarsi del proprio sito: è possibile testare un servizio API REST da cui si dipende.

Sono disponibili due tipi di test di disponibilità:

* [Test di ping URL](#create): un semplice test che può essere creato nel portale di Azure.
* [Test Web in più passi](#multi-step-web-tests): viene creato in Visual Studio Enterprise e caricato nel portale.

È possibile creare fino a 100 test di disponibilità per ogni risorsa dell'applicazione.


## <a name="create"></a>Aprire una risorsa per i report dei test di disponibilità

**Se si è già configurato Application Insights** per l'app Web, aprire la risorsa di Application Insights nel [portale di Azure](https://portal.azure.com).

**Se invece si vogliono visualizzare i report in una nuova risorsa**, accedere al [portale di Azure](https://portal.azure.com) e creare una risorsa di Application Insights.

![Crea una risorsa > Strumenti di sviluppo > Application Insights](./media/monitor-web-app-availability/1create-resource-appinsights.png)

Fare clic su **Tutte le risorse** per aprire il pannello Panoramica per la nuova risorsa.

## <a name="setup"></a>Creare un test di ping URL
Aprire il pannello Disponibilità e aggiungere un test.

![Fill at least the URL of your website](./media/monitor-web-app-availability/2addtest-url.png)

* **L'URL** può essere qualsiasi pagina Web che si vuole testare, ma deve essere visibile da Internet pubblico. L'URL può includere una stringa di query. In questo modo, ad esempio, è possibile esercitarsi nell'uso del database. Se l'URL comporta un reindirizzamento, l'operazione viene effettuata fino a un numero massimo di 10 reindirizzamenti.
* **Analizza richieste dipendenti**: se questa opzione è selezionata, il test richiede immagini, script, file di stile e altri file che fanno parte della pagina Web sottoposta a test. Il tempo di risposta registrato include il tempo impiegato per ottenere questi file. Il test avrà esito negativo se non è possibile scaricare tutte queste risorse entro il timeout definito per l'intero test. Se l'opzione non viene selezionata, il test richiede solo il file in corrispondenza dell'URL specificato.

* **Abilita nuovi tentativi**:  se questa opzione viene selezionata, quando il test ha esito negativo, viene eseguito un nuovo tentativo dopo un breve intervallo. Un errore viene segnalato solo se tre tentativi successivi non riescono. I test successivi vengono quindi eseguiti in base alla frequenza di test normale. I nuovi tentativi saranno temporaneamente sospesi fino al completamento successivo. Questa regola viene applicata in modo indipendente in ogni località di test. Questa opzione è consigliata. In media, circa l'80% degli errori non si ripresenta al nuovo tentativo.

* **Frequenza test**: impostare la frequenza di esecuzione del test da ogni località di test. Con una frequenza predefinita di cinque minuti e cinque località di test, il sito verrà testato in media ogni minuto.

* **posizioni dei test** sono le ubicazioni da cui i server inviano richieste Web all'URL indicato. Il numero minimo di località di test consigliate è cinque per essere certi di poter distinguere i problemi del sito Web da quelli della rete. È possibile selezionare fino a 16 località.

> [!NOTE]
> * È consigliabile eseguire test da più località, con un numero minimo pari a cinque. Questo serve a evitare falsi allarmi che possono essere dovuti a problemi temporanei di una località specifica. Inoltre, si è constatato che, per una configurazione ottimale, è necessario che il numero di località di test sia uguale alla soglia località di avviso + 2. 
> * L'abilitazione dell'opzione "Analizza richieste dipendenti" determina l'esecuzione di un controllo più rigoroso. Il test può avere esito negativo per i casi che possono non essere evidenti quando si esplora manualmente il sito.

* **Criteri di successo**:

    **Timeout test**: ridurre questo valore per ricevere avvisi in merito alle risposte lente. Il test viene conteggiato come non riuscito se le risposte dal sito non sono state ricevute entro questo periodo. Se è stata selezionata l'opzione **Analizza richieste dipendenti**, è necessario che tutti gli script, i file di stile, le immagini e le altre risorse dipendenti siano stati ricevuti entro questo periodo.

    **Risposta HTTP**: codice di stato restituito che indica un'operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web.

    **Il contenuto corrisponde a**: stringa, ad esempio "Benvenuto", Verifichiamo che in ogni risposta ci una corrispondenza esatta di maiuscolo e minuscolo. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. È importante ricordare che, se il contenuto cambia, potrebbe essere necessario aggiornare la stringa.

* **Soglia località di avviso**: Si consiglia un minimo di 3-5 posizioni. Il rapporto ottimale tra la soglia località di avviso e il numero di località di test è dato da **soglia località di avviso** = **numero di località di test** - 2, con un numero minimo pari a cinque località di test.

## <a name="multi-step-web-tests"></a>Test Web in più passaggi
È possibile monitorare uno scenario che comporta una sequenza di URL. Ad esempio, se si monitora un sito Web di vendita, si potrebbe testare il corretto funzionamento dell'aggiunta di articoli al carrelli acquisti.

> [!NOTE]
> È prevista una tariffa per i test Web in più passaggi. Vedere lo [schema dei prezzi](https://azure.microsoft.com/pricing/details/application-insights/).
> 

Per creare un test in più passaggi, registrare lo scenario con Visual Studio Enterprise, quindi caricare la registrazione in Application Insights. Application Insights riprodurrà lo scenario a intervalli e verificherà le risposte.

> [!NOTE]
> * Non è possibile usare funzioni codificate o cicli nei test. Il test deve essere interamente contenuto nello script con estensione webtest. È tuttavia possibile usare plug-in standard.
> * I test Web in più passaggi supportano solo caratteri inglesi. Se si usa Visual Studio in altre lingue, aggiornare il file di definizione dei test Web per tradurre o escludere i caratteri non inglesi.
>

#### <a name="1-record-a-scenario"></a>1. Registrare uno scenario
Usare Visual Studio Enterprise per registrare una sessione Web.

1. Creare un progetto di test delle prestazioni Web.

    ![In Visual Studio Enterprise creare un progetto dal modello di test di carico e prestazioni Web.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

 * *Se il modello di test di carico e prestazioni Web non viene visualizzato*, chiudere Visual Studio Enterprise. Aprire il **programma di installazione di Visual Studio** per modificare l'installazione di Visual Studio Enterprise. In **Singoli componenti** selezionare **Strumenti per test di carico e delle prestazioni Web**.

2. Aprire il file con estensione webtest e iniziare la registrazione.

    ![Aprire il file con estensione webtest e fare clic su Registra.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Eseguire le azioni utente che si vuole simulare nel test: aprire il sito Web, aggiungere un prodotto al carrello e così via. Quindi, arrestare il test.

    ![Il registratore esecuzioni dei test Web viene eseguito in Internet Explorer.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Non creare uno scenario lungo, in quanto è presente un limite di 100 passaggi e 2 minuti.
4. Modificare il test per:

   * Aggiungere convalide per verificare i codici di testo e di risposta ricevuti.
   * Rimuovere tutte le interazioni superflue. È inoltre possibile rimuovere le richieste dipendenti per le immagini o per Active Directory o i siti di rilevamento.

     Tenere presente che è possibile modificare solo lo script del test ma non è possibile aggiungere codice personalizzato o chiamare altri test web. Non inserire cicli nel test. È possibile utilizzare i plug-in del test web standard.
5. Eseguire il test in Visual Studio per verificarne il corretto funzionamento.

    Il programma di esecuzione del test Web apre un browser Web e ripete le azioni registrate. Verificare che funzioni come previsto.

    ![In Visual Studio, aprire il file con estensione webtest e fare clic su Esegui.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Caricare il test Web in Application Insights
1. Nel portale di Application Insights nel pannello Disponibilità fare clic su Aggiungi test.

    ![Nel pannello Disponibilità, scegliere Aggiungi test.](./media/monitor-web-app-availability/3addtest-web.png)
2. Selezionare un test in più passaggi e caricare il file con estensione webtest.

    Impostare le posizioni di test, la frequenza e i parametri di avviso allo stesso modo dei test ping.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Inserimento di plug-in relativi a tempo e numeri casuali nel test in più passaggi
Si supponga di voler testare uno strumento che riceva dati dipendenti dal tempo, come ad esempio valori di scorte da un feed esterno. Quando si registra il test Web, è necessario usare tempi specifici impostandoli come parametri del test, StartTime e EndTime.

![Un test Web con parametri.](./media/monitor-web-app-availability/appinsights-72webtest-parameters.png)

Quando si esegue il test, si vuole che EndTime sia sempre l'ora corrente e StartTime 15 minuti fa.

I plug-in del test Web consentono di impostare questi parametri.

1. Aggiungere un plug-in del test Web per ciascun valore di parametro desiderato. Nella barra degli strumenti del test Web scegliere **Aggiungi plug-in test Web**.

    ![Scegliere Aggiungi plug-in test Web e selezionare un tipo.](./media/monitor-web-app-availability/appinsights-72webtest-plugins.png)

    In questo esempio vengono usate due istanze di Plug-in data e ora, una per "15 minuti fa" e l'altra per "ora".
2. Aprire le proprietà di ciascun plug-in. Assegnare un nome al plug-in e impostarlo in modo che usi l'ora corrente. Per uno di essi, impostare Aggiungi minuti = -15.

    ![Set name, Use Current Time e Add Minutes.](./media/monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. Nei parametri del test Web, usare {{nome plug-in}} per fare riferimento a un nome di plug-in.

    ![Nel parametro di test usare {{nome plug-in}}.](./media/monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Caricare quindi il test nel portale. Userà i valori dinamici ogni volta che verrà eseguito.


## <a name="monitor"></a>Visualizzare i risultati dei test di disponibilità

La scheda Panoramica mostra la percentuale di riuscita dei test, mentre la scheda Dettagli mostra un grafico a dispersione e una griglia di dettagli specifici.

Dopo pochi minuti, fare clic su **Aggiorna** per visualizzare i risultati del test.

![Grafico a dispersione nel pannello Dettagli](./media/monitor-web-app-availability/4refresh.png)

Il grafico a dispersione mostra alcuni campioni dei risultati del test che includono dettagli diagnostici sui passaggi del test. Il motore di test archivia i dettagli diagnostici per i test che hanno restituito errori. Per i test riusciti, vengono archiviati i dettagli diagnostici per un subset delle esecuzioni. Posizionare il puntatore del mouse su uno dei punti verdi/rossi per visualizzare il timestamp del test, la durata del test, la posizione e il nome del test. Fare clic su qualsiasi punto del grafico a dispersione per visualizzare i dettagli del risultato del test.  

Selezionare una posizione o un test specifico oppure ridurre il periodo di tempo per visualizzare più risultati riguardo all'intervallo desiderato. Usare Esplora ricerche per visualizzare i risultati di tutte le esecuzioni oppure usare query di analisi per eseguire report personalizzati per i dati.

Oltre ai risultati non elaborati, Esplora metriche include due metriche di disponibilità: 

1. Disponibilità: percentuale dei test riusciti rispetto a tutte le esecuzioni di test. 
2. Durata test: durata media dei test rispetto a tutte le esecuzioni di test.

È possibile applicare filtri per il nome di test e la posizione per analizzare le tendenze per un test e/o una posizione specifici.

## <a name="edit"></a> Esaminare e modificare i test

Dalla scheda Dettagli, in un test specifico selezionare i puntini di sospensione a destra per modificare, disattivare temporaneamente, eliminare o scaricare test Web.

Selezionare **Visualizza dettagli test** da un test specifico per visualizzare il grafico a dispersione e i dettagli località del test specifico.

![Visualizzare dettagli del test, modificare e disabilitare un test Web](./media/monitor-web-app-availability/5viewdetails.png)

Può essere necessario disabilitare i test di disponibilità o le regole di avviso associate ai test durante le operazioni di manutenzione del servizio.

![Disabilitare un test Web](./media/monitor-web-app-availability/6disable.png)
![Modificare test](./media/monitor-web-app-availability/8edittest.png)

## <a name="failures"></a>In caso di errori
Fare clic su un punto rosso.

![Click a red dot](./media/monitor-web-app-availability/open-instance-3.png)

Dal risultato di un test di disponibilità è possibile visualizzare i dettagli delle transazioni in tutti i componenti. A questo punto è possibile:

* Controllare la risposta ricevuta dal server.
* Diagnosticare l'errore con i dati di telemetria lato server correlati, raccolti durante l'elaborazione del test di disponibilità non riuscito.
* Registrare un problema o elemento di lavoro in Git o Azure Boards per tenere traccia del problema. Il bug conterrà un collegamento a questo evento.
* Aprire il risultato del test Web in Visual Studio.

Per altre informazioni sull'esperienza di diagnostica delle transazioni end-to-end, fare clic [qui](../../azure-monitor/app/transaction-diagnostics.md).

Fare clic sulla riga dell'eccezione per visualizzare i dettagli dell'eccezione lato server che ha causato l'errore durante il test di disponibilità sintetico. È anche possibile ottenere lo [snapshot di debug](../../azure-monitor/app/snapshot-debugger.md) per una diagnostica più avanzata a livello di codice.

![Diagnostica lato server](./media/monitor-web-app-availability/open-instance-4.png)

## <a name="alerts"></a> Avvisi di disponibilità
Usando l'esperienza degli avvisi classici è possibile avere, per i dati di disponibilità, i tipi di regole di avviso seguenti:
1. X di Y località con segnalazione di errori in un intervallo di tempo
2. Percentuale di disponibilità aggregata sotto una determinata soglia
3. Durata media del test oltre una determinata soglia

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Inviare avvisi per X di Y località con segnalazione di errori
Questa regola di avviso è abilitata per impostazione predefinita nell'[esperienza dei nuovi avvisi unificati](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), quando si crea un nuovo test di disponibilità. È possibile rifiutarla esplicitamente selezionando l'opzione "classica" o scegliendo di disabilitare la regola di avviso.

![Esperienza di creazione](./media/monitor-web-app-availability/appinsights-71webtestUpload.png)

> [!NOTE]
>  con i [nuovi avvisi unificati](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), la gravità delle regole di avviso e le preferenze di notifica con **gruppi di azioni** devono essere configurate nell'esperienza degli avvisi. Se non si esegue la procedura seguente, non si riceveranno notifiche all'interno del portale.

1. Dopo il salvataggio del test di disponibilità, nella scheda Dettagli fare clic sui puntini di sospensione accanto al test appena creato. Fare clic su "Modifica avviso".
![Modifica dopo il salvataggio](./media/monitor-web-app-availability/9editalert.png)

2. Impostare il livello di gravità desiderato, la descrizione della regola ed, elemento ancora più importante, il gruppo di azioni con le preferenze di notifica che si vogliono usare per questa regola di avviso.
![Modifica dopo il salvataggio](./media/monitor-web-app-availability/10editalert.png)


> [!NOTE]
> * Configurare i gruppi di azioni per ricevere le notifiche quando viene attivato l'avviso seguendo la procedura precedente. Se non si esegue questa procedura, le notifiche all'interno del portale si riceveranno solo quando la regola viene attivata.
>

### <a name="alert-on-availability-metrics"></a>Inviare avvisi per le metriche di disponibilità
Usando i [nuovi avvisi unificati](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) è possibile generare avvisi per la disponibilità aggregata segmentata e anche per le metriche di durata dei test:

1. Selezionare una risorsa di Application Insights nell'esperienza delle metriche e quindi selezionare una metrica di disponibilità:  ![Selezione delle metriche di disponibilità](./media/monitor-web-app-availability/selectmetric.png)

2. L'opzione Configura avvisi disponibile nel menu consentirà di accedere alla nuova esperienza in cui è possibile selezionare test specifici o località per le quali configurare la regola di avviso. In questa schermata è anche possibile configurare i gruppi di azioni per questa regola di avviso.
    ![Configurazione degli avvisi di disponibilità](./media/monitor-web-app-availability/availabilitymetricalert.png)

### <a name="alert-on-custom-analytics-queries"></a>Inviare avvisi per le query di analisi personalizzate
Usando i [nuovi avvisi unificati](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) è possibile inviare avvisi per le [query di log personalizzate](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log). Con le query personalizzate è possibile inviare avvisi per qualsiasi condizione arbitraria che consenta di ottenere il segnale più affidabile di problemi di disponibilità. Questa opzione si applica in particolare se si inviano risultati di disponibilità personalizzati tramite l'SDK TrackAvailability. 

> [!Tip]
> * Le metriche sui dati di disponibilità includono tutti i risultati di disponibilità personalizzati che si possono inviare chiamando l'SDK TrackAvailability. È possibile usare gli avvisi per il supporto delle metriche per inviare avvisi sui risultati di disponibilità personalizzati.
>

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

![Esempio di segreto client](./media/monitor-web-app-availability/110.png)

1. Ottenere il token da AAD usando il segreto client (AppKey).
2. Estrarre il token di connessione dalla risposta.
3. Chiamare l'API usando il token di connessione nell'intestazione dell'autorizzazione.

Verificare che il test Web sia effettivamente un client, ovvero che abbia una propria app in AAD, e usare i relativi valori ClientId e AppKey. Anche il servizio sottoposto a test ha una propria app in AAD, il cui URI ID app è riportato nel campo �resource� del test Web.

### <a name="open-authentication"></a>Autenticazione aperta
Un esempio di autenticazione aperta è l'accesso con il proprio account Microsoft o Google. Molte app che usano OAuth offrono l'alternativa del segreto client ed è quindi consigliabile ricercare prima di tutto tale possibilità.

Se il test deve eseguire l'accesso con OAuth, procedere come indicato di seguito:

* Usare uno strumento come Fiddler per esaminare il traffico tra il Web browser, il sito di autenticazione e l'app.
* Eseguire due o più accessi usando computer o browser diversi oppure a distanza di tempo, per lasciar scadere i token.
* Mettendo a confronto le diverse sessioni, identificare il token restituito dal sito di autenticazione e poi passato al server applicazioni dopo l'accesso.
* Registrare un test Web usando Visual Studio.
* Impostare i parametri dei token quando questi vengono restituiti dall'autenticatore e usarli per l'esecuzione di query nel sito.
  Visual Studio prova a impostare i parametri del test, ma non imposta correttamente i parametri dei token.

## <a name="performance-tests"></a>Test delle prestazioni
È possibile eseguire un test di carico nel sito Web. Analogamente al test di disponibilità, è possibile inviare semplici richieste o richieste in più passaggi da diversi punti in tutto il mondo. A differenza dei test di disponibilità, vengono inviate molte richieste, simulando più utenti simultanei.

In **Configura**, passare a **Test delle prestazioni** e fare clic su Nuovo per creare un test.

![Creazione di un nuovo test delle prestazioni](./media/monitor-web-app-availability/11new-performance-test.png)

Al termine del test verranno visualizzati i tempi di risposta e le percentuali di successo.

![Risultati test delle prestazioni](./media/monitor-web-app-availability/12performance-test.png)

> [!TIP]
> Per osservare gli effetti di un test delle prestazioni, usare [Live Stream](../../azure-monitor/app/live-stream.md) e [Profiler](../../azure-monitor/app/profiler.md).
>

## <a name="automation"></a>Automazione
* [Usare script di PowerShell per configurare un test di disponibilità](../../azure-monitor/app/powershell.md#add-an-availability-test) automaticamente.
* Configurare un [webhook](../../azure-monitor/platform/alerts-webhooks.md) che verrà chiamato quando viene generato un avviso.

## <a name="qna"></a> Domande frequenti

* *Il sito sembra funzionare correttamente, ma i test segnalano errori. Perché Application Insights invia avvisi?*

    * Se nel test è abilitata l'opzione "Analizza richieste dipendenti", viene eseguito un controllo rigoroso sulle risorse, ad esempio script, immagini e così via. Questi tipi di errori possono non essere evidenti in un browser.  Controllare tutte le immagini, gli script, i fogli di stile e qualsiasi altro file caricato dalla pagina. In caso di errore in uno di essi, il test verrà segnalato come non superato, anche se la pagina HTML principale viene caricata correttamente. Per eliminare la sensibilità del test a errori delle risorse di questo tipo, è sufficiente deselezionare "Analizza richieste dipendenti" nella configurazione di test. 

    * Per ridurre le probabilità di risultati non significativi causati da problemi di rete temporanei e così via, verificare che sia selezionata l'opzione di configurazione che abilita nuovi tentativi in caso di test non superati. È anche possibile eseguire test da più posizioni e gestire la soglia delle regole di avviso di conseguenza per evitare che problemi specifici della posizione causino avvisi non dovuti.

    * Fare clic su uno qualsiasi dei punti rossi dell'esperienza di disponibilità oppure su qualsiasi errore di disponibilità presente in Esplora ricerche per visualizzare i dettagli del motivo per cui è stato segnalato l'errore. Il risultato del test, insieme ai dati di telemetria lato server correlati (se abilitati), dovrebbe consentire di comprendere perché il test non è riuscito. Le cause più comuni dei problemi temporanei sono problemi di connessione o di rete. 

    * Controllare se si è verificato il timeout del test. I test vengono interrotti dopo 2 minuti. Se il ping o il test in più passaggi richiede più di 2 minuti, verrà segnalato come errore. È consigliabile suddividere il test in più test di durata più breve.

    * Valutare se sono stati segnalati errori in tutte le località e solo in alcune. Se gli errori sono stati segnalati solo in alcune località, potrebbero essere causati da errori di rete/CDN. Anche in questo caso, facendo clic sui puntini rossi dovrebbe essere possibile comprendere perché nella località sono stati segnalati errori.

* *Non si è ricevuto un messaggio di posta elettronica quando l'avviso è stato attivato o risolto o in entrambi i casi?*

    Controllare la configurazione degli avvisi classici per verificare se l'indirizzo di posta elettronica è inserito direttamente nell'elenco o se per la ricezione delle notifiche è configurata una lista di distribuzione che include tale indirizzo. In quest'ultimo caso, controllare la configurazione della lista di distribuzione per verificare che possa ricevere messaggi di posta elettronica esterni. Controllare anche se l'amministratore di posta elettronica ha eventualmente configurato criteri che possono causare questo problema.

* *Non si è ricevuto la notifica webhook?*

    Verificare che l'applicazione che riceve la notifica webhook sia disponibile e che riesca a elaborare le richieste di un webhook. Per altre informazioni, vedere [qui](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).

* *Esito negativo intermittente dei test con un errore di violazione del protocollo?*

    Un errore di tipo "Violazione del protocollo... CR deve essere seguito da LF" indica un problema relativo al server o alle dipendenze. Questa situazione si verifica quando nella risposta vengono impostate intestazioni in formato non valido e può essere causata da servizi di bilanciamento del carico o reti per la distribuzione di contenuti. In particolare, è possibile che alcune intestazioni non usino CRLF per indicare la fine della riga, violando così la specifica HTTP e non superando quindi la convalida a livello di WebRequest .NET. Controllare la risposta per individuare le intestazioni in cui potrebbe trovarsi la violazione.
    
    Note: l'errore dell'URL potrebbe non verificarsi in browser con una convalida delle intestazioni HTTP meno rigida. Per una spiegazione dettagliata del problema, vedere questo post di blog: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/  
    
* *Non vengono visualizzati dati di telemetria lato server correlati per eseguire la diagnosi per i test non superati?*
    
    Se Application Insights è configurato per l'applicazione lato server, il motivo può essere l'esecuzione del [campionamento](../../azure-monitor/app/sampling.md). Selezionare un risultato di disponibilità diverso.

* *È possibile chiamare codice da un test Web?*

     No. I passaggi del test devono essere nel file con estensione webtest. Inoltre non è possibile chiamare altri test web o utilizzare cicli. Esistono diversi plug-in che potrebbero risultare utili.

* *HTTPS è supportato?*

    Sono supportati TLS 1.1 e TLS 1.2.
* *Esiste una differenza tra "test Web" e "test di disponibilità"?*

    I due termini vengono usati in modo intercambiabile. Test di disponibilità è un termine più generico che include i singoli test di ping URL oltre ai test Web in più passaggi.
    
* *È possibile usare test di disponibilità nel server interno protetto da un firewall?*

    Le soluzioni possono essere due:
    
    * Configurare il firewall per consentire richieste in ingresso dagli [indirizzi IP degli agenti di test Web](../../azure-monitor/app/ip-addresses.md).
    * Scrivere il proprio codice per testare periodicamente il server interno. Eseguire il codice come processo in background in un server di prova protetto da firewall. Il processo di test può inviare i risultati ad Application Insights tramite l'API [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) nel pacchetto SDK di base. In questo modo è necessario testare il server per avere un accesso in uscita per l'endpoint di inserimento di Application Insights, ma questo rappresenta un minore rischio per la sicurezza rispetto all'alternativa di consentire le richieste in ingresso. I risultati non verranno visualizzati nei pannelli dei test Web di disponibilità, ma verranno mostrati come risultati relativi alla disponibilità in Analisi, Ricerca ed Esplorazione metriche.

* *Non è possibile caricare un test Web in più passi*

    Di seguito sono riportate alcune delle possibili cause:
    * È previsto un limite di dimensioni pari a 300 KB.
    * I cicli non sono supportati.
    * I riferimenti ad altri test Web non sono supportati.
    * Le origini dati non sono supportate.

* *Il test in più passi non viene completato*

    È previsto un limite di 100 richieste per ogni test. Inoltre, il test viene arrestato se la durata dell'esecuzione è superiore a due minuti.

* *È possibile eseguire un test con certificati client?*

    Questa funzionalità non è supportata.


## <a name="next"></a>Passaggi successivi
[Ricerca nei registri di diagnostica][diagnostic]

[Risoluzione dei problemi][qna]

[Indirizzi IP degli agenti di test Web](../../azure-monitor/app/ip-addresses.md)

<!--Link references-->

[azure-availability]: ../../insights-create-web-tests.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
