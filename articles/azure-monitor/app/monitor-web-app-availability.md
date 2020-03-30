---
title: Monitorare la disponibilità e la velocità di risposta dei siti Web | Microsoft Docs
description: Configurare i test Web in Application Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente.
ms.topic: conceptual
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 61358051a8ddc32bc01ec5e231f4c28ebfa18ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670033"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorare la disponibilità di qualsiasi sito web

Dopo aver distribuito l'app Web o il sito Web, è possibile configurare test ricorrenti per monitorare la disponibilità e la velocità di risposta. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) invia richieste Web all'applicazione a intervalli regolari da diversi punti in tutto il mondo. Può avvisarti se l'applicazione non risponde o se risponde troppo lentamente.

È possibile configurare test di disponibilità per qualsiasi endpoint HTTP o HTTPS accessibile dalla rete Internet pubblica. Non è necessario apportare modifiche al sito Web che si sta testando. Infatti, non deve nemmeno essere un sito che possiedi. È possibile testare la disponibilità di un'API REST da cui dipende il servizio.

### <a name="types-of-availability-tests"></a>Tipi di test di disponibilità:

Esistono tre tipi di test di disponibilità:

* [Test di ping URL](#create-a-url-ping-test): un semplice test che può essere creato nel portale di Azure.
* [Test Web a più fasi](availability-multistep.md): una registrazione di una sequenza di richieste Web, che può essere riprodotta per testare scenari più complessi. I test Web in più passaggi vengono creati in Visual Studio Enterprise e caricati nel portale per l'esecuzione.
* Test di [disponibilità traccia personalizzati:](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet)se si decide di `TrackAvailability()` creare un'applicazione personalizzata per eseguire test di disponibilità, il metodo può essere utilizzato per inviare i risultati ad Application Insights.Custom Track Availability Tests : If you decide to create a custom application to run availability tests, the method can be used to send the results to Application Insights.

**È possibile creare fino a 100 test di disponibilità per ogni risorsa di Application Insights.You can create up to 100 availability tests per Application Insights resource.**

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Per creare un test di disponibilità, è innanzitutto necessario creare una risorsa di Application Insights.In order to create an availability test, you first need to create an Application Insights resource. Se è già stata creata una risorsa, passare alla sezione successiva per [creare un test ping URL](#create-a-url-ping-test).

Nel portale di Azure selezionare **Crea una risorsa** > **Strumenti** > di sviluppo**Application Insights** e [creare una risorsa di Application Insights.](create-new-resource.md)

## <a name="create-a-url-ping-test"></a>Creare un test di ping URL

Il nome "URL ping test" è un po ' un termine improprio. Per essere chiari, questo test non fa alcun uso di ICMP (Internet Control Message Protocol) per verificare la disponibilità del tuo sito. Utilizza invece funzionalità di richiesta HTTP più avanzate per convalidare se un endpoint risponde. Misura inoltre le prestazioni associate a tale risposta e aggiunge la possibilità di impostare criteri di successo personalizzati, insieme a funzionalità più avanzate come l'analisi delle richieste dipendenti e l'abilitazione di nuovi tentativi.

Per creare la prima richiesta di disponibilità, aprire il riquadro Disponibilità e selezionare **Crea test**.

![Fill at least the URL of your website](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Creare un testCreate a test

|Impostazione| Spiegazione
|----|----|----|
|**Url** |  L'URL può essere qualsiasi pagina Web che si vuole testare, ma deve essere visibile da Internet pubblico. L'URL può includere una stringa di query. In questo modo, ad esempio, è possibile esercitarsi nell'uso del database. Se l'URL comporta un reindirizzamento, l'operazione viene effettuata fino a un numero massimo di 10 reindirizzamenti.|
|**Analizzare le richieste dipendentiParse dependent requests**| Test richiede immagini, script, file di stile e altri file che fanno parte della pagina Web sottoposta a test. Il tempo di risposta registrato include il tempo impiegato per ottenere questi file. Il test ha esito negativo se una di queste risorse non può essere scaricata correttamente entro il timeout per l'intero test. Se l'opzione non viene selezionata, il test richiede solo il file in corrispondenza dell'URL specificato. L'abilitazione di questa opzione comporta un controllo più rigoroso. Il test potrebbe non riuscire per i casi, che potrebbero non essere evidenti durante la navigazione manuale del sito.
|**Abilitare i tentativiEnable retries**|quando il test ha esito negativo, viene ritentato dopo un breve intervallo. Un errore viene segnalato solo se tre tentativi successivi non riescono. I test successivi vengono quindi eseguiti in base alla frequenza di test normale. I nuovi tentativi saranno temporaneamente sospesi fino al completamento successivo. Questa regola viene applicata in modo indipendente in ogni località di test. **Si consiglia questa opzione**. In media, circa l'80% degli errori non si ripresenta al nuovo tentativo.|
|**Frequenza di prova**| impostare la frequenza di esecuzione del test da ogni località di test. Con una frequenza predefinita di cinque minuti e cinque località di test, il sito verrà testato in media ogni minuto.|
|**Posizioni dei test**| Sono i luoghi da cui i nostri server inviano richieste web al tuo URL. **Il nostro numero minimo di posizioni di prova consigliate è cinque** per assicurare che tu possa distinguere i problemi nel tuo sito web da problemi di rete. È possibile selezionare fino a 16 località.

**Se l'URL non è visibile da Internet pubblico, è possibile scegliere di aprire selettivamente il firewall per consentire solo le transazioni**di prova tramite . Per ulteriori informazioni sulle eccezioni del firewall per gli agenti di test di disponibilità, consultare la [guida all'indirizzo IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Si consiglia vivamente di testare da più posizioni con **un minimo di cinque posizioni**. Questo serve a evitare falsi allarmi che possono essere dovuti a problemi temporanei di una località specifica. Inoltre abbiamo scoperto che la configurazione ottimale è quella di avere il **numero di posizioni di test essere uguale alla soglia**della posizione di avviso - 2 .

### <a name="success-criteria"></a>Criteri di installazione riuscita

|Impostazione| Spiegazione
|----|----|----|
| **Timeout test** |ridurre questo valore per ricevere avvisi in merito alle risposte lente. Il test viene conteggiato come non riuscito se le risposte dal sito non sono state ricevute entro questo periodo. Se è stata selezionata l'opzione **Analizza richieste dipendenti**, è necessario che tutti gli script, i file di stile, le immagini e le altre risorse dipendenti siano stati ricevuti entro questo periodo.|
| **Risposta HTTP** | codice di stato restituito che indica un'operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web.|
| **Corrispondenza contenuto** | Una stringa, come "Benvenuto!" Verifichiamo che in ogni risposta ci una corrispondenza esatta di maiuscolo e minuscolo. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. È importante ricordare che, se il contenuto cambia, potrebbe essere necessario aggiornare la stringa. **Solo i caratteri inglesi sono supportati con la corrispondenza del contenuto** |

### <a name="alerts"></a>Avvisi

|Impostazione| Spiegazione
|----|----|----|
|**Quasi in tempo reale (anteprima)** | Ti consigliamo di utilizzare avvisi quasi in tempo reale. La configurazione di questo tipo di avviso viene eseguita dopo la creazione del test di disponibilità.  |
|**Classico** | Non è più consigliabile usare gli avvisi classici per i nuovi test di disponibilità.|
|**Soglia posizione avviso**|Si consiglia un minimo di 3-5 posizioni. La relazione ottimale tra la soglia della posizione di avviso e il numero di posizioni di test è il numero di posizioni di **posizione** = **di avviso di posizioni di test - 2, con un minimo di cinque posizioni di test.**|

## <a name="see-your-availability-test-results"></a>Visualizzare i risultati del test di disponibilità

I risultati dei test di disponibilità possono essere visualizzati con le viste del grafico a linee e a dispersione.

Dopo alcuni minuti, fare clic su **Aggiorna** per visualizzare i risultati del test.

![visualizzazione Riga](./media/monitor-web-app-availability/availability-refresh-002.png)

La visualizzazione scatterplot mostra esempi dei risultati del test che contengono dettagli diagnostici per i test. Il motore di test archivia i dettagli diagnostici per i test che hanno restituito errori. Per i test riusciti, vengono archiviati i dettagli diagnostici per un subset delle esecuzioni. Passare il puntatore del mouse su uno dei punti verdi/rossi per visualizzare il test, il nome e la posizione del test.

![visualizzazione Riga](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selezionare una posizione o un test specifico oppure ridurre il periodo di tempo per visualizzare più risultati riguardo all'intervallo desiderato. Usare Esplora ricerche per visualizzare i risultati di tutte le esecuzioni oppure usare query di analisi per eseguire report personalizzati per i dati.

## <a name="inspect-and-edit-tests"></a> Esaminare e modificare i test

Per modificare, disattivare temporaneamente o eliminare un test, fare clic sui puntini di sospensione accanto al nome di un test. La propagazione delle modifiche alla configurazione a tutti gli agenti di test dopo che è stata apportata una modifica può richiedere fino a 20 minuti.

![Visualizzare i dettagli del test. Modificare e disabilitare un test Web](./media/monitor-web-app-availability/edit.png)

Può essere necessario disabilitare i test di disponibilità o le regole di avviso associate ai test durante le operazioni di manutenzione del servizio.

## <a name="if-you-see-failures"></a>In caso di errori

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

Oltre ai risultati non elaborati, è anche possibile visualizzare due metriche di disponibilità chiave in [Esplora metriche:](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

1. Disponibilità: percentuale dei test riusciti rispetto a tutte le esecuzioni di test.
2. Durata test: durata media dei test rispetto a tutte le esecuzioni di test.

## <a name="automation"></a>Automazione

* [Usare script di PowerShell per configurare un test di disponibilità](../../azure-monitor/app/powershell.md#add-an-availability-test) automaticamente.
* Configurare un [webhook](../../azure-monitor/platform/alerts-webhooks.md) che verrà chiamato quando viene generato un avviso.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Articolo dedicato [sulla risoluzione dei problemi](troubleshoot-availability.md).

## <a name="next-steps"></a>Passaggi successivi

* [Avvisi di disponibilitàAvailability Alerts](availability-alerts.md)
* [Test Web in più passaggi](availability-multistep.md)


