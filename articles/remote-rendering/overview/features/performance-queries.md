---
title: Query sulle prestazioni lato server
description: Come eseguire query sulle prestazioni lato server tramite chiamate APIHow to do server-side performance queries via API calls
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 9a28dee2d1e6d1355b729a56e8eeb8447e4ed8c8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80682026"
---
# <a name="server-side-performance-queries"></a>Query sulle prestazioni lato server

Una buona prestazione di rendering sul server è fondamentale per una frequenza fotogrammi stabile e una buona esperienza utente. È importante monitorare attentamente le caratteristiche delle prestazioni del server ed ottimizzare dove necessario. È possibile eseguire query sui dati sulle prestazioni tramite funzioni API dedicate.

Il valore più incisivo per le prestazioni di rendering sono i dati di input del modello. È possibile modificare i dati di input come descritto in [Configurazione della conversione del modello](../../how-tos/conversion/configure-model-conversion.md).

Anche le prestazioni delle applicazioni lato client potrebbero essere un collo di bottiglia. Per un'analisi approfondita delle prestazioni lato client, è consigliabile prendere una [traccia delle prestazioni](../../how-tos/performance-tracing.md).

## <a name="clientserver-timeline"></a>Sequenza temporale client/server

Prima di entrare nei dettagli relativi ai vari valori di latenza, è utile dare un'occhiata ai punti di sincronizzazione tra client e server sulla sequenza temporale:

![Sequenza temporale della pipeline](./media/server-client-timeline.png)

L'illustrazione mostra come:

* una *stima di Posa* viene espulsa dal client a una frequenza fotogrammi costante a 60 Hz (ogni 16,6 ms)
* il server inizia quindi il rendering, in base alla posa
* il server invia l'immagine video codificata
* il client decodifica l'immagine, esegue alcune operazioni di CPU e GPU su di essa e quindi visualizza l'immagine

## <a name="frame-statistics-queries"></a>Query sulle statistiche dei frame

Le statistiche sui frame forniscono alcune informazioni generali per l'ultimo frame, ad esempio la latenza. I dati forniti `FrameStatistics` nella struttura vengono misurati sul lato client, pertanto l'API è una chiamata sincrona:The data provided in the structure is measured on the client side, so the API is a synchronous call:

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

L'oggetto recuperato `FrameStatistics` contiene i seguenti membri:

| Membro | Spiegazione |
|:-|:-|
| latencyPoseToReceive | Latenza dalla stima della posizione della fotocamera nel dispositivo client fino a quando un frame del server per questa posa non è completamente disponibile per l'applicazione client. Questo valore include il percorso di andata e ritorno in rete, il tempo di rendering del server, la decodifica video e la compensazione del jitter. Vedere **l'intervallo 1 nell'illustrazione precedente.**|
| latencyReceiveToPresent | Latenza dalla disponibilità di un frame remoto ricevuto fino a quando l'app client non chiama PresentFrame sulla CPU. |
| latenzaPresentToDisplay  | Latenza dalla presentazione di un frame sulla CPU fino all'accesa dello schermo. Questo valore include il tempo GPU del client, qualsiasi buffer ing al frame eseguito dal sistema operativo, la riproiezione hardware e il tempo di analisi dello schermo dipendente dal dispositivo. Vedere **l'intervallo 2 nell'illustrazione precedente.**|
| timeSinceLastPresent | Tempo tra le chiamate successive a PresentFrame sulla CPU. I valori maggiori della durata di visualizzazione (ad esempio 16,6 ms su un dispositivo client a 60 Hz) indicano problemi causati dal fatto che l'applicazione client non termina il carico di lavoro della CPU in tempo. Vedere **l'intervallo 3 nell'illustrazione precedente.**|
| videoFramesReceived (Oggetti videoricevuti) | Il numero di frame ricevuti dal server nell'ultimo secondo. |
| videoFrameReusedCount | Numero di frame ricevuti nell'ultimo secondo utilizzati sul dispositivo più di una volta. Valori diversi da zero indicano che i fotogrammi devono essere riutilizzati e riproiettati a causa di un jitter di rete o di un tempo di rendering eccessivo del server. |
| videoFramesSaltato | Numero di fotogrammi ricevuti nell'ultimo secondo che sono stati decodificati, ma non visualizzati sul display perché è arrivato un fotogramma più recente. Valori diversi da zero indicano che il jittering di rete ha causato il ritardo di più frame e quindi l'arrivo sul dispositivo client insieme in un burst. |
| videoFramesSdiscardi | Molto simile a **videoFramesSkipped**, ma il motivo per essere scartati è che un fotogramma è arrivato così tardi che non può nemmeno essere correlato con qualsiasi posa in sospeso più. In questo caso, c'è qualche grave contesa di rete.|
| videoFrameMinDelta | Quantità minima di tempo tra due fotogrammi consecutivi in arrivo durante l'ultimo secondo. Insieme a videoFrameMaxDelta, questo intervallo fornisce un'indicazione di jitter causato dal codec di rete o video. |
| videoFrameMaxDelta | Quantità massima di tempo tra due fotogrammi consecutivi in arrivo durante l'ultimo secondo. Insieme a videoFrameMinDelta, questo intervallo fornisce un'indicazione di jitter causato dalla rete o codec video. |

