---
title: Usare il feed di modifiche di Azure Cosmos DB per visualizzare l'analisi dei dati in tempo reale | Microsoft Docs
description: Questo articolo descrive come il feed di modifiche può essere usato da una società di vendita al dettaglio per comprendere i criteri definiti dall'utente, eseguire la visualizzazione e l'analisi dei dati in tempo reale.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 08/12/2018
ms.author: sngun
ms.openlocfilehash: 241cd2fddf31373ed9ca208efb0612dd4332131c
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740978"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>Usare il feed di modifiche di Azure Cosmos DB per visualizzare l'analisi dei dati in tempo reale

Il feed di modifiche di Azure Cosmos DB è un meccanismo per ottenere un feed continuo e incrementale di record da un contenitore di Azure Cosmos DB mentre i record vengono creati o modificati. Il supporto del feed di modifiche rimane in ascolto del contenitore per qualsiasi modifica. Restituisce quindi l'elenco di documenti cambiati nell'ordine in cui sono stati modificati. Per altre informazioni sul feed di modifiche, vedere l'articolo sull'[uso di feed di modifiche](change-feed.md). 

Questo articolo descrive come il feed di modifiche può essere usato da una società di e-commerce al dettaglio per comprendere i criteri definiti dall'utente ed eseguire la visualizzazione e l'analisi dei dati in tempo reale. Verranno analizzati eventi, ad esempio un utente che visualizza un elemento, aggiunge un elemento al carrello o acquista un elemento. Quando si verifica uno di questi eventi, viene creato un nuovo record e il feed di modifiche registra quel record. Il feed di modifiche quindi attiva una serie di passaggi che consentono la visualizzazione delle metriche che analizzano le prestazioni aziendali e l'attività risultante. Le metriche di esempio che è possibile visualizzare includono i ricavi, i visitatori univoci del sito, gli elementi più diffusi e il prezzo medio degli elementi che vengono visualizzati rispetto a quelli aggiunti al carrello e a quelli acquistati. Queste metriche di esempio consentono a una società di e-commerce di valutare la popolarità del sito, sviluppare le strategie relative a pubblicità e prezzi e prendere decisioni riguardanti gli inventari in cui investire.

Se l'utente è interessato a guardare un video che illustra la soluzione prima di iniziare, vedere il video seguente:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Componenti della soluzione
Il diagramma seguente rappresenta il flusso di dati e i componenti coinvolti nella soluzione:

![Immagine del progetto](./media/changefeed-ecommerce-solution/project-visual.png)
 
1. **Generazione di dati:** il simulatore di dati viene usato per generare i dati delle vendite al dettaglio che rappresentano gli eventi, ad esempio un utente che visualizza un elemento, aggiunge un elemento al carrello e acquista un elemento. È possibile generare grandi set di dati di esempio tramite il generatore di dati. I dati di esempio generati contengono i documenti nel formato seguente:
   
   ```json
   {      
     "CartID": 2486,
     "Action": "Viewed",
     "Item": "Women's Denim Jacket",
     "Price": 31.99
   }
   ```

2. **Cosmos DB:** i dati generati sono archiviati in una raccolta Azure Cosmos DB.  

3. **Feed di modifiche:** il feed di modifiche sarà in ascolto per le modifiche alla raccolta Azure Cosmos DB. Ogni volta che viene aggiunto un nuovo documento nella raccolta (ovvero quando si verifica un evento ad esempio un utente che visualizza un elemento, aggiunge un elemento al carrello o acquista un elemento), il feed di modifiche attiverà una [funzione di Azure](../azure-functions/functions-overview.md).  

4. **Funzione di Azure:** la funzione di Azure elabora i nuovi dati e li invia a un [Hub eventi di Azure](../event-hubs/event-hubs-about.md).  

5. **Hub eventi:** Hub eventi di Azure archivia questi eventi e li invia ad [Analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md) per eseguire un'altra analisi.  

