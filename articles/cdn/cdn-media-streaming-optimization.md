---
title: Ottimizzazione dello streaming multimediale tramite la rete CDN di Azure
description: Ottimizzazione dello streaming multimediale per una distribuzione uniforme
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
ms.openlocfilehash: 582eb69ccd2830643644760753cca1c96d72128d
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---
# <a name="media-streaming-optimization-via-azure-cdn"></a>Ottimizzazione dello streaming multimediale tramite la rete CDN di Azure 
 
Il video ad alta definizione sta diventando una realtà in continua espansione su Internet, creando diverse difficoltà a livello di distribuzione efficiente dei file di grandi dimensioni su Internet. L'aspettativa dei clienti è quella di poter riprodurre in modo uniforme gli asset video live o VOD su una vasta gamma di client e reti in tutto il mondo. Fornire un meccanismo di distribuzione più veloce ed efficiente per questi file di streaming multimediale è fondamentale per garantire un'esperienza consumer uniforme e piacevole.  

La distribuzione dello streaming live è particolarmente difficile, non solo perché i file hanno grandi dimensioni e il numero di utenti simultanei è elevato, ma anche perché i ritardi sono un ostacolo insormontabile per gli utenti. Lo streaming live non può essere memorizzato in anticipo nella cache e latenze di grandi dimensioni non sono accettabili per i visualizzatori; i frammenti video devono quindi essere distribuiti in modo tempestivo. 

I modelli di richiesta dello streaming pongono alcune nuove sfide. Per uno streaming live generale o al rilascio di una nuova serie di video on demand (VOD), è possibile che migliaia o persino milioni di visualizzatori tentino di richiedere lo streaming nello stesso momento. In questo caso, il consolidamento intelligente delle richieste è essenziale per non sovraccaricare i server di origine quando gli asset non sono stati ancora memorizzati nella cache.
 
La rete CDN di Azure fornita da Akamai offre ora una funzionalità finalizzata a fornire lo streaming efficiente di asset multimediali agli utenti finali di tutto il mondo su larga scala e con bassa latenza, riducendo al contempo il carico sui server di origine. Questa caratteristica è disponibile tramite la funzionalità "Ottimizzato per" sull'endpoint della rete CDN di Azure creata in un profilo di rete CDN di Azure con il piano tariffario "Standard Akamai". Se si esegue una combinazione di streaming live o VOD, è consigliabile usare l'ottimizzazione "Streaming multimediale video on demand" per gli asset VOD e "Streaming multimediale generale".

La rete CDN di Azure fornita da Verizon consente di distribuire i file multimediali di streaming direttamente nel tipo di ottimizzazione "Distribuzione Web generale".
 
## <a name="configuring-cdn-endpoint-to-optimize-delivery-of-media-streaming-in-azure-cdn-from-akamai"></a>Configurazione dell'endpoint della rete CDN per ottimizzare la distribuzione dello streaming multimediale nella rete CDN di Azure fornita da Akamai
 
È possibile configurare l'endpoint della rete CDN in modo da ottimizzare la distribuzione di file di grandi dimensioni tramite il portale di Azure, selezionando l'opzione "Streaming multimediale generale" o "Streaming multimediale video on demand" sotto la selezione per la proprietà "Ottimizzato per" durante la creazione dell'endpoint. A tale scopo è anche possibile usare le API REST o qualsiasi SDK client. Le schermate riportate di seguito illustrano il processo tramite il portale di Azure. 
  
![Aggiunta di un nuovo endpoint](./media/cdn-media-streaming-optimization/01_Adding.png)

*Figura 1: Aggiunta di un nuovo endpoint della rete CDN dal profilo della rete CDN*

![Streaming selezionato](./media/cdn-media-streaming-optimization/02_Creating.png) 
  
*Figura 2: Creazione di un endpoint della rete CDN con l'opzione Streaming multimediale video on demand selezionata* 
 
Dopo avere creato l'endpoint della rete CDN, questo applicherà le ottimizzazioni per tutti i file che corrispondono a determinati criteri. La sezione seguente descrive questo aspetto in modo dettagliato. 
 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Ottimizzazioni dello streaming multimediale per la rete CDN di Azure fornita da Akamai
 
L'ottimizzazione del flusso multimediale da Akamai è utile per i file multimediali live o VOD che usano singoli frammenti multimediali per la distribuzione, anziché un singolo asset di grandi dimensioni trasferito tramite download progressivo o usando richieste di intervalli di byte. Per lo stile di distribuzione di file multimediali, estrarre [Ottimizzazione di file di grandi dimensioni](cdn-large-file-optimization.md).

