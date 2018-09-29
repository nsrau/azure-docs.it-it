---
title: 'Esercitazione: Creare e gestire un processo di Analisi di flusso usando il portale di Azure | Microsoft Docs'
description: Questa esercitazione offre una spiegazione completa sull'uso di Analisi di flusso di Azure per analizzare le chiamate fraudolente in un flusso di telefonate.
services: stream-analytics
author: sidramadoss
ms.author: sidram
manager: kfile
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.openlocfilehash: 1955fc033e0351be9da89bbee11dc41d6281a63a
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433991"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>Creare un processo di Analisi di flusso per analizzare i dati delle telefonate e visualizzare i risultati in un dashboard di Power BI
 
Questa esercitazione illustra come usare Analisi di flusso di Azure per analizzare una telefonata di esempio generata da un'applicazione client. I dati della telefonata generati dall'applicazione client contengono alcune chiamate fraudolente e verrà definito un processo di Analisi di flusso per filtrare tali chiamate.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Generare dati delle telefonate di esempio e inviarli a Hub eventi di Azure  
> * Creare un processo di Analisi di flusso.   
> * Configurare input e output per il processo  
> * Definire una query per filtrare le chiamate fraudolente  
> * Testare e avviare il processo  
> * Visualizzare i risultati in Power BI 

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/).  
* Accedere al [Portale di Azure](https://portal.azure.com/).  
* Scaricare l'app generatore eventi di telefonata [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) dall'Area download Microsoft oppure ottenere il codice sorgente da [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).  

## <a name="create-an-azure-event-hub"></a>Creare un Hub di eventi di Azure 

Affinché Analisi di flusso possa analizzare il flusso di dati delle chiamate fraudolente, è necessario inviare i dati ad Azure. In questa esercitazione si invieranno i dati ad Azure usando [Hub eventi di Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs). Per questa esercitazione si creerà un hub eventi e si farà in modo che l'app generatore di eventi invii i dati a tale hub eventi. Per creare un hub eventi, seguire questa procedura:

1. Accedere al portale di Azure.  
2. Selezionare **Crea una risorsa** > **Internet delle cose** > **Hub eventi**.  

   ![Trovare l'hub eventi](media/stream-analytics-manage-job/find-eh.png)
3. Compilare il riquadro **Crea spazio dei nomi** con i valori seguenti:  

   |**Impostazione**  |**Valore consigliato** |**Descrizione**  |
   |---------|---------|---------|
   |NOME     | myEventHubNS        |  Nome univoco che identifica lo spazio dei nomi dell'hub eventi.       |
   |Sottoscrizione     |   \<Sottoscrizione in uso\>      |   Selezionare una sottoscrizione di Azure in cui creare l'hub eventi.      |
   |Gruppo di risorse     |   MyASADemoRG      |  Selezionare **Crea nuovo** e immettere il nome di un nuovo gruppo di risorse per l'account.       |
   |Località     |   Stati Uniti occidentali 2      |    Località in cui lo spazio dei nomi dell'hub eventi può essere distribuito.     |

4. Usare le opzioni predefinite per le restanti impostazioni e selezionare **Crea**.  

   ![Creare lo spazio dei nomi dell'hub eventi](media/stream-analytics-manage-job/create-ehns.png)

5. Una volta completata la distribuzione dello spazio dei nomi, passare a **Tutte le risorse** > individuare "myEventHubNS" nell'elenco delle risorse di Azure > selezionare la voce per aprire l'hub.  
6. Selezionare quindi **+ Hub eventi** > **Nome** e specificare il nome dell'hub eventi "MyEventHub". È possibile usare un nome diverso. Usare le opzioni predefinite per le restanti impostazioni, selezionare **Crea** e attendere il completamento della distribuzione.

   ![Creare un hub eventi](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Concedere l'accesso all'hub eventi e ottenere una stringa di connessione

Affinché un'applicazione possa inviare dati ad Hub eventi di Azure, è necessario che l'hub eventi abbia criteri che consentono l'accesso appropriato. I criteri di accesso generano una stringa di connessione che include informazioni di autorizzazione.

1. Passare all'istanza di **Hub eventi** creata nel passaggio precedente, ovvero "MyEventHub", selezionare **Criteri di accesso condiviso** nel riquadro dell'hub eventi e quindi selezionare **+ Aggiungi**.  
2. Assegnare ai criteri il nome **Mypolicy** > selezionare **Gestisci** > selezionare **Crea**.  

   ![Creare criteri di accesso condiviso dell'hub eventi](media/stream-analytics-manage-job/create-ehpolicy.png)

3. Dopo la distribuzione dei criteri, selezionare per aprirli, individuare **Stringa di connessione - chiave primaria** e selezionare **copia** accanto alla stringa di connessione.  
4. Incollare la stringa di connessione in un editor di testo. La stringa sarà necessaria nella sezione seguente.  

   La stringa di connessione è simile a quanto segue:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   Si noti che la stringa di connessione contiene più coppie chiave-valore, separate da punti e virgola: Endpoint, SharedAccessKeyName, SharedAccessKey ed EntityPath.  

5. Rimuovere la coppia EntityPath dalla stringa di connessione. Non dimenticare di rimuovere il punto e virgola che la precede.

## <a name="start-the-event-generator-application"></a>Avviare l'applicazione generatore eventi

Prima di avviare l'app TelcoGenerator, configurarla per inviare i dati all'istanza di Hub eventi di Azure creata in precedenza.

1. Estrarre il contenuto del file [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).  
2. Aprire il file `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` in un editor di testo a propria scelta. Ci sono più file con estensione config, quindi assicurarsi di aprire quello giusto.  

3. Aggiornare l'elemento <appSettings> nel file di configurazione con i dettagli seguenti:

   * Impostare il valore della chiave EventHubName sul valore di EntityPath nella stringa di connessione.  
   * Impostare il valore della chiave Microsoft.ServiceBus.ConnectionString sulla stringa di connessione senza il valore di EntityPath, ovvero il valore ottenuto nel passaggio 5 nella sezione precedente.

4. Salvare il file.  
5. Aprire quindi una finestra di comando, passare alla cartella in cui è stata decompressa l'applicazione TelcoGenerator e immettere il comando seguente:

   ```
   telcodatagen.exe 1000 0.2 2
   ```

   Questo comando accetta i parametri seguenti:
   * **Numero di record di dati delle chiamate all'ora**.  
   * **Percentuale di probabilità di frode**, ovvero frequenza con cui l'app deve simulare una chiamata fraudolenta. Il valore 0,2 significa che circa il 20% dei record delle chiamate apparirà come fraudolento.  
   * **Durata in ore**, ovvero numero di ore per cui l'app deve essere eseguita. È anche possibile arrestare l'app in qualsiasi momento terminando il processo (CTRL+C) alla riga di comando.

   Dopo alcuni secondi, l'app inizierà a visualizzare i record delle chiamate mentre ne esegue l'invio all'hub eventi. I dati delle telefonate contengono i campi seguenti:

   |**Record**  |**Definizione**  |
   |---------|---------|
   |CallrecTime    |  Timestamp dell'ora di inizio della chiamata.       |
   |SwitchNum     |  Commutatore telefonico usato per la connessione della chiamata. Per questo esempio i commutatori sono stringhe che rappresentano il paese di origine (Stati Uniti, Cina, Regno Unito, Germania o Australia).       |
   |CallingNum     |  Numero di telefono del chiamante.       |
   |CallingIMSI     |  Codice IMSI (International Mobile Subscriber Identity). Identificatore univoco del chiamante.       |
   |CalledNum     |   Numero di telefono del destinatario della chiamata.      |
   |CalledIMSI     |  Codice IMSI (International Mobile Subscriber Identity). Identificatore univoco del destinatario della chiamata.       |

## <a name="create-a-stream-analytics-job"></a>Creare un processo di Analisi di flusso. 

Dopo aver creato un flusso di eventi di chiamata, è possibile creare un processo di Analisi di flusso che legge i dati dall'hub eventi.

1. Per creare un processo di Analisi di flusso, passare al portale di Azure  

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

   ![Creare un processo](media/stream-analytics-manage-job/create-a-job.png)   

4. Usare le opzioni predefinite per le restanti impostazioni, selezionare **Crea** e attendere il completamento della distribuzione.

## <a name="configure-job-input"></a>Configurare l'input del processo

Il passaggio successivo consiste nel definire un'origine di input da cui il processo può leggere i dati. Per questa esercitazione, come input si userà l'hub eventi creato nella sezione precedente. Seguire questa procedura per configurare l'input per il processo:

1. Dal portale di Azure aprire il riquadro **Tutte le risorse** e individuare il processo di Analisi di flusso ASATutorial.  

2. Nella sezione **Topologia processo** del riquadro del processo di Analisi di flusso selezionare l'opzione **Input**.  

3. Selezionare **+ Aggiungi input del flusso** (l'input di riferimento si riferisce ai dati di ricerca statici, che non verranno usati in questa esercitazione), **Hub eventi** e quindi compilare il riquadro con i valori seguenti:  

   |**Impostazione**  |**Valore consigliato**  |**Descrizione**  |
   |---------|---------|---------|
   |Alias di input     |  CallStream       |  Specificare un nome descrittivo per identificare l'input. L'alias di input può contenere solo caratteri alfanumerici, trattini e caratteri di sottolineatura e deve avere una lunghezza compresa tra 3 e 63 caratteri.       |
   |Sottoscrizione    |   \<Sottoscrizione in uso\>      |   Selezionare la sottoscrizione di Azure in cui è stato creato l'hub eventi. L'hub eventi può trovarsi nella stessa sottoscrizione del processo di Analisi di flusso o in una sottoscrizione diversa.       |
   |Spazio dei nomi dell'hub eventi    |  MyEventHubNS       |  Selezionare lo spazio dei nomi dell'hub eventi creato nella sezione precedente. Nell'elenco a discesa sono presenti tutti gli spazi dei nomi degli hub eventi disponibili nella sottoscrizione corrente.       |
   |Nome dell'hub eventi    |   MyEventHub      |  Selezionare l'hub eventi creato nella sezione precedente. Nell'elenco a discesa sono presenti tutti gli hub eventi disponibili nella sottoscrizione corrente.       |
   |Nome criteri hub eventi   |  MyPolicy       |  Selezionare i criteri di accesso condiviso dell'hub eventi creati nella sezione precedente. Nell'elenco a discesa sono presenti tutti i criteri degli hub eventi disponibili nella sottoscrizione corrente.       |

   ![Configurare l'input](media/stream-analytics-manage-job/configure-input.png) 

4. Usare le opzioni predefinite per le restanti impostazioni, selezionare **Salva** e attendere il completamento della distribuzione.

## <a name="configure-job-output"></a>Configurare l'output del processo 

L'ultimo passaggio consiste nel definire un sink di output per il processo, in cui scrivere i dati trasformati. Per questa esercitazione, i risultati vengono inviati a Power BI, da dove è possibile visualizzare i dati. Seguire questa procedura per configurare l'output per il processo:

1. Dal portale di Azure aprire il riquadro **Tutte le risorse** e individuare il processo di Analisi di flusso ASATutorial.  

2. Nella sezione **Topologia processo** del riquadro del processo di Analisi di flusso selezionare l'opzione **Output**.  

3. Selezionare **+ Aggiungi** > **Power BI**, compilare il modulo con i dettagli seguenti (è possibile fornire un nome descrittivo per identificare l'alias di output, il nome del set di dati e il nome della tabella, come illustrato nella tabella) e selezionare **Autorizza**:  

   |**Impostazione**  |**Valore consigliato**  |
   |---------|---------|---------|
   |Alias di output  |  MyPBIoutput  |
   |Nome del set di dati  |   ASAdataset  | 
   |Nome tabella |  ASATable  | 

   ![Configurare l'output](media/stream-analytics-manage-job/configure-output.png)  

4. Dopo aver selezionato **Autorizza**, verrà visualizzata una finestra popup in cui viene chiesto di fornire le credenziali per l'autenticazione dell'account di Power BI. Una volta completato il processo di autorizzazione, fare clic su **Salva** per salvare le impostazioni. 

## <a name="define-a-query-to-analyze-input-data"></a>Definire una query per analizzare i dati di input

Dopo aver configurato un processo di Analisi di flusso per leggere un flusso di dati in ingresso, il passaggio successivo consiste nel creare una trasformazione che analizza i dati in tempo reale. Per definire la query di trasformazione, si usa il [linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/dn834998.aspx). In questa esercitazione si definisce una query che rileva le chiamate fraudolente dai dati telefonici. 

Per questo esempio vengono considerate fraudolente le chiamate provenienti dallo stesso utente ma da località diverse che avvengono a cinque secondi di distanza una dall'altra. Ad esempio, lo stesso utente non può eseguire legittimamente una chiamata dagli Stati Uniti e dall'Australia nello stesso momento. Per definire la query di trasformazione per il processo di Analisi di flusso, seguire questa procedura:

1. Dal portale di Azure aprire il riquadro **Tutte le risorse** e aprire il processo di Analisi di flusso **ASATutorial** creato in precedenza.  

2. Nella sezione **Topologia processo** del riquadro del processo di Analisi di flusso selezionare l'opzione **Query**. La finestra popup elenca gli input e gli output configurati per il processo e consente di creare una query per trasformare il flusso di input.  

3. Sostituire quindi la query esistente nell'editor con i dati seguenti. La query esegue un self-join su un intervallo di 5 secondi di dati delle chiamate:

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

   Per controllare le chiamate fraudolente, è necessario eseguire un self-join sui dati di streaming in base al valore di `CallRecTime`. Cercare quindi i record delle chiamate in cui il valore `CallingIMSI` (numero di origine) è lo stesso, ma il valore `SwitchNum` (paese di origine) è diverso. Quando si usa un'operazione JOIN con i dati di streaming, il join deve garantire alcuni limiti per la distanza di separazione delle righe corrispondenti nel tempo. Poiché i dati di streaming non presentano una fine, i limiti di tempo per la relazione sono specificati all'interno della clausola ON del join usando la funzione [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics). 

   La query è simile a un normale join SQL, ad eccezione della funzione DATEDIFF. La funzione DATEDIFF usata in questa query è specifica di Analisi di flusso e deve essere inclusa nella clausola `ON...BETWEEN`.  

4. Fare clic su **Salva** per salvare la query.  

   ![Definire la query](media/stream-analytics-manage-job/define-query.png) 

## <a name="test-your-query"></a>Testare la query

È possibile testare una query dall'editor di query. A tale scopo, sono necessari dati di esempio. Per questa procedura dettagliata si estrarranno i dati di esempio dal flusso di telefonate in arrivo nell'hub eventi. Per testare la query, seguire questa procedura:

1. Assicurarsi che l'app TelcoGenerator sia in esecuzione e generi record delle telefonate.  

2. Nel riquadro **Query** selezionare i puntini di sospensione accanto all'input CallStream e quindi selezionare **Campiona dati da input**. Verrà aperto un riquadro che consente di specificare la quantità di dati di esempio da leggere dal flusso di input.  

   ![Dati di input di esempio](media/stream-analytics-manage-job/sample-input-data.png)  

3. Impostare **Minuti** su 3 e selezionare **OK**. Vengono campionati tre minuti di dati dal flusso di input e viene inviata una notifica quando i dati di esempio sono pronti. È possibile visualizzare lo stato del campionamento sulla barra di notifica. 

   I dati di esempio vengono archiviati temporaneamente e sono disponibili finché rimane aperta la finestra di query. Se si chiude la finestra di query, i dati di esempio verranno rimossi e sarà necessario creare un nuovo set di dati. In alternativa, è possibile ottenere un file con estensione json che contiene i dati di esempio da [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) e quindi caricare il file con estensione json da usare come dati di esempio per l'input CallStream.  

4. Selezionare **Test** per testare la query. Dovrebbero venire visualizzati i risultati di output come nello screenshot seguente:  

   ![Output del test](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>Avviare il processo e visualizzare l'output

1. Per avviare il processo, passare al riquadro **Panoramica** del processo e selezionare **Avvia**.  

2. Selezionare **Ora** come orario di avvio per l'output del processo e selezionare **Avvia**. Il processo viene avviato in pochi minuti ed è possibile visualizzarne lo stato sulla barra di notifica.  

3. Dopo il completamento del processo, passare a [Powerbi.com](https://powerbi.com/) e accedere con l'account aziendale o dell'istituto di istruzione. Se la query del processo di Analisi di flusso produce risultati, si noterà che il set di dati è già creato. Passare alla scheda **Set di dati**, dove è presente un set di dati denominato "ASAdataset".  

4. Nell'area di lavoro selezionare **+ Crea**. Creare un nuovo dashboard e denominarlo Fraudulent Calls. Si aggiungeranno due riquadri a questo dashboard, uno usato per visualizzare il numero di chiamate fraudolente in una determinata istanza e l'altro con una visualizzazione di grafico a linee.  

5. Nella parte superiore della finestra selezionare **Aggiungi riquadro** > selezionare **Dati in streaming personalizzati** > Avanti > scegliere **ASAdataset** > per Tipo di visualizzazione selezionare **Scheda** > e per Campi impostare **fraudulentcalls**. Selezionare **Avanti** > immettere un nome per il riquadro e selezionare **Applica**.  

   ![Creare i riquadri](media/stream-analytics-manage-job/create-tiles.png)

6. Ripetere il passaggio 4 con le opzioni seguenti:
   * Per Tipo di visualizzazione selezionare Grafico a linee.  
   * Aggiungere un asse e selezionare **windowend**.  
   * Aggiungere un valore e selezionare **fraudulentcalls**.  
   * Per **Intervallo di tempo da visualizzare**  selezionare gli ultimi 10 minuti.  

7. Dopo l'aggiunta di entrambi i riquadri, il dashboard è simile a quello nello screenshot seguente. Si noterà che, se l'applicazione mittente dell'hub eventi e l'applicazione di Analisi di flusso sono in esecuzione, il dashboard di Power BI si aggiorna periodicamente man mano che arrivano nuovi dati.  

   ![Risultati di Power BI](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>Incorporamento del dashboard di Power BI in un'applicazione Web

Per questa parte dell'esercitazione, si userà un'applicazione Web [ASP.NET](http://asp.net/) di esempio creata dal team di Power BI per incorporare il dashboard. Per altre informazioni sull'incorporamento di dashboard, vedere l'articolo [Incorporamento con Power BI](https://docs.microsoft.com/power-bi/developer/embedding).

In questa esercitazione verranno seguiti i passaggi per l'utente proprietario dell'applicazione dati. Per configurare l'applicazione, passare al repository GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) e seguire le istruzioni nella sezione **User Owns Data** (Utente proprietario dei dati) (usare gli URL di reindirizzamento e della home page nella sottosezione **integrate-dashboard-web-app**). Poiché si sta usando l'esempio relativo al dashboard, usare il codice di esempio integrate-dashboard-web-app disponibile nel [repository GitHub](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app).
Una volta che l'applicazione è in esecuzione nel browser, seguire questa procedura per incorporare il dashboard creato in precedenza nella pagina Web:

1. Selezionare **Accedi a Power BI**, per concedere all'applicazione l'accesso ai dashboard nell'account di PowerBI.  

2. Fare clic sul pulsante **Get Dashboards** (Ottieni dashboard), per visualizzare i dashboard dell'account in una tabella. Individuare il nome del dashboard creato in precedenza (powerbi-embedded-dashboard) e copiare il valore di **EmbedUrl** corrispondente.  

3. Infine, incollare **EmbedUrl** nel campo di testo corrispondente e selezionare **Embed Dashboard** (Incorpora dashboard). È ora possibile visualizzare lo stesso dashboard incorporato in un'applicazione Web.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato un processo di Analisi di flusso di esempio, sono stati analizzati i dati in ingresso e i risultati sono stati visualizzati in un dashboard di Power BI. Per altre informazioni sui processi di Analisi di flusso, continuare con l'esercitazione successiva:

> [!div class="nextstepaction"]
> [Eseguire Funzioni di Azure in processi di Analisi di flusso](stream-analytics-with-azure-functions.md)
