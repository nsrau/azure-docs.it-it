---
title: Ottimizzazione del download di file di grandi dimensioni mediante la rete CDN di Azure
description: Approfondimento dell'ottimizzazione del download di file di grandi dimensioni
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 27e202b05f86eeee7071f3fae145caeba3d66827
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---
# <a name="large-file-download-optimization-via-azure-cdn"></a>Ottimizzazione del download di file di grandi dimensioni mediante la rete CDN di Azure

La dimensione di file dei contenuti pubblicati su Internet è aumentata costantemente come conseguenza delle funzionalità avanzate, della grafica migliorata e dei contenuti multimediali ricchi. Questo ha origine da numerosi fattori, tra cui la penetrazione della banda larga, dispositivi di archiviazione più grandi ed economici, proliferazione dei video in alta definizione, dispositivi connessi a Internet (IoT) e così via.  Fornire un meccanismo di recapito più veloce ed efficiente per questi file di grandi dimensioni è fondamentale per garantire un'esperienza utente uniforme e semplice. 

La distribuzione di file di grandi dimensioni comporta diverse sfide. In primo luogo il tempo medio per scaricare un file di grandi dimensioni può essere significativo e molte applicazioni non possono scaricare in modo sequenziale tutti i dati. In alcuni casi le applicazioni potrebbero scaricare l'ultima parte di un file prima della prima. Pertanto, quando è richiesta solo una piccola parte di un file o l'utente mette in pausa il download, il download può non andare a buon fine o essere ritardato fino a dopo che l'intero file è stato recuperato dall'origine attraverso la rete CDN. 

In secondo luogo, con l'aumentare del numero di file di grandi dimensioni su Internet, gli utenti spesso osservano che in definitiva è la latenza tra l'utente e il file a determinare la velocità effettiva o la velocità con cui gli utenti possono visualizzare il contenuto. Inoltre, anche i problemi di congestione e capacità della rete influiscono sulla velocità effettiva e questi problemi, associati con la maggiore distanza tra il server e l'utente finale, crea ulteriori opportunità di perdita di pacchetti, riducendo ulteriormente la qualità. La riduzione della qualità causata dalla velocità effettiva limitata e dalla maggiore perdita di pacchetti può manifestarsi in un tempo di attesa notevolmente maggiore per il completamento del download di un file. 

Infine, molti file di grandi dimensioni non vengono recapitati interamente. Gli utenti possono annullare un download a metà o guardare solo i primi minuti di un video MP4 lungo. Pertanto è utile, per molte aziende di software e provider di contenuti multimediali, recapitare solo la parte di un file richiesta dall'utente finale. In questo modo solo le parti richieste vengono distribuite in modo efficiente ai destinatari più lontani su Internet, riducendo il traffico in uscita dall'origine e di conseguenza la pressione sulla memoria e dull'IO nel server di origine. 

La rete CDN di Azure di Akamai offre ora una funzionalità finalizzata a fornire file di grandi dimensioni in modo efficiente agli utenti finali in tutto il mondo su larga scala e con bassa latenza, riducendo al contempo il carico sul server di origine. Questa caratteristica è disponibile tramite la funzionalità "Ottimizzato per" sull'endpoint della rete CDN di Azure creata in un profilo di rete CDN di Azure con il piano tariffario "Standard Akamai".

## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configurazione dell'endpoint della rete CDN per ottimizzare il recapito dei file di grandi dimensioni

È possibile configurare l'endpoint della rete CDN in modo da ottimizzare il recapito dei file di grandi dimensioni tramite il portale di Azure, selezionando l'opzione "Download di file di grandi dimensioni" sotto la selezione per la proprietà "Ottimizzato per" durante la creazione dell'endpoint. A tale scopo è anche possibile usare le API REST o qualsiasi SDK client. Le schermate riportate di seguito illustrano il processo tramite il portale di Azure.

![Nuovo endpoint della rete CDN](./media/cdn-large-file-optimization/01_Adding.png)  
 
*Figura 1: Aggiunta di un nuovo endpoint della rete CDN dal profilo della rete CDN*
 
![LFO selezionato](./media/cdn-large-file-optimization/02_Creating.png)

*Figura 2: Creazione di un endpoint della rete CDN con l'opzione per l'ottimizzazione del download dei file di grandi dimensioni selezionata*

Una volta creato l'endpoint della rete CDN, applicherà le ottimizzazioni per i file di grandi dimensioni per tutti i file che corrispondono a determinati criteri. La sezione seguente descrive questo aspetto in modo dettagliato.

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-akamai"></a>Ottimizzazione per la distribuzione dei file di grandi dimensioni con la rete CDN di Azure di Akamai

