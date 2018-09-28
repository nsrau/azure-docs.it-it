---
title: Informazioni su Video Indexer
titlesuffix: Azure Cognitive Services
description: Questo argomento fornisce informazioni generali sul servizio Video Indexer.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: overview
ms.date: 09/15/2018
ms.author: nolachar
ms.openlocfilehash: fd92e91989bd1a37626227b327d644c9d704ab6c
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983016"
---
# <a name="what-is-video-indexer"></a>Informazioni su Video Indexer

Video Indexer di Azure è un'applicazione cloud creata sulla base di Analisi Servizi multimediali di Azure, Ricerca di Azure, Servizi cognitivi (ad esempio API Viso, Microsoft Translator, API Visione artificiale e Servizio di riconoscimento vocale personalizzato). Consente di estrarre informazioni dettagliate dai video tramite i modelli di Video Indexer descritti di seguito:
 
- **Rilevamento automatico della lingua**: identifica automaticamente la lingua parlata dominante. Le lingue supportate sono italiano, inglese, spagnolo, francese, tedesco, cinese (semplificato), giapponese, russo e portoghese (brasiliano). Se non è possibile rilevare la lingua, verrà usato per impostazione predefinita l'inglese.
- **Trascrizione audio**: converte il parlato in testo in 10 lingue e consente estensioni. Le lingue supportate sono italiano, inglese, spagnolo, francese, tedesco, cinese (semplificato), giapponese, arabo russo e portoghese (brasiliano).
- **Sottotitoli codificati**: consente di creare sottotitoli in tre formati: VTT, TTML, SRT.
- **Elaborazione a due canali**: rileva automaticamente trascrizioni separate e le unisce in una singola sequenza temporale.
- **Riduzione del rumore**: ripulisce le registrazioni audio di telefonia o le registrazioni con rumori (in base ai filtri Skype).
- **Personalizzazione della trascrizione (CRIS)**: esegue il training ed esegue modelli di riconoscimento vocale personalizzati per creare trascrizioni specifiche del settore.
- **Enumerazione dei relatori**: identifica e riconosce quale relatore ha pronunciato determinate parole e quando.
- **Statistiche dei relatori**: fornisce statistiche in merito al parlato dei relatori.
- **Riconoscimento del testo visivo (OCR)**: estrae il testo visualizzato nel video.
- **Estrazione di fotogrammi chiave**: rileva i fotogrammi chiave stabili in un video.
- **Analisi del sentiment**: identifica sentiment positivi, negativi e neutri dal parlato e dal testo visivo.
- **Moderazione del contenuto visivo**: rileva contenuto visivo per adulti e/o razzista.
- **Estrazione di parole chiave**: estrae parole chiave dal parlato e dal testo visivo.
- **Identificazione delle etichette**: identifica gli oggetti visivi e le azioni visualizzate.
- **Estrazione di marchi**: estrae i marchi da parlato e testo visivo.
- **Rilevamento volto**: rileva e raggruppa i visi visualizzati nel video.
- **Estrazione dell'anteprima per i visi ("viso migliore")**: identifica automaticamente il viso migliore acquisito in ogni gruppo di visi (in base a qualità, dimensioni e posizione frontale) e lo estrae come risorsa immagine.
- **Identificazione di celebrità**: riconosce le celebrità nel video in base a un database di 1 milione di celebrità. Queste informazioni provengono da IMDB, Wikipedia e i principali influencer di Linkedin.
- **Identificazione dei visi personalizzata**: riconosce i visi nel video in base a un modello personalizzato sottoposto a training per l'account specifico.
- **Moderazione del contenuto testuale**: rileva testo esplicito nella trascrizione audio.
- **Rilevamento inquadratura**: determina i cambi di scena nel video.
- **Rilevamento fotogramma nero**: identifica i fotogrammi neri presentati nel video.
- **Effetti audio**: identifica effetti audio, come applausi, parlato e silenzio.
- **Inferenza degli argomenti**: deriva gli argomenti principali dalle trascrizioni. È inclusa la tassonomia [IPTC](https://iptc.org/standards/media-topics/) di primo livello.
- **Rilevamento delle emozioni**: identifica le emozioni in base a segnali vocali e audio. L'emozione potrebbe essere gioia, tristezza, rabbia o paura.
- **Artefatti**: estrae un ampio set di artefatti con "livello di dettaglio successivo" per ognuno dei modelli.
- **Traduzione**: crea traduzioni della trascrizione audio in 54 lingue diverse.

Quando Video Indexer ha completato l'elaborazione e l'analisi, è possibile verificare, modificare, eseguire ricerche e pubblicare le informazioni dettagliate sui video.

Il servizio Video Indexer soddisfa le esigenze dei gestori di contenuti e degli sviluppatori. I gestori di contenuti possono usare il portale Web di Video Indexer per utilizzare il servizio senza scrivere una singola riga di codice. Vedere l'[introduzione al sito Web di Video Indexer](video-indexer-get-started.md). Gli sviluppatori possono sfruttare le API per elaborare contenuti su larga scala. Vedere [Usare l'API REST Video Indexer](video-indexer-use-apis.md). Il servizio consente anche ai clienti di usare i widget per pubblicare i flussi video e le informazioni dettagliate estratte nelle rispettive applicazioni. Vedere [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione).

È possibile iscriversi al servizio con un account AAD, LinkedIn, Facebook, Google o MSA esistente. Per altre informazioni, vedere [Getting started](video-indexer-get-started.md) (Introduzione).

## <a name="scenarios"></a>Scenari

Ecco alcuni scenari in cui Video Indexer può risultare utile.

- Ricerca: le informazioni dettagliate estratte dal video possono essere usate per migliorare l'esperienza di ricerca in un catalogo video. L'indicizzazione del testo parlato e dei visi ad esempio può consentire all'esperienza di ricerca di individuare i momenti in un video in cui una persona specifica ha pronunciato determinate parole o in cui due persone sono visualizzate insieme. La ricerca basata su tali informazioni dettagliate estratte dai video è utile per agenzie di stampa, istituti di istruzione, operatori che trasmettono eventi live, proprietari di contenuti per l'intrattenimento, app line-of-business aziendali e in genere per qualsiasi settore che ha un catalogo video in cui gli utenti devono eseguire ricerche.

- Monetizzazione: Video Indexer può contribuire al miglioramento del valore dei video. I settori che si basano ad esempio sui profitti degli annunci pubblicitari (media, social media e così via) possono offrire annunci più rilevanti usando le informazioni dettagliate estratte come segnali aggiuntivi per il server di annunci. La presentazione di un annuncio relativo a una scarpa sportiva, ad esempio, è più rilevante durante una partita di calcio rispetto a una gara di nuoto.

- Engagement degli utenti: le informazioni dettagliate estratte dai video possono essere usate per migliorare l'engagement degli utenti tramite il posizionamento dei momenti rilevanti del video per gli utenti. Si consideri ad esempio un video formativo che illustra le sfere per i primi 30 minuti e le piramidi per i 30 minuti successivi. Uno studente che sta leggendo informazioni sulle piramidi otterrebbe maggiori vantaggi se il video venisse posizionato a partire dal marcatore dei 30 minuti.

Per altre informazioni, vedere questo [blog](http://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Passaggi successivi

È ora possibile iniziare a usare Video Indexer. Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione al sito Web di Video Indexer](video-indexer-get-started.md)
- [Elaborare contenuti con l'API REST Video Indexer](video-indexer-use-apis.md)
- [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione)
