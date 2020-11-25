---
title: Aspetti da considerare quando si usa Video Indexer su larga scala-Azure
titleSuffix: Azure Media Services
description: In questo argomento vengono illustrati gli aspetti da considerare quando si usa Video Indexer su larga scala.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: 7d5108d2c155c7e21f2f94f532bd1aa0a96c5b3f
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "96020533"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Aspetti da considerare quando si usa Video Indexer su larga scala

Quando si usa l'indicizzatore video di servizi multimediali di Azure per indicizzare i video e l'archivio dei video cresce, provare a ridimensionare. 

Questo articolo risponde a domande come:

* Sono presenti vincoli tecnologici che è necessario tenere in considerazione?
* Esiste un modo intelligente ed efficiente per eseguire questa operazione?
* È possibile evitare di spendere denaro in eccesso nel processo?

Questo articolo fornisce sei procedure consigliate per l'uso di Video Indexer su larga scala.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Quando si caricano video, è consigliabile usare un URL su una matrice di byte

Video Indexer offre la possibilità di caricare i video dall'URL o direttamente inviando il file come matrice di byte, il secondo presenta alcuni vincoli. Per altre informazioni, vedere [caricamento di considerazioni e limitazioni](upload-index-videos.md#uploading-considerations-and-limitations) .

In primo luogo, presenta limitazioni alle dimensioni del file. Le dimensioni del file della matrice di byte sono limitate a 2 GB rispetto alla limitazione delle dimensioni di caricamento di 30 GB durante l'utilizzo dell'URL.

In secondo luogo, prendere in considerazione solo alcuni dei problemi che possono influire sulle prestazioni e quindi la possibilità di ridimensionare:

* L'invio di file con più parti significa una dipendenza elevata dalla rete, 
* affidabilità del servizio 
* connettività 
* velocità di caricamento, 
* perdita di pacchetti in un punto qualsiasi del World Wide Web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Prima considerazione per l'uso di Video Indexer su larga scala":::

Quando si caricano video usando l'URL, è sufficiente specificare un percorso per il percorso di un file multimediale e Video Indexer si occupi del resto (vedere il `videoUrl` campo nell'API di [caricamento video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) ).

> [!TIP]
> Usare il `videoUrl` parametro facoltativo dell'API upload video.

