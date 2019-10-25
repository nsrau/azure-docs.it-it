---
title: Monitorare la disponibilità e la velocità di risposta dei siti Web | Microsoft Docs
description: Configurare i test Web in Application Insights. Ottenere avvisi se un sito Web diventa non disponibile o risponde lentamente.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/16/2019
ms.reviewer: sdash
ms.openlocfilehash: 074b5c175305131cd67cc6660d13756a83386c11
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819289"
---
# <a name="monitor-the-availability-of-any-website"></a>Monitorare la disponibilità di qualsiasi sito Web

Dopo aver distribuito l'app Web o il sito Web, è possibile configurare test ricorrenti per monitorare la disponibilità e la velocità di risposta. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) invia richieste Web all'applicazione a intervalli regolari da diversi punti in tutto il mondo. Consente di avvisare l'utente se l'applicazione non risponde o se risponde troppo lentamente.

È possibile configurare test di disponibilità per qualsiasi endpoint HTTP o HTTPS accessibile dalla rete Internet pubblica. Non è necessario apportare modifiche al sito Web che si sta testando. Infatti, non è nemmeno necessario che sia un sito di cui si è proprietari. È possibile verificare la disponibilità di un'API REST da cui dipende il servizio.

### <a name="types-of-availability-tests"></a>Tipi di test di disponibilità:

Sono disponibili tre tipi di test di disponibilità:

