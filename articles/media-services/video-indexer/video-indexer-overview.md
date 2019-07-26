---
title: Informazioni su Video Indexer
titlesuffix: Azure Media Services
description: Questo argomento fornisce informazioni generali sul servizio Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/22/2019
ms.author: juliako
ms.openlocfilehash: b0d0df5d113b1d75602022085b8bb17133f07333
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422972"
---
# <a name="what-is-video-indexer"></a>Informazioni su Video Indexer

Video Indexer di Azure è un'applicazione cloud creata sulla base di Analisi Servizi multimediali di Azure, Ricerca di Azure, Servizi cognitivi (ad esempio API Viso, Microsoft Translator, API Visione artificiale e Servizio di riconoscimento vocale personalizzato). Consente di estrarre informazioni dettagliate dai video tramite i modelli audio e video di Video Indexer descritti di seguito:
  
## <a name="video-insights"></a>Informazioni dettagliate sui contenuti video

- **Rilevamento volto**: rileva e raggruppa i visi visualizzati nel video.
- **Identificazione di celebrità**: Video Indexer identifica automaticamente più di 1 milione celebrità, ad esempio leader mondiali, attori, attrici, atleti, ricercatori, aziende e leader della tecnologia in tutto il mondo. I dati relativi a queste celebrità possono anche essere trovati su diversi siti Web noti, ad esempio IMDB e Wikipedia.
- **Identificazione viso basata sull'account**: Video Indexer esegue il training di un modello per un account specifico. Riconosce quindi i visi nel video in base al modello sottoposto a training. Per altre informazioni, vedere [personalizzare un modello di persona dal sito web video Indexer](customize-person-model-with-website.md) e [personalizzare un modello di persona con l'API video Indexer](customize-person-model-with-api.md).
- **Estrazione dell'anteprima per i visi** ("viso migliore"): identifica automaticamente il viso migliore acquisito in ogni gruppo di visi (in base a qualità, dimensioni e posizione frontale) e lo estrae come risorsa immagine.
- **Riconoscimento del testo visivo** (OCR): estrae il testo visualizzato nel video.
- **Moderazione del contenuto visivo**: rileva contenuto visivo per adulti e/o razzista.
- **Identificazione delle etichette**: identifica gli oggetti visivi e le azioni visualizzate.
- **Segmentazione della scena**: determina quando una scena cambia in video in base a segnali visivi. Una scena raffigura un singolo evento ed è costituita da una serie di scatti consecutivi, che sono semanticamente correlati. 
- **Rilevamento shot**: determina quando viene modificato il video in base ai segnali visivi. Un'immagine è una serie di frame ricavati dalla stessa fotocamera di movimento. Per altre informazioni, vedere [scene, scatti e fotogrammi chiave](scenes-shots-keyframes.md).
- **Rilevamento fotogramma nero**: identifica i fotogrammi neri presentati nel video.
- **Estrazione di fotogrammi chiave**: rileva i fotogrammi chiave stabili in un video.
- **Crediti in sequenza**: identificare l'inizio e la fine dei crediti in corso alla fine della TV e dei film.

## <a name="audio-insights"></a>Informazioni dettagliate sui contenuti audio

- **Rilevamento automatico della lingua**: identifica automaticamente la lingua parlata dominante. Le lingue supportate sono inglese, spagnolo, francese, tedesco, italiano, cinese (semplificato), giapponese, russo e portoghese brasiliano. Se la lingua non può essere identificata in tutta sicurezza, Video Indexer presuppone che la lingua parlata sia l'inglese. Per altre informazioni, vedere [modello di identificazione della lingua](language-identification-model.md).
- **Trascrizione audio**: converte la voce in testo in 12 lingue diverse e consente le estensioni. Le lingue supportate sono inglese, spagnolo, francese, tedesco, italiano, cinese (semplificato), giapponese, arabo, russo, Portoghese brasiliano, Hindi e coreano.
- **Sottotitoli**: consente di creare sottotitoli in tre formati: VTT, TTML e SRT.
- **Elaborazione a due canali**: rileva automaticamente trascrizioni separate e le unisce in una singola sequenza temporale.
- **Riduzione del rumore**: ripulisce le registrazioni audio di telefonia o le registrazioni con rumori (in base ai filtri Skype).
- **Personalizzazione della trascrizione** (CRIS): Consente di eseguire il training del riconoscimento vocale personalizzato nei modelli di testo per creare trascrizioni specifiche del settore. Per altre informazioni, vedere [personalizzare un modello di linguaggio dal sito Web di video Indexer](customize-language-model-with-website.md) e [personalizzare un modello di linguaggio con le API video Indexer](customize-language-model-with-api.md).
- **Enumerazione dei relatori**: identifica e riconosce quale relatore ha pronunciato determinate parole e quando.
- **Statistiche dei relatori**: fornisce statistiche in merito al parlato dei relatori.
- **Moderazione del contenuto testuale**: rileva testo esplicito nella trascrizione audio.
- **Effetti audio**: identifica effetti audio, come applausi, parlato e silenzio.
- **Rilevamento delle emozioni**: Identifica le emozioni in base al parlato (cosa viene detto) e alla tonalità vocale (come viene detto).  L'emozione potrebbe essere gioia, tristezza, rabbia o paura.
- **Traduzione**: crea traduzioni della trascrizione audio in 54 lingue diverse.