Per la rete CDN di Azure di Akamai è possibile usare la funzionalità di ottimizzazione per i file di grandi dimensioni per attivare le ottimizzazioni e le configurazioni di rete che consentono di recapitare i file di grandi dimensioni in modo più rapido e dinamico. Il recapito Web generico con Akamai è in grado di memorizzare in cache soltanto i file minori di 1,8 GB e di eseguire il tunnelling (non la memorizzazione in cache) dei file fino a 150 GB, mentre l'ottimizzazione dei file di grandi dimensioni consente di memorizzare nella cache i file fino a 150 GB.

L'ottimizzazione dei file di grandi dimensioni viene applicata quando sono soddisfatte determinate condizioni riguardanti il funzionamento del server di origine, i tipi di file richiesti e la dimensione dei file richiesti. Prima di entrare nei dettagli di ognuno di questi aspetti, è importante conoscere una panoramica generale del funzionamento dell'ottimizzazione. 

### <a name="object-chunking"></a>Suddivisione degli oggetti in blocchi 

La rete CDN di Azure di Akamai usa una tecnica di suddivisione degli oggetti in blocchi in cui la rete CDN recupera piccole parti del file dall'origine quando viene richiesto un file di grandi dimensioni. Quando il server CDN perimetrale/POP riceve una richiesta di file completa o espressa come intervallo di byte da un utente finale, verifica innanzitutto se il tipo di file appartiene all'elenco dei tipi di file supportati per l'ottimizzazione e se soddisfa i requisiti di dimensione. Se la dimensione del file è maggiore di 10 MB, viene avviato il server perimetrale della rete CDN che richiede il file dal server di origine in blocchi di 2 MB. Quando il blocco arriva al perimetro della rete CDN, viene memorizzato nella cache e viene immediatamente servito all'utente finale, mentre la rete CDN "pre-recupera" il blocco successivo in parallelo. Questo "pre-recupero" assicura che il contenuto sia disponibile più rapidamente, rimanendo avanti di un blocco rispetto all'utente e riducendo la latenza verso l'utente finale. Questo processo continua finché l'intero file viene scaricato (se l'utente finale ha richiesto l'intero file), fino a quando non sono disponibili tutti gli intervalli di byte richiesti (se l'utente finale ha richiesto intervalli di byte) o il client termina la connessione. 