6. **Analisi di flusso di Azure:** Analisi di flusso di Azure definisce le query per elaborare gli eventi ed eseguire analisi in tempo reale dei dati. Questi dati sono quindi inviati a [Microsoft Power BI](https://docs.microsoft.com/power-bi/desktop-what-is-desktop).  

7. **Power BI:** Power BI viene usato per visualizzare i dati inviati da Analisi di flusso di Azure. È possibile creare una dashboard per visualizzare la modalità con cui le metriche si modificano in tempo reale.  

## <a name="prerequisites"></a>Prerequisiti

* Microsoft .NET Framework 4.7.1 o versione successiva

* Microsoft .NET Core 2.1 (o versione successiva)

* Visual Studio con lo sviluppo della piattaforma UWP (Universal Windows Platform), sviluppo per desktop .NET e carichi di lavoro di sviluppo ASP.NET e Web

* Sottoscrizione di Microsoft Azure

* Account di Microsoft Power BI

* Scaricare l'[esercitazione del feed di modifiche di Azure Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) da GitHub. 

## <a name="create-azure-resources"></a>Creare le risorse di Azure 

Creare le risorse di Azure richieste dalla soluzione: Azure Cosmos DB, Account di archiviazione, Hub eventi, Analisi di flusso. Sarà possibile distribuire queste risorse tramite un modello di Azure Resource Manager. Usare la procedura seguente per distribuire queste risorse: 

1. Impostare i criteri di esecuzione di Windows PowerShell su **Senza restrizioni**. A tal fine, aprire **Windows PowerShell come amministratore** ed eseguire i comandi seguenti:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Dal repository di GitHub scaricato nel passaggio precedente, andare alla cartella **Azure Resource Manager** e aprire il file **parameters.json**.  

3. Specificare i valori per i parametri cosmosdbaccount_name, eventhubnamespace_name e storageaccount_name come indicato nel file **parameters.json**. È necessario usare nomi che consentono di accedere alle risorse in un secondo momento.  

4. Da **Windows PowerShell**, passare alla cartella **Azure Resource Manager** ed eseguire il comando seguente:

   ```powershell
   .\deploy.ps1
   ```
5. Quando richiesto, immettere l'**ID sottoscrizione**, **changefeedlab** per il nome del gruppo di risorse e **run1** per il nome della distribuzione. Una volta che le risorse di iniziano la distribuzione, potrebbero occorrere fino a 10 minuti per il completamento.

## <a name="create-a-database-and-the-collection"></a>Creare un database e una raccolta

Ora sarà possibile creare una raccolta per memorizzare gli eventi del sito di e-commerce. Quando un utente visualizza un elemento, aggiunge un elemento al carrello o acquista un articolo, la raccolta riceverà un record che include l'azione ("visualizzato", "aggiunto" o "acquistato"), il nome dell'elemento, il prezzo dell'elemento e il numero di ID del carrello dell'utente coinvolti.

1. Passare al [portale di Azure](http://portal.azure.com/) e trovare l'**account Azure Cosmos DB** creato dalla distribuzione del modello.  

2. Dal riquadro **Esplora dati**, selezionare **Nuova raccolta** e compilare il modulo con i dettagli seguenti:  

   * Per il campo **id database**, selezionare **Crea nuovo**, quindi inserire **changefeedlabdatabase**. Lasciare deselezionata la casella **Provisioning della velocità effettiva del database**.  
   * Per il campo id della **Raccolta**, inserire **changefeedlabcollection**.  
   * Per **Capacità di archiviazione**, selezionare **Illimitata**.  
   * Per la **chiave di partizione** inserire **/elemento**. Si tratta di un campo con distinzione tra maiuscole/minuscole, pertanto assicurarsi di compilarlo correttamente.  
   * Per **Velocità effettiva** inserire **10000**.  
   * Selezionare il pulsante **OK**.  

3. Creare quindi un'altra raccolta denominata **lease** per l'elaborazione del feed di modifiche. La raccolta di lease coordina l'elaborazione del feed di modifiche in più processi di lavoro. Una raccolta separata viene usata per archiviare i lease con un lease per partizione.  

4.  Ritornare al riquadro **Esplora dati**, selezionare **Nuova raccolta** e compilare il modulo con i dettagli seguenti:

   * Per il campo **id database**, selezionare **Usa esistente**, quindi inserire **changefeedlabdatabase**.  
   * Per il campo **id raccolta**, inserire **lease**.  
   * Per **Capacità di archiviazione**, selezionare **Fissa**.  
   * Lasciare il campo della **velocità effettiva** impostato sul valore predefinito.  
   * Selezionare il pulsante **OK**.

## <a name="get-the-connection-string-and-keys"></a>Ottiene la stringa di connessione e le chiavi

### <a name="get-the-azure-cosmos-db-connection-string"></a>Ottenere la stringa di connessione di Azure Cosmos DB

1. Passare al [portale di Azure](http://portal.azure.com/) e trovare l'**account Azure Cosmos DB** creato dalla distribuzione del modello.  

2. Passare al riquadro delle **Chiavi**, copiare la STRINGA DI CONNESSIONE PRIMARIA e copiarla in un blocco note o un altro documento di cui si disporrà durante l'intera esercitazione. È consigliabile chiamarla **Stringa di connessione di Cosmos DB**. È necessario copiare la stringa nel codice in un secondo momento, quindi prenderne nota e ricordare dove è archiviata.

### <a name="get-the-storage-account-key-and-connection-string"></a>Ottenere la chiave della stringa di connessione e la stringa di connessione

Gli account di Archiviazione di Azure consentono agli utenti di archiviare i dati. In questa esercitazione, si userà un account di archiviazione per archiviare i dati usati dalla funzione di Azure. La funzione di Azure viene attivata quando viene apportata qualsiasi modifica alla raccolta.

1. Tornare al gruppo di risorse e aprire l'account di archiviazione creato in precedenza  

2. Selezionare **Chiavi di accesso** dal menu sul lato sinistro.  

3. Copiare i valori in **chiave 1** in un blocco note o un altro documento di cui si disporrà durante l'intera esercitazione. È consigliabile chiamare la **chiave** come **Chiave di archiviazione** e la **stringa di connessione** come **Stringa di connessione di archiviazione**. È necessario copiare le stringhe nel codice in un secondo momento, quindi prenderne nota e ricordare dove sono archiviate.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Ottenere la stringa di connessione dello spazio dei nomi dell'hub eventi

Hub eventi di Azure riceve i dati dell'evento, li archivia, li elabora e inoltra i dati. In questa esercitazione, Hub eventi di Azure riceverà un documento ogni volta che si verifica un nuovo evento (vale a dire quando un elemento viene visualizzato da un utente, aggiunto al carrello dell'utente o acquistato da un utente) e quindi inoltrerà quel documento ad Analisi di flusso di Azure.

1. Tornare al gruppo di risorse e aprire lo **spazio dei nomi di Hub eventi** creato e assegnarvi un nome nell'introduzione all'esercitazione.  

2. Selezionare **Criteri di accesso condivisi** dal menu sul lato sinistro.  

3. Selezionare **RootManageSharedAccessKey**. Copiare la **chiave primaria della stringa di connessione** in un blocco note o un altro documento di cui si disporrà durante l'intera esercitazione. Assegnare alla stringa di connessione lo **spazio dei nomi di Hub eventi**. È necessario copiare la stringa nel codice in un secondo momento, quindi prenderne nota e ricordare dove è archiviata.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Impostare una funzione di Azure per leggere il feed di modifiche

Quando viene creato un nuovo documento o un documento corrente viene modificato in una raccolta di Cosmos DB, tale il feed di modifiche aggiunge automaticamente il documento modificato alla sua cronologia delle modifiche alla raccolta. Ora è necessario compilare ed eseguire una funzione di Azure che elabori i feed di modifiche. Quando un documento viene creato o modificato nella raccolta creata, la funzione di Azure verrà attivata dal feed di modifiche. La funzione di Azure invierà quindi il documento modificato per l'Hub eventi.

1. Tornare al repository clonato nel dispositivo.  

2. Fare clic col tasto destro sul file denominato **ChangeFeedLabSolution.sln** e selezionare **Apri con Visual Studio**.  

3. Passare a **local.settings.json** in Visual Studio. Usare quindi i valori annotati in precedenza per compilare i campi vuoti.  

4. Passare a **ChangeFeedProcessor.cs**. Nei parametri per la funzione di **esecuzione**, eseguire le azioni seguenti:  

   * Sostituire il testo **NOME RACCOLTA QUI** con il nome della raccolta. Se si sono seguite le istruzioni precedenti, il nome della raccolta sarà changefeedlabcollection.  
   * Sostituire il testo **NOME RACCOLTA LEASE QUI** con il nome del lease. Se si sono seguite le istruzioni precedenti, il nome della raccolta lease sarà **lease**.  
   * Nella parte superiore di Visual Studio, assicurarsi che la casella di progetto di avvio a sinistra della freccia verde presenti **ChangeFeedFunction**.  
   * Selezionare **Avvia** nella parte superiore della pagina per eseguire il programma  
   * È possibile confermare che la funzione è in esecuzione quando l'app console riporta"Host del processo avviato".

## <a name="insert-data-into-azure-cosmos-db"></a>Inserire dati in Azure Cosmos DB 

Per vedere come feed di modifiche elabora nuove azioni in un sito di e-commerce, è necessario simulare i dati che rappresentano gli utenti che visualizzano gli elementi dal catalogo dei prodotti, aggiungono tali elementi al carrello e acquistano gli elementi nel carrello. Questi dati sono arbitrari e hanno lo scopo di replicare l'aspetto dei dati in un sito di e-commerce.

1. Passare al repository in Esplora file e fare doppio clic su **ChangeFeedFunction.sln** per aprirlo nuovamente in una nuova finestra di Visual Studio.  

2. Andare al file **App.config**. All'interno del blocco <appSettings>, aggiungere la **CHIAVE PRIMARIA** univoca e l'URI dell'account di Azure Cosmos DB recuperati in precedenza.  

3. Aggiungere i nomi della **raccolta** e del **database**. (Questi nomi devono essere **changefeedlabcollection** e **changefeedlabdatabase** a meno che non si scelga di assegnare un nome diverso.)

   ![Aggiornare le stringhe di connessione](./media/changefeed-ecommerce-solution/update-connection-string.png)
 
4. Salvare le modifiche su tutti i file modificati.  

5. Nella parte superiore di Visual Studio, assicurarsi che la casella di **progetto di avvio** a sinistra della freccia verde presenti **DataGenerator**. Selezionare quindi **Avvia** nella parte superiore della pagina per eseguire il programma.  
 
6. Attendere l'esecuzione del programma. Le stelle indicano che i dati sono in arrivo! Mantenere il programma in esecuzione, è importante che vengono raccolti molti dati.  

7. Se si passa al [portale di Azure](http://portal.azure.com/), poi all'account di Cosmos DB all'interno del gruppo di risorse, quindi a **Esplora dati**, verranno visualizzati i dati in ordine casuale importati in **changefeedlabcollection** .
 
   ![Dati generati nel portale](./media/changefeed-ecommerce-solution/data-generated-in-portal.png)

## <a name="set-up-a-stream-analytics-job"></a>Configurazione di un processo di Analisi di flusso

Analisi di flusso di Azure è un servizio cloud completamente gestito per l'elaborazione dei dati in streaming in tempo reale. In questa esercitazione si userà l'analisi del flusso per elaborare nuovi eventi da Hub eventi (ad esempio quando un elemento è visualizzato, aggiunto al carrello o acquistato), per incorporare tali eventi nell'analisi dei dati in tempo reale e per inviarli a Power BI per la visualizzazione.

1. Dal [portale di Azure](http://portal.azure.com/), passare al gruppo di risorse, quindi a **streamjob1** (il processo di analisi del flusso creato nell'introduzione all'esercitazione).  

2. Selezionare **Input** come illustrato di seguito.  

   ![Creare input](./media/changefeed-ecommerce-solution/create-input.png)

3. Selezionare **+ Aggiungi input del flusso**. Quindi selezionare **Hub eventi** dal menu a discesa.  

4. Compilare il nuovo modulo di input con i dettagli seguenti:

   * Nel campo alias **Input**, inserire **Input**.  
   * Selezionare l'opzione per **Selezionare l'Hub eventi dalle sottoscrizioni correnti**.  
   * Impostare il campo **Sottoscrizione** nella sottoscrizione.  
   * Nello **spazio dei nomi di Hub eventi** immettere il nome nello spazio dei nomi di Hub eventi creati durante l'introduzione all'esercitazione.  
   * Nel campo **nome dell'Hub eventi** selezionare l'opzione **Usa esistente** e scegliere **event-hub1** dal menu a discesa.  
   * Lasciare il campo del nome **Criterio dell'Hub eventi** impostato sul valore predefinito.  
   * Lasciare **Formato di serializzazione eventi** su **JSON**.  
   * Lasciare il **campo Codifica** impostato su **UTF-8**.  
   * Lasciare il campo **Tipo di compressione eventi** impostato su **Nessuno**.  
   * Fare clic sul pulsante **Salva**.

5. Tornare alla pagina del processo di analisi del flusso e selezionare **Output**.  

6. Selezionare **+ Aggiungi**. Quindi selezionare **Power BI** dal menu a discesa.  

7. Per creare un nuovo output di Power BI per visualizzare il prezzo medio, eseguire le azioni seguenti:

   * Nel campo **Alias di output** inserire **averagePriceOutput**.  
   * Lasciare il campo **Area di lavoro di gruppo** impostato su **Autorizza connessione per caricare le aree di lavoro**.  
   * Nel campo **Nome del set di dati** inserire **averagePrice**.  
   * Nel campo **Nome della tabella** inserire **averagePrice**.  
   * Selezionare il pulsante **Autorizza** e quindi seguire le istruzioni per autorizzare la connessione a Power BI.  
   * Fare clic sul pulsante **Salva**.  

8. Tornare quindi a **streamjob1** e selezionare **Modifica query**.

   ![Edit query](./media/changefeed-ecommerce-solution/edit-query.png)
 
9. Incollare la seguente query nella relativa finestra. La query **PREZZO MEDIO** calcola il prezzo medio di tutti gli elementi visualizzati da utenti, il prezzo medio di tutti gli elementi che vengono aggiunti ai carrelli degli utenti e il prezzo medio di tutti gli elementi che vengono acquistati dagli utenti. Questa metrica può aiutare le società di e-commerce a decidere a quali prezzi vendere gli elementi e in quale inventario investire. Ad esempio, se il prezzo medio degli elementi visualizzati è molto superiore al prezzo medio degli elementi acquistati, una società potrebbe scegliere di aggiungere elementi meno costosi per l'inventario.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Selezionare quindi **Salva** nell'angolo superiore sinistro.  

11. Tornare ora a **streamjob1** e selezionare il pulsante **Avvio** nella parte superiore della pagina. Analisi di flusso di Azure può richiedere alcuni minuti per l'avvio, ma alla fine si visualizzerà una modifica da "Iniziale" a "In corso".

## <a name="connect-to-power-bi"></a>Connettersi a Power BI

Power BI è una suite di strumenti di analisi business che consente di analizzare i dati e condividere informazioni dettagliate. È un ottimo esempio di come è possibile visualizzare in modo strategico i dati analizzati.

1. Accedere a Power BI e passare all'**Area di lavoro personale** aprendo il menu sul lato sinistro della pagina.  

2. Selezionare **+ Crea** nell'angolo superiore destro e quindi selezionare **Dashboard** per creare una dashboard.  

3. Selezionare **+ Aggiungi riquadro** nell'angolo superiore destro.  

4. Selezionare **Dati in streaming personalizzati** e quindi selezionare il pulsante **Avanti**.  
 
5. Selezionare **averagePrice** da **SET DI DATI** e quindi selezionare **Avanti**.  

6. Nel campo **Tipo di visualizzazione**, scegliere **Grafico a barre raggruppate** dal menu a discesa. In **Asse**, aggiungere un'azione. Lasciare in sospeso **Legenda** senza aggiungere alcun dato. Quindi, nella sezione successiva chiamata **Valore**, aggiungere **avg**. Selezionare **Avanti**, quindi dare un titolo al grafico e selezionare **Applica**. Dovrebbe essere possibile visualizzare un nuovo grafico nella dashboard!  

7. A questo punto, se si desidera visualizzare altre metriche, è possibile tornare alla schermata **streamjob1** e creare altri tre output con i campi seguenti.

   a. **Alias di output:** incomingRevenueOutput, nome del set di dati: incomingRevenue, nome della tabella: incomingRevenue  
   b. **Alias di output:** top5Output, nome del set di dati: top5, nome della tabella: top5  
   c. **Alias di output:** uniqueVisitorCountOutput, nome del set di dati: uniqueVisitorCount, nome della tabella: uniqueVisitorCount

   Selezionare quindi **Modifica query** e incollare le query seguenti **sopra** quella già scritta.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   La query TOP 5 calcola i primi 5 elementi, classificati in base al numero di volte in cui sono stati acquistati. Questa metrica può consentire alle società di e-commerce di valutare quali elementi sono i più diffusi e come possono influire sulla pubblicità della società, sulle informazioni sui prezzi e sulle decisioni in merito all'inventario.

   La query RICAVI calcola i ricavi sommando i prezzi di tutti gli elementi acquistati ogni minuto. Questa metrica può aiutare le società di e-commerce a valutare le proprie prestazioni finanziarie e a comprendere quali ore del giorno registrano la maggior parte dei ricavi. Ciò può influire sulla strategia aziendale complessiva, in particolare sul marketing.

   La query di VISITATORI UNIVOCI calcola il numero di visitatori univoci che si trovano nel sito ogni 5 secondi rilvando l'ID del carrello univoco. Questa metrica può aiutare le società di e-commerce a valutare le attività del sito e creare strategie per acquisire più clienti.

8. È ora possibile aggiungere riquadri per anche questi set di dati.

   * Per Top 5, è opportuno creare un grafico a colonne raggruppate con gli elementi a rappresentare l'asse e il numero come il valore.  
   * Per i ricavi, è opportuno creare un grafico a linee con il tempo a rappresentare l'asse e la somma dei prezzi come valore. L'intervallo di tempo per la visualizzazione deve essere il più ampio possibile al fine di fornire quante più informazioni possibili.  
   * Per i visitatori univoci, è opportuno eseguire una visualizzazione della scheda con il numero di visitatori univoci come valore.

   Ecco come appare un dashboard di esempio con questi grafici:

   ![visualizzazioni](./media/changefeed-ecommerce-solution/visualizations.png)

## <a name="optional-visualize-with-an-e-commerce-site"></a>Facoltativo: visualizzazione con un sito di e-commerce

A questo punto, sarà possibile osservare come è possibile usare il nuovo strumento di analisi dei dati per la connessione con un vero sito di e-commerce. Per creare il sito di e-commerce, usare un database Azure Cosmos DB per archiviare l'elenco delle categorie di prodotti (donne, uomini, unisex), il catalogo dei prodotti e un elenco degli elementi più diffusi.

1. Tornare al [portale di Azure](http://portal.azure.com/), quindi all'**account Cosmos DB** e in seguito a **Esplora dati**.  

   Aggiungere due raccolte in **prodotti** - **changefeedlabdatabase** e **categorie** con capacità di archiviazione fissa.

   Aggiungere un'altra raccolta in **changefeedlabdatabase** chiamata **topItems** con capacità di archiviazione **Illimitata**. Scrivere **/elemento** come chiave di partizione.

2. Selezionare la raccolta **topItems** e in **Scalabilità e Impostazioni** impostare la **Durata (TTL)** su **30 secondi** in modo tale che topItems si aggiorni ogni 30 secondi.

   ![Durata (TTL)](./media/changefeed-ecommerce-solution/time-to-live.png)

3. Per popolare la raccolta **topItems** con gli articoli più frequentemente acquistati, ritornare a **streamjob1** e aggiungere un nuovo **Output**. Selezionare **Cosmos DB**.

4. Compilare i campi obbligatori nel modo seguente.

   ![Output di Cosmos](./media/changefeed-ecommerce-solution/cosmos-output.png)
 
5. Se si aggiungono le query TOP 5 facoltativa nella parte precedente dell'esercitazione, passare alla parte 5a. In caso contrario, andare alla parte 5b.

   5a. In **streamjob1**, selezionare **Modifica query** e incollare la query seguente nell'editor delle query di Analisi di flusso di Azure sotto alla query TOP 5, ma sopra rispetto alle altre query.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. In **streamjob1**, selezionare **Modifica query** e incollare la query seguente nell'editor delle query di Analisi di flusso di Azure sopra rispetto alle altre query.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Aprire **EcommerceWebApp.sln** e passare al file **Web.config** in **Esplora soluzioni**.  

7. All'interno del blocco `<appSettings>`, aggiungere l'**URI** e la **CHIAVE PRIMARIA** salvati in precedenza in corrispondenza del testo **URI qui** e **chiave primaria qui**. Quindi aggiungere il **nome del database** e il **nome della raccolta** come indicato. (Questi nomi devono essere **changefeedlabdatabase** e **changefeedlabcollection** a meno che non si scelga di assegnare un nome diverso.)

   Compilare il **nome della raccolta di prodotti**, il **nome della raccolta di categorie** e il **nome della raccolta di elementi principali** come indicato. (Questi nomi devono essere **prodotti, categorie e topItems** a meno che non si abbia scelto di assegnare i propri in modo differente.)  

8. Individuare e aprire la **Cartella di estrazione** all'interno di **EcommerceWebApp.sln.** Quindi aprire il file **Web.config** all'interno della cartella.  

9. All'interno del blocco `<appSettings>`, aggiungere l'**URI** e la **CHIAVE PRIMARIA** che sono stati salvati in precedenza dove indicato. Quindi aggiungere il **nome del database** e il **nome della raccolta** come indicato. (Questi nomi devono essere **changefeedlabdatabase** e **changefeedlabcollection** a meno che non si scelga di assegnare un nome diverso.)  

10. Selezionare **Avvia** nella parte superiore della pagina per eseguire il programma.  

11. A questo punto è possibile visitare e scoprire il sito di e-commerce. Quando si visualizza un elemento, si aggiunge un elemento al carrello, si modifica la quantità di elementi al carrello o si acquista un elemento, questi eventi vengono trasmessi tramite il feed di modifiche del database Cosmos DB a Hub eventi, ASA e quindi a Power BI. È consigliabile continuare a eseguire DataGenerator per generare dati del traffico Web significativi e fornire un set realistico di "prodotti più visualizzati" nel sito di e-commerce.

## <a name="delete-the-resources"></a>Eliminare le risorse

Per eliminare le risorse create durante questa esercitazione, passare al gruppo di risorse sul [portale di Azure](http://portal.azure.com/), quindi selezionare **Elimina gruppo di risorse** dal menu nella parte superiore della pagina e seguire le istruzioni fornite.

## <a name="next-steps"></a>Passaggi successivi 
  
* Per altre informazioni sul feed di modifiche, vedere l'articolo [Uso del supporto del feed di modifiche in Azure Cosmos DB](change-feed.md) 
* [Soluzione di notifica del feed di modifiche](change-feed-hl7-fhir-logic-apps.md) per l'organizzazione sanitaria usando Azure Cosmos DB.
