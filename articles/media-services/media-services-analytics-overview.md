<properties
	pageTitle="Panoramica di Analisi servizi multimediali di Azure | Microsoft Azure"
	description="Servizi multimediali di Azure offre un'anteprima pubblica di Analisi Servizi multimediali, una serie di servizi di riconoscimento vocale e visione artificiale per scalabilità, conformità, sicurezza e portata globale per le aziende. I servizi di Analisi Servizi multimediali di Azure sono stati creati usando i componenti principali della piattaforma Servizi multimediali di Azure e quindi sono subito pronti per l'elaborazione di file multimediali su larga scala. "
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="erikre"
	editor=""/> 

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/14/2016"   
	ms.author="milanga;juliako;johndeu"/> 

# Panoramica di Analisi servizi multimediali di Azure | Microsoft Azure

##Overview

Sempre più organizzazioni e aziende adottano i video come mezzo per formare i dipendenti, coinvolgere i clienti e documentare le funzioni aziendali. Il cloud computing agevola l'archiviazione, il flusso e l'accesso a questi file multimediali di grandi dimensioni. Tuttavia, man mano che la raccolta di contenuti video cresce, le aziende devono poter usufruire di mezzi altrettanto efficienti per estrarre dal video le nuove informazioni dettagliate al fine di creare interazioni più consistenti e personalizzate con i destinatari e portare l'azienda a un livello superiore.

Per soddisfare questa esigenza in crescita sul marketplace, Servizi multimediali di Azure offre Analisi Servizi multimediali, una serie di componenti di riconoscimento vocale e visione artificiale per scalabilità, conformità, sicurezza e portata globale per le aziende, che permettono a organizzazioni e aziende di derivare in modo più semplice analisi approfondite di utilità pratica dai propri file video. I servizi di Analisi Servizi multimediali di Azure sono stati creati usando i componenti principali della piattaforma Servizi multimediali di Azure e quindi sono subito pronti per l'elaborazione di file multimediali su larga scala.

Analisi Servizi multimediali consente agli sviluppatori di introdurre rapidamente le capacità di visione per i video su scala limitata e implementare queste funzionalità avanzate nei robot e nelle applicazioni. Analisi Servizi multimediali di Azure è ideato per l'uso negli ambienti aziendali con scalabilità, conformità, sicurezza e portata globale complete necessarie per organizzazioni di grandi dimensioni.

Il diagramma seguente mostra **Analisi Servizi multimediali** e altre parti importanti della piattaforma di Analisi Servizi multimediali. Si noti che i processori di contenuti multimediali di Analisi Servizi multimediali producono file MP4 o JSON. Se un processore di contenuti multimediali produce un file MP4, è possibile scaricare progressivamente il file. Se un processore di contenuti multimediali produce un file JSON, è possibile scaricare il file dall'archiviazione BLOB di Azure.

![Flusso di lavoro VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)


## Servizi di Analisi Servizi multimediali di Azure

- **Indexer**: Azure Media Indexer consente di rendere i contenuti disponibili per la ricerca, oltre a generare tracce per i sottotitoli codificati. Servizi multimediali di Azure ha lanciato **Azure Media Indexer 2 Preview** con supporto più ampio e veloce per indicizzazione e linguaggio. Le lingue supportate includono l'inglese, lo spagnolo, il francese, il tedesco, l'italiano, il cinese, il portoghese e l'arabo. Per informazioni dettagliate ed esempi, vedere [Process videos with Azure Media Indexer 2 (Elaborare video con Azure Media Indexer 2)](media-services-process-content-with-indexer2.md)
 
- **Hyperlapse**: Microsoft Hyperlapse è il risultato di oltre 20 anni di ricerca sulla visione artificiale condotta presso Microsoft Research (MSR), combinando stabilizzazione video e intervalli per creare video rapidi, fruibili e accattivanti, partendo da contenuti più lunghi. Oltre alla creazione di intervalli, è anche possibile usare Hyperlapse per creare video stabili da video traballanti acquisiti con telefoni cellulari e videocamere. Per informazioni dettagliate ed esempi, vedere [File multimediali di Hyperlapse con Azure Media Hyperlapse](media-services-hyperlapse-content.md)
 
- **Rilevamento movimento**: è possibile usare questo servizio per rilevare i movimenti in un video con sfondi fissi. Questa funzione è ideale per i clienti che desiderano controllare la presenza di falsi positivi nei movimenti rilevati da videocamere di sorveglianza all'interno dei feed del video di sorveglianza. Per informazioni dettagliate ed esempi, vedere [Motion Detection for Azure Media Analytics (Rilevamento del movimento per Analisi Servizi multimediali di Azure)](media-services-motion-detection.md).
 