La somma di tutti i valori di latenza è in genere molto maggiore del tempo di frame disponibile a 60 Hz. Questo è OK, perché più fotogrammi sono in volo in parallelo e le nuove richieste di frame vengono espulse alla frequenza fotogrammi desiderata, come mostrato nell'illustrazione. Tuttavia, se la latenza diventa troppo grande, influisce sulla qualità della [riproiezione](../../overview/features/late-stage-reprojection.md)in fase avanzata e può compromettere l'esperienza complessiva.

`videoFramesReceived`, `videoFrameReusedCount`, `videoFramesDiscarded` e può essere utilizzato per misurare le prestazioni della rete e del server. Se `videoFramesReceived` è `videoFrameReusedCount` basso ed è elevato, ciò può indicare la congestione della rete o una riduzione delle prestazioni del server. Un `videoFramesDiscarded` valore elevato indica anche la congestione della rete.

Infine,`timeSinceLastPresent`, `videoFrameMinDelta`e `videoFrameMaxDelta` dare un'idea della varianza dei fotogrammi video in entrata e delle chiamate presenti locali. L'alta varianza indica una frequenza fotogrammi instabile.

Nessuno dei valori precedenti fornisce una chiara indicazione della latenza di rete pura (le frecce rosse nell'illustrazione), perché `latencyPoseToReceive`l'ora esatta in cui il server è occupato il rendering deve essere sottratta dal valore di andata e ritorno. La parte lato server della latenza complessiva è costituita da informazioni non disponibili per il client. Tuttavia, il paragrafo successivo spiega come questo valore viene approssimato tramite input aggiuntivo dal server ed esposto tramite il `networkLatency` valore.

## <a name="performance-assessment-queries"></a>Query di valutazione delle prestazioni

*Le query* di valutazione delle prestazioni forniscono informazioni più approfondite sul carico di lavoro della CPU e della GPU sul server. Poiché i dati vengono richiesti dal server, l'esecuzione di query su uno snapshot delle prestazioni segue il modello asincrono usuale:Since the data is requested from the server, querying a performance snapshot follows the usual async pattern:

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

Contrariamente `FrameStatistics` all'oggetto, l'oggetto `PerformanceAssessment` contiene informazioni sul lato server:

| Membro | Spiegazione |
|:-|:-|
| tempoCPU | Tempo medio CPU del server per fotogramma in millisecondi |
| timeGPU | Tempo medio GPU del server per fotogramma in millisecondi |
| utilizzoCPU | Utilizzo totale della CPU del server in percentuale |
| utilizationGPU | Utilizzo totale della GPU del server in percentuale |
| memoryCPU (CPU di memoria) | Utilizzo totale della memoria principale del server in percentuale |
| memoryGPU | Utilizzo totale della memoria video dedicata nella percentuale della GPU del server |
| networkLatency (Latenza di rete) | Latenza approssimativa di rete di andata e ritorno in millisecondi. Nell'illustrazione precedente, questo corrisponde alla somma delle frecce rosse. Il valore viene calcolato sottraendo `latencyPoseToReceive` il `FrameStatistics`tempo di rendering effettivo del server dal valore di . Sebbene questa approssimazione non sia accurata, fornisce qualche indicazione della latenza di rete, isolata dai valori di latenza calcolati nel client. |
| poligoniRendered | Il numero di triangoli sottoposti a rendering in un fotogramma. Questo numero include anche i triangoli che vengono abbattuti in un secondo momento durante il rendering. Ciò significa che questo numero non varia molto tra diverse posizioni della fotocamera, ma le prestazioni possono variare drasticamente, a seconda del tasso di abbattimento del triangolo.|

Per aiutarti a valutare i valori, ogni porzione viene fornita con una classificazione di qualità come **Great**, **Good**, **Mediocre**o **Bad**.
Questa metrica di valutazione fornisce un'indicazione approssimativa dell'integrità del server, ma non deve essere considerata assoluta. Ad esempio, supponiamo che venga visualizzato un punteggio "mediocre" per il tempo GPU. È considerato mediocre perché si avvicina al limite per il budget complessivo del tempo di frame. Nel tuo caso, tuttavia, potrebbe essere comunque un buon valore, perché stai eseguendo il rendering di un modello complesso.

## <a name="statistics-debug-output"></a>Output di debug delle statistiche

La `ARRServiceStats` classe esegue il wrapping sia delle statistiche del frame che delle query di valutazione delle prestazioni e fornisce funzionalità utili per restituire statistiche come valori aggregati o come stringa predefinita. Il codice seguente è il modo più semplice per visualizzare le statistiche sul lato server nell'applicazione client.

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

![Output della stringa ArrServiceStats](./media/arr-service-stats.png)

L'API `GetStatsString` formatta una stringa di tutti i valori, ma ogni `ARRServiceStats` singolo valore può anche essere sottoposto a query a livello di codice dall'istanza.

Esistono anche varianti dei membri, che aggregano i valori nel tempo. Vedere i `*Avg`membri `*Max`con `*Total`suffisso , , o . Il `FramesUsedForAverage` membro indica quanti frame sono stati utilizzati per questa aggregazione.

## <a name="next-steps"></a>Passaggi successivi

* [Creare tracce delle prestazioniCreate performance traces](../../how-tos/performance-tracing.md)
* [Configurazione della conversione del modello](../../how-tos/conversion/configure-model-conversion.md)
