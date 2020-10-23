---
title: Kit di elaborazione batch per contenitori di sintesi vocale
titleSuffix: Azure Cognitive Services
description: Utilizzare il kit di elaborazione batch per ridimensionare le richieste del contenitore vocale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: a3b2a9db688104c168017863910745427a3a68f9
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425789"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Kit di elaborazione batch per contenitori di sintesi vocale

Usare il kit di elaborazione batch per integrare e scalare in orizzontale i carichi di lavoro nei contenitori di riconoscimento vocale. Disponibile come contenitore, questa utilità Open Source consente di semplificare la trascrizione in batch per un numero elevato di file audio, in un numero qualsiasi di endpoint di contenitori di sintesi vocale locali e basati sul cloud. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Diagramma che illustra un flusso di lavoro contenitore di batch di esempio.":::

Il contenitore del kit batch è disponibile gratuitamente su [GitHub](https://github.com/microsoft/batch-processing-kit) e sull'   [Hub Docker](https://hub.docker.com/r/batchkit/speech-batch-kit/tags). Vengono [addebitati](speech-container-howto.md#billing) solo i contenitori di riconoscimento vocale usati.

| Funzionalità  | Descrizione  |
|---------|---------|
| Distribuzione file audio batch     | Invia automaticamente un numero elevato di file a endpoint del contenitore vocale locale o basato sul cloud. I file possono trovarsi in qualsiasi volume conforme a POSIX, inclusi i file System di rete.       |
| Integrazione dell'SDK vocale | Passare i flag comuni all'SDK di riconoscimento vocale, tra cui: n-le ipotesi migliori, la messa in pratica, il linguaggio, la maschera volgare.  |
|Modalità di esecuzione     | Eseguire il client batch una volta, in modo continuo in background o creare endpoint HTTP per i file audio.         |
| Tolleranza di errore | Riprovare e continuare la trascrizione senza perdere lo stato di avanzamento e distinguere tra quali errori possono e non è possibile riprovare. |
| Rilevamento disponibilità endpoint | Se un endpoint non è più disponibile, il client batch continuerà a trascrivere, usando altri endpoint contenitore. Dopo essere stato nuovamente disponibile, il client inizierà automaticamente a usare l'endpoint.   |
| Scambio a caldo dell'endpoint | Aggiungere, rimuovere o modificare gli endpoint del contenitore vocale durante il runtime senza interrompere lo stato di avanzamento del batch. Gli aggiornamenti sono immediatamente disponibili. |
| Registrazione in tempo reale | Registrazione in tempo reale di richieste di tentativi, timestamp e motivi di errore, con i file di log dell'SDK vocale per ogni file audio. |

## <a name="get-the-container-image-with-docker-pull"></a>Ottenere l'immagine del contenitore con `docker pull`

Usare il comando [Docker pull](https://docs.docker.com/engine/reference/commandline/pull/) per scaricare il contenitore del kit batch più recente.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Configurazione dell'endpoint

Il client batch accetta un file di configurazione YAML che specifica gli endpoint del contenitore locale. L'esempio seguente può essere scritto in `/mnt/my_nfs/config.yaml` , usato negli esempi seguenti. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

Questo esempio di YAML specifica tre contenitori di riconoscimento vocale in tre host. Il primo host viene specificato da un indirizzo IPv4, il secondo viene eseguito nella stessa macchina virtuale del client batch e il terzo contenitore viene specificato dal nome host DNS di un'altra macchina virtuale. Il `concurrency` valore specifica il numero massimo di trascrizioni simultanee dei file che possono essere eseguite nello stesso contenitore. Il `rtf` valore (fattore in tempo reale) è facoltativo e può essere utilizzato per ottimizzare le prestazioni.
 
Il client batch può rilevare in modo dinamico se un endpoint non è più disponibile, ad esempio a causa di un riavvio del contenitore o di un problema di rete, e quando diventa nuovamente disponibile. Le richieste di trascrizione non verranno inviate a contenitori non disponibili e il client continuerà a usare altri contenitori disponibili. È possibile aggiungere, rimuovere o modificare gli endpoint in qualsiasi momento senza interrompere lo stato del batch.


## <a name="run-the-batch-processing-container"></a>Eseguire il contenitore di elaborazione batch
  
> [!NOTE] 
> * In questo esempio viene usata la stessa directory ( `/my_nfs` ) per il file di configurazione e le directory di input, output e log. Per queste cartelle è possibile usare directory ospitate o montate da NFS.
> * Eseguendo il client con `–h` , vengono elencati i parametri della riga di comando disponibili e i relativi valori predefiniti. 
> * Il contenitore di elaborazione batch è supportato solo in Linux.

Usare il comando Docker `run` per avviare il contenitore. Verrà avviata una shell interattiva all'interno del contenitore.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

Per eseguire il client batch:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

Per eseguire il client e il contenitore batch in un singolo comando:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


Il client avvierà l'esecuzione. Se un file audio è già stato trascritto in un'esecuzione precedente, il client ignorerà automaticamente il file. I file vengono inviati con un nuovo tentativo se si verificano errori temporanei ed è possibile distinguere tra quali errori si vuole ritentare il client. In un errore di trascrizione, il client continuerà la trascrizione ed è possibile riprovare senza perdere lo stato di avanzamento.  

## <a name="run-modes"></a>Modalità di esecuzione 

Il kit di elaborazione batch offre tre modalità, usando il `--run-mode` parametro.

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` la modalità consente di trascrivere un singolo batch di file audio (da una directory di input e un elenco di file facoltativo) a una cartella di output.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Diagramma che illustra un flusso di lavoro contenitore di batch di esempio.":::

1. Definire gli endpoint del contenitore vocale che il client batch utilizzerà nel `config.yaml` file. 
2. Inserire i file audio per la trascrizione in una directory di input.  
3. Richiamare il contenitore nella directory, che inizierà l'elaborazione dei file. Se il file audio è già stato trascritto in un'esecuzione precedente con la stessa directory di output (stesso nome file e checksum), il client ignorerà il file. 
4. I file vengono inviati agli endpoint del contenitore del passaggio 1.
5. I log e l'output del contenitore vocale vengono restituiti alla directory di output specificata. 

#### <a name="daemon"></a>[Daemon](#tab/daemon)

> [!TIP]
> Se contemporaneamente più file vengono aggiunti alla directory di input, è possibile migliorare le prestazioni aggiungendoli a intervalli regolari.

`DAEMON` la modalità esegue la trascrizione dei file esistenti in una determinata cartella e li registra continuamente quando vengono aggiunti.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Diagramma che illustra un flusso di lavoro contenitore di batch di esempio.":::

1. Definire gli endpoint del contenitore vocale che il client batch utilizzerà nel `config.yaml` file. 
2. Richiamare il contenitore in una directory di input. Il client batch inizierà a monitorare la directory per i file in ingresso. 
3. Configurare il recapito del file audio continuo alla directory di input. Se il file audio è già stato trascritto in un'esecuzione precedente con la stessa directory di output (stesso nome file e checksum), il client ignorerà il file. 
4. Quando viene rilevato un segnale di scrittura o POSIX di un file, il contenitore viene attivato per rispondere.
5. I file vengono inviati agli endpoint del contenitore del passaggio 1.
6. I log e l'output del contenitore vocale vengono restituiti alla directory di output specificata. 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` la modalità è una modalità Server API che fornisce un set di base di endpoint HTTP per l'invio in batch di file audio, il controllo dello stato e il polling prolungato. Consente anche l'uso a livello di codice usando un'estensione del modulo Python o l'importazione come modulo secondario.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="Diagramma che illustra un flusso di lavoro contenitore di batch di esempio.":::

1. Definire gli endpoint del contenitore vocale che il client batch utilizzerà nel `config.yaml` file. 
2. Inviare una richiesta di richiesta HTTP a uno degli endpoint del server API. 
        
    |Endpoint  |Descrizione  |
    |---------|---------|
    |`/submit`     | Endpoint per la creazione di nuove richieste batch.        |
    |`/status`     | Endpoint per il controllo dello stato di una richiesta batch. La connessione resterà aperta fino al completamento del batch.       |
    |`/watch`     | Endpoint per l'utilizzo del polling lungo HTTP finché il batch non viene completato.        |

3. I file audio vengono caricati dalla directory di input. Se il file audio è già stato trascritto in un'esecuzione precedente con la stessa directory di output (stesso nome file e checksum), il client ignorerà il file. 
4. Se una richiesta viene inviata all' `/submit` endpoint, i file vengono inviati agli endpoint del contenitore del passaggio 1.
5. I log e l'output del contenitore vocale vengono restituiti alla directory di output specificata. 

---

## <a name="logging"></a>Registrazione

> [!NOTE]
> Il client batch può sovrascrivere periodicamente il file *Run. log* se è troppo grande.

Il client crea un file *Run. log* nella directory specificata dall' `-log_folder` argomento nel comando Docker `run` . Per impostazione predefinita, i log vengono acquisiti a livello di DEBUG. Gli stessi log vengono inviati a `stdout/stderr` e filtrati a seconda dell' `-log_level` argomento. Questo log è necessario solo per il debug o se è necessario inviare una traccia per il supporto. La cartella di registrazione contiene anche i log dell'SDK vocale per ogni file audio.

La directory di output specificata da conterrà `-output_folder` un *run_summary.jssu*   file, che viene periodicamente riscritto ogni 30 secondi o ogni volta che vengono completate nuove trascrizioni. È possibile usare questo file per controllare lo stato di avanzamento del batch. Conterrà anche le statistiche di esecuzione finale e lo stato finale di ogni file al termine del batch. Il batch viene completato quando il processo ha un'uscita pulita. 

## <a name="next-steps"></a>Passaggi successivi

* [Come installare ed eseguire i contenitori](speech-container-howto.md)
