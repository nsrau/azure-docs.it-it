---
title: Query sulle prestazioni lato server
description: Come eseguire query sulle prestazioni lato server con chiamate API
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 3207ffca6fd0fbc943f4a2873b8b6c9029d565af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84022795"
---
# <a name="server-side-performance-queries"></a>Query sulle prestazioni lato server

Ottenere prestazioni di rendering ottimali sul server è essenziale per avere frequenze dei fotogrammi stabili e offrire agli utenti un'esperienza soddisfacente. È importante monitorare con attenzione le caratteristiche delle prestazioni sul server e ottimizzare laddove necessario. È possibile eseguire query sui dati sulle prestazioni usando funzioni API dedicate.

L'elemento di maggior impatto riguardo alle prestazioni di rendering è rappresentato dai dati di input del modello. È possibile modificare i dati di input come descritto in [Configurare la conversione di modelli](../../how-tos/conversion/configure-model-conversion.md).

Anche le prestazioni delle applicazioni lato client possono costituire un collo di bottiglia. Per un'analisi approfondita delle prestazioni lato client, è consigliabile usare [:::no-loc text="performance trace":::](../../how-tos/performance-tracing.md) .

## <a name="clientserver-timeline"></a>Sequenza temporale client/server

Prima di approfondire i dettagli relativi ai diversi valori di latenza, è opportuno esaminare i punti di sincronizzazione tra client e server nella sequenza temporale:

![Sequenza temporale della pipeline](./media/server-client-timeline.png)

L'illustrazione indica che:

* viene avviata una *stima della posa* dal client con una frequenza dei fotogrammi costante di 60 Hz (ogni 16,6 ms)
* il server quindi avvia il rendering in base alla posa
* il server restituisce l'immagine video codificata
* il client decodifica l'immagine, esegue alcune operazioni di CPU e GPU sulla stessa e poi la visualizza

## <a name="frame-statistics-queries"></a>Query sulle statistiche dei fotogrammi

Le statistiche dei fotogrammi offrono alcune informazioni di alto livello per l'ultimo fotogramma, ad esempio la latenza. I dati specificati nella struttura `FrameStatistics` vengono misurati sul lato client, quindi l'API è una chiamata sincrona:

```cs
void QueryFrameData(AzureSession session)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        // do something with the result
    }
}
```

```cpp
void QueryFrameData(ApiHandle<AzureSession> session)
{
    FrameStatistics frameStatistics;
    if (*session->GetGraphicsBinding()->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        // do something with the result
    }
}
```

L'oggetto `FrameStatistics` recuperato include i membri seguenti:

| Membro | Spiegazione |
|:-|:-|
| latencyPoseToReceive | Latenza dalla stima della posa della fotocamera nel dispositivo client al momento in cui è completamente disponibile un fotogramma del server per l'applicazione client. Questo valore include round trip della rete, tempo di rendering del server, decodifica video e compensazione dell'instabilità. Vedere l'**intervallo 1 nell'illustrazione precedente.**|
| latencyReceiveToPresent | Latenza dalla disponibilità di un fotogramma ricevuto in remoto al momento in cui l'app client chiama PresentFrame sulla CPU. |
| latencyPresentToDisplay  | Latenza dalla presentazione di un fotogramma sulla CPU fino all'attivazione del display. Questo valore include tempo GPU client, qualsiasi buffer dei fotogrammi eseguito dal sistema operativo, riproiezione hardware e tempo di analisi del display dipendente dal dispositivo. Vedere l'**intervallo 2 nell'illustrazione precedente.**|
| timeSinceLastPresent | Intervallo di tempo tra le chiamate successive a PresentFrame sulla CPU. I valori superiori alla durata della visualizzazione, ad esempio 16,6 ms in un dispositivo client a 60 Hz, indicano i problemi causati dall'applicazione client che non termina in tempo il carico di lavoro della CPU. Vedere l'**intervallo 3 nell'illustrazione precedente.**|
| videoFramesReceived | Numero di fotogrammi ricevuti dal server nell'ultimo secondo. |
| videoFrameReusedCount | Numero di fotogrammi ricevuti nell'ultimo secondo che sono stati usati più volte nel dispositivo. I valori diversi da zero indicano che i fotogrammi devono essere riutilizzati e riproiettati a causa di un'instabilità della rete o di un tempo di rendering eccessivo del server. |
| videoFramesSkipped | Numero di fotogrammi ricevuti nell'ultimo secondo che sono stati decodificati, ma non visualizzati sul display perché è arrivato un fotogramma più recente. I valori diversi da zero indicano che l'instabilità della rete ha causato il ritardo di più fotogrammi che poi sono arrivati nel dispositivo client tutti insieme. |
| videoFramesDiscarded | Molto simile a **videoFramesSkipped**, ma il motivo per cui viene ignorato è che un fotogramma è arrivato così tardi da non poter nemmeno essere correlato a eventuali pose in sospeso. In questa situazione si verifica un grave conflitto di rete.|
| videoFrameMinDelta | Quantità minima di tempo tra due fotogrammi consecutivi in arrivo nell'ultimo secondo. Insieme a videoFrameMaxDelta, questo intervallo indica un'instabilità causata dalla rete o dal codec video. |
| videoFrameMaxDelta | Quantità massima di tempo tra due fotogrammi consecutivi in arrivo nell'ultimo secondo. Insieme a videoFrameMinDelta, questo intervallo indica un'instabilità causata dalla rete o dal codec video. |

