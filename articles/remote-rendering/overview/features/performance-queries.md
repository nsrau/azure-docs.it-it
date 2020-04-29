---
title: Query sulle prestazioni lato server
description: Come eseguire query sulle prestazioni lato server tramite chiamate API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80682026"
---
# <a name="server-side-performance-queries"></a>Query sulle prestazioni lato server

Un miglioramento delle prestazioni di rendering sul server è essenziale per le tariffe di fotogrammi stabili e per un'esperienza utente ottimale. È importante monitorare con attenzione le caratteristiche relative alle prestazioni del server e ottimizzare laddove necessario. È possibile eseguire query sui dati sulle prestazioni tramite funzioni API dedicate.

La maggior parte degli effetti sulle prestazioni di rendering è rappresentata dai dati di input del modello. È possibile modificare i dati di input come descritto in [configurazione della conversione del modello](../../how-tos/conversion/configure-model-conversion.md).

Anche le prestazioni dell'applicazione lato client possono costituire un collo di bottiglia. Per un'analisi approfondita delle prestazioni lato client, è consigliabile eseguire una [traccia delle prestazioni](../../how-tos/performance-tracing.md).

## <a name="clientserver-timeline"></a>Sequenza temporale client/server

Prima di approfondire i dettagli relativi ai diversi valori di latenza, è opportuno esaminare i punti di sincronizzazione tra client e server nella sequenza temporale:

![Sequenza temporale pipeline](./media/server-client-timeline.png)

Nell'illustrazione viene mostrato come:

* una *stima di posa* viene avviata dal client alla frequenza dei fotogrammi 60-Hz costante (ogni 16,6 ms)
* il server avvia il rendering in base alla posa
* il server restituirà l'immagine video codificata
* il client decodifica l'immagine, esegue alcune operazioni di CPU e GPU e quindi Visualizza l'immagine

## <a name="frame-statistics-queries"></a>Query statistiche frame

Le statistiche dei frame forniscono alcune informazioni di alto livello per l'ultimo frame, ad esempio la latenza. I dati forniti nella `FrameStatistics` struttura sono misurati sul lato client, quindi l'API è una chiamata sincrona:

````c#
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
````

L'oggetto `FrameStatistics` recuperato include i membri seguenti:

| Membro | Spiegazione |
|:-|:-|
| latencyPoseToReceive | Latenza dalla stima della formula della fotocamera sul dispositivo client fino a quando non si dispone di un frame server per questa formula completamente disponibile per l'applicazione client. Questo valore include round trip della rete, tempo di rendering del server, decodifica video e compensazione di jitter. Vedere **l'intervallo 1 nell'illustrazione precedente.**|
| latencyReceiveToPresent | Latenza dalla disponibilità di un frame remoto ricevuto fino a quando l'app client chiama PresentFrame sulla CPU. |
| latencyPresentToDisplay  | Latenza rispetto alla presentazione di un frame sulla CPU fino a quando non viene visualizzata la visualizzazione. Questo valore include tempo GPU client, qualsiasi buffer dei frame eseguito dal sistema operativo, riproiezione hardware e tempo di analisi della visualizzazione dipendente dal dispositivo. Vedere **l'intervallo 2 nell'illustrazione precedente.**|
| timeSinceLastPresent | Tempo tra le chiamate successive a PresentFrame sulla CPU. I valori maggiori della durata dello schermo, ad esempio 16,6 ms in un dispositivo client 60-Hz, indicano i problemi causati dall'applicazione client che non termina il carico di lavoro della CPU nel tempo. Vedere **l'intervallo 3 nell'illustrazione precedente.**|
| videoFramesReceived | Numero di frame ricevuti dal server nell'ultimo secondo. |
| videoFrameReusedCount | Numero di frame ricevuti nell'ultimo secondo che sono stati usati più volte nel dispositivo. I valori diversi da zero indicano che i frame devono essere riutilizzati e riproiettati a causa di un'instabilità della rete o di un tempo di rendering eccessivo del server. |
| videoFramesSkipped | Numero di frame ricevuti nell'ultimo secondo decodificati, ma non visualizzati durante la visualizzazione perché è arrivato un frame più recente. I valori diversi da zero indicano che la disattivazione della rete ha causato il ritardo di più frame e l'arrivo del dispositivo client insieme in un impeto. |
| videoFramesDiscarded | Molto simile a **videoFramesSkipped**, ma il motivo per cui viene scartato è il fatto che un frame è arrivato in quel momento che non può essere neanche correlato con le altre pose in sospeso. In tal caso, si verifica un conflitto di rete grave.|
| videoFrameMinDelta | Quantità minima di tempo tra due frame consecutivi in arrivo nell'ultimo secondo. Insieme a videoFrameMaxDelta, questo intervallo indica un'instabilità causata dal codec di rete o video. |
| videoFrameMaxDelta | Quantità massima di tempo tra due frame consecutivi in arrivo nell'ultimo secondo. Insieme a videoFrameMinDelta, questo intervallo indica un'instabilità causata dal codec di rete o video. |

La somma di tutti i valori di latenza è in genere molto maggiore del tempo di frame disponibile a 60 Hz. Questo è accettabile, poiché più frame sono in esecuzione in parallelo e le nuove richieste di frame vengono avviate alla frequenza dei fotogrammi desiderata, come illustrato nella figura. Tuttavia, se la latenza diventa troppo grande, influiscono sulla qualità della [riproiezione della fase tardiva](../../overview/features/late-stage-reprojection.md)e può compromettere l'esperienza complessiva.

