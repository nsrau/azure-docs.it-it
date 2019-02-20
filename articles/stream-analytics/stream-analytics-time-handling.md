---
title: Informazioni sulla gestione del tempo in Analisi di flusso di Azure
description: Informazioni su come gestire il tempo in Analisi di flusso di Azure
author: jasonwhowell
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 4accff7410d17e76a000b7cef957b75c65a16960
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007669"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Informazioni sulla gestione del tempo in Analisi di flusso di Azure

Questo articolo illustra come prendere decisioni di progettazione per risolvere alcuni problemi pratici di gestione del tempo nel servizio Analisi di flusso di Azure. Le decisioni di progettazione della gestione del tempo sono strettamente correlate ai fattori di ordinamento degli eventi. Per altre informazioni, vedere questo articolo correlato: [Considerazioni sull'ordine degli eventi con Analisi di flusso di Azure](stream-analytics-out-of-order-and-late-events.md).

## <a name="background-time-concepts"></a>Concetti di base sul tempo

Per strutturare meglio la discussione, è opportuno definire alcuni concetti di base:

- **Ora dell'evento**: l'ora in cui si è verificato l'evento originale. Ad esempio, quando un'automobile in corsa su un'autostrada si avvicina a un casello.

- **Ora di elaborazione**: l'ora in cui l'evento raggiunge il sistema di elaborazione e viene osservato. Ad esempio, quando il sensore di un casello avvista l'automobile e il sistema impiega alcuni istanti ad elaborare i dati.

- **Limite**: indicatore temporale che indica tutti gli eventi inseriti nel processore di flusso fino a un certo punto. I limiti consentono al sistema di indicare chiaramente lo stato di inserimento degli eventi. Per la natura stessa dei flussi, i dati degli eventi in ingresso non si arrestano mai, quindi i limiti indicano l'avanzamento fino a un determinato punto del flusso.

   Il concetto di limite è importante. I limiti consentono ad Analisi di flusso di Azure di determinare quando il sistema è in grado di produrre risultati completi, corretti e ripetibili che non è necessario ritirare. L'elaborazione può essere eseguita in un modo che garantisce prevedibilità e ripetibilità. Ad esempio, se è necessario ripetere un conteggio per una qualche condizione di gestione degli errori, i limiti sono punti di inizio e fine sicuri.

Per altre informazioni su questo argomento, vedere i post di blog di Tyler Akidau [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) e [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>Scelta dell'ora di inizio migliore

Analisi di flusso di Azure offre due opzioni di selezione dell'ora dell'evento:

1. **Ora di arrivo**  

   L'ora di arrivo viene assegnata nell'origine di input quando l'evento raggiunge l'origine. È possibile accedere all'ora di arrivo usando la proprietà **EventEnqueuedUtcTime** per gli input di Hub eventi, la proprietà **IoTHub.EnqueuedTime** per l'hub IoT e la proprietà **BlobProperties.LastModified** per l'input del BLOB.

   L'uso dell'ora di arrivo è il comportamento predefinito e trova il suo impiego ottimale negli scenari di archiviazione dei dati, dove la logica temporale non è necessaria.

2. **Tempo applicazione** (o Ora dell'evento)

   Il tempo applicazione viene assegnato quando l'evento viene generato e fa parte del payload dell'evento. Per elaborare gli eventi in base al tempo applicazione, usare la clausola **Timestamp by** nella query di selezione. Se la clausola **Timestamp by** è assente, gli eventi vengono elaborati in base all'ora di arrivo.

   Quando è coinvolta la logica temporale, è importante usare un timestamp nel payload. In questo modo è possibile tenere in considerazione gli eventuali ritardi nel sistema di origine o nella rete.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Avanzamento del tempo in Analisi di flusso di Azure

Quando si usa il tempo applicazione, l'avanzamento del tempo si basa sugli eventi in ingresso. È difficile per il sistema di elaborazione del flusso sapere se non ci sono eventi o se gli eventi sono ritardati. Per questo motivo, Analisi di flusso di Azure genera limiti euristici per ogni partizione di input nei modi seguenti:

1. Quando c'è un evento in ingresso, il limite viene calcolato sottraendo dall'ora dell'evento maggiore registrata fino a quel momento le dimensioni della finestra di tolleranza elementi non in ordine.

2. Quando non c'è alcun evento in ingresso, il limite corrisponde all'ora di arrivo stimata corrente (il tempo trascorso nell'elaborazione in background degli eventi sulla VM dall'ultimo evento di input più l'ora di arrivo di tale evento di input) meno la finestra di tolleranza per arrivo in ritardo.

   L'ora di arrivo può essere solo stimata, in quanto l'ora di arrivo reale viene generata nel gestore eventi di input, ad esempio Hub eventi, e non nella macchina virtuale di Analisi di flusso di Azure che elabora gli eventi.

La struttura ha due altri scopi oltre alla generazione dei limiti:

1. Il sistema genera risultati in modo tempestivo con o senza eventi in ingresso.

   È possibile controllare il livello di tempestività con cui visualizzare i risultati di output. Nella pagina **Ordinamento eventi** del processo di Analisi di flusso nel portale di Azure è possibile configurare l'impostazione **Eventi non in ordine**. Nella configurazione considerare il compromesso fra tempestività e tolleranza degli eventi non in ordine nel flusso di eventi.

   La finestra di tolleranza per arrivo in ritardo è importante per continuare a generare limiti, anche in assenza di eventi in ingresso. Possono esserci periodi senza eventi in ingresso, ad esempio quando un flusso di input degli eventi è di tipo sparse. Questo problema viene aggravato dall'uso di più partizioni nel gestore eventi di input.

   I sistemi di elaborazione dati in streaming senza una finestra di tolleranza per arrivo in ritardo possono presentare un ritardo degli output quando gli input sono di tipo sparse e vengono usate più partizioni.

2. Il comportamento del sistema deve essere ripetibile. La ripetibilità è una proprietà importante dei sistemi di elaborazione dati in streaming.

   Il limite viene derivato da ora di arrivo e tempo applicazione. Entrambi sono persistenti nel gestore eventi e pertanto sono ripetibili. Se l'ora di arrivo deve essere stimata in assenza di eventi, Analisi di flusso di Azure inserisce nel journal l'ora di arrivo stimata per garantirne la ripetibilità durante la riproduzione ai fini del ripristino da errori.

Quando si sceglie di usare l'**ora di arrivo** come ora dell'evento, non c'è bisogno di configurare la tolleranza elementi non in ordine e la tolleranza per arrivo in ritardo. Poiché per l'**ora di arrivo** vi è una garanzia di incremento progressivo nel gestore eventi di input, Analisi di flusso di Azure ignora semplicemente le configurazioni.

## <a name="late-arriving-events"></a>Eventi che arrivano in ritardo

In base alla definizione della finestra di tolleranza per arrivo in ritardo, per ogni evento in ingresso Analisi di flusso di Azure confronta l'**ora dell'evento** con l'**ora di arrivo**. Se l'ora dell'evento è all'esterno della finestra di tolleranza, è possibile configurare il sistema in modo che elimini l'evento o modifichi l'ora dell'evento per farla rientrare nella finestra di tolleranza.

Tenere presente che dopo la generazione dei limiti, il servizio può potenzialmente ricevere eventi con un'ora inferiore al limite. È possibile configurare il servizio in modo da **eliminare** questi eventi o **modificare** l'ora dell'evento in base al valore del limite.

Nell'ambito della modifica, il **System.Timestamp** dell'evento viene impostato sul nuovo valore, ma il campo dell'**ora dell'evento** in sé non viene modificato. Questa modifica è l'unica situazione in cui il **System.Timestamp** di un evento può essere diverso dal valore indicato nel campo dell'ora dell'evento e può causare la generazione di risultati imprevisti.

## <a name="handling-time-variation-with-substreams"></a>Gestione delle variazioni temporali con i flussi secondari

Il meccanismo di generazione di limiti euristici descritto qui funziona bene nella maggior parte dei casi in cui il tempo è per lo più sincronizzato tra i vari mittenti di eventi. Nella vita reale però, specialmente in molti scenari IoT, il sistema ha poco controllo sull'orologio dei mittenti di eventi. Questi mittenti possono essere qualsiasi tipo di dispositivo disponibile sul mercato, con versioni di hardware e software diverse.

Invece di usare un limite comune a tutti gli eventi in una partizione di input, Analisi di flusso usa un altro meccanismo, ossia i flussi secondari. È possibile usare i flussi secondari in un processo scrivendo una query di processo che usa la clausola [**TIMESTAMP BY**](/stream-analytics-query/timestamp-by-azure-stream-analytics) e la parola chiave **OVER**. Per designare il flusso secondario, specificare un nome di colonna chiave dopo la parola chiave **OVER**, ad esempio `deviceid`, in modo che il sistema applichi i criteri temporali in base a quella colonna. Ogni flusso secondario ottiene il proprio limite indipendente. Questo meccanismo è utile per consentire la generazione tempestiva dell'output, in presenza di notevoli sfasamenti di orario o ritardi di rete tra i mittenti di eventi.

I flussi secondari sono una soluzione fornita esclusivamente da Analisi di flusso di Azure e non vengono offerti da altri sistemi di elaborazione dati in streaming. Quando usa i flussi secondari, Analisi di flusso applica la finestra di tolleranza per arrivo in ritardo agli eventi in ingresso. L'impostazione predefinita (5 secondi) è probabilmente insufficiente per i dispositivi con timestamp non coerenti. È consigliabile iniziare con 5 minuti e quindi regolare l'impostazione in base allo schema di sfasamento orario del dispositivo.

## <a name="early-arriving-events"></a>Eventi che arrivano in anticipo

Oltre alla finestra di tolleranza per arrivo in ritardo esiste anche, all'opposto, la finestra di tolleranza per arrivo in anticipo. Questa finestra è impostata su 5 minuti e ha uno scopo diverso da quella per l'arrivo in ritardo.

Poiché Analisi di flusso di Azure garantisce sempre la generazione di risultati completi, è possibile specificare solo l'**ora di inizio processo** come prima ora di output del processo, invece dell'ora di input. L'ora di inizio processo è necessaria per elaborare la finestra completa, invece che a partire dalla parte centrale.

Analisi di flusso deriva quindi l'ora di inizio dalla specifica della query. Tuttavia, poiché il gestore eventi di input viene indicizzato solo in base all'ora di arrivo, il sistema deve convertire l'ora di inizio dell'evento in ora di arrivo. Il sistema può iniziare a elaborare gli eventi da quel punto nel gestore eventi di input. Con il limite della finestra di tolleranza per arrivo in anticipo la conversione è semplice. Corrisponde all'ora di inizio dell'evento meno i 5 minuti della finestra di tolleranza per arrivo in anticipo. In base a questo calcolo, il sistema elimina tutti gli eventi la cui ora dell'evento è superiore all'ora di arrivo di oltre 5 minuti.

Questo concetto viene usato per garantire la ripetibilità dell'elaborazione indipendentemente dal punto di inizio dell'output. Senza questo meccanismo non sarebbe possibile garantire la ripetibilità, come invece pretendono di fare molti altri sistemi di streaming.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Effetti collaterali delle tolleranze di ordinamento temporale degli eventi

I processi di Analisi di flusso offrono diverse opzioni di **Ordinamento eventi**. Due di queste possono essere configurate nel portale di Azure, ossia **Eventi non in ordine** (tolleranza elementi non in ordine) e **Eventi pervenuti in ritardo** (tolleranza per arrivo in ritardo). La tolleranza per **arrivo in anticipo** è fissa e non può essere modificata. Questi criteri temporali vengono usati da Analisi di flusso per fornire solide garanzie. Queste impostazioni hanno tuttavia alcune implicazioni talvolta impreviste:

1. Invio accidentale di eventi troppo anticipati.

   Gli eventi anticipati non dovrebbero essere restituiti normalmente. Potrebbero però essere inviati all'output se l'orologio del mittente è troppo veloce. Tuti gli eventi che arrivano in anticipo vengono eliminati, quindi non compaiono nell'output.

2. Invio di eventi non recenti a Hub eventi perché vengano elaborati da Analisi di flusso di Azure.

   Benché possano apparire innocui a prima vista, a causa della tolleranza per arrivo in ritardo dell'applicazione, gli eventi non recenti possono essere eliminati. Se gli eventi sono troppo vecchi, il valore di **System.Timestamp** viene alterato durante l'inserimento degli eventi. A causa di questo comportamento, attualmente Analisi di flusso di Azure è più adatto per gli scenari di elaborazione degli eventi quasi in tempo reale, piuttosto che per quelli di elaborazione di eventi cronologici. Per ovviare a questo comportamento, in alcuni casi è possibile impostare il tempo di **Eventi pervenuti in ritardo** sul valore più grande possibile (20 giorni).

3. Si osserva un ritardo degli output.

   Il primo limite viene generato in base al calcolo del **tempo massimo dell'evento** osservato fino a questo momento dal sistema meno le dimensioni della finestra di tolleranza elementi non in ordine. Per impostazione predefinita, la tolleranza elementi non in ordine è configurata su zero (00 minuti e 00 secondi). Se la si imposta su un valore più alto, diverso da zero, il primo output del processo di streaming viene ritardato in base a quel valore temporale (o a un valore superiore) per via del tempo del primo limite calcolato.

4. Gli input sono di tipo sparse.

   Quando una determinata partizione è priva di input, il tempo del limite viene calcolato sottraendo all'**ora di arrivo** la finestra di tolleranza per arrivo in ritardo. Di conseguenza, se gli eventi di input sono poco frequenti e di tipo sparse, l'output può essere ritardato di quella quantità di tempo. Il valore predefinito di **Eventi pervenuti in ritardo** è 5 secondi. Ad esempio, un certo ritardo è normale quando si inviano eventi di input uno alla volta. I ritardi possono peggiorare quando si imposta la finestra **Eventi pervenuti in ritardo** su un valore elevato.

5. Il valore di **System.Timestamp** è diverso dal tempo indicato nel campo dell'**ora dell'evento**.

   Come descritto in precedenza, il sistema regola l'ora dell'evento in base alla finestra di tolleranza elementi non in ordine o alla finestra di tolleranza per arrivo in ritardo. Il valore di **System.Timestamp** viene modificato, ma non quello del campo dell'**ora dell'evento**.

## <a name="metrics-to-observe"></a>Metriche da osservare

È possibile osservare diversi effetti delle tolleranze di ordinamento temporale degli eventi tramite le [metriche dei processi di Analisi di flusso](stream-analytics-monitoring.md). Le metriche seguenti sono rilevanti:

|Metrica  | Descrizione  |
|---------|---------|
| **Eventi non in ordine** | Indica il numero di eventi ricevuti non in ordine, che sono stati eliminati o a cui è stato assegnato un timestamp modificato. Questa metrica è interessata direttamente dalla configurazione dell'impostazione **Eventi non in ordine** nella pagina **Ordinamento eventi** del processo nel portale di Azure. |
| **Ultimi eventi di input** | Indica il numero di eventi arrivati in ritardo dall'origine. Questa metrica include gli eventi che sono stati eliminati o di cui è stato modificato il timestamp. È interessata direttamente dalla configurazione dell'impostazione **Eventi pervenuti in ritardo** nella pagina **Ordinamento eventi** del processo nel portale di Azure. |
| **Eventi di input anticipati** | Indica il numero di eventi arrivati in anticipo dall'origine che sono stati eliminati o il cui timestamp è stato modificato se l'anticipo è superiore a 5 minuti. |
| **Ritardo limite** | Indica il ritardo del processo di elaborazione dati di streaming. Per altre informazioni, vedere la sezione seguente.|

## <a name="watermark-delay-details"></a>Dettagli della metrica Ritardo limite

La metrica **Ritardo limite** viene calcolata sottraendo al tempo del nodo di elaborazione il valore del limite più grande osservato fino a quel momento. Per altre informazioni, vedere il [post di blog sulla metrica Ritardo limite](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Il valore di questa metrica può essere superiore a 0 in condizioni normali per diversi motivi:

1. Ritardo di elaborazione inerente della pipeline di streaming. In genere questo ritardo è nominale.

2. Il ritardo è stato introdotto dalla finestra di tolleranza elementi non in ordine, in quanto il limite è ridotto in base alle dimensioni della finestra di tolleranza.

3. Il ritardo è stato introdotto dalla finestra di tolleranza per arrivo in ritardo, in quanto il limite è ridotto in base alle dimensioni della finestra di tolleranza.

4. Sfasamento orario del nodo di elaborazione che ha generato la metrica.

Esistono alcuni altri vincoli delle risorse che possono causare il rallentamento della pipeline di streaming. Il valore della metrica Ritardo limite può aumentare per i motivi seguenti:

1. Le risorse di elaborazione in Analisi di flusso non sono sufficienti per gestire il volume di eventi di input. Per aumentare le risorse, vedere [Informazioni sulle unità di flusso e su come modificarle](stream-analytics-streaming-unit-consumption.md).

2. La velocità effettiva nei gestori eventi di input non è sufficiente, pertanto vengono limitati. Per le possibili soluzioni, vedere [Aumentare automaticamente le unità elaborate di Hub eventi di Azure](../event-hubs/event-hubs-auto-inflate.md).

3. Il provisioning dei sink di output non viene effettuato con sufficiente capacità, pertanto vengano limitati. Le soluzioni possibili variano notevolmente a seconda della versione del servizio di output usato.

## <a name="output-event-frequency"></a>Frequenza degli eventi di output

Analisi di flusso di Azure usa lo stato del limite come unico trigger per produrre eventi di output. Essendo derivato dai dati di input, il limite è ripetibile durante il ripristino da errori e anche nella rielaborazione avviata dall'utente.

Quando si usano [funzioni di aggregazione finestra](stream-analytics-window-functions.md), il servizio produce output solo alla fine delle finestre. In alcuni casi gli utenti potrebbero avere l'esigenza di visualizzare le funzioni di aggregazione parziali generate dalle finestre. Le funzioni di aggregazione parziali non sono tuttavia attualmente supportate in Analisi di flusso di Azure.

In altre soluzioni di streaming gli eventi di output potrebbero materializzarsi in corrispondenza di vari punti di trigger, a seconda delle circostanze esterne. In alcune soluzioni è possibile che gli eventi di output per una determinata coppia di intervalli di tempo vengano generati più volte. Man mano che i valori di input vengono ridefiniti, i risultati aggregati diventano più precisi. Gli eventi potrebbero essere soggetti a un'iniziale previsione e quindi essere rivisti nel tempo. Ad esempio, quando un determinato dispositivo è offline, un sistema potrebbe usare un valore stimato. Quando, successivamente, il dispositivo torna online, i dati dell'evento effettivi potrebbero essere inclusi nel flusso di input. I risultati di output dell'elaborazione di tale intervallo di tempo producono un output più preciso.

## <a name="illustrated-example-of-watermarks"></a>Esempio illustrato di limiti

Le immagini seguenti illustrano lo stato di avanzamento dei limiti in diverse circostanze.

Questa tabella contiene i dati di esempio riportati nei grafici seguenti. Si noti che l'ora dell'evento e l'ora di arrivo cambiano: a volte coincidono, altre volte no.

| Ora dell'evento | Ora di arrivo | DeviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

In questa illustrazione vengono usate le tolleranze seguenti:

- La finestra di tolleranza per arrivo in anticipo è di 5 minuti
- La finestra di tolleranza per arrivo in ritardo è di 5 minuti
- La finestra di riordino è di 2 minuti

1. Illustrazione dell'avanzamento del limite attraverso questi eventi:

   ![Illustrazione del limite di Analisi di flusso di Azure](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Processi rilevanti illustrati nell'immagine precedente:

   1. Il primo evento (device1) e il secondo evento (device2) hanno ore allineate e vengono elaborati senza modifiche. Il limite avanza in ciascun evento.

   2. Quando viene elaborato il terzo evento (device1), l'ora di arrivo (12:11) è precedente all'ora dell'evento (12:17). L'evento è arrivato 6 minuti prima, quindi viene eliminato per via della tolleranza per arrivo in anticipo di 5 minuti.

      In questo caso di evento anticipato, il limite non avanza.

   3. Il quarto evento (device3) e il quinto evento (device1) hanno ore allineate e vengono elaborati senza modifiche. Il limite avanza in ciascun evento.

   4. Quando viene elaborato il sesto evento (device3), l'ora di arrivo (12:17) e l'ora dell'evento (12:12) sono sotto il livello limite. L'ora dell'evento viene regolata sul livello limite (12:17).

   5. Quando viene elaborato il nono evento (device3), l'ora di arrivo (12:27) è di 6 minuti successiva all'ora dell'evento (12:21). Viene applicato il criterio di arrivo in ritardo. L'ora dell'evento viene modificata (12:22) e, poiché il nuovo valore è sopra il limite (12:21), non vengono applicate ulteriori modifiche.

2. Seconda illustrazione dell'avanzamento del limite senza un criterio di arrivo in anticipo:

   ![Illustrazione del limite di Analisi di flusso di Azure senza criterio di arrivo in anticipo](media/stream-analytics-time-handling/watermark-graph-2.png)

   In questo esempio non viene applicato alcun criterio di arrivo in anticipo. Gli eventi outlier che arrivano in anticipo aumentano notevolmente il limite. Si noti che il terzo evento (device1 alle 12:11) non viene eliminato in questo scenario e il limite viene alzato a 12:15. Come risultato, il quarto evento viene regolato in avanti di 7 minuti (dalle 12:08 alle 12:15).

3. Nell'ultima illustrazione vengono usati i flussi secondari (SU DeviceId). Vengono tracciati più limiti, uno per flusso. Di conseguenza, il numero di eventi di cui viene modificata l'ora è minore.

   ![Illustrazione del limite di Analisi di flusso di Azure con flussi secondari](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Passaggi successivi

- [Considerazioni sull'ordine degli eventi con Analisi di flusso di Azure](stream-analytics-out-of-order-and-late-events.md)
- [Informazioni sul monitoraggio dei processi di Analisi di flusso e su come monitorare le query](stream-analytics-monitoring.md)