Per un esempio di come caricare video usando l'URL, vedere [questo esempio](upload-index-videos.md#code-sample). In alternativa, è possibile usare [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) per un modo rapido e affidabile per ottenere i contenuti in un account di archiviazione da cui è possibile inviarli a video Indexer usando l' [URL SAS](https://docs.microsoft.com/azure/storage/common/storage-sas-overview).

## <a name="increase-media-reserved-units-if-needed"></a>Aumenta le media reserved Unit, se necessario

In genere, nella fase del modello di verifica quando si inizia a usare Video Indexer, non è necessaria una grande potenza di calcolo. Quando si inizia a disporre di un archivio di video più ampio che è necessario indicizzare e si vuole che il processo sia in grado di adattare il caso d'uso, è necessario aumentare il livello di utilizzo del Video Indexer. Pertanto, è necessario considerare l'aumento del numero di risorse di calcolo utilizzate se la quantità corrente di potenza di elaborazione non è sufficiente.

In servizi multimediali di Azure, quando si vuole aumentare la potenza di calcolo e la parallelizzazione, è necessario prestare attenzione a media [reserved Unit](../latest/concept-media-reserved-units.md)(UR). Le UR sono le unità di calcolo che determinano i parametri per le attività di elaborazione di contenuti multimediali. Il numero di ur influiscono sul numero di attività multimediali che possono essere elaborate contemporaneamente in ogni account e il tipo determina la velocità di elaborazione e un video potrebbe richiedere più di un'unità richiesta se la relativa indicizzazione è complessa. Quando le UR sono occupate, le nuove attività verranno mantenute in una coda finché non sarà disponibile un'altra risorsa.

Per operare in modo efficiente e per evitare che le risorse siano inattive in parte del tempo, Video Indexer offre un sistema di scalabilità automatica che ruota le UR quando è necessaria una minore elaborazione e spine le UR quando si è nelle ore di punta (fino a usare completamente tutte le UR). È possibile abilitare questa funzionalità attivando [la scalabilità](manage-account-connected-to-azure.md#autoscale-reserved-units) automatica nelle impostazioni dell'account o usando l' [API Update-paid-account-Azure-Media-Services](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update).

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Seconda considerazione per l'uso di Video Indexer su larga scala":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="Unità riservate AMS":::

Per ridurre al minimo la durata dell'indicizzazione e la velocità effettiva bassa, è consigliabile iniziare con 10 UR di tipo S3. In seguito, se si aumentano le prestazioni per supportare più contenuti o una concorrenza più elevata ed è necessario disporre di più risorse, è possibile [contattarci usando il sistema di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (solo per gli account a pagamento) per richiedere una maggiore allocazione di ur.

## <a name="respect-throttling"></a>Rispettare la limitazione

Video Indexer è progettato per gestire l'indicizzazione su larga scala e quando si desidera sfruttare al meglio le funzionalità del sistema e progettare di conseguenza l'integrazione. Non si vuole inviare una richiesta di caricamento per un batch di video solo per scoprire che alcuni dei film non sono stati caricati e si riceve un codice di risposta HTTP 429 (troppe richieste). Questo può accadere a causa del fatto che sono state inviate più richieste rispetto al [limite dei film al minuto supportati](upload-index-videos.md#uploading-considerations-and-limitations). Video Indexer aggiunge un' `retry-after` intestazione nella risposta http, l'intestazione specifica quando provare a eseguire il tentativo successivo. Assicurarsi di rispettarlo prima di provare la richiesta successiva.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Progettare correttamente l'integrazione, rispettare la limitazione delle richieste":::

## <a name="use-callback-url"></a>Usa URL callback

Invece di eseguire il polling dello stato della richiesta in modo costante dal secondo invio della richiesta di caricamento, è possibile aggiungere un [URL di callback](upload-index-videos.md#callbackurl)e attendere che video Indexer aggiorni l'utente. Non appena viene apportata una modifica di stato nella richiesta di caricamento, si riceverà una notifica POST per l'URL specificato.

È possibile aggiungere un URL di callback come uno dei parametri dell' [API carica video](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload). Vedere gli esempi di codice nel [repository GitHub](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/). 

Per l'URL callback è anche possibile usare funzioni di Azure, una piattaforma guidata dagli eventi senza server che può essere attivata da HTTP e implementare un flusso seguente.

### <a name="callback-url-definition"></a>definizione URL callBack

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Usare i parametri di indicizzazione corretti

Quando si prendono decisioni relative all'uso di Video Indexer su larga scala, è possibile vedere come sfruttare al meglio i parametri appropriati per le proprie esigenze. Per quanto riguarda il caso d'uso, definendo parametri diversi, è possibile risparmiare denaro e rendere più veloce il processo di indicizzazione per i video.

Prima di caricare e indicizzare il video, leggere questa breve [documentazione](upload-index-videos.md), controllare [indexingPreset](upload-index-videos.md#indexingpreset) e [streamingPreset](upload-index-videos.md#streamingpreset) per ottenere un'idea più approfondita delle opzioni disponibili.

Ad esempio, non impostare il set di impostazioni su streaming se non si prevede di guardare il video, non indicizzare le informazioni dettagliate video se è necessario solo l'audio Insights.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Indice con risoluzione ottimale, senza risoluzione più elevata

È possibile che si stia chiedendo quale qualità video è necessario per l'indicizzazione dei video? 

In molti casi, le prestazioni di indicizzazione non hanno quasi alcuna differenza tra video HD (720P) e video 4K. Alla fine, otterrete quasi tutte le stesse informazioni con la stessa confidenza. Maggiore è la qualità del film caricato, le dimensioni del file sono superiori e questo comporta una maggiore potenza di elaborazione e il tempo necessario per caricare il video.

Per la funzionalità di rilevamento viso, ad esempio, una risoluzione più elevata può essere utile per lo scenario in cui sono presenti molti visi di dimensioni ridotte ma contestuali. Questa operazione, tuttavia, verrà rilevata con un aumento quadratico del runtime e un rischio maggiore di falsi positivi.

Si consiglia pertanto di verificare di ottenere i risultati corretti per il caso d'uso e di testarli in locale. Caricare lo stesso video in 720P e in 4K e confrontare le informazioni riportate.

## <a name="next-steps"></a>Passaggi successivi

[Esaminare l'output del Video Indexer di Azure prodotto dall'API](video-indexer-output-json-v2.md)