- **Rilevamento viso ed emozioni viso**: usando questo servizio, è possibile rilevare i volti delle persone e le relativi emozioni, tra cui felicità, tristezza, sorpresa, rabbia, disprezzo, paura, disgusto e indifferenza/neutralità. Questa funzionalità vanta numerose applicazioni utili nel settore, descritte di seguito, tra cui l'aggregazione e l'analisi delle reazioni delle persone che partecipano a un evento. Per informazioni dettagliate ed esempi, vedere [Face and Emotion Detection for Azure Media Analytics (Rilevamento del viso e delle emozioni per Analisi Servizi multimediali di Azure)](media-services-face-and-emotion-detection.md).
 
- **Riepilogo video**: il riepilogo del video consente di creare un riepilogo per video lunghi selezionando in modo automatico frammenti interessanti del video di origine. Questa funzione risulta particolarmente utile quando si intende creare una panoramica rapida dei contenuti offerti nella versione più lunga del video. Per informazioni dettagliate ed esempi, vedere [Uso delle anteprime video multimediali di Azure per creare un riepilogo video](media-services-video-summarization.md)

- **Riconoscimento ottico dei caratteri**: il riconoscimento ottico dei caratteri (OCR) di Analisi servizi multimediali di Azure consente di convertire il contenuto di testo dei file video in testo digitale modificabile e sui cui è possibile eseguire ricerche. Ciò consente di automatizzare l'estrazione di metadati importanti dal segnale video del contenuto multimediale.
 
- **Offuscamento dei volti scalabile**: **Azure Media Redactor** è un processore di contenuti multimediali di Analisi Servizi multimediali di Azure che offre funzionalità scalabili di offuscamento dei volti nel cloud. L'offuscamento dei volti consente di modificare un video per sfocare i volti di persone selezionate. Può essere opportuno usare tale servizio in scenari di pubblica sicurezza e notizie giornalistiche. Offuscare manualmente alcuni minuti di filmato contenenti più volti può richiedere ore, ma con questo servizio il processo di offuscamento dei volti richiederà pochi semplici passaggi. Per altre informazioni, vedere [questo](media-services-face-redaction.md) articolo.

 
## Scenari comuni

Di seguito vengono presentati un paio di scenari in cui Analisi Servizi multimediali di Azure può aiutare le organizzazioni e le aziende di vari settori a raccogliere nuove informazioni dai video per aumentare la personalizzazione del coinvolgimento di destinatari e dipendenti, nonché per gestire più efficacemente un volume elevato di contenuti video:

- **Call center**: anche con l'avvento dei social media, i call center dedicati ai clienti continuano a costituire un'alta percentuale delle transazioni del servizio clienti. Questi dati audio contengono una grande quantità di informazioni sui clienti che possono essere analizzate per migliorare la roadmap e formare i dipendenti dei call center al fine di accrescere la soddisfazione dei clienti. Con Azure Media Indexer, i clienti possono estrarre testo e creare un indice e dashboard di ricerca per estrarre informazioni sui reclami più comuni, sui motivi dei reclami e altri dati rilevanti.

- **Moderazione dei contenuti generati dall'utente**: dalle notizie giornalistiche ai reparti di polizia, molte organizzazioni dispongono di portali pubblici in cui vengono accettati file multimediali UGC, ad esempio immagini e video. A causa di eventi imprevisti, il volume dei contenuti potrebbe raggiungere il limite. In questi scenari è quasi impossibile eseguire un efficace controllo manuale sull'adeguatezza dei contenuti. I clienti possono fare affidamento sul servizio di moderazione dei contenuti per concentrare l'attenzione sui contenuti appropriati.

- **Sorveglianza**: con l'aumento delle videocamere IP, si assiste all'esplosione dei video di sorveglianza. Il controllo manuale dei video di sorveglianza richiede molto tempo ed è soggetto a errori umani. Analisi Servizi multimediali di Azure offre diversi componenti, ad esempio il rilevamento del movimento, il rilevamento viso e Hyperlapse per agevolare il processo di controllo, gestione e creazione dei derivati.

## Processori di contenuti multimediali di Analisi servizi multimediali 

Questa sezione elenca tutti i processori di contenuti multimediali (MP) di Analisi servizi multimediali e illustra come usare .NET o REST per ottenere un oggetto MP.

### Nomi dei processori di contenuti multimediali


- Anteprima di Azure Media Indexer 2
- Azure Media Indexer
- Azure Media Hyperlapse
- Rilevamento multimediale volti di Azure
- Rilevatore multimediale di movimento Azure
- Anteprime video multimediali di Azure
- Riconoscimento ottico dei caratteri multimediale di Azure

### .NET

La funzione seguente acquisisce uno dei nomi MP specificati e restituisce un oggetto MP.

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


## REST

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

##Demo

[Demo di Analisi servizi multimediali di Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

##Passaggio successivo

Analizzare i percorsi di apprendimento di Servizi multimediali.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Articoli correlati

[Annuncio di Analisi di Servizi multimediali](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)
  

<!-- Images --> 

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

<!---HONumber=AcomDC_0921_2016-->