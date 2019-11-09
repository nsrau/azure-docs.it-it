---
title: Domande frequenti su Video Indexer - Azure
titleSuffix: Azure Media Services
description: Ottenere risposte a domande frequenti relative a Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 95f2af1b192564290551f9ed31f0fe158b4536f6
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838239"
---
# <a name="frequently-asked-questions"></a>Domande frequenti

Questo articolo contiene le risposte alle domande frequenti relative a Video Indexer.

## <a name="general-questions"></a>Domande generali

### <a name="what-is-video-indexer"></a>Informazioni su Video Indexer

Video Indexer è un servizio di intelligenza artificiale che fa parte di Servizi multimediali di Microsoft Azure. Video Indexer fornisce un'orchestrazione di più modelli di Machine Learning che consentono di estrarre facilmente informazioni molto dettagliate da un video. Per fornire informazioni avanzate e accurate, Video Indexer si serve di più canali del video: audio, parlato e oggetti visivi. Le informazioni dettagliate di Video Indexer possono essere usate in diversi modi, ad esempio per migliorare l'individuabilità e l'accessibilità dei contenuti, creare nuove opportunità di monetizzazione o sviluppare nuove esperienze che usano le informazioni dettagliate. Video Indexer fornisce un'interfaccia basata sul Web per il test, la configurazione e la personalizzazione dei modelli nell'account. Gli sviluppatori possono usare un'API basata su REST per integrare Video Indexer nel sistema di produzione. 

### <a name="what-can-i-do-with-video-indexer"></a>Quali operazioni si possono eseguire con Video Indexer?

Di seguito sono elencate alcune delle operazioni che Video Indexer può eseguire sui file multimediali:

* Identificazione ed estrazione della voce e identificazione di chi sta parlando
* Identificazione ed estrazione del testo visualizzato sullo schermo in un video
* Rilevamento degli oggetti in un file video
* Identificare i marchi (ad esempio: Microsoft) dalle tracce audio e dal testo sullo schermo in un video.
* Rilevamento e riconoscimento dei volti da un database di celebrità e un database di volti definito dall'utente
* Estrazione degli argomenti discussi, ma non necessariamente menzionati, nel contenuto audio e video
* Creazione dei sottotitoli dalla traccia audio

