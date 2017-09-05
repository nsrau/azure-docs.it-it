---
title: Ottimizzazione del download di file di grandi dimensioni tramite la rete di distribuzione dei contenuti di Azure
description: Descrizione approfondita dell'ottimizzazione dei download di file di grandi dimensioni
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
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 7a5d5d1d0de24ebb0a5115ede1e572f38454bd78
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="large-file-download-optimization-via-the-azure-content-delivery-network"></a>Ottimizzazione del download di file di grandi dimensioni tramite la rete di distribuzione dei contenuti di Azure

Le dimensioni di file dei contenuti distribuiti in Internet continua ad aumentare come conseguenza delle funzionalità avanzate, della grafica migliorata e della complessità dei contenuti multimediali. Questa crescita è determinata da diversi fattori: penetrazione della banda larga, dispositivi di archiviazione economici e con capacità maggiore, elevata diffusione di video ad alta definizione e dispositivi connessi a Internet (IoT). Un meccanismo di distribuzione rapido ed efficiente per file di grandi dimensioni è essenziale per garantire a ogni cliente un'esperienza uniforme e soddisfacente.

La distribuzione di file di grandi dimensioni pone diverse sfide. In primo luogo, il tempo medio per scaricare un file di grandi dimensioni può essere significativo, in quanto molte applicazioni potrebbero non scaricare tutti i dati in sequenza. In alcuni casi, le applicazioni potrebbero scaricare l'ultima parte di un file prima della prima. Quando è necessaria solo una piccola parte di un file o un utente sospende un download, il download può non riuscire. Il download può anche subire ritardi, finché la rete per la distribuzione di contenuti (rete CDN) non recupera l'intero file dal server di origine. 

In secondo luogo, la latenza tra il computer di un utente e il file determina la velocità a cui l'utente può visualizzare il contenuto. Inoltre, problemi di congestione e capacità di rete possono influire sulla velocità effettiva. Distanze maggiori tra server e utenti creano rischi aggiuntivi di perdita dei pacchetti, riducendo la qualità. La qualità inferiore causata dalla velocità effettiva limitata e dalla crescente perdita di pacchetti può comportare tempi di attesa notevolmente superiori per il completamento del download di un file. 

In terzo luogo, molti file di grandi dimensioni non vengono distribuiti interamente. Gli utenti potrebbero annullare un download a metà o guardare solo i primi minuti di un lungo video MP4. Di conseguenza, per molte aziende di distribuzione di software e contenuti multimediali può essere utile distribuire solo la parte richiesta di un file. Una distribuzione efficiente delle parti richieste riduce il traffico in uscita dal server di origine. Una distribuzione efficiente riduce anche la pressione in termini di memoria e I/O sul server di origine. 

La rete di distribuzione dei contenuti di Azure con tecnologia Akamai offre ora una funzionalità che permette di distribuire in modo efficiente e scalabile file di grandi dimensioni agli utenti di tutto il mondo. Questa funzionalità riduce le latenze perché riduce il carico sui server di origine. Questa funzionalità è disponibile con il piano tariffario Standard di Akamai.

## <a name="configure-a-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Configurare un endpoint di rete CDN per ottimizzare la distribuzione di file di grandi dimensioni

È possibile configurare l'endpoint di rete CDN per ottimizzare la distribuzione di file di grandi dimensioni tramite il portale di Azure. A tale scopo è anche possibile usare le API REST o qualsiasi SDK client. I passaggi seguenti mostrano il processo tramite il portale di Azure.

1. Per aggiungere un nuovo endpoint, nella pagina **Profilo CDN** selezionare **Endpoint**.

    ![Nuovo endpoint](./media/cdn-large-file-optimization/01_Adding.png)  
 
