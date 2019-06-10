---
title: 'Esercitazione: Creare e gestire un processo di Analisi di flusso usando il portale di Azure'
description: Questa esercitazione offre una dimostrazione completa relativa all'uso di Analisi di flusso di Azure per analizzare le chiamate fraudolente in un flusso di telefonate.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/03/2019
ms.openlocfilehash: f78555b37cc82c1e97a6f51ec504bc47937ee8c4
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493421"
---
# <a name="analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Analizzare i dati delle telefonate con Analisi di flusso di Azure e visualizzare i risultati in una dashboard Power BI

Questa esercitazione spiega come analizzare i dati delle telefonate con Analisi di flusso di Azure. I dati delle telefonate, generati da un'applicazione client, contengono alcune chiamate fraudolente che verranno filtrate dal processo di Analisi di flusso.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Generare dati delle telefonate di esempio e inviarli a Hub eventi di Azure
> * Creare un processo di Analisi di flusso.
> * Configurare input e output del processo
> * Definire una query per filtrare le chiamate fraudolente
> * Testare e avviare il processo
> * Visualizzare i risultati in Power BI

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, eseguire queste azioni:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).
* Accedere al [portale di Azure](https://portal.azure.com/).
* Scaricare l'app generatore eventi di telefonata [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) dall'Area download Microsoft oppure ottenere il codice sorgente da [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Sarà necessario un account Power BI.

## <a name="create-an-azure-event-hub"></a>Creare un Hub di eventi di Azure

Per consentire ad Analisi di flusso di analizzare il flusso di dati delle chiamate fraudolente, è necessario che i dati vengano inviati ad Azure. In questa esercitazione si invieranno i dati ad Azure usando [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs).

Per creare un nuovo hub eventi e inviare i dati delle chiamate, seguire la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **Crea una risorsa** > **Internet delle cose** > **Hub eventi**.

   ![Creare un Hub eventi di Azure nel portale](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Compilare il riquadro **Crea spazio dei nomi** con i valori seguenti:

   |**Impostazione**  |**Valore consigliato** |**Descrizione**  |
   |---------|---------|---------|
   |NOME     | myEventHubsNS        |  Nome univoco che identifica lo spazio dei nomi dell'hub eventi.       |
   |Sottoscrizione     |   \<Sottoscrizione in uso\>      |   Selezionare una sottoscrizione di Azure in cui creare l'hub eventi.      |
   |Gruppo di risorse     |   MyASADemoRG      |  Selezionare **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse per l'account.       |
   |Località     |   Stati Uniti occidentali 2      |    Località in cui lo spazio dei nomi dell'hub eventi può essere distribuito.     |

4. Usare le opzioni predefinite per le restanti impostazioni e selezionare **Crea**.

   ![Creare lo spazio dei nomi dell'hub eventi nel portale di Azure](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. Quando la distribuzione dello spazio dei nomi è stata completata, passare a **Tutte le risorse** e individuare *myEventHubsNS* nell'elenco delle risorse di Azure. Selezionare *myEventHubsNS* per aprirlo.
6. Selezionare quindi **+Hub eventi** e in **Nome** immettere *MyEventHub* o il nome desiderato. Usare le opzioni predefinite per le restanti impostazioni e selezionare **Crea**. Attendere il completamento della distribuzione.

   ![Configurazione di Hub eventi nel portale di Azure](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Concedere l'accesso all'hub eventi e ottenere una stringa di connessione

Affinché un'applicazione possa inviare dati ad Hub eventi di Azure, è necessario che l'hub eventi abbia criteri che consentono l'accesso appropriato. I criteri di accesso generano una stringa di connessione che include informazioni di autorizzazione.

1. Passare all'hub eventi MyEventHub creato nel passaggio precedente. Selezionare **Criteri di accesso condivisi** in **Impostazioni** e quindi selezionare **+ Aggiungi**.

2. Assegnare al criterio il nome **MyPolicy** e assicurarsi che **Gestisci** sia selezionato. Selezionare quindi **Crea**.

   ![Creare criteri di accesso condiviso dell'hub eventi](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. Dopo la creazione del criterio, selezionarlo per aprirlo e individuare **Stringa di connessione - chiave primaria**. Selezionare il pulsante blu **copia** accanto alla stringa di connessione.

   ![Salvare la stringa di connessione dei criteri di accesso condiviso](media/stream-analytics-manage-job/save-connection-string.png)

4. Incollare la stringa di connessione in un editor di testo. La stringa sarà necessaria nella sezione seguente.

   La stringa di connessione è simile a quanto segue:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Si noti che la stringa di connessione contiene più coppie chiave-valore separate da punti e virgola: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey**, e **EntityPath**.

## <a name="start-the-event-generator-application"></a>Avviare l'applicazione generatore eventi

Prima di avviare l'app TelcoGenerator, configurarla per inviare i dati all'istanza di Hub eventi di Azure creata in precedenza.

1. Estrarre il contenuto del file [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Aprire il file `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` in un editor di testo a propria scelta. Ci sono più file con estensione config, quindi assicurarsi di aprire quello giusto.

3. Aggiornare l'elemento `<appSettings>` nel file di configurazione con i dettagli seguenti:

   * Impostare il valore della chiave *EventHubName* sul valore di EntityPath nella stringa di connessione.
   * Impostare il valore della chiave *Microsoft.ServiceBus.ConnectionString* sulla stringa di connessione senza il valore di EntityPath (non dimenticare di rimuovere il punto e virgola che lo precede).

4. Salvare il file.
5. Aprire quindi una finestra di comando e passare alla cartella in cui è stata decompressa l'app TelcoGenerator. Immettere quindi il comando seguente:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   Questo comando accetta i parametri seguenti:
   * Numero di record di dati delle chiamate all'ora.
   * Percentuale di probabilità di frode, ovvero frequenza con cui l'app deve simulare una chiamata fraudolenta. Il valore 0,2 significa che circa il 20% dei record delle chiamate apparirà come fraudolento.
   * Durata in ore, ovvero numero di ore per cui l'app deve essere eseguita. È anche possibile arrestare l'app in qualsiasi momento terminando il processo (**CTRL+C**) dalla riga di comando.

   Dopo alcuni secondi, l'app inizierà a visualizzare i record delle chiamate mentre ne esegue l'invio all'hub eventi. I dati delle telefonate contengono i campi seguenti:

   |**Record**  |**Definizione**  |
   |---------|---------|
   |CallrecTime    |  Timestamp dell'ora di inizio della chiamata.       |
   |SwitchNum     |  Commutatore telefonico usato per la connessione della chiamata. Per questo esempio i commutatori sono stringhe che rappresentano il paese/area di origine (Stati Uniti, Cina, Regno Unito, Germania o Australia).       |
   |CallingNum     |  Numero di telefono del chiamante.       |
   |CallingIMSI     |  Codice IMSI (International Mobile Subscriber Identity). Identificatore univoco del chiamante.       |
   |CalledNum     |   Numero di telefono del destinatario della chiamata.      |
   |CalledIMSI     |  Codice IMSI (International Mobile Subscriber Identity). Identificatore univoco del destinatario della chiamata.       |

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso.

Dopo aver creato un flusso di eventi di chiamata, è possibile creare un processo di Analisi di flusso che legge i dati dall'hub eventi.

1. Per creare un processo di Analisi di flusso, passare al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Crea una risorsa** > **Internet delle cose** > **Processo di Analisi di flusso**.

3. Compilare il riquadro **Nuovo processo di Analisi di flusso** con i valori seguenti:

   |**Impostazione**  |**Valore consigliato**  |**Descrizione**  |
   |---------|---------|---------|
   |Nome processo     |  ASATutorial       |   Nome univoco che identifica lo spazio dei nomi dell'hub eventi.      |
   |Sottoscrizione    |  \<Sottoscrizione in uso\>   |   Selezionare una sottoscrizione di Azure in cui creare il processo.       |
   |Gruppo di risorse   |   MyASADemoRG      |   Selezionare **Usa esistente** e immettere il nome di un nuovo gruppo di risorse per l'account.      |
   |Località   |    Stati Uniti occidentali 2     |      Località in cui il processo può essere distribuito. È consigliabile posizionare il processo e l'hub eventi nella stessa area per ottenere prestazioni ottimali ed evitare di pagare il trasferimento dei dati tra aree.      |
   |Ambiente di hosting    | Cloud        |     Per la distribuzione dei processi di Analisi di flusso è possibile scegliere tra Cloud o Edge. L'opzione Cloud consente di eseguire la distribuzione nel cloud di Azure, mentre l'opzione Edge consente di eseguire la distribuzione in un dispositivo IoT Edge.    |
   |Unità di streaming     |    1       |      Le unità di streaming rappresentano le risorse di calcolo necessarie per eseguire un processo. Il valore predefinito di questa impostazione è 1. Per informazioni sul ridimensionamento delle unità di streaming, vedere [Informazioni sulle unità di flusso e su come modificarle](stream-analytics-streaming-unit-consumption.md).      |

4. Usare le opzioni predefinite per le restanti impostazioni, selezionare **Crea** e attendere il completamento della distribuzione.

   ![Creare un processo di Analisi di flusso di Azure](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Configurare l'input del processo

Il passaggio successivo consiste nel definire un'origine di input da cui il processo può leggere i dati usando l'hub eventi creato nella sezione precedente.

1. Nel portale di Azure aprire il riquadro **Tutte le risorse** e individuare il processo *ASATutorial* di Analisi di flusso.

2. Nella sezione **Topologia processo** del riquadro del processo di Analisi di flusso selezionare l'opzione **Input**.

3. Selezionare **+ Aggiungi input del flusso** e **Hub eventi**. Compilare il riquadro con i valori seguenti:

   |**Impostazione**  |**Valore consigliato**  |**Descrizione**  |
   |---------|---------|---------|
   |Alias di input     |  CallStream       |  Specificare un nome descrittivo per identificare l'input. L'alias di input può contenere solo caratteri alfanumerici, trattini e caratteri di sottolineatura e deve avere una lunghezza compresa tra 3 e 63 caratteri.       |
   |Sottoscrizione    |   \<Sottoscrizione in uso\>      |   Selezionare la sottoscrizione di Azure in cui è stato creato l'hub eventi. L'hub eventi può trovarsi nella stessa sottoscrizione del processo di Analisi di flusso o in una sottoscrizione diversa.       |
   |Spazio dei nomi dell'hub eventi    |  myEventHubsNS       |  Selezionare lo spazio dei nomi dell'hub eventi creato nella sezione precedente. Nell'elenco a discesa sono presenti tutti gli spazi dei nomi degli hub eventi disponibili nella sottoscrizione corrente.       |
   |Nome dell'hub eventi    |   MyEventHub      |  Selezionare l'hub eventi creato nella sezione precedente. Nell'elenco a discesa sono presenti tutti gli hub eventi disponibili nella sottoscrizione corrente.       |
   |Nome criteri hub eventi   |  MyPolicy       |  Selezionare i criteri di accesso condiviso dell'hub eventi creati nella sezione precedente. Nell'elenco a discesa sono presenti tutti i criteri degli hub eventi disponibili nella sottoscrizione corrente.       |

4. Usare le opzioni predefinite per le restanti impostazioni e selezionare **Salva**.

   ![Configurare l'input Analisi di flusso di Azure](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Configurare l'output del processo

L'ultimo passaggio consiste nel definire un sink di output per il processo, in cui scrivere i dati trasformati. In questa esercitazione ottenere i dati di output e visualizzarli con Power BI.

1. Nel portale di Azure aprire il riquadro **Tutte le risorse** e individuare il processo *ASATutorial* di Analisi di flusso.

2. Nella sezione **Topologia processo** del riquadro del processo di Analisi di flusso selezionare l'opzione **Output**.

3. Selezionare **+ Aggiungi** > **Power BI**. Compilare quindi il modulo con i dettagli seguenti e selezionare **Autorizza**:

   |**Impostazione**  |**Valore consigliato**  |
   |---------|---------|
   |Alias di output  |  MyPBIoutput  |
   |Nome del set di dati  |   ASAdataset  |
   |Nome tabella |  ASATable  |

   ![Configurare l'output Analisi di flusso di Azure](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

4. Quando si seleziona **Autorizza**, viene visualizzata una finestra popup in cui viene chiesto di fornire le credenziali per l'autenticazione dell'account di Power BI. Una volta completato il processo di autorizzazione, fare clic su **Salva** per salvare le impostazioni.

## <a name="define-a-query-to-analyze-input-data"></a>Definire una query per analizzare i dati di input

Il passaggio successivo consiste nel creare una trasformazione che analizzi i dati in tempo reale. Per definire la query di trasformazione, si usa il [linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/dn834998.aspx). La query usata in questa esercitazione consente di rilevare le chiamate fraudolente dai dati delle telefonate.

In questo esempio le chiamate fraudolente provengono dallo stesso utente ma da località diverse e vengono effettuate a cinque secondi di distanza una dall'altra. Ad esempio, lo stesso utente non può eseguire legittimamente una chiamata dagli Stati Uniti e dall'Australia nello stesso momento. Per definire la query di trasformazione per il processo di Analisi di flusso:

1. Nel portale di Azure aprire il riquadro **Tutte le risorse** e passare al processo **ASATutorial** di Analisi di flusso creato in precedenza.

2. Nella sezione **Topologia processo** del riquadro del processo di Analisi di flusso selezionare l'opzione **Query**. La finestra di query elenca gli input e gli output configurati per il processo e consente di creare una query per trasformare il flusso di input.

3. Sostituire la query esistente nell'editor con la query seguente, che esegue un self-join su un intervallo di cinque secondi di dati delle chiamate:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Per controllare le chiamate fraudolente, è possibile eseguire un self-join sui dati di streaming in base al valore di `CallRecTime`. Cercare quindi i record delle chiamate in cui il valore `CallingIMSI` (numero di origine) è lo stesso, ma il valore `SwitchNum` (paese/area di origine) è diverso. Quando si usa un'operazione JOIN con i dati di streaming, il join deve garantire alcuni limiti per la distanza di separazione delle righe corrispondenti nel tempo. Dal momento che i dati di streaming sono infiniti, è necessario specificare i limiti di tempo per la relazione all'interno della clausola **ON** del join usando la funzione [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics).

   La query è simile a un normale join SQL, ad eccezione della funzione **DATEDIFF**. La funzione **DATEDIFF** usata in questa query è specifica di Analisi di flusso e deve essere inclusa nella clausola `ON...BETWEEN`.

4. Fare clic su **Salva** per salvare la query.

   ![Definire la query di Analisi di flusso di Azure nel portale](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>Testare la query

È possibile testare una query dall'editor di query usando i dati di esempio. Per testare la query, seguire questa procedura:

1. Assicurarsi che l'app TelcoGenerator sia in esecuzione e generi record delle telefonate.

2. Nel riquadro **Query** selezionare i puntini di sospensione accanto all'input *CallStream* e quindi selezionare **Dati di esempio da input**.

3. Impostare **Minuti** su 3 e selezionare **OK**. Vengono campionati tre minuti di dati dal flusso di input e viene inviata una notifica quando i dati di esempio sono pronti. È possibile visualizzare lo stato del campionamento sulla barra di notifica.

   I dati di esempio vengono archiviati temporaneamente e sono disponibili finché rimane aperta la finestra di query. Se si chiude la finestra di query, i dati di esempio verranno rimossi e sarà necessario creare un nuovo set di dati se si intende eseguire il test. In alternativa, è possibile usare un file JSON che contiene i dati di esempio da [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) e quindi caricare il file con estensione JSON da usare come dati di esempio per l'input *CallStream*.

   ![Oggetto visivo di come campionare dati di input per Analisi di flusso di Azure](media/stream-analytics-manage-job/sample-input-data-asa.png)

4. Selezionare **Test** per testare la query. I risultati visualizzati sono simili ai seguenti:

   ![Output del test di query di Analisi di flusso](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>Avviare il processo e visualizzare l'output

1. Per avviare il processo, passare al riquadro **Panoramica** del processo e selezionare **Avvia**.

2. Selezionare **Ora** come orario di avvio per l'output del processo e selezionare **Avvia**. È possibile visualizzare lo stato del processo nella barra di notifica.

3. Dopo il completamento del processo, passare a [Power BI](https://powerbi.com/) e accedere con l'account aziendale o dell'istituto di istruzione. Se la query del processo di Analisi di flusso restituisce risultati, il set di dati *ASAdataset* creato è presente nella scheda **Set di dati**.

4. Nell'area di lavoro di Power BI selezionare **+ Crea** per creare un nuovo dashboard denominato *Fraudulent Calls*.

5. Nella parte superiore della finestra selezionare **Aggiungi riquadro**. Selezionare quindi **Dati in streaming personalizzati** e **Avanti**. Scegliere **ASAdataset** in **Set di dati personali**. Selezionare **Scheda** nell'elenco a discesa **Tipo di visualizzazione** e aggiungere **fraudulent calls** a **Campi**. Selezionare **Avanti** per immettere un nome per il riquadro e quindi selezionare **Applica** per creare il riquadro.

   ![Creare i riquadri della dashboard di Power BI](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Ripetere il passaggio 5 con le opzioni seguenti:
   * Per Tipo di visualizzazione selezionare Grafico a linee.
   * Aggiungere un asse e selezionare **windowend**.
   * Aggiungere un valore e selezionare **fraudulentcalls**.
   * Per **Intervallo di tempo da visualizzare**  selezionare gli ultimi 10 minuti.

7. Dopo l'aggiunta di entrambi i riquadri, il dashboard è simile a quello dell'esempio seguente. Si noti che, se l'applicazione mittente dell'hub eventi e l'applicazione di Analisi di flusso sono in esecuzione, il dashboard di Power BI viene aggiornato periodicamente all'arrivo di nuovi dati.

   ![Visualizzare i risultati nella dashboard di Power BI](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Incorporamento del dashboard di Power BI in un'applicazione Web

Per questa parte dell'esercitazione si userà un'applicazione Web [ASP.NET](https://asp.net/) di esempio creata dal team di Power BI per incorporare il dashboard. Per altre informazioni sull'incorporamento di dashboard, vedere l'articolo [Incorporamento con Power BI](https://docs.microsoft.com/power-bi/developer/embedding).

Per configurare l'applicazione, passare al repository GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) e seguire le istruzioni nella sezione **User Owns Data** (Utente proprietario dei dati). Usare gli URL di reindirizzamento e della home page nella sottosezione **integrate-dashboard-web-app**. Dal momento che si sta usando l'esempio relativo al dashboard, usare il codice di esempio **integrate-dashboard-web-app** disponibile nel [repository GitHub](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app).
Una volta che l'applicazione è in esecuzione nel browser, seguire questa procedura per incorporare il dashboard creato in precedenza nella pagina Web:

1. Selezionare **Accedi a Power BI**, per concedere all'applicazione l'accesso ai dashboard nell'account Power BI.

2. Fare clic sul pulsante **Get Dashboards** (Ottieni dashboard), per visualizzare i dashboard dell'account in una tabella. Individuare il nome del dashboard creato in precedenza, ovvero **powerbi-embedded-dashboard** e copiare il valore di **EmbedUrl** corrispondente.

3. Infine, incollare **EmbedUrl** nel campo di testo corrispondente e selezionare **Embed Dashboard** (Incorpora dashboard). È ora possibile visualizzare lo stesso dashboard incorporato in un'applicazione Web.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un processo di Analisi di flusso di esempio, sono stati analizzati i dati in ingresso e i risultati sono stati visualizzati in un dashboard di Power BI. Per altre informazioni sui processi di Analisi di flusso, continuare con l'esercitazione successiva:

> [!div class="nextstepaction"]
> [Eseguire Funzioni di Azure in processi di Analisi di flusso](stream-analytics-with-azure-functions.md)