Con la distribuzione di file multimediali generali o di tipo VOD il tipo di ottimizzazione userà una rete CDN con le ottimizzazioni del back-end per fornire più rapidamente gli asset multimediali e le configurazioni per gli asset multimediali in base alle procedure consigliate apprese nel tempo.

### <a name="caching"></a>Memorizzazione nella cache

Se la rete CDN di Azure fornita da Akamai rileva che l'asset è un manifesto o un frammento di streaming (l'elenco completo è riportato di seguito), le ore di scadenza per la memorizzazione nella cache usate dalla rete CDN saranno diverse rispetto alla distribuzione generale. Come sempre, le intestazioni della scadenza o del controllo cache inviate dall'origine verranno rispettate e, se l'asset non è di tipo multimediale, verrà memorizzato nella cache usando le ore di scadenza per la distribuzione Web generale.

Un tempo di memorizzazione nella cache negativo breve è utile per l'offload dell'origine quando molti utenti richiedono un frammento che non esiste ancora, ad esempio in uno streaming live in cui i pacchetti non sono disponibili dall'origine in quel secondo. Un intervallo di memorizzazione nella cache più lungo consente anche l'offload delle richieste dall'origine poiché il contenuto video in genere non viene modificato.
 

|    | Generale<br> Web<br>di contenuti | Generale<br> diagramma<br> streaming | VOD<br>diagramma<br> streaming  
--- | --- | --- | ---
Memorizzazione nella cache - Positiva <br> HTTP 200, 203, 300, <br> 301, 302 e 410 | 7 giorni |365 giorni | 365 giorni   
Memorizzazione nella cache - Negativa <br> HTTP 204, 305, 404, <br> e 405 | Nessuno | 1 secondo | 1 secondo
 
### <a name="dealing-with-origin-failure"></a>Gestione degli errori di origine  

La distribuzione di file multimediali generali e VOD prevede il timeout dell'origine e il log di tentativi in base alle procedure consigliate per i modelli di richiesta tipici. Ad esempio, poiché la distribuzione di file multimediali generale è mirata per la distribuzione di contenuti sia live che VOD, il timeout di connessione è molto più breve poiché lo streaming live dipende dall'ora.

Quando una connessione va in timeout, la rete CDN ritenta un'operazione per un determinato numero di volte prima dell'invio dell'errore di timeout gateway 504 al client. 

Quando un file corrisponde all'elenco di condizioni per la dimensione e il tipo di file, la rete CDN usa il comportamento per lo streaming multimediale e in caso contrario la distribuzione Web generale. 
   
### <a name="conditions-for-media-streaming-optimization"></a>Condizioni per l'ottimizzazione dello streaming multimediale 

La tabella seguente elenca il set di criteri che devono essere soddisfatti per questa ottimizzazione 
 
Tipi di streaming supportati | Estensioni di file  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, key, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Struttura URL Seg-Frag <br> (espressione regolare corrispondente: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Smooth Streaming | /manifest/,/QualityLevels/Fragments/
  

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Ottimizzazioni dello streaming multimediale per la rete CDN di Azure fornita da Verizon

La rete CDN di Azure fornita da Verizon consente di distribuire gli asset multimediali di streaming direttamente nel tipo di ottimizzazione "Distribuzione Web generale". Per impostazione predefinita, nella rete CDN sono disponibili anche alcune funzionalità utili per la distribuzione di asset multimediali.

### <a name="partial-cache-sharing"></a>Condivisione della cache parziale

Il contenuto memorizzato parzialmente nella cache viene reso disponibile dalla rete CDN alle nuove richieste. Ciò significa che, ad esempio, la prima richiesta alla rete CDN genera un mancato riscontro nella cache e la richiesta viene inviata all'origine. Mentre questo contenuto è ancora caricato nella cache della rete CDN, anche se il processo non è stato ancora completato, altre richieste alla rete CDN possono iniziare a recuperare questi dati. 

### <a name="cache-fill-wait-time"></a>Tempo di attesa di riempimento della cache

Utile insieme alla condivisione della cache parziale, questa funzionalità forza il server perimetrale a contenere le richieste successive per la stessa risorsa fino alla ricezione delle intestazioni di risposta HTTP da parte del server di origine. Se le intestazioni di risposta HTTP dal server di origine vengono ricevute prima della scadenza del timer, tutte le richieste messe in attesa verranno servite dalla cache con i dati in aumento; allo stesso tempo la cache viene riempita con i dati provenienti dall'origine. Per impostazione predefinita, il tempo di attesa di riempimento della cache è 3000 millisecondi. 


