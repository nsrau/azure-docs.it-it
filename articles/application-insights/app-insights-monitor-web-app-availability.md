<properties
	pageTitle="Monitorare la disponibilità e la velocità di risposta dei siti Web | Microsoft Azure"
	description="Configurare i test Web in Application Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/18/2016"
	ms.author="awills"/>

# Monitorare la disponibilità e la velocità di risposta dei siti Web

Dopo aver distribuito l'applicazione Web, è possibile configurare test web per monitorarne la disponibilità e la velocità di risposta. Application Insights invia richieste Web a intervalli regolari da vari punti distribuiti in tutto il mondo e può inviare avvisi all'utente nel caso in cui l'applicazione risponda lentamente o non risponda affatto.

![Esempio di test Web](./media/app-insights-monitor-web-app-availability/appinsights-10webtestresult.png)

È possibile configurare test Web per qualsiasi endpoint HTTPS accessibile dalla rete Internet pubblica.

Sono disponibili due tipi di test Web:

* [Test di ping URL](#set-up-a-url-ping-test): un semplice test che può essere creato nel portale di Azure.
* [Test Web in più passaggi](#multi-step-web-tests): viene creato in Visual Studio Ultimate o Visual Studio Enterprise e caricato nel portale.

È possibile creare fino a 10 test Web per ogni risorsa dell'applicazione.


## Configurare un test di ping URL

### <a name="create"></a>1. Creare una nuova risorsa

Ignorare questo passaggio se è già stata [configurata una risorsa di Application Insights][start] per questa applicazione e si vuole visualizzare i dati sulla disponibilità nella stessa posizione.

Accedere a [Microsoft Azure](http://azure.com), passare al [portale di Azure](https://portal.azure.com) e creare una nuova risorsa di Application Insights.

![New > Application Insights](./media/app-insights-monitor-web-app-availability/11-new-app.png)

Verrà aperto il pannello Panoramica per la nuova risorsa. Per trovarlo in qualsiasi momento nel [portale di Azure](https://portal.azure.com), fare clic su **Sfoglia**.

### <a name="setup"></a>2. Creare un test Web

Nella risorsa di Application Insights cercare il riquadro Disponibilità. Fare clic per aprire il pannello dei test Web per l'applicazione e aggiungere un test Web.

![Fill at least the URL of your website](./media/app-insights-monitor-web-app-availability/13-availability.png)

- L'**URL **deve essere visibile dalla rete Internet pubblica. Può includere una stringa di query&#151;pertanto è possibile, ad esempio, esercitarsi nell'uso del database. Se l'URL comporta un reindirizzamento, l'operazione verrà effettuata fino a un numero massimo di 10 reindirizzamenti.
- **Analizza richieste dipendenti**: immagini, script, file di stile e altre risorse della pagina sono richiesti come parte del test. Il test avrà esito negativo se non è possibile scaricare tutte queste risorse entro la scadenza definita per l'intero test.
- Se la casella **Abilitare tentativi** è selezionata, quando il test non riesce verrà eseguito un nuovo tentativo dopo un breve intervallo. Un errore viene segnalato solo se tre tentativi successivi non riescono. I test successivi vengono quindi eseguiti in base alla frequenza di test normale. I nuovi tentativi saranno temporaneamente sospesi fino al completamento successivo. Questa regola viene applicata in modo indipendente in ogni posizione di test. Questa è un'impostazione consigliata. In media, circa l'80% degli errori non si ripresenta al nuovo tentativo.
- **Frequenza test**: impostare la frequenza di esecuzione dei test da ogni posizione di test. Con una frequenza di 5 minuti e cinque posizioni di test, il sito verrà testato in media ogni minuto.
- Le **posizioni dei test** sono le ubicazioni da cui i server inviano richieste Web all'URL indicato. Sceglierne più di una, per poter distinguere i problemi del sito Web dai problemi di rete. È possibile selezionare fino a 16 posizioni.

- **Criteri di successo**:

    **Test timeout**: diminuire questo valore per ricevere avvisi in merito alle risposte lente. Il test viene conteggiato come non riuscito se le risposte dal sito non sono state ricevute entro questo periodo. Se è stata selezionata l'opzione **Analizza richieste dipendenti**, è necessario che tutti gli script, i file di stile, le immagini e altre risorse dipendenti siano stati ricevuti entro questo intervallo.

    **Risposta HTTP**: codice di stato restituito che indica un'operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web.

    Stringa relativa alla **corrispondenza di contenuto**, quale "Benvenuto!" Verrà verificato che sia presente in tutte le risposte. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. È importante ricordare che, se il contenuto cambia, potrebbe essere necessario aggiornare la stringa.


- Per impostazione predefinita, gli **Avvisi** vengono inviati se si verificano errori in tre posizioni in cinque minuti. Un errore in una posizione può indicare un errore di rete e non un problema con il sito. È comunque possibile modificare la soglia in modo da aumentare la sensibilità del test e modificare i destinatari a cui inviare i messaggi di posta elettronica.

    È possibile configurare un [webhook](../azure-portal/insights-webhooks-alerts.md) chiamato quando viene generato un avviso.

#### Testare più URL

Aggiungere altri test. Ad esempio, oltre a testare la home page, è possibile verificare che il database sia in esecuzione testando l'URL per una ricerca.


### <a name="monitor"></a>3. Visualizzare i report di disponibilità

Dopo 1 o 2 minuti fare clic su **Aggiorna** nel pannello della disponibilità o dei test Web. (l'aggiornamento non viene eseguito automaticamente).

![Summary results on the home blade](./media/app-insights-monitor-web-app-availability/14-availSummary.png)

Fare clic su qualsiasi barra nel grafico di riepilogo in alto per ottenere una visualizzazione più dettagliata di quel periodo di tempo.

Questi grafici combinano i risultati per tutti i test Web di questa applicazione.

#### Componenti della pagina Web

Immagini, fogli di stile, script e altri componenti statici della pagina Web sottoposta a test sono richiesti nell'ambito del test.

Il tempo di risposta registrato è il tempo impiegato per il caricamento completo di tutti i componenti.

In caso di errore di caricamento dei componenti, il test viene contrassegnato come non riuscito.

## <a name="failures"></a>Se si verificano errori...

Fare clic su un punto rosso.

![Click a red dot](./media/app-insights-monitor-web-app-availability/14-availRedDot.png)

In alternativa, scorrere verso il basso e fare clic su un test con risultato inferiore al 100%.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/15-webTestList.png)

Ciò consente di visualizzare i risultati di tale test.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/16-1test.png)

Il test viene eseguito da diverse posizioni&#151;selezionarne una in cui i risultati siano inferiori al 100%.

![Click a specific webtest](./media/app-insights-monitor-web-app-availability/17-availViewDetails.png)


Scorrere verso il basso fino a **Test non superati** e selezionare un risultato.

Fare clic sul risultato per valutarlo nel portale e visualizzare il motivo dell'errore.

![Webtest run result](./media/app-insights-monitor-web-app-availability/18-availDetails.png)


In alternativa, scaricare il file dei risultati ed esaminarlo in Visual Studio.


*Ha un aspetto corretto ma è segnalato come errore?* Controllare tutte le immagini, gli script, i fogli di stile e qualsiasi altro file caricato dalla pagina. Se uno di essi non riesce, il test verrà segnalato come non superato, anche se la pagina HTML principale viene caricata correttamente.



## Test Web in più passaggi

È possibile monitorare uno scenario che comporta una sequenza di URL. Ad esempio, se si monitora un sito Web di vendita, si potrebbe testare il corretto funzionamento dei carrelli acquisti.

Per creare un test in più passaggi, registrare lo scenario con Visual Studio, quindi caricare la registrazione in Application Insights. Application Insights riprodurrà lo scenario a intervalli e verificherà le risposte.

Si noti che non è possibile usare funzioni codificate nei test: i passaggi dello scenario devono essere contenuti sotto forma di script nel file con estensione webtest.

#### 1\. Registrare uno scenario

Usare Visual Studio Enterprise o Ultimate per registrare una sessione Web.

1. Creare un progetto di test delle prestazioni Web.

    ![In Visual Studio, creare un nuovo progetto dal modello di test di carico e prestazioni Web.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

2. Aprire il file con estensione webtest e iniziare la registrazione.

    ![Aprire il file con estensione webtest e fare clic su Registra.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)

3. Eseguire le azioni utente che si vuole simulare nel test: aprire il sito Web, aggiungere un prodotto al carrello e così via. Quindi, arrestare il test.

    ![Il registratore esecuzioni dei test Web viene eseguito in Internet Explorer.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Non creare uno scenario lungo, in quanto è presente un limite di 100 passaggi e 2 minuti.

4. Modificare il test per:
 - Aggiungere convalide per verificare i codici di testo e di risposta ricevuti.
 - Rimuovere tutte le interazioni superflue. È inoltre possibile rimuovere le richieste dipendenti per le immagini o per Active Directory o i siti di rilevamento.

    Tenere presente che è possibile modificare solo lo script del test ma non è possibile aggiungere codice personalizzato o chiamare altri test web. Non inserire cicli nel test. È possibile utilizzare i plug-in del test web standard.

5. Eseguire il test in Visual Studio per verificarne il corretto funzionamento.

    Il programma di esecuzione del test Web apre un browser Web e ripete le azioni registrate. Verificare che funzioni come previsto.

    ![In Visual Studio, aprire il file con estensione webtest e fare clic su Esegui.](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)


#### 2\. Caricare il test Web in Application Insights

1. Nel portale Application Insights, creare un nuovo test Web.

    ![Nel pannello Test Web, scegliere Aggiungi.](./media/app-insights-monitor-web-app-availability/16-another-test.png)

2. Selezionare un test in più passaggi e caricare il file con estensione webtest.

    ![Selezionare Test Web in più passaggi.](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    Impostare le posizioni di test, la frequenza e i parametri di avviso allo stesso modo dei test ping.

Visualizzare i risultati del test e gli eventuali errori nello stesso modo dei test con singolo URL.

Un motivo frequente di errore è l'eccessiva durata del test. L'esecuzione non deve superare i due minuti.

Non dimenticare che, perché il test riesca, tutte le risorse di una pagina devono essere caricate correttamente, inclusi script, fogli di stile, immagini e così via.

Si noti che il test Web deve essere interamente contenuto nel file con estensione webtest: non è possibile usare funzioni codificate nel test.


### Inserimento di plug-in relativi a tempo e numeri casuali nel test in più passaggi

Si supponga di voler testare uno strumento che riceva dati dipendenti dal tempo, come ad esempio valori di scorte da un feed esterno. Quando si registra il test Web, è necessario usare tempi specifici impostandoli come parametri del test, StartTime e EndTime.

![Un test Web con parametri.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

Quando si esegue il test, si vuole che EndTime sia sempre l'ora corrente e StartTime 15 minuti fa.

I plug-in del test Web consentono di definire questa impostazione.

1. Aggiungere un plug-in del test Web per ciascun valore di parametro desiderato. Nella barra degli strumenti del test Web scegliere **Aggiungi plug-in test Web**.

    ![Scegliere Aggiungi plug-in test Web e selezionare un tipo.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    In questo esempio, verranno usate due istanze di Plug-in data e ora, una per "15 minuti fa" e l'altra per "ora".

2. Aprire le proprietà di ciascun plug-in. Assegnare un nome al plug-in e impostarlo in modo che usi l'ora corrente. Per uno di essi, impostare Aggiungi minuti = -15.

    ![Set name, Use Current Time e Add Minutes.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)

3. Nei parametri del test Web, usare {{nome plug-in}} per fare riferimento a un nome di plug-in.

    ![Nel parametro di test usare {{nome plug-in}}.](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Caricare quindi il test nel portale. Userà i valori dinamici ogni volta che verrà eseguito.

## Gestione degli accessi

Se gli utenti accedono all'app, è possibile simulare l'accesso in diversi modi per testare le pagine usate per l'accesso. L'approccio da preferire dipende dal tipo di sicurezza fornito dall'app.

In tutti i casi è necessario creare un account solo a scopo di test. Se possibile, limitarne le autorizzazioni in modo che sia un account di sola lettura.

* Nome utente e password semplici: registrare un test Web nel modo consueto. Eliminare prima di tutto i cookie.
* Autenticazione SAML: è possibile usare il plug-in SAML disponibile per i test Web.
* Segreto client: da usare se l'applicazione ha un percorso di accesso che comporta un segreto client. Azure Active Directory ne fornisce uno. 
* Autenticazione aperta: ad esempio, l'accesso con il proprio account Microsoft o Google. Molte app che usano OAuth offrono l'alternativa del segreto client, si consiglia quindi di cercare prima di tutto il segreto client. Se il test deve eseguire l'accesso tramite OAuth, procedere come indicato di seguito:
 * Usare uno strumento come Fiddler per esaminare il traffico tra il Web browser, il sito di autenticazione e l'app. 
 * Eseguire due o più accessi usando computer o browser diversi oppure a distanza di tempo, per lasciar scadere i token.
 * Mettendo a confronto le diverse sessioni, identificare il token restituito dal sito di autenticazione e poi passato al server applicazioni dopo l'accesso. 
 * Registrare un test Web usando Visual Studio. 
 * Impostare i parametri dei token quando questi vengono restituiti dall'autenticatore e usarli per l'esecuzione di query nel sito. Visual Studio prova a impostare i parametri del test, ma non imposta correttamente i parametri dei token.


## <a name="edit"></a> Modificare o disabilitare un test

Aprire un singolo test per modificarlo o disabilitarlo.

![Modificare o disabilitare un test Web](./media/app-insights-monitor-web-app-availability/19-availEdit.png)

Disabilitare i test Web può essere utile durante l'esecuzione di operazioni di manutenzione sul servizio.

## Automazione

* È possibile [usare script di PowerShell per configurare un test Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/) automaticamente. 
* Configurare un [webhook](../azure-portal/insights-webhooks-alerts.md) chiamato quando viene generato un avviso.

## Domande? Problemi?

* *È possibile chiamare codice da un test Web?*

    No. I passaggi del test devono essere nel file con estensione webtest. Inoltre non è possibile chiamare altri test web o utilizzare cicli. Esistono numerosi plug-in che potrebbero risultare utili.

* *HTTPS è supportato?*

    Attualmente, sono supportati SSL 3.0 e TLS 1.0.

* *Esiste una differenza tra "test Web" e "test di disponibilità"?*

    I due termini vengono usati in modo intercambiabile.

* *È possibile usare test di disponibilità nel server interno protetto da un firewall?*

    Configurare il firewall per consentire le richieste dagli indirizzi IP nell'elenco riportato alla fine di questo articolo.

## <a name="video"></a>Video

> [AZURE.VIDEO monitoring-availability-with-application-insights]

## <a name="next"></a>Passaggi successivi

[Ricerca nei registri di diagnostica][diagnostic]

[Risoluzione dei problemi][qna]


## Indirizzi IP dei test Web

Di seguito è riportato l'elenco corrente di indirizzi IP per la configurazione di un firewall per consentire i test Web. L'elenco può variare nel tempo.

Aprire le porte 80 (HTTP) e 443 (HTTPS).

```

213.199.178.54
213.199.178.55
213.199.178.56
213.199.178.61
213.199.178.57
213.199.178.58
213.199.178.59
213.199.178.60
213.199.178.63
213.199.178.64
207.46.98.158
207.46.98.159
207.46.98.160
207.46.98.157
207.46.98.152
207.46.98.153
207.46.98.156
207.46.98.162
207.46.98.171
207.46.98.172
65.55.244.40
65.55.244.17
65.55.244.42
65.55.244.37
65.55.244.15
65.55.244.16
65.55.244.44
65.55.244.18
65.55.244.46
65.55.244.47
207.46.14.60
207.46.14.61
207.46.14.62
207.46.14.55
207.46.14.63
207.46.14.64
207.46.14.51
207.46.14.52
207.46.14.56
207.46.14.65
157.55.14.60
157.55.14.61
157.55.14.62
157.55.14.47
157.55.14.64
157.55.14.65
157.55.14.43
157.55.14.44
157.55.14.49
157.55.14.50
65.54.66.56
65.54.66.57
65.54.66.58
65.54.66.61
207.46.71.54
207.46.71.52
207.46.71.55
207.46.71.38
207.46.71.51
207.46.71.57
207.46.71.58
207.46.71.37
202.89.228.67
202.89.228.68
202.89.228.69
202.89.228.57
65.54.78.49
65.54.78.50
65.54.78.51
65.54.78.54
94.245.82.32
94.245.82.33
94.245.82.37
94.245.82.38
94.245.72.44
94.245.72.45
94.245.72.46
94.245.72.49
207.46.56.57
207.46.56.58
207.46.56.59
207.46.56.67
207.46.56.61
207.46.56.62
207.46.56.63
207.46.56.64
65.55.82.84
65.55.82.85
65.55.82.86
65.55.82.81
65.55.82.87
65.55.82.88
65.55.82.89
65.55.82.90
65.55.82.91
65.55.82.92
94.245.78.40
94.245.78.41
94.245.78.42
94.245.78.45
70.37.147.43
70.37.147.44
70.37.147.45
70.37.147.48
94.245.66.43
94.245.66.44
94.245.66.45
94.245.66.48

```


<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

<!---HONumber=AcomDC_0427_2016-->