La somma di tutti i valori di latenza è in genere molto maggiore rispetto alla durata dei fotogrammi disponibili a 60 Hz. Questo è accettabile, poiché vengono distribuiti più fotogrammi in parallelo e le nuove richieste di fotogrammi vengono avviate alla frequenza dei fotogrammi desiderata, come illustrato nella figura. Tuttavia, se la latenza diventa troppo grande, influisce sulla qualità della [riproiezione con ritardo della fase](../../overview/features/late-stage-reprojection.md) e può compromettere l'esperienza complessiva.

`videoFramesReceived`, `videoFrameReusedCount` e `videoFramesDiscarded` possono essere usati per misurare le prestazioni della rete e del server. Se `videoFramesReceived` è basso e `videoFrameReusedCount` è alto, questo può indicare una congestione della rete o una riduzione delle prestazioni del server. Un valore `videoFramesDiscarded` elevato indica anche la congestione della rete.

Infine, `timeSinceLastPresent`, `videoFrameMinDelta` e `videoFrameMaxDelta` danno un'idea della varianza dei fotogrammi video in arrivo e delle chiamate locali presenti. Una varianza elevata indica una frequenza dei fotogrammi instabile.

Nessuno dei valori precedenti offre un'indicazione chiara della latenza di rete pura (le frecce rosse nell'illustrazione), perché l'ora esatta in cui il server è occupato con il rendering deve essere sottratto dal valore di round trip `latencyPoseToReceive`. La parte lato server della latenza complessiva è costituita da informazioni non disponibili per il client. Tuttavia, il paragrafo successivo spiega come questo valore viene approssimato attraverso un input aggiuntivo dal server ed esposto usando il valore `networkLatency`.

## <a name="performance-assessment-queries"></a>Query di valutazione delle prestazioni

Le *query di valutazione delle prestazioni* offrono informazioni più dettagliate sul carico di lavoro di CPU e GPU sul server. Poiché i dati vengono richiesti dal server, l'esecuzione di query su uno snapshot delle prestazioni segue il consueto modello asincrono:

```cs
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

```cpp
void QueryPerformanceAssessment(ApiHandle<AzureSession> session)
{
    ApiHandle<PerformanceAssessmentAsync> assessmentQuery = *session->Actions()->QueryServerPerformanceAssessmentAsync();
    assessmentQuery->Completed([] (ApiHandle<PerformanceAssessmentAsync> res)
    {
        // do something with the result:
        PerformanceAssessment result = *res->Result();
        // ...

    });
}
```

Contrariamente all'oggetto `FrameStatistics`, l'oggetto `PerformanceAssessment` contiene informazioni sul lato server:

| Membro | Spiegazione |
|:-|:-|
| timeCPU | Tempo medio CPU server per fotogramma in millisecondi |
| timeGPU | Tempo medio GPU server per fotogramma in millisecondi |
| utilizationCPU | Utilizzo CPU totale nel server in percentuale |
| utilizationGPU | Utilizzo GPU totale nel server in percentuale |
| memoryCPU | Utilizzo totale della memoria principale nel server in percentuale |
| memoryGPU | Utilizzo totale della memoria video dedicata in percentuale della GPU del server |
| networkLatency | Latenza media di rete approssimativa del round trip in millisecondi. Nell'illustrazione precedente corrisponde alla somma delle frecce rosse. Il valore viene calcolato sottraendo il tempo effettivo di rendering del server dal valore `latencyPoseToReceive` di `FrameStatistics`. Anche se questa approssimazione non è precisa, offre alcune indicazioni sulla latenza di rete, isolata dai valori di latenza calcolati sul client. |
| polygonsRendered | Numero di triangoli sottoposti a rendering in un fotogramma. Questo numero include anche i triangoli che vengono selezionati in seguito durante il rendering. Ciò significa che questo numero non varia molto tra le diverse posizioni della fotocamera, ma le prestazioni possono variare drasticamente, in base alla frequenza di selezione dei triangoli.|

Per facilitare la valutazione dei valori, ogni parte viene classificata in base alla qualità, ad esempio **Great**, **Good**, **Mediocre** o **Bad**.
Questa metrica di valutazione offre un'indicazione approssimativa dell'integrità del server, ma non deve essere considerata come un valore assoluto. Si supponga, ad esempio, che venga visualizzato il punteggio "mediocre" per il tempo GPU. Il tempo è considerato mediocre perché si avvicina al limite del budget complessivo previsto per la durata dei fotogrammi. In questo caso, tuttavia, potrebbe essere comunque un valore valido, perché si sta eseguendo il rendering di un modello complesso.

## <a name="statistics-debug-output"></a>Output del debug delle statistiche

La classe `ARRServiceStats` è una classe C# che esegue il wrapping delle statistiche dei fotogrammi e delle query sulla valutazione delle prestazioni e offre una comoda funzionalità per restituire le statistiche come valori aggregati o come stringa predefinita. Il codice seguente rappresenta il modo più semplice per visualizzare le statistiche sul lato server nell'applicazione client.

```cs
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

L'API `GetStatsString` formatta una stringa di tutti i valori, ma ogni singolo valore può anche essere sottoposto a query a livello di codice dall'istanza di `ARRServiceStats`.

Sono inoltre disponibili varianti dei membri che aggregano i valori nel tempo. Vedere i membri con suffisso `*Avg`, `*Max` o `*Total`. Il membro `FramesUsedForAverage` indica il numero di fotogrammi usati per questa aggregazione.

## <a name="next-steps"></a>Passaggi successivi

* [Creare analisi delle prestazioni](../../how-tos/performance-tracing.md)
* [Configurare la conversione di modelli](../../how-tos/conversion/configure-model-conversion.md)
