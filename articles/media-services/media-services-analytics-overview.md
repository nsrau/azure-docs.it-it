---
title: Analisi Servizi multimediali nella piattaforma Servizi multimediali | Microsoft Docs
description: "Una panoramica dell'anteprima pubblica di Analisi Servizi multimediali, una serie di servizi di riconoscimento vocale e visione artificiale per scalabilità, conformità, sicurezza e copertura globale per le aziende"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2017
ms.author: milanga;juliako;johndeu
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 63dd4299f5d4bcd98f87fa78c2d7da080a105696
ms.contentlocale: it-it
ms.lasthandoff: 04/15/2017

---
# <a name="media-analytics-on-the-media-services-platform"></a>Analisi Servizi multimediali nella piattaforma Servizi multimediali
## <a name="overview"></a>Panoramica
Sempre più organizzazioni adottano i video come mezzo per formare i dipendenti, coinvolgere i clienti e documentare le funzioni aziendali. Il cloud computing consente di archiviare e riprodurre in streaming questi file multimediali di grandi dimensioni, nonché di accedervi. Tuttavia, man mano che la libreria aziendale di contenuti video aumenta, è necessario un mezzo altrettanto efficace per estrarre informazioni dal contenuto. 

Per soddisfare questa esigenza in crescita, i Servizi multimediali di Azure offrono Analisi Servizi multimediali. Analisi Servizi multimediali è una raccolta di componenti per sintesi vocale e visione artificiale che permettono a organizzazioni e aziende di derivare in modo più semplice analisi approfondite di utilità pratica dai loro file video. Analisi Servizi multimediali, una soluzione creata usando i componenti principali della piattaforma Servizi multimediali, può iniziare fin da subito a elaborare i file multimediali su larga scala.

Con Analisi Servizi multimediali, gli sviluppatori possono introdurre rapidamente funzionalità video avanzate nelle applicazioni. Questa soluzione offre agli ambienti aziendali scalabilità, conformità, sicurezza e copertura globale complete necessarie per organizzazioni di grandi dimensioni.

Il diagramma seguente mostra Analisi Servizi multimediali e altre parti importanti della piattaforma Servizi multimediali. 