## <a name="audio-and-video-insights-multi-channels"></a>Informazioni dettagliate sui contenuti audio e video (più canali)

Quando si esegue l'indicizzazione in base a un canale, sarà disponibile il risultato parziale per tali modelli

- **Estrazione di parole chiave**: estrae parole chiave dal parlato e dal testo visivo.
- **Estrazione di marchi**: estrae i marchi da parlato e testo visivo.
- **Inferenza dell'argomento**: deduce gli argomenti principali dalle trascrizioni. È inclusa la tassonomia IPTC di primo livello.
- **Artefatti**: estrae un ampio set di artefatti con "livello di dettaglio successivo" per ognuno dei modelli.
- **Analisi del sentiment**: identifica sentiment positivi, negativi e neutri dal parlato e dal testo visivo.
 
Quando Video Indexer ha completato l'elaborazione e l'analisi, è possibile verificare, modificare, eseguire ricerche e pubblicare le informazioni dettagliate sui video.

Il servizio Video Indexer soddisfa le esigenze dei gestori di contenuti e degli sviluppatori. I gestori di contenuti possono usare il portale Web di Video Indexer per utilizzare il servizio senza scrivere una singola riga di codice. Vedere l'[introduzione al sito Web di Video Indexer](video-indexer-get-started.md). Gli sviluppatori possono sfruttare le API per elaborare contenuti su larga scala. Vedere [Usare l'API REST Video Indexer](video-indexer-use-apis.md). Il servizio consente anche ai clienti di usare i widget per pubblicare i flussi video e le informazioni dettagliate estratte nelle rispettive applicazioni. Vedere [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione).

È possibile iscriversi al servizio con un account AAD, LinkedIn, Facebook, Google o MSA esistente. Per altre informazioni, vedere [Getting started](video-indexer-get-started.md) (Introduzione).

## <a name="scenarios"></a>Scenari

Ecco alcuni scenari in cui Video Indexer può risultare utile.

- Ricerca: le informazioni dettagliate estratte dal video possono essere usate per migliorare l'esperienza di ricerca in un catalogo video. L'indicizzazione del testo parlato e dei visi ad esempio può consentire all'esperienza di ricerca di individuare i momenti in un video in cui una persona specifica ha pronunciato determinate parole o in cui due persone sono visualizzate insieme. La ricerca basata su tali informazioni dettagliate estratte dai video è utile per agenzie di stampa, istituti di istruzione, operatori che trasmettono eventi live, proprietari di contenuti per l'intrattenimento, app line-of-business aziendali e in genere per qualsiasi settore che ha un catalogo video in cui gli utenti devono eseguire ricerche.
- Creazione di contenuto: informazioni dettagliate estratte dai video e che consentono di creare in modo efficace contenuto, ad esempio trailer, contenuto di social media, clip di notizie e così via, dal contenuto esistente nell'archivio dell'organizzazione 
- Monetizzazione: Video Indexer può contribuire al miglioramento del valore dei video. I settori che si basano ad esempio sui profitti degli annunci pubblicitari (media, social media e così via) possono offrire annunci più rilevanti usando le informazioni dettagliate estratte come segnali aggiuntivi per il server di annunci. La presentazione di un annuncio relativo a una scarpa sportiva, ad esempio, è più rilevante durante una partita di calcio rispetto a una gara di nuoto.
- Engagement degli utenti: le informazioni dettagliate estratte dai video possono essere usate per migliorare l'engagement degli utenti tramite il posizionamento dei momenti rilevanti del video per gli utenti. Si consideri ad esempio un video formativo che illustra le sfere per i primi 30 minuti e le piramidi per i 30 minuti successivi. Uno studente che sta leggendo informazioni sulle piramidi otterrebbe maggiori vantaggi se il video venisse posizionato a partire dal marcatore dei 30 minuti.

## <a name="next-steps"></a>Passaggi successivi

È ora possibile iniziare a usare Video Indexer. Per altre informazioni, vedere i seguenti articoli:

- [Introduzione al sito Web di Video Indexer](video-indexer-get-started.md)
- [Elaborare contenuti con l'API REST Video Indexer](video-indexer-use-apis.md)
- [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione)
