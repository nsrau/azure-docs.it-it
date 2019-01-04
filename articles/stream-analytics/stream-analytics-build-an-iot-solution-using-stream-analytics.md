---
title: Creare una soluzione IoT tramite Analisi di flusso di Azure
description: Esercitazione introduttiva per la soluzione IoT di Analisi di flusso relativa allo scenario di un casello
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 4817efcb5cfa5f8692f2b7e5c65d411bc0d21942
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317390"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Compilare una soluzione IoT con Analisi di flusso

## <a name="introduction"></a>Introduzione
In questa soluzione viene descritto come usare Analisi di flusso di Azure per ottenere informazioni approfondite in tempo reale dai dati. Gli sviluppatori possono combinare facilmente flussi di dati, come clickstream, log ed eventi generati da dispositivi, con record cronologici o dati di riferimento per ottenere informazioni aziendali approfondite. Analisi di flusso di Azure è un servizio di calcolo dei flussi in tempo reale completamente gestito, ospitato in Microsoft Azure, con caratteristiche di resilienza predefinita, bassa latenza e scalabilità che consentono la piena operatività in pochi minuti.

Dopo aver completato questa soluzione, sarà possibile:

* Acquisire familiarità con il portale di Analisi di flusso di Azure.
* Configurare e distribuire un processo di flusso.
* Articolare problemi reali e risolverli con il linguaggio di query di Analisi di flusso.
* Sviluppare in tutta sicurezza soluzioni di streaming per i clienti usando Analisi di flusso.
* Usare l'esperienza di monitoraggio e registrazione per risolvere i problemi.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa soluzione, è necessario soddisfare i prerequisiti seguenti:
* Una [sottoscrizione di Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Introduzione dello scenario: "Ciao, casello!"
Un casello rappresenta una situazione piuttosto comune. Se ne incontrano sulle autostrade e su molti ponti e tunnel in tutto il mondo. Ogni barriera è costituita da più caselli. In quelli manuali ci si ferma per pagare il pedaggio a un addetto. In quelli automatizzati al passaggio attraverso il casello un sensore posto al di sopra di esso analizza una scheda RFID posizionata sul parabrezza del veicolo. È semplice visualizzare il passaggio dei veicoli nei caselli come un flusso di eventi, sui quali è possibile eseguire alcune operazioni interessanti.

![Immagine di automobili ai caselli](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth .jpg)

## <a name="incoming-data"></a>Dati di ingresso
Questa soluzione usa due flussi di dati. Il primo flusso viene prodotto da sensori installati all'entrata e all'uscita del casello. Il secondo flusso è un set di dati di ricerca statico contenente dati di registrazione dei veicoli.

### <a name="entry-data-stream"></a>Flusso di dati di ingresso
Il flusso di dati di ingresso contiene informazioni sulle automobili che entrano nel casello. Gli eventi dei dati di uscita vengono trasmessi live in una coda di Hub eventi da un'app Web inclusa nell'app di esempio.

| ID casello | Tempo ingresso | Targa | Stato | Assicurarsi | Modello | Tipo veicolo | Peso veicolo | Casello | Tag |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Ecco una breve descrizione delle colonne:

| Colonna | DESCRIZIONE |
| --- | --- |
| ID casello |ID casello che identifica in modo univoco un casello |
| Tempo ingresso |Data e ora (UTC) di ingresso del veicolo nel casello |
| Targa |Numero di targa del veicolo |
| Stato |Stato degli Stati Uniti |
| Assicurarsi |Il produttore dell'automobile |
| Modello |Numero di modello dell'automobile |
| Tipo veicolo |1 per autovetture o 2 per veicoli commerciali |
| Peso veicolo |Peso del veicolo in tonnellate, 0 per veicoli passeggeri |
| Casello |Il valore del pedaggio in USD |
| Tag |e-Tag sull'automobile che permette di automatizzare il pagamento, lasciato vuoto quando il pagamento viene effettuato manualmente |

### <a name="exit-data-stream"></a>Flusso di dati di uscita
Il flusso di dati di uscita contiene informazioni sulle automobili che escono dal casello. Gli eventi dei dati di uscita vengono trasmessi live in una coda di Hub eventi da un'app Web inclusa nell'app di esempio.

| **ID casello** | **Tempo ingresso** | **Targa** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Ecco una breve descrizione delle colonne:

| Colonna | DESCRIZIONE |
| --- | --- |
| ID casello |ID casello che identifica in modo univoco un casello |
| Tempo ingresso |Data e ora (UTC) di uscita del veicolo dal casello |
| Targa |Numero di targa del veicolo |

### <a name="commercial-vehicle-registration-data"></a>Dati di registrazione di veicoli commerciali
Questa soluzione usa uno snapshot statico di un database di registrazione di veicoli commerciali. I dati vengono salvati come file JSON nell'archiviazione BLOB di Azure e sono inclusi nell'esempio.

| Targa | ID registrazione | Scaduto |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Ecco una breve descrizione delle colonne:

| Colonna | DESCRIZIONE |
| --- | --- |
| Targa |Numero di targa del veicolo |
| ID registrazione |ID registrazione del veicolo |
| Scaduto |Stato di registrazione del veicolo: 0 se la registrazione del veicolo è attiva, 1 se la registrazione è scaduta |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Configurare l'ambiente per Analisi di flusso di Azure
Per completare la soluzione, è necessaria una sottoscrizione di Microsoft Azure. Se non si ha un account Azure, è possibile [richiedere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

Per poter usare al meglio il credito Azure gratuito, seguire la procedura riportata nella sezione "Eseguire la pulizia dell'account Azure" alla fine di questo articolo.

## <a name="deploy-the-sample"></a>Distribuire l'esempio
Diverse risorse possono essere facilmente distribuite in un gruppo di risorse in pochi clic. La definizione della soluzione è ospitata nel repository GitHub all'indirizzo [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Distribuire il modello TollApp nel portale di Azure
1. Per distribuire l'ambiente TollApp in Azure, usare questo collegamento per [distribuire il modello TollApp di Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Se richiesto, accedere al portale di Azure.

3. Scegliere la sottoscrizione usata per la fatturazione delle diverse risorse.

4. Specificare un nuovo gruppo di risorse con un nome univoco, ad esempio `MyTollBooth`.

5. Selezionare una località di Azure.

6. Specificare un valore come numero di secondi in **Intervallo**. Questo valore viene usato nell'app Web di esempio per specificare come inviare i dati in Hub eventi.

7. Fare clic su **Seleziona** per accettare i termini e le condizioni.

8. Selezionare **Aggiungi al dashboard** in modo da poter individuare facilmente le risorse in seguito.

9. Selezionare **Acquista** per distribuire il modello di esempio.

10. Dopo alcuni istanti, viene visualizzata la notifica **La distribuzione è riuscita**, che conferma l'operazione.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Esaminare le risorse di Analisi di flusso di Azure per TollApp
1. Accedere al Portale di Azure

2. Individuare il gruppo di risorse denominato nella sezione precedente.

3. Verificare che le risorse seguenti siano elencate nel gruppo di risorse:
   - Un account Cosmos DB
   - Un processo di Analisi di flusso di Azure
   - Un account di archiviazione di Azure
   - Un hub eventi di Azure
   - Due app Web

## <a name="examine-the-sample-tollapp-job"></a>Esaminare il processo TollApp di esempio
1. Iniziando dal gruppo di risorse della sezione precedente, selezionare il processo di streaming di Analisi di flusso di Azure che inizia con il nome **tollapp** (il nome contiene caratteri casuali per scopi di univocità).

2. Nella pagina **Panoramica** del processo notare la casella **Query**, in cui è visualizzata la sintassi di query.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Parafrasando la finalità della query, è necessario conteggiare il numero di veicoli che entrano in un casello. Poiché un casello in autostrada ha un flusso continuo di veicoli in entrata, questi eventi di entrata sono analoghi a un flusso senza fine. Per quantificare il flusso, è necessario definire un "periodo di tempo" in base al quale eseguire la misurazione. Affinare ulteriormente la domanda, chiedendo: "Quanti veicoli entrano in un casello ogni tre minuti?" Questo tipo di conteggio viene detto a cascata.

   Come si può notare, Analisi di flusso di Azure usa un linguaggio di query simile a SQL e aggiunge alcune estensioni per specificare gli aspetti temporali della query.  Per maggiori dettagli, vedere i costrutti relativi alla [gestione del tempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) e al [windowing](https://msdn.microsoft.com/library/azure/dn835019.aspx) usati nella query.

3. Esaminare gli input del processo di esempio TollApp. Nella query corrente viene usato solo l'input EntryStream.
   - L'input **EntryStream** è una connessione a Hub eventi che accoda i dati che rappresentano ogni momento in cui un'auto entra in un casello in autostrada. Un'app Web che fa parte dell'esempio sta creando gli eventi e i dati vengono accodati in Hub eventi. Notare che le query su questo input vengono eseguite nella clausola FROM della query di streaming.
   - L'input **ExitStream** è una connessione a Hub eventi che accoda i dati che rappresentano ogni momento in cui un'auto esce da un casello in autostrada. Questo input di streaming viene usato in variazioni successive della sintassi di query.
   - L'input **Registration** è una connessione all'archiviazione BLOB di Azure che punta a un file JSON di registrazione statico, usato per le ricerche in base alle necessità. Questo input di dati di riferimento viene usato in variazioni successive della sintassi di query.

4. Esaminare gli output del processo di esempio TollApp.
   - L'output **Cosmos DB** è una raccolta di database Cosmos che riceve gli eventi sink di output. Notare che questo output viene usato nella clausola INTO della query di streaming.

## <a name="start-the-tollapp-streaming-job"></a>Avviare il processo di streaming TollApp
Per avviare il processo di streaming, completare questi passaggi:

1. Nella pagina **Panoramica** del processo selezionare **Avvia**.

2. Nel riquadro **Avvia processo** selezionare **Ora**.

3. Dopo alcuni istanti, quando il processo è in esecuzione, nella pagina **Panoramica** del processo di streaming visualizzare il grafico **Monitoraggio**. Il grafico mostrerà diverse migliaia di eventi in input e decine di eventi di output.

## <a name="review-the-cosmosdb-output-data"></a>Esaminare i dati di output di Cosmos DB
1. Individuare il gruppo di risorse che contiene le risorse di TollApp.

2. Selezionare l'account Azure Cosmos DB con il modello di denominazione **tollapp<random>-cosmos**.

3. Selezionare l'intestazione **Esplora dati** per aprire la pagina Esplora dati.

4. Espandere **tollAppDatabase** > **tollAppCollection** > **Documenti**.

5. Nell'elenco di ID diversi documenti vengono visualizzati solo quando è disponibile l'output.

6. Selezionare ogni ID per esaminare il documento JSON. Notare ogni ID casello, ogni momento di fine finestra e il numero di auto della finestra.

7. Dopo altri tre minuti, è disponibile un altro set di quattro documenti, un documento per ogni ID casello.


## <a name="report-total-time-for-each-car"></a>Segnalare il tempo totale per ogni auto
Il tempo medio necessario a un'automobile per passare attraverso il casello consente di valutare l'efficienza del processo e l'esperienza dell'utente.

Per trovare il tempo totale, unire il flusso EntryTime al flusso ExitTime. Unire i due flussi di input nelle colonne TollId e LicencePlate corrispondenti. L'operatore **JOIN** richiede di specificare un margine temporale che descrive la differenza di tempo accettabile tra gli eventi uniti. Usare la funzione **DATEDIFF** per specificare che gli eventi non devono essere distanti più di 15 minuti uno dall'altro. Applicare anche la funzione **DATEDIFF** ai tempi di uscita ed entrata per calcolare il tempo effettivo impiegato da un'auto nel casello. Si noti il diverso uso di **DATEDIFF** in un'istruzione **SELECT** rispetto a una condizione **JOIN**.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Per aggiornare la sintassi di query del processo di streaming TollApp:

1. Nella pagina **Panoramica** del processo selezionare **Arresta**.

2. Attendere alcuni istanti la notifica di arresto del processo.

3. Nell'intestazione TOPOLOGIA PROCESSO selezionare **Query < >**

4. Incollare la query SQL di streaming modificata.

5. Selezionare **Salva** per salvare la query. Confermare **Sì** per salvare le modifiche.

6. Nella pagina **Panoramica** del processo selezionare **Avvia**.

7. Nel riquadro **Avvia processo** selezionare **Ora**.

### <a name="review-the-total-time-in-the-output"></a>Esaminare il tempo totale nell'output
Ripetere i passaggi nella sezione precedente per esaminare i dati di output di Cosmos DB dal processo di streaming. Esaminare i documenti JSON più recenti.

Ad esempio, questo documento mostra un'auto di esempio con una certa targa, l'ora di ingresso e l'ora di uscita e il campo della durata DATEDIFF calcolata in minuti che mostra la durata in casello come due minuti:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Segnalare i veicoli con registrazione scaduta
Analisi di flusso di Azure può usare snapshot statici dei dati di riferimento da unire ai flussi di dati temporali. Per illustrare questa funzionalità, usare la domanda di esempio seguente. L'input Registration è un file JSON BLOB statico che elenca le scadenze delle targhe. Unendo la targa, i dati di riferimento vengono confrontati con ogni veicolo che attraversa il casello.

Se un veicolo commerciale è registrato presso l'azienda che gestisce il casello, lo può attraversare senza essere fermato per un controllo. Usare la tabella di ricerca relativa alla registrazione per identificare tutti i veicoli commerciali la cui registrazione è scaduta.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Ripetere i passaggi indicati nella sezione precedente per aggiornare la sintassi di query del processo di streaming TollApp.

2. Ripetere i passaggi nella sezione precedente per esaminare i dati di output di Cosmos DB dal processo di streaming.

Output di esempio:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Scalare orizzontalmente il processo
Analisi di flusso di Azure è progettato per offrire scalabilità elastica in modo da gestire volumi elevati di dati. La query di Analisi di flusso di Azure può usare la clausola **PARTITION BY** per indicare al sistema che questo passaggio aumenterà il numero di istanze. **PartitionId** è una colonna speciale aggiunta dal sistema e corrispondente all'ID partizione dell'input, ovvero l'hub eventi.

Per scalare orizzontalmente la query nelle partizioni, modificare la sintassi di query in base al codice seguente:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Per ridimensionare il processo di streaming in base a più unità di streaming:

1. Fare clic su **Arresta** per arrestare il processo corrente.

2. Aggiornare la sintassi di query nella pagina **Query < >** e salvare le modifiche.

3. Nell'intestazione CONFIGURA nel processo di streaming selezionare **Scala**.

4. Spostare il dispositivo di scorrimento **Unità di streaming** da 1 a 6. Le unità di streaming definiscono la quantità di potenza di calcolo che il processo può ricevere. Selezionare **Salva**.

5. Fare clic su **Avvia** per avviare il processo di streaming per provare la scalabilità aggiuntiva. Analisi di flusso di Azure distribuisce il lavoro tra più risorse di calcolo e ottiene velocità effettiva migliore, partizionando il lavoro tra risorse tramite la colonna designata nella clausola PARTITION BY.

## <a name="monitor-the-job"></a>Monitorare il processo
L'area **MONITORAGGIO** contiene le statistiche relative al processo in esecuzione. La configurazione iniziale è necessaria per usare l'account di archiviazione nella stessa area (denominare il casello come nelle altre parti di questo documento).

![Monitoraggio dei processi di Analisi di flusso di Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

È possibile accedere a **Log attività** anche dall'area **Impostazioni** del dashboard del processo.

## <a name="clean-up-the-tollapp-resources"></a>Pulire le risorse di TollApp
1. Arrestare il processo di Analisi di flusso nel portale di Azure.

2. Individuare il gruppo di risorse che contiene otto risorse correlate al modello TollApp.

3. Selezionare **Elimina gruppo di risorse**. Digitare il nome del gruppo di attività per confermare l'eliminazione.

## <a name="conclusion"></a>Conclusioni
Questa soluzione ha presentato il servizio Analisi di flusso di Azure. È stato illustrato come configurare input e output per il processo di Analisi di flusso. Usando lo scenario dei dati del casello, la soluzione ha descritto tipi comuni di problemi che si verificano nello spazio dei dati in movimento e come risolverli con semplici query di tipo SQL in Analisi di flusso di Azure. La soluzione ha descritto i costrutti di estensioni SQL per l'uso di dati temporali. È stato illustrato come creare un join tra flussi di dati, come arricchire il flusso di dati con dati di riferimento statici e come aumentare il numero di istanze di una query per ottenere una maggiore produttività.

Anche se questa soluzione offre una buona introduzione, non può ritenersi completa. Per altri modelli di query che usano il linguaggio SAQL, vedere [Esempi di query per modelli di uso comune di Analisi di flusso](stream-analytics-stream-analytics-query-patterns.md).
