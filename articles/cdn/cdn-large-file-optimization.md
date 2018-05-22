---
title: Ottimizzazione del download di file di grandi dimensioni mediante la rete CDN di Azure
description: Questo articolo descrive come ottimizzare i download di file di grandi dimensioni.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: v-deasim
ms.openlocfilehash: 2bdb6bdea7b6180e34458883d026161403e4cb58
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Ottimizzazione del download di file di grandi dimensioni mediante la rete CDN di Azure

Le dimensioni file dei contenuti distribuiti su Internet continuano ad aumentare come conseguenza delle funzionalità avanzate, della grafica migliorata e della complessità dei contenuti multimediali. Questa crescita ha origine da numerosi fattori, tra cui la penetrazione della banda larga, i dispositivi di archiviazione più grandi ed economici, la maggiore diffusione di video in alta definizione e di dispositivi connessi a Internet (IoT). Un meccanismo di distribuzione veloce ed efficiente per i file di grandi dimensioni è fondamentale per garantire un'esperienza utente uniforme e piacevole.

La distribuzione di file di grandi dimensioni pone diverse sfide. In primo luogo, il tempo medio per scaricare un file di grandi dimensioni può essere significativo, in quanto molte applicazioni potrebbero non scaricare tutti i dati in sequenza. In alcuni casi, le applicazioni potrebbero scaricare l'ultima parte di un file prima della prima. Quando è necessaria solo una piccola parte di un file o un utente sospende un download, il download può non riuscire. Il download può anche subire ritardi, finché la rete per la distribuzione di contenuti (rete CDN) non recupera l'intero file dal server di origine. 

In secondo luogo, la latenza tra il computer di un utente e il file determina la velocità a cui l'utente può visualizzare il contenuto. Inoltre, problemi di congestione e capacità di rete possono influire sulla velocità effettiva. Distanze maggiori tra server e utenti creano rischi aggiuntivi di perdita dei pacchetti, riducendo la qualità. La qualità inferiore causata dalla velocità effettiva limitata e dalla crescente perdita di pacchetti può comportare tempi di attesa notevolmente superiori per il completamento del download di un file. 

In terzo luogo, molti file di grandi dimensioni non vengono distribuiti interamente. Gli utenti potrebbero annullare un download a metà o guardare solo i primi minuti di un lungo video MP4. Di conseguenza, per molte aziende di distribuzione di software e contenuti multimediali può essere utile distribuire solo la parte richiesta di un file. Una distribuzione efficiente delle parti richieste riduce il traffico in uscita dal server di origine. Una distribuzione efficiente riduce anche la pressione in termini di memoria e I/O sul server di origine. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Ottimizzazione per la distribuzione di file di grandi dimensioni con la rete CDN di Azure

Gli endpoint **della rete CDN Standard di Azure di Microsoft** distribuiscono i file di grandi dimensioni senza porre limiti sulle dimensioni dei file. Altre funzionalità sono attivate per impostazione predefinita per accelerare la distribuzione di file di grandi dimensioni.

### <a name="object-chunking"></a>Suddivisione degli oggetti in blocchi 

**La rete CDN Standard di Azure di Microsoft** usa una tecnica denominata suddivisione di oggetti in blocchi. Quando viene richiesto un file di grandi dimensioni, la rete CDN recupera piccole parti del file dall'origine. Dopo che il server POP della rete CDN riceve la richiesta di un file completo o di un intervallo di byte, il server perimetrale della rete CDN richiede il file dall'origine in blocchi da 8 MB. 

Quando il blocco arriva al server perimetrale della rete CDN, viene memorizzato nella cache e reso immediatamente disponibile all'utente. La rete CDN esegue la prelettura del blocco successivo in parallelo. Questa prelettura fa sì che il contenuto resti in anticipo di un blocco rispetto all'utente, riducendo la latenza. Questo processo continua finché non viene scaricato l'intero file (se richiesto), non sono disponibili tutti gli intervalli di byte (se richiesto) o il client non termina la connessione. 