`videoFramesReceived`, `videoFrameReusedCount`e `videoFramesDiscarded` possono essere usati per misurare le prestazioni della rete e del server. Se `videoFramesReceived` è basso ed `videoFrameReusedCount` è elevato, questo può indicare una congestione della rete o una riduzione delle prestazioni del server. Un valore `videoFramesDiscarded` elevato indica anche la congestione della rete.

Infine,`timeSinceLastPresent` `videoFrameMinDelta`, e `videoFrameMaxDelta` offrono un'idea della varianza dei fotogrammi video in arrivo e delle chiamate locali presenti. Una varianza elevata indica una frequenza di fotogrammi instabile.

Nessuno dei valori precedenti fornisce un'indicazione chiara della latenza di rete pura (le frecce rosse nell'illustrazione), perché l'ora esatta in cui il rendering del server è occupato deve essere sottratto dal valore `latencyPoseToReceive`round trip. La parte lato server della latenza complessiva è costituita da informazioni non disponibili per il client. Tuttavia, il paragrafo successivo illustra il modo in cui questo valore viene approssimato attraverso input aggiuntivo dal server ed `networkLatency` esposto tramite il valore.

## <a name="performance-assessment-queries"></a>Query di valutazione delle prestazioni

Le *query di valutazione delle prestazioni* forniscono informazioni più dettagliate sul carico di lavoro CPU e GPU sul server. Poiché i dati vengono richiesti dal server, l'esecuzione di query su uno snapshot delle prestazioni segue il modello asincrono usuale:

``` cs
PerformanceAssessmentAsync _assessmentQuery = null;

void QueryPerformanceAssessment(AzureSession session)
{
    _assessmentQuery = session.Actions.QueryServerPerformanceAssessmentAsync();
    _assessmentQuery.Completed += (PerformanceAssessmentAsync res) =>
    {
        // do something with the result:
        PerformanceAssessment result = res.Result;
        // ...

        _assessmentQuery = null;
    };
}
```

Contrariamente all' `FrameStatistics` oggetto, l' `PerformanceAssessment` oggetto contiene informazioni sul lato server:

| Membro | Spiegazione |
|:-|:-|
| timeCPU | Tempo medio CPU server per fotogramma in millisecondi |
| timeGPU | Tempo medio GPU server per fotogramma in millisecondi |
| utilizationCPU | Totale utilizzo CPU server in percentuale |
| utilizationGPU | Totale utilizzo GPU server in percentuale |
| memoryCPU | Totale utilizzo memoria principale server in percentuale |
| memoryGPU | Utilizzo totale della memoria video dedicata in percentuale della GPU del server |
| networkLatency | Latenza media del round trip approssimativa in millisecondi. Nell'illustrazione precedente corrisponde alla somma delle frecce rosse. Il valore viene calcolato sottraendo il tempo effettivo di rendering del server dal `latencyPoseToReceive` valore di `FrameStatistics`. Anche se questa approssimazione non è precisa, fornisce alcune indicazioni sulla latenza di rete, isolata dai valori di latenza calcolati sul client. |
| polygonsRendered | Numero di triangoli sottoposti a rendering in un frame. Questo numero include anche i triangoli che vengono raccolti in un secondo momento durante il rendering. Questo significa che questo numero non varia molto tra le diverse posizioni della fotocamera, ma le prestazioni possono variare drasticamente, a seconda della frequenza di selezione dei triangoli.|

Per facilitare la valutazione dei valori, ogni parte viene fornita con una classificazione di qualità, ad esempio **grande**, **buona**, **mediocre**o **negativa**.
Questa metrica di valutazione fornisce un'indicazione approssimativa dell'integrità del server, ma non dovrebbe essere considerata assoluta. Si supponga, ad esempio, che venga visualizzato un punteggio "mediocre" per il tempo della GPU. Viene considerato mediocre perché si avvicina al limite per l'intero budget del tempo di frame. In questo caso, tuttavia, potrebbe essere un valore valido, perché si sta eseguendo il rendering di un modello complesso.

## <a name="statistics-debug-output"></a>Output debug statistiche

La classe `ARRServiceStats` esegue il wrapping delle query sulle statistiche dei frame e della valutazione delle prestazioni e offre una comoda funzionalità per restituire le statistiche come valori aggregati o come stringa predefinita. Il codice seguente rappresenta il modo più semplice per visualizzare le statistiche sul lato server nell'applicazione client.

``` cs
ARRServiceStats _stats = null;

void OnConnect()
{
    _stats = new ARRServiceStats();
}

void OnDisconnect()
{
    _stats = null;
}

void Update()
{
    if (_stats != null)
    {
        // update once a frame to retrieve new information and build average values
        _stats.Update(Service.CurrentActiveSession);

        // retrieve a string with relevant stats information
        InfoLabel.text = _stats.GetStatsString();
    }
}
```

Il codice precedente popola l'etichetta di testo con il testo seguente:

![Output stringa ArrServiceStats](./media/arr-service-stats.png)

L' `GetStatsString` API formatta una stringa di tutti i valori, ma ogni singolo valore può anche essere sottoposto a query a livello `ARRServiceStats` di codice dall'istanza.

Sono inoltre disponibili varianti dei membri che aggregano i valori nel tempo. Vedere membri con `*Avg`suffisso `*Max`, o `*Total`. Il membro `FramesUsedForAverage` indica il numero di frame utilizzati per questa aggregazione.

## <a name="next-steps"></a>Passaggi successivi

* [Creazione di tracce di prestazioni](../../how-tos/performance-tracing.md)
* [Configurazione della conversione del modello](../../how-tos/conversion/configure-model-conversion.md)