Per altre informazioni e altre funzionalità di Video Indexer, vedere [Panoramica](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Come si può iniziare a usare Video Indexer?

Video Indexer include un'offerta di valutazione gratuita che offre 600 minuti nell'interfaccia basata sul Web e 2.400 minuti tramite l'API. È possibile [accedere all'interfaccia basata sul Web di Video Indexer](https://www.videoindexer.ai/) e provarla usando qualsiasi identità Web e senza dover configurare una sottoscrizione di Azure. 

Per indicizzare i file audio e video su vasta scala, è possibile connettere Video Indexer a una sottoscrizione di Microsoft Azure a pagamento. Per altre informazioni sui prezzi, vedere la pagina [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/).

Per altre informazioni su come iniziare, vedere [Introduzione](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Sono necessarie competenze nella creazione di codice per usare Video Indexer?

È possibile usare l'interfaccia basata sul Web di Video Indexer per valutare, configurare e gestire l'account **senza dover scrivere codice**.  Quando si è pronti per sviluppare applicazioni più complesse, è possibile usare l'[API Video Indexer](https://api-portal.videoindexer.ai/) per integrare Video Indexer nelle applicazioni, nei siti Web o nei [flussi di lavoro personalizzati usando tecnologie serverless come App per la logica di Azure](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) o Funzioni di Azure.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Sono necessarie competenze di Machine Learning per usare Video Indexer?

No, Video Indexer offre l'integrazione di più modelli di Machine Learning in una singola pipeline. L'indicizzazione di un file video o audio tramite Video Indexer recupera un set completo di informazioni dettagliate estratte in una sequenza temporale condivisa senza che il cliente debba avere competenze di Machine Learning o conoscere gli algoritmi.

### <a name="what-media-formats-does-video-indexer-support"></a>Quali formati multimediali sono supportati da Video Indexer?

Video Indexer supporta i formati multimediali più comuni. Per altre informazioni, vedere l'elenco dei [formati standard di Azure Media Encoder](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats).

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Come si carica un file multimediale in Video Indexer?

Nel portale basato sul Web di Video Indexer è possibile caricare un file multimediale tramite la finestra di dialogo per il caricamento dei file oppure facendo riferimento a un URL che ospita direttamente il file di origine. Vedere l'[esempio](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4). Gli URL che ospitano il contenuto multimediale usando un codice di incorporamento o iFrame non funzioneranno. Vedere l'[esempio](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11). L'API Video Indexer richiede di specificare il file di input tramite un URL o una matrice di byte. I caricamenti tramite URL che usa l'API sono limitati a 10 GB, ma non hanno un limite di durata. Per altre informazioni, vedere questa [guida pratica](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Quanto tempo occorre a Video Indexer per estrarre informazioni dettagliate dai file multimediali?

Il tempo necessario per l'indicizzazione di un file audio o video, sia tramite l'API Video Indexer che usando l'interfaccia basata sul Web di Video Indexer, dipende da più parametri, come la lunghezza e la qualità del file, il numero di informazioni dettagliate contenute nel file, il numero di [unità riservate](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) disponibili e il fatto che l'[endpoint di streaming](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) sia abilitato o meno. Per avere un'idea migliore, è consigliabile eseguire alcuni file di test con il proprio contenuto e calcolare il tempo medio.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>È possibile creare flussi di lavoro personalizzati per automatizzare i processi con Video Indexer?

Sì, è possibile integrare Video Indexer nelle tecnologie serverless, ad esempio App per la logica, Flow e [Funzioni di Azure](https://azure.microsoft.com/services/functions/). Per altre informazioni dettagliate sui connettori [App per la logica](https://azure.microsoft.com/services/logic-apps/) e [Flow](https://flow.microsoft.com/en-us/) per Video Indexer, vedere [qui](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>In quali aree di Azure è disponibile Video Indexer?

È possibile conoscere le aree di Azure in cui è disponibile Video Indexer nella pagina [Aree](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

### <a name="what-is-the-sla-for-video-indexer"></a>Qual è il contratto di servizio per Video Indexer?

Il contratto di servizio di Servizi multimediali di Azure copre Video Indexer ed è disponibile nella pagina [Contratto di Servizio](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/). Il contratto di servizio si applica solo agli account a pagamento di Video Indexer e non alla versione di valutazione gratuita.

## <a name="privacy-questions"></a>Domande sulla privacy

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>I file video e audio indicizzati da Video Indexer vengono archiviati?

Sì, a meno che non si elimini il file da Video Indexer tramite il sito o l'API di Video Indexer, i file audio e video vengono archiviati. Per la versione di valutazione gratuita, i file audio e video indicizzati sono archiviati nell'area di Azure Stati Uniti orientali. In caso contrario, i file audio e video vengono archiviati nell'account di archiviazione della sottoscrizione di Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>È possibile eliminare i file archiviati nel portale di Video Indexer?

Sì, è sempre possibile eliminare i file audio e video, nonché i metadati e le informazioni dettagliate estratte, da Video Indexer. Dopo aver eliminato un file da Video Indexer, il file, i metadati e le informazioni dettagliate vengono rimossi definitivamente da Video Indexer. Se tuttavia è stata implementata la propria soluzione di backup in Archiviazione di Azure, il file rimane nella risorsa di archiviazione di Azure.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>È possibile controllare l'accesso utente all'account di Video Indexer?

Sì, solo gli amministratori dell'account possono invitare utenti ai propri account e annullare l'invito, nonché assegnare chi ha privilegi di modifica e chi ha accesso di sola lettura.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Chi ha accesso ai file audio e video che sono stati indicizzati e/o archiviati da Video Indexer e ai metadati e alle informazioni dettagliate estratti?

Il contenuto video o audio specificato come pubblico tramite l'impostazione di privacy è accessibile a chiunque abbia il collegamento al contenuto video o audio e alle relative informazioni dettagliate. Il contenuto video o audio specificato come privato tramite l'impostazione di privacy è accessibile solo agli utenti che sono stati invitati nell'account del contenuto video o audio. L'impostazione di privacy del contenuto si applica anche ai metadati e alle informazioni dettagliate estratti da Video Indexer. Si assegna l'impostazione di privacy quando si carica il file video o audio. È anche possibile modificare l'impostazione di privacy dopo l'indicizzazione.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Che tipo di accesso ha Microsoft ai file audio o video che sono stati indicizzati e/o archiviati da Video Indexer e ai metadati e alle informazioni dettagliate estratti?

In base alle [condizioni per i Servizi online di Azure](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), il contenuto è interamente di proprietà dell'utente e Microsoft accederà al contenuto e ai metadati e alle informazioni dettagliate del contenuto estratti da Video Indexer solo in base a quanto stabilito dalle condizioni per i Servizi online e dall'informativa sulla privacy di Microsoft.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>I modelli personalizzati creati nell'account di Video Indexer sono disponibili per altri account?

 No, i modelli personalizzati creati nell'account non sono disponibili per nessun altro account. Attualmente Video Indexer consente di creare modelli personalizzati di [marchi](customize-brands-model-overview.md), [linguaggi](customize-language-model-overview.md) e [persone](customize-person-model-overview.md) nell'account. Questi modelli sono disponibili solo nell'account in cui sono stati creati i modelli.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Il contenuto indicizzato da Video Indexer viene mantenuto all'interno dell'area di Azure in cui si usa Video Indexer?

Sì, il contenuto e le informazioni dettagliate vengono mantenuti all'interno dell'area di Azure, a meno che non si abbia una configurazione manuale nella sottoscrizione di Azure che usa più aree di Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Qual è l'Informativa sulla privacy per Video Indexer?

Video Indexer è coperto dall'[Informativa sulla privacy di Microsoft](https://privacy.microsoft.com/privacystatement). L'informativa sulla privacy illustra i dati personali elaborati da Microsoft, come Microsoft li elabora e per quali scopi Microsoft li elabora. Per altre informazioni sulla privacy, vedere [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Quali certificazioni ha Video Indexer?

Video Indexer attualmente ha la certificazione SOC. Per informazioni sulla certificazione di Video Indexer, vedere [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

## <a name="api-questions"></a>Domande sull'API

### <a name="what-apis-does-video-indexer-offer"></a>Quali API offre Video Indexer?

Le API di Video Indexer consentono l'indicizzazione, l'estrazione dei metadati, la gestione degli asset, la traduzione, l'incorporamento, la personalizzazione dei modelli e altro ancora. Per trovare informazioni più dettagliate sull'uso dell'API di Video Indexer, vedere il [portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Quali SDK client offre Video Indexer?

Attualmente non sono offerti SDK client. Il team di Video Indexer sta lavorando agli SDK e ai piani per distribuirli a breve.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Come si può iniziare a usare l'API di Video Indexer?

Vedere [Esercitazione: Iniziare a usare l'API Video Indexer](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Qual è la differenza tra l'API di Video Indexer e l'API di Servizi multimediali di Azure v3?

Esistono attualmente alcune sovrapposizioni nelle funzionalità offerte dall'API di Video Indexer e dall'API di Servizi multimediali di Azure v3. Per altre informazioni su come confrontare entrambi i servizi, vedere [qui](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Che cos'è un token di accesso all'API e perché è necessario?

L'API di Video Indexer contiene un'API di autorizzazione e un'API delle operazioni. L'API di autorizzazione contiene chiamate che forniscono un token di accesso. Ogni chiamata all'API delle operazioni deve essere associata a un token di accesso, che corrisponde all'ambito di autorizzazione della chiamata.

I token di accesso sono necessari per l'uso delle API di Video Indexer per motivi di sicurezza. Ciò garantisce che tutte le chiamate provengano dagli utenti che dispongono delle autorizzazioni di accesso all'account. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Qual è la differenza tra i token di accesso account, i token di accesso utente e i token di accesso video?

* Livello di account: i token di accesso a livello di account consentono di eseguire operazioni a livello di account o a livello di video. Ad esempio, caricare un video, elencare tutti i video e ottenere informazioni dettagliate sui video.
* Livello utente: i token di accesso a livello di utente consentono di eseguire operazioni a livello di utente. ad esempio ottenere gli account associati.
* Livello di video: i token di accesso a livello di video consentono di eseguire operazioni su uno specifico video. ad esempio ottenere informazioni dettagliate sul video, scaricare i sottotitoli, ottenere i widget e così via.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Con quale frequenza è necessario ottenere un nuovo token di accesso? Quando scadono i token di accesso?

I token di accesso scadono ogni ora, pertanto è necessario generare un nuovo token di accesso ogni ora. 

## <a name="billing-questions"></a>Domande sulla fatturazione

### <a name="how-much-does-video-indexer-cost"></a>Quanto costa Video Indexer?

Video Indexer usa un semplice modello di determinazione dei prezzi con pagamento in base al consumo che dipende dalla durata dell'input del contenuto indicizzato. La codifica, lo streaming, l'archiviazione, l'uso della rete e le unità riservate di codifica potrebbero richiedere costi aggiuntivi. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/).

### <a name="when-am-i-billed-for-using-video-indexer"></a>Quando viene addebitato l'utilizzo di Video Indexer?

Quando invia un video per l'indicizzazione, l'utente definirà l'indicizzazione come analisi video, analisi audio o entrambe. Questa selezione determina gli SKU che verranno addebitati. Se si verifica un errore di livello critico durante l'elaborazione, verrà restituito come risposta un codice errore. In tale caso non viene applicata alcuna fatturazione.  Un errore critico può essere provocato da un bug nel codice o da un errore critico in una dipendenza interna del server. Errori quali un'identificazione o un'estrazione di informazioni dettagliate non corretta non vengono considerati critici e viene restituita una risposta. Nei casi in cui viene restituita una risposta valida, ovvero un codice non di errore, viene applicata la fatturazione.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>È disponibile una versione di valutazione gratuita di Video Indexer?

Sì, è disponibile una versione di valutazione gratuita di Video Indexer che consente di accedere a tutte le caratteristiche del servizio e alle funzionalità dell'API. È prevista una quota di video equivalente a 600 minuti per gli utenti dell'interfaccia basata sul Web e a 2.400 minuti per gli utenti delle API. 

## <a name="next-steps"></a>Passaggi successivi

[Panoramica](video-indexer-overview.md)