2. Nell'elenco a discesa **Ottimizzato per** selezionare **Download di file di grandi dimensioni**.

    ![Selezione dell'ottimizzazione per file di grandi dimensioni](./media/cdn-large-file-optimization/02_Creating.png)


Dopo aver creato l'endpoint di rete CDN, a tutti i file che soddisfano determinati criteri viene applicata l'ottimizzazione per file di grandi dimensioni. La sezione seguente descrive questo processo.

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-akamai"></a>Ottimizzare la distribuzione di file di grandi dimensioni con la rete di distribuzione dei contenuti di Azure con tecnologia Akamai

La funzionalità di ottimizzazione per file di grandi dimensioni attiva le ottimizzazioni e le configurazioni di rete per consentire la distribuzione di file di grandi dimensioni più rapidamente e tempestivamente. La distribuzione Web generica con Akamai memorizza nella cache solo i file con dimensioni inferiori a 1,8 GB e può effettuare il tunneling (senza memorizzazione nella cache) dei file di dimensioni fino a 150 GB. L'ottimizzazione per file di grandi dimensioni memorizza nella cache file di dimensioni fino a 150 GB.

Questa ottimizzazione è efficace in determinate condizioni. Le condizioni includono il modo in cui opera il server di origine e le dimensioni e i tipi dei file richiesti. Prima di affrontare dettagliatamente questi argomenti, è utile comprendere il funzionamento dell'ottimizzazione. 

### <a name="object-chunking"></a>Suddivisione degli oggetti in blocchi 

La rete di distribuzione dei contenuti di Azure con tecnologia Akamai usa una tecnica chiamata suddivisione degli oggetti in blocchi. Quando viene richiesto un file di grandi dimensioni, la rete CDN recupera piccole parti del file dall'origine. Dopo che il server perimetrale/POP della rete CDN riceve la richiesta di un file completo o di un intervallo di byte, controlla se il tipo di file è supportato per l'ottimizzazione. Controlla inoltre che il tipo di file soddisfi i requisiti di dimensioni dei file. Se il file ha dimensioni maggiori di 10 MB, il server perimetrale della rete CDN richiede il file dall'origine in blocchi di 2 MB. 

Quando il blocco arriva al server perimetrale della rete CDN, viene memorizzato nella cache e reso immediatamente disponibile all'utente. La rete CDN esegue la prelettura del blocco successivo in parallelo. Questa prelettura fa sì che il contenuto resti in anticipo di un blocco rispetto all'utente, riducendo la latenza. Questo processo continua finché non viene scaricato l'intero file (se richiesto), non sono disponibili tutti gli intervalli di byte (se richiesto) o il client non termina la connessione. 

Per altre informazioni sulla richiesta di intervalli di byte, vedere [RFC 7233](https://tools.ietf.org/html/rfc7233).

La rete CDN memorizza nella cache tutti i blocchi alla loro ricezione. Il file non deve essere memorizzato interamente nella cache della rete CDN. Le richieste successive del file o di intervalli di byte vengono soddisfatte dalla cache della rete CDN. Se non tutti i blocchi vengono memorizzati nella cache della rete CDN, viene usata la prelettura per richiedere i blocchi dall'origine. Questa ottimizzazione presuppone il fatto che il server di origine supporti le richieste di intervalli di byte. _Se il server di origine non supporta le richieste di intervalli di byte, questa ottimizzazione non è efficace._ 

### <a name="caching"></a>Memorizzazione nella cache
L'ottimizzazione per file di grandi dimensioni usa tempi di scadenza della memorizzazione nella cache predefiniti diversi rispetto alla distribuzione Web generica. Differenzia la memorizzazione nella cache positiva e negativa in base ai codici di risposta HTTP. Se il server di origine specifica una scadenza tramite l'intestazione Cache-Control o Expires nella risposta, la rete CDN rispetta questo valore. Quando l'origine non specifica alcuna scadenza e il file soddisfa le condizioni di tipo e dimensioni per questo tipo di ottimizzazione, la rete CDN usa i valori predefiniti per l'ottimizzazione per file di grandi dimensioni. In caso contrario, la rete CDN usa i valori predefiniti per la distribuzione Web generica.


|    | Distribuzione Web generica | Ottimizzazione per file di grandi dimensioni 
--- | --- | --- 
Memorizzazione nella cache: positiva <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 giorni |1 giorno  
Memorizzazione nella cache: negativa <br> HTTP 204, 305, 404, <br> e 405 | None | 1 secondo 

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

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-verizon"></a>Ottimizzare la distribuzione di file di grandi dimensioni con la rete di distribuzione dei contenuti di Azure con tecnologia Verizon

La rete di distribuzione dei contenuti di Azure con tecnologia Verizon distribuisce file di grandi dimensioni senza alcun limite riguardo alle dimensioni dei file. Altre funzionalità sono attivate per impostazione predefinita per accelerare la distribuzione di file di grandi dimensioni.

### <a name="complete-cache-fill"></a>Riempimento completo della cache

La funzionalità predefinita di riempimento completo della cache permette alla rete CDN di eseguire il pull di un file nella cache quando la richiesta iniziale viene abbandonata o persa. 

Il riempimento completo della cache è particolarmente utile per asset di grandi dimensioni. In genere, gli utenti non scaricano questi asset dall'inizio alla fine, ma usano il download progressivo. Il comportamento predefinito forza il server perimetrale ad avviare un recupero in background dell'asset dal server di origine. Al termine di questa operazione, l'asset si trova nella cache locale del server perimetrale. Quando l'oggetto si trova interamente nella cache, il server perimetrale può soddisfare le richieste di intervalli di byte alla rete CDN per l'oggetto memorizzato nella cache.

È possibile disabilitare il comportamento predefinito tramite il motore delle regole nel livello Premium di Verizon.

### <a name="peer-cache-fill-hot-filing"></a>Hotfiling del riempimento della cache peer

Questa funzionalità predefinita usa un sofisticato algoritmo proprietario. La funzionalità usa server perimetrali aggiuntivi per la memorizzazione nella cache in base alla larghezza di banda e ad altre metriche delle richieste aggregate per soddisfare le richieste client di oggetti di grandi dimensioni e usati spesso. Questa funzionalità previene situazioni in cui un numero elevato di richieste aggiuntive viene inviato al server di origine di un utente. 

### <a name="conditions-for-large-file-optimization"></a>Condizioni per l'ottimizzazione dei file di grandi dimensioni

Le funzionalità di ottimizzazione per Verizon sono attivate per impostazione predefinita. Non viene applicato alcun limite per le dimensioni massime dei file. 

## <a name="additional-considerations"></a>Ulteriori considerazioni

Per questo tipo di ottimizzazione, tenere presenti gli aspetti aggiuntivi seguenti.
 
### <a name="azure-content-delivery-network-from-akamai"></a>Rete di distribuzione dei contenuti di Azure con tecnologia Akamai

- Il processo di suddivisione in blocchi genera richieste aggiuntive al server di origine. Tuttavia, il volume complessivo dei dati distribuiti dall'origine è notevolmente inferiore. La suddivisione in blocchi assicura caratteristiche di memorizzazione nella cache migliori nella rete CDN.

- La pressione in termini di memoria e I/O risulta ridotta nell'origine, perché vengono distribuite piccole parti del file.

- Per i blocchi memorizzati nella cache nella rete CDN non vi sono richieste aggiuntive all'origine fino alla scadenza del contenuto o finché il contenuto non viene rimosso dalla cache.

- L'utente può effettuare richieste di intervalli di byte alla rete CDN, che vengono considerate normali richieste di file. L'ottimizzazione viene applicata solo se il tipo di file è valido e l'intervallo di byte è compreso tra 10 MB e 150 GB. Se la dimensione media dei file richiesta è inferiore a 10 MB, può essere preferibile usare la distribuzione Web generica.

### <a name="azure-content-delivery-network-from-verizon"></a>Rete di distribuzione dei contenuti di Azure con tecnologia Verizon

L'ottimizzazione basata sulla distribuzione Web generica è in grado di distribuire file di grandi dimensioni.