Per altre informazioni sulla richiesta di intervalli di byte, vedere [RFC 7233](https://tools.ietf.org/html/rfc7233).

La rete CDN memorizza nella cache tutti i blocchi alla loro ricezione. Il file non deve essere necessariamente memorizzato interamente nella cache della rete CDN. Le richieste successive del file o di intervalli di byte vengono soddisfatte dalla cache della rete CDN. Se non tutti i blocchi vengono memorizzati nella cache della rete CDN, viene usata la prelettura per richiedere i blocchi dall'origine. Questa ottimizzazione si basa sulla capacità del server di origine di supportare le richieste di intervallo di byte. Se il server di origine non supporta le richieste di intervallo di byte, questa ottimizzazione non è efficace. 

### <a name="conditions-for-large-file-optimization"></a>Condizioni per l'ottimizzazione dei file di grandi dimensioni
Le funzionalità di ottimizzazione dei file di grandi dimensioni per la **rete CDN Standard di Azure di Microsoft** sono attivate per impostazione predefinita quando si usa il tipo di ottimizzazione della distribuzione Web generale. Non viene applicato alcun limite per le dimensioni massime dei file.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Ottimizzazione per la distribuzione di file di grandi dimensioni con la rete CDN di Azure di Verizon

Gli endpoint della **rete CDN Standard di Azure di Verizon** e della **rete CDN Premium di Azure di Verizon** distribuiscono i file di grandi dimensioni senza porre limiti sulle dimensioni dei file. Altre funzionalità sono attivate per impostazione predefinita per accelerare la distribuzione di file di grandi dimensioni.

### <a name="complete-cache-fill"></a>Riempimento completo della cache

La funzionalità predefinita di riempimento completo della cache permette alla rete CDN di eseguire il pull di un file nella cache quando la richiesta iniziale viene abbandonata o persa. 

Il riempimento completo della cache è particolarmente utile per asset di grandi dimensioni. In genere, gli utenti non scaricano questi asset dall'inizio alla fine, ma usano il download progressivo. Il comportamento predefinito forza il server perimetrale ad avviare un recupero in background dell'asset dal server di origine. Al termine di questa operazione, l'asset si trova nella cache locale del server perimetrale. Quando l'oggetto si trova interamente nella cache, il server perimetrale può soddisfare le richieste di intervalli di byte alla rete CDN per l'oggetto memorizzato nella cache.

Il comportamento predefinito può essere disattivato attraverso il motore delle regole nella **rete CDN Premium di Azure di Verizon**.

### <a name="peer-cache-fill-hot-filing"></a>Hotfiling del riempimento della cache peer

Questa funzionalità predefinita usa un sofisticato algoritmo proprietario. La funzionalità usa server perimetrali aggiuntivi per la memorizzazione nella cache in base alla larghezza di banda e ad altre metriche delle richieste aggregate per soddisfare le richieste client di oggetti di grandi dimensioni e usati spesso. Questa funzionalità previene situazioni in cui un numero elevato di richieste aggiuntive viene inviato al server di origine di un utente. 

### <a name="conditions-for-large-file-optimization"></a>Condizioni per l'ottimizzazione dei file di grandi dimensioni

Le funzionalità di ottimizzazione dei file di grandi dimensioni per la **rete CDN Standard di Azure di Verizon** e la **rete CDN Premium di Azure di Verizon** sono attivate per impostazione predefinita quando si usa il tipo di ottimizzazione della distribuzione Web generale. Non viene applicato alcun limite per le dimensioni massime dei file. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Ottimizzazione per la distribuzione dei file di grandi dimensioni con la rete CDN Standard di Azure di Akamai

Gli endpoint della **rete CDN Standard di Azure di Akamai** offrono una funzionalità che consente di distribuire in modo efficiente e scalabile file di grandi dimensioni agli utenti di tutto il mondo. Questa funzionalità riduce le latenze perché riduce il carico sui server di origine.

La funzionalità di ottimizzazione per file di grandi dimensioni attiva le ottimizzazioni e le configurazioni di rete per consentire la distribuzione di file di grandi dimensioni più rapidamente e tempestivamente. La distribuzione Web generale con gli endpoint della **rete CDN Standard di Azure di Akamai** memorizza nella cache solo i file con dimensioni inferiori a 1,8 GB e può effettuare il tunneling (senza memorizzazione nella cache) di file di dimensioni fino a 150 GB. L'ottimizzazione per file di grandi dimensioni memorizza nella cache file di dimensioni fino a 150 GB.

Questa ottimizzazione è efficace in determinate condizioni. Le condizioni includono il modo in cui opera il server di origine e le dimensioni e i tipi dei file richiesti. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configurare un endpoint della rete CDN di Akamai per ottimizzare la distribuzione di file di grandi dimensioni

È possibile configurare l'endpoint della **rete CDN Standard di Azure di Akamai** per ottimizzare la distribuzione di file di grandi dimensioni tramite il portale di Azure. A questo scopo, è anche possibile usare le API REST o qualsiasi SDK client. I passaggi seguenti mostrano il processo tramite il portale di Azure per un profilo di **rete CDN Standard di Azure di Akamai**:

1. Per aggiungere un nuovo endpoint, nella pagina **Profilo CDN** di Akamai selezionare **Endpoint**.

    ![Nuovo endpoint](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Nell'elenco a discesa **Ottimizzato per** selezionare **Download di file di grandi dimensioni**.

    ![Selezione dell'ottimizzazione per file di grandi dimensioni](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Dopo aver creato l'endpoint di rete CDN, a tutti i file che soddisfano determinati criteri viene applicata l'ottimizzazione per file di grandi dimensioni. La sezione seguente descrive questo processo.

### <a name="object-chunking"></a>Suddivisione degli oggetti in blocchi 

L'ottimizzazione dei file di grandi dimensioni con la **rete CDN Standard di Azure di Akamai** usa una tecnica denominata suddivisione di oggetti in blocchi. Quando viene richiesto un file di grandi dimensioni, la rete CDN recupera piccole parti del file dall'origine. Dopo che il server POP della rete CDN riceve la richiesta di un file completo o di un intervallo di byte, controlla se il tipo di file è supportato per l'ottimizzazione. Controlla inoltre che il tipo di file soddisfi i requisiti di dimensioni dei file. Se il file ha dimensioni maggiori di 10 MB, il server perimetrale della rete CDN richiede il file dall'origine in blocchi di 2 MB. 

Quando il blocco arriva al server perimetrale della rete CDN, viene memorizzato nella cache e reso immediatamente disponibile all'utente. La rete CDN esegue la prelettura del blocco successivo in parallelo. Questa prelettura fa sì che il contenuto resti in anticipo di un blocco rispetto all'utente, riducendo la latenza. Questo processo continua finché non viene scaricato l'intero file (se richiesto), non sono disponibili tutti gli intervalli di byte (se richiesto) o il client non termina la connessione. 

Per altre informazioni sulla richiesta di intervalli di byte, vedere [RFC 7233](https://tools.ietf.org/html/rfc7233).

La rete CDN memorizza nella cache tutti i blocchi alla loro ricezione. Il file non deve essere necessariamente memorizzato interamente nella cache della rete CDN. Le richieste successive del file o di intervalli di byte vengono soddisfatte dalla cache della rete CDN. Se non tutti i blocchi vengono memorizzati nella cache della rete CDN, viene usata la prelettura per richiedere i blocchi dall'origine. Questa ottimizzazione si basa sulla capacità del server di origine di supportare le richieste di intervallo di byte. Se il server di origine non supporta le richieste di intervallo di byte, questa ottimizzazione non è efficace.

### <a name="caching"></a>Memorizzazione nella cache
L'ottimizzazione per file di grandi dimensioni usa tempi di scadenza della memorizzazione nella cache predefiniti diversi rispetto alla distribuzione Web generica. Differenzia la memorizzazione nella cache positiva e negativa in base ai codici di risposta HTTP. Se il server di origine specifica una scadenza tramite l'intestazione Cache-Control o Expires nella risposta, la rete CDN rispetta questo valore. Quando l'origine non specifica alcuna scadenza e il file soddisfa le condizioni di tipo e dimensioni per questo tipo di ottimizzazione, la rete CDN usa i valori predefiniti per l'ottimizzazione per file di grandi dimensioni. In caso contrario, la rete CDN usa i valori predefiniti per la distribuzione Web generica.


|    | Distribuzione Web generica | Ottimizzazione per file di grandi dimensioni 
--- | --- | --- 
Memorizzazione nella cache: positiva <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 giorni |1 giorno  
Memorizzazione nella cache: negativa <br> HTTP 204, 305, 404, <br> e 405 | Nessuna | 1 secondo 

### <a name="deal-with-origin-failure"></a>Gestire gli errori di origine

La durata del timeout di lettura dell'origine aumenta da due secondi per la distribuzione Web generica a due minuti per il tipo di ottimizzazione per file di grandi dimensioni. Questo aumento è dovuto alle dimensioni di file maggiori, per evitare il timeout prematuro della connessione.

Quando si verifica il timeout di una connessione, la rete CDN esegue un certo numero di tentativi prima di inviare l'errore "504 - Timeout gateway" al client. 

### <a name="conditions-for-large-file-optimization"></a>Condizioni per l'ottimizzazione dei file di grandi dimensioni

La tabella seguente elenca il set di criteri che devono essere soddisfatti per l'ottimizzazione dei file di grandi dimensioni:

Condizione | Valori 
--- | --- 
Tipi di file supportati | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, tar, <br> tgz, wdp, webm, webp, wma, wmv, zip  
Dimensione minima dei file | 10 MB 
Dimensione massima dei file | 150 GB 
Caratteristiche del server di origine | Deve supportare richieste di intervalli di byte 

## <a name="additional-considerations"></a>Ulteriori considerazioni

Per questo tipo di ottimizzazione, tenere presenti gli aspetti aggiuntivi seguenti:

- Il processo di suddivisione in blocchi genera richieste aggiuntive al server di origine. Tuttavia, il volume complessivo dei dati distribuiti dall'origine è notevolmente inferiore. La suddivisione in blocchi assicura caratteristiche di memorizzazione nella cache migliori nella rete CDN.

- La pressione in termini di memoria e I/O risulta ridotta nell'origine, perché vengono distribuite piccole parti del file.

- Per i blocchi memorizzati nella cache nella rete CDN non vi sono richieste aggiuntive all'origine fino alla scadenza del contenuto o finché il contenuto non viene rimosso dalla cache.

- Gli utenti possono effettuare richieste di intervalli di byte alla rete CDN, che vengono considerate normali richieste di file. L'ottimizzazione viene applicata solo se il tipo di file è valido e l'intervallo di byte è compreso tra 10 MB e 150 GB. Se la dimensione media dei file richiesta è inferiore a 10 MB, è preferibile usare la distribuzione Web generale.