I dettagli della richiesta di intervalli di byte sono reperibili nella [RFC 7233](https://tools.ietf.org/html/rfc7233).

La rete CDN memorizzerà nella cache tutti i blocchi non appena vengono ricevuti e non richiede che l'intero file sia memorizzato nella cache della rete CDN. Le richieste successive per file o intervalli di byte verranno servite dalla cache della rete CDN e sarà utilizzato il "pre-recupero" per richiedere i blocchi dall'origine, se non tutti i blocchi sono memorizzati nella cache della rete CDN. Come si può notare, questa ottimizzazione fa affidamento sul fatto che il server di origine supporti le richieste di intervalli di byte. _Se il server di origine non supporta le richieste di intervalli di byte, questa ottimizzazione non sarà operativa._ 

### <a name="caching"></a>Memorizzazione nella cache
I file di grandi dimensioni usano tempi di scadenza della cache predefiniti diversi rispetto al recapito generico. Differenzia la memorizzazione nella cache positiva e negativa in base ai codici di risposta HTTP. Se l'origine specifica un'ora utilizzando un'ora di scadenza tramite l'intestazione Cache-Control o Expires nella risposta, la rete CDN rispetterà sempre tale valore. Quando l'origine non specificata nulla e il file soddisfa le condizioni riguardanti il tipo e la dimensione del file per questo tipo di ottimizzazione, la rete CDN utilizzerà i valori predefiniti per l'ottimizzazione dei file di grandi dimensioni. In caso contrario, la rete CDN utilizzerà i valori predefiniti per il recapito Web generico.

 
|    | Web generico | Ottimizzazione dei file di grandi dimensioni 
--- | --- | --- 
Memorizzazione nella cache - Positiva <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 giorni |1 giorno  
Memorizzazione nella cache - Negativa <br> HTTP 204, 305, 404, <br> e 405 | Nessuno | 1 secondo 

### <a name="dealing-with-origin-failure"></a>Gestione degli errori di origine

Nel tipo di ottimizzazione dei file di grandi dimensioni, la durata del timeout di lettura originale aumenta dai 2 secondi del recapito Web generico a 2 minuti, per tenere conto della maggiore dimensione, in modo da non mandare in timeout la connessione prematuramente.

Come nel recapito Web generico, quando una connessione va in timeout, l'operazione viene ritentata un determinato numero di volte prima dell'invio dell'errore di timeout gateway 504 al client. 

### <a name="conditions-for-large-file-optimization"></a>Condizioni per l'ottimizzazione dei file di grandi dimensioni

La tabella seguente elenca il set di criteri che devono essere soddisfatti per l'ottimizzazione dei file di grandi dimensioni:

Condizione | Valori 
--- | --- 
Tipi di file supportati | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Minima dimensione di file | 10 MB 
Massima dimensione di file | 150 GB 
Caratteristiche del server di origine | Deve supportare le richieste di intervalli di byte 

## <a name="optimizing-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Ottimizzazione per il recapito dei file di grandi dimensioni con la rete CDN di Azure di Akamai

La rete CDN di Azure di Verizon è in grado di recapitare file di grandi dimensioni senza un limite sulla dimensione del file e dispone di varie funzionalità, attivate per impostazione predefinita, che rendono più veloce il recapito dei file di grandi dimensioni.

### <a name="complete-cache-fill"></a>Completa riempimento cache

La rete CDN di Azure di Verizon include una caratteristica predefinita, chiamata riempimento completo della cache, in cui la rete CDN esegue il pull di un file nella cache quando la richiesta iniziale viene abbandonata o persa. 

Questa funzionalità è particolarmente utile per gli asset di grandi dimensioni che gli utenti in genere non scaricano per intero (ad esempio i video con download progressivo). Ecco perché questa funzionalità è abilitata per impostazione predefinita con la rete CDN di Azure di Verizon. Questo comportamento predefinito prevede di obbligare il server periferico a inizializzare il recupero in background dell'asset dal server di origine. Al termine di questa operazione, l'asset si trova nella cache locale del server perimetrale. Una volta che l'oggetto completo è nella cache, il dispositivo perimetrale può soddisfare le richieste di intervalli di byte per la rete CDN per l'oggetto memorizzato nella cache.

È possibile disabilitare il comportamento predefinito di riempimento completo della cache tramite il motore delle regole nel livello Verizon Premium.

### <a name="peer-cache-fill-hotfiling"></a>Hotfiling del riempimento della cache peer

Questa è una funzionalità predefinita della rete CDN di Azure di Verizon, in cui un sofisticato algoritmo proprietario può sfruttare server di cache periferici aggiuntivi in base a metriche come la larghezza di banda e le richieste aggregate, per soddisfare le richieste client per oggetti di grandi dimensioni molto popolari. Ciò impedisce che si verifichino situazioni in cui un numero elevato di richieste aggiuntive viene inviato al server di origine di un cliente. 

### <a name="conditions-for-large-file-optimization"></a>Condizioni per l'ottimizzazione dei file di grandi dimensioni

Le funzionalità di ottimizzazione per Verizon sono attivate per impostazione predefinita e non esistono limiti alle dimensioni massime del file. 

## <a name="additional-considerations"></a>Considerazione aggiuntive

Quando si usa questo tipo di ottimizzazione, esistono alcuni aspetti aggiuntivi da considerare.
 
### <a name="azure-cdn-from-akamai"></a>Rete CDN di Azure di Akamai

- Il processo di suddivisione in blocchi genera richieste aggiuntive al server di origine, ma il numero complessivo dei dati recapitati dall'origine sarà molto inferiore, poiché la suddivisione in blocchi migliora le caratteristiche di memorizzazione nella cache per la rete CDN.
- Ci sarà anche un altro vantaggio dato dalla minore pressione sulla memoria e sull'IO dell'origine, grazie al recapito di parti più piccole del file. 
- Per i blocchi che vengono memorizzati nella cache nella rete CDN non ci saranno richieste aggiuntive all'origine finché il contenuto non scadrà dalla cache o verrà rimosso dalla cache per altri motivi. 
- L'utente può effettuare richieste di intervallo alla rete CDN e saranno trattate come qualsiasi normale file. L'ottimizzazione verrà applicata solo se il tipo di file è valido e l'intervallo di byte è compreso tra 10 e 150 GB. Se la dimensiona media dei file richiesta è minore di 10 MB, è preferibile usare il recapito Web generico.

### <a name="azure-cdn-from-verizon"></a>Rete CDN di Azure di Verizon

L'ottimizzazione del recapito Web generico è in grado di recapitare file di grandi dimensioni.