![Flusso di lavoro VoD](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

I processori di contenuti multimediali di Analisi Servizi multimediali producono file MP4 o JSON. Se un processore di contenuti multimediali produce un file MP4, è possibile scaricare progressivamente il file. Se un processore di contenuti multimediali produce un file JSON, è possibile scaricare il file dall'Archiviazione BLOB di Azure. 

## <a name="media-analytics-services"></a>Servizi di Analisi Servizi multimediali

### <a name="indexer"></a>Indexer
Azure Media Indexer consente di rendere disponibili per la ricerca i contenuti, oltre a generare tracce per i sottotitoli codificati. Rispetto alla versione precedente, Azure Media Indexer 2 Preview offre prestazioni di indicizzazione superiori e un più ampio supporto di lingue. Le lingue supportate includono inglese, spagnolo, francese, tedesco, italiano, cinese, portoghese e arabo. Per informazioni dettagliate ed esempi, vedere [Elaborare video con Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="hyperlapse"></a>Hyperlapse
Microsoft Hyperlapse combina la stabilizzazione video e la funzionalità di time-lapse per creare brevi video visualizzabili partendo da contenuti in formato esteso. Oltre alla creazione di video time-lapse, è anche possibile usare Hyperlapse per creare video stabili da video traballanti girati con telefoni cellulari e videocamere. Per informazioni dettagliate ed esempi, vedere [File multimediali di Hyperlapse con Azure Media Hyperlapse](media-services-hyperlapse-content.md).
### <a name="motion-detector"></a>Motion Detector
È possibile usare Motion Detector per rilevare i movimenti in un video con sfondi fissi. Questo rende possibile per verificare la presenza di falsi positivi in eventi di movimento rilevati da videocamere di sorveglianza. Per informazioni dettagliate ed esempi, vedere [Rilevamento dei movimenti per Analisi Servizi multimediali di Azure](media-services-motion-detection.md).
### <a name="face-detector"></a>Face Detector
Grazie all'uso di Face Detector, è possibile individuare i volti delle persone e le relative emozioni, ad esempio felicità, tristezza e sorpresa. Questa funzionalità vanta numerose applicazioni utili nel settore, descritte di seguito, tra cui l'aggregazione e l'analisi delle reazioni delle persone che partecipano a un evento. Per informazioni dettagliate ed esempi, vedere [Rilevamento di volti ed emozioni per Analisi Servizi multimediali di Azure](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Riepilogo video
Il riepilogo video consente di creare un riepilogo per video lunghi selezionando in modo automatico frammenti interessanti del video di origine. Questa funzione risulta particolarmente utile quando si intende creare una panoramica rapida dei contenuti offerti nella versione più lunga del video. Per informazioni dettagliate ed esempi, vedere [Uso di Azure Media Video Thumbnails per creare un riepilogo video](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Riconoscimento ottico dei caratteri
Il riconoscimento ottico dei caratteri (OCR) di Analisi Servizi multimediali di Azure consente di convertire il contenuto di testo dei file video in testo digitale modificabile e sui cui è possibile eseguire ricerche. È possibile automatizzare così l'estrazione di metadati importanti dal segnale video del contenuto multimediale.
### <a name="scalable-face-redaction"></a>Offuscamento dei volti scalabile
Azure Media Redactor è un processore di contenuti multimediali di Analisi Servizi multimediali di Azure che offre funzionalità scalabili di offuscamento dei volti nel cloud. Usando l'offuscamento dei volti è possibile modificare un video per sfocare i volti di persone selezionate. Si potrebbe scegliere di usare tale servizio in scenari di notizie giornalistiche o pubblica sicurezza. Offuscare manualmente alcuni minuti di filmato contenenti più volti può richiedere ore, ma con questo servizio l'offuscamento dei volti richiederà pochi semplici passaggi. Per altre informazioni, vedere l'articolo [Offuscare i volti con Analisi Servizi multimediali di Azure](media-services-face-redaction.md).

## <a name="common-scenarios"></a>Scenari comuni
Analisi Servii multimediali può aiutare le organizzazioni e le aziende a scoprire nuovi orizzonti nel mondo dei video e gestire più efficacemente grandi volumi di contenuti video. Di seguito sono descritti diversi scenari:

* **Call center**. Anche con l'avvento dei social media, i call center dedicati ai clienti continuano a ospitare un'alta percentuale delle transazioni del servizio clienti. In questi dati audio sono codificate numerose informazioni sui clienti che possono essere analizzate per ottenere una maggiore soddisfazione del cliente. Con Media Indexer, le organizzazioni possono estrarre il testo e creare indici di ricerca e dashboard. Possono quindi estrarre informazioni su reclami comuni, origini dei reclami e altri dati rilevanti.
* **Moderazione dei contenuti generati dall'utente**. Molte organizzazioni, dalle agenzie di stampa ai dipartimenti di polizia, dispongono di portali pubblici che accettano file multimediali generati dagli utenti, ad esempio immagini e video. A causa di eventi imprevisti, il volume dei contenuti potrebbe raggiungere il limite. In questi scenari è difficile eseguire un efficace controllo manuale sull'adeguatezza dei contenuti. I clienti possono fare affidamento sul servizio di moderazione dei contenuti per concentrare l'attenzione sui contenuti appropriati.
* **Sorveglianza**. L'incremento nell'uso di telecamere IP comporta un aumento nell'inventario dei video di sorveglianza. Il controllo manuale dei video di sorveglianza richiede molto tempo ed è soggetto a errori umani. Analisi Servizi multimediali offre servizi come il rilevamento dei movimenti, il rilevamento viso e Hyperlapse per agevolare il processo di controllo, gestione e creazione dei derivati.

## <a name="media-analytics-media-processors"></a>Processori di contenuti multimediali di Analisi Servizi multimediali
Questa sezione elenca tutti i processori di contenuti multimediali di Analisi Servizi multimediali e illustra come usare .NET o REST per ottenere un oggetto dei processori di contenuti multimediali.

### <a name="mp-names"></a>Nomi dei processori di contenuti multimediali
* Azure Media Indexer 2 Preview
* Azure Media Indexer
* Azure Media Hyperlapse
* Rilevamento multimediale volti di Azure
* Rilevatore multimediale di movimento Azure
* Anteprime video multimediali di Azure
* Riconoscimento ottico dei caratteri multimediale di Azure

### <a name="net"></a>.NET
La funzione seguente acquisisce uno dei nomi di processori di contenuti multimediali specificati e restituisce un oggetto.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
Richiesta:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

Risposta:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Demo
Vedere le [demo di Analisi Servizi multimediali di Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="next-steps"></a>Passaggi successivi
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Articoli correlati
Vedere l'[annuncio di Analisi Servizi multimediali di Azure](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