* [Test di ping URL](#create-a-url-ping-test): un semplice test che può essere creato nel portale di Azure.
* [Test Web](availability-multistep.md)in più passaggi: registrazione di una sequenza di richieste Web, che può essere riprodotta per testare scenari più complessi. I test Web in più passaggi vengono creati in Visual Studio Enterprise e caricati nel portale per l'esecuzione.
* [Test di disponibilità del rilevamento personalizzato](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability?view=azure-dotnet): se si decide di creare un'applicazione personalizzata per eseguire i test di disponibilità, è possibile usare il metodo `TrackAvailability()` per inviare i risultati a Application Insights.

**È possibile creare fino a 100 test di disponibilità per ogni risorsa Application Insights.**

## <a name="create-an-application-insights-resource"></a>Creare una risorsa di Application Insights

Per creare un test di disponibilità, è prima di tutto necessario creare una risorsa Application Insights. Se è già stata creata una risorsa, passare alla sezione successiva per [creare un test di ping URL](#create-a-url-ping-test).

Dal portale di Azure selezionare **Crea una risorsa** > **strumenti di sviluppo** > **Application Insights** e [creare una risorsa Application Insights](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Creare un test di ping URL

Il nome "URL ping test" è un po' di un nome non appropriato. Per chiarire, questo test non sta utilizzando ICMP (Internet Control Message Protocol) per verificare la disponibilità del sito. USA invece una funzionalità di richiesta HTTP più avanzata per verificare se un endpoint sta rispondendo. Vengono inoltre misurate le prestazioni associate a tale risposta e viene aggiunta la possibilità di impostare criteri di riuscita personalizzati abbinati a funzionalità più avanzate, ad esempio l'analisi di richieste dipendenti e la possibilità di eseguire nuovi tentativi.

Per creare la prima richiesta di disponibilità, aprire il riquadro di disponibilità e selezionare **Crea test**.

![Fill at least the URL of your website](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Creare un test

|Impostazione| Spiegazione
|----|----|----|
|**URL** |  L'URL può essere qualsiasi pagina Web che si desidera testare, ma deve essere visibile dalla rete Internet pubblica. L'URL può includere una stringa di query. In questo modo, ad esempio, è possibile esercitarsi nell'uso del database. Se l'URL comporta un reindirizzamento, l'operazione viene effettuata fino a un numero massimo di 10 reindirizzamenti.|
|**Analizza richieste dipendenti**| Test richiede immagini, script, file di stile e altri file che fanno parte della pagina Web sottoposta a test. Il tempo di risposta registrato include il tempo impiegato per ottenere questi file. Il test ha esito negativo se una di queste risorse non può essere scaricata correttamente entro il timeout per l'intero test. Se l'opzione non viene selezionata, il test richiede solo il file in corrispondenza dell'URL specificato. L'abilitazione di questa opzione comporta un controllo più restrittivo. Il test potrebbe non riuscire per i casi, che potrebbero non essere evidenti quando si esplorano manualmente il sito.
|**Abilita tentativi**|Quando il test ha esito negativo, viene eseguito un nuovo tentativo dopo un breve intervallo. Un errore viene segnalato solo se tre tentativi successivi non riescono. I test successivi vengono quindi eseguiti in base alla frequenza di test normale. I nuovi tentativi saranno temporaneamente sospesi fino al completamento successivo. Questa regola viene applicata in modo indipendente in ogni località di test. **Questa opzione è consigliata**. In media, circa l'80% degli errori non si ripresenta al nuovo tentativo.|
|**Frequenza test**| impostare la frequenza di esecuzione del test da ogni località di test. Con una frequenza predefinita di cinque minuti e cinque località di test, il sito verrà testato in media ogni minuto.|
|**Percorsi test**| Sono le posizioni da cui i server inviano richieste Web all'URL. **Il numero minimo di posizioni di test consigliate è cinque** per garantire che sia possibile distinguere i problemi nel sito Web dai problemi di rete. È possibile selezionare fino a 16 località.

**Se l'URL non è visibile dalla rete Internet pubblica, è possibile scegliere di aprire selettivamente il firewall per consentire solo le transazioni di test tramite**. Per ulteriori informazioni sulle eccezioni del firewall per gli agenti di test di disponibilità, consultare la [Guida all'indirizzo IP](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses#availability-tests).

> [!NOTE]
> Si consiglia vivamente di eseguire test da più posizioni con **un minimo di cinque posizioni**. Questo serve a evitare falsi allarmi che possono essere dovuti a problemi temporanei di una località specifica. È stato inoltre rilevato che la configurazione ottimale prevede che il **numero di percorsi di test sia uguale alla soglia della posizione di avviso + 2**.

### <a name="success-criteria"></a>Criteri di esito positivo

|Impostazione| Spiegazione
|----|----|----|
| **Timeout test** |ridurre questo valore per ricevere avvisi in merito alle risposte lente. Il test viene conteggiato come non riuscito se le risposte dal sito non sono state ricevute entro questo periodo. Se è stata selezionata l'opzione **Analizza richieste dipendenti**, è necessario che tutti gli script, i file di stile, le immagini e le altre risorse dipendenti siano stati ricevuti entro questo periodo.|
| **Risposta HTTP** | codice di stato restituito che indica un'operazione riuscita. 200 è il codice che indica che è stata restituita una normale pagina Web.|
| **Corrispondenza contenuto** | Stringa, ad esempio "Welcome!" Verifichiamo che in ogni risposta ci una corrispondenza esatta di maiuscolo e minuscolo. Deve trattarsi di una stringa di testo normale, senza caratteri jolly. È importante ricordare che, se il contenuto cambia, potrebbe essere necessario aggiornare la stringa. **Con la corrispondenza del contenuto sono supportati solo i caratteri inglesi** |

### <a name="alerts"></a>Avvisi

|Impostazione| Spiegazione
|----|----|----|
|**Quasi in tempo reale (anteprima)** | Si consiglia di usare gli avvisi near-realtime. La configurazione di questo tipo di avviso viene eseguita dopo la creazione del test di disponibilità.  |
|**Classico** | Non è più consigliabile usare gli avvisi classici per i nuovi test di disponibilità.|
|**Soglia posizione avviso**|Si consiglia un minimo di 3-5 posizioni. La relazione ottimale tra la soglia della posizione di avviso e il numero di percorsi di test è la **soglia di posizione degli avvisi**  = **numero di percorsi di test-2, con almeno cinque posizioni di test.**|

## <a name="see-your-availability-test-results"></a>Visualizzare i risultati del test di disponibilità

I risultati del test di disponibilità possono essere visualizzati con visualizzazioni di tracciato a dispersione e a linee.

Dopo alcuni minuti, fare clic su **Aggiorna** per visualizzare i risultati del test.

![Visualizzazione linea](./media/monitor-web-app-availability/availability-refresh-002.png)

La vista scatterplot Mostra esempi dei risultati del test con i dettagli del passo del test diagnostici. Il motore di test archivia i dettagli diagnostici per i test che hanno restituito errori. Per i test riusciti, vengono archiviati i dettagli diagnostici per un subset delle esecuzioni. Passare il mouse su uno dei punti verdi/rossi per visualizzare il test, il nome del test e la posizione.

![Visualizzazione linea](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Selezionare una posizione o un test specifico oppure ridurre il periodo di tempo per visualizzare più risultati riguardo all'intervallo desiderato. Usare Esplora ricerche per visualizzare i risultati di tutte le esecuzioni oppure usare query di analisi per eseguire report personalizzati per i dati.

## <a name="inspect-and-edit-tests"></a>Esaminare e modificare i test

Per modificare, disabilitare temporaneamente o eliminare un test, fare clic sui puntini di sospensione accanto a un nome di test. Potrebbero essere necessari fino a 20 minuti per la propagazione delle modifiche della configurazione a tutti gli agenti di test dopo la modifica.

![Visualizza i dettagli del test. Modificare e disabilitare un test Web](./media/monitor-web-app-availability/edit.png)

Può essere necessario disabilitare i test di disponibilità o le regole di avviso associate ai test durante le operazioni di manutenzione del servizio.

## <a name="if-you-see-failures"></a>Se vengono visualizzati errori

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

Oltre ai risultati non elaborati, è anche possibile visualizzare due metriche di disponibilità chiave in [Esplora metriche](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started):

1. Disponibilità: percentuale dei test riusciti rispetto a tutte le esecuzioni di test.
2. Durata test: durata media dei test rispetto a tutte le esecuzioni di test.

## <a name="automation"></a>Automazione

* [Usare script di PowerShell per configurare un test di disponibilità](../../azure-monitor/app/powershell.md#add-an-availability-test) automaticamente.
* Configurare un [webhook](../../azure-monitor/platform/alerts-webhooks.md) che verrà chiamato quando viene generato un avviso.

## <a name="troubleshooting"></a>risoluzione dei problemi

Articolo dedicato alla [risoluzione dei problemi](troubleshoot-availability.md).

## <a name="next-steps"></a>Passaggi successivi

* [Avvisi di disponibilità](availability-alerts.md)
* [Test Web in più passaggi](availability-multistep.md)


