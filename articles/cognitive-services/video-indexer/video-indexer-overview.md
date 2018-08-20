---
title: Panoramica di Azure Video Indexer | Microsoft Docs
description: Questo argomento fornisce informazioni generali sul servizio Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: overview
ms.date: 07/25/2018
ms.author: nolachar
ms.openlocfilehash: f52c4af29d0c7de8b5edbe869640ffc5dddb5c5e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397892"
---
# <a name="what-is-video-indexer-preview"></a>Informazioni su Video Indexer (anteprima)

Video Indexer è un'applicazione cloud creata con Analisi Servizi multimediali di Azure, Servizi cognitivi (ad esempio API Viso, Microsoft Translator, API Visione artificiale e Servizio di riconoscimento vocale personalizzato) e Ricerca di Azure. Consente di estrarre le informazioni dettagliate seguenti dai video tramite tecnologie di intelligenza artificiale:

- **Rilevamento della lingua automatico**: Video Indexer può rilevare automaticamente la lingua del video. Il rilevamento della lingua automatico supporta attualmente l'inglese, lo spagnolo, il francese, il tedesco, l'italiano, il cinese (semplificato), il giapponese e il russo. Se non è possibile rilevare la lingua, verrà usato per impostazione predefinita l'inglese.
- **Trascrizione audio**: Video Indexer include una funzionalità di riconoscimento vocale che consente ai clienti di ottenere una trascrizione del testo parlato. Le lingue supportate includono l'inglese, lo spagnolo, il francese, il tedesco, l'italiano, il cinese (semplificato), il portoghese (Brasile), il giapponese e il russo e molte altre lingue saranno disponibili in futuro. 
- **Rilevamento e identificazione di visi**: le tecnologie per il rilevamento dei visi consentono l'individuazione dei visi in un video. I visi individuati vengono confrontati con un database di celebrità per rilevare le celebrità presenti nel video. I clienti possono anche assegnare etichette a visi non corrispondenti a una celebrità. Video Indexer crea un modello di viso in base a tali etichette ed è in grado di riconoscere tali visi nei video inviati in futuro.
- **Indicizzazione del parlante**: Video Indexer consente di mappare e comprendere il parlante che ha pronunciato determinate parole e di indicare quando sono state pronunciate.
- **Riconoscimento del testo visivo**: con questa tecnologia Video Indexer estrae il testo visualizzato nei video.  
- **Rilevamento attività vocale**: il rilevamento consente a Video Indexer di separare il rumore di fondo e l'attività vocale. 
- **Rilevamento di scene**: Video Indexer è in grado di eseguire un'analisi visiva del video per determinare quando si verifica un cambiamento di scena in un video.
- **Estrazione di fotogrammi chiave**: Video Indexer rileva automaticamente i fotogrammi chiave in un video. 
- **Analisi del sentiment**: Video Indexer esegue l'analisi del sentiment sul testo estratto usando il riconoscimento vocale e il riconoscimento ottico dei caratteri (OCR) e fornisce tali informazioni sotto forma di sentiment positivo, negativo neutrale, oltre ai timecode.
- **Traduzione**: Video Indexer è in grado di tradurre la trascrizioni audio da una lingua a un'altra. Sono supportate le lingue seguenti: inglese, spagnolo, francese, tedesco, italiano, cinese (semplificato), portoghese (Brasile), giapponese e russo. Dopo la traduzione, l'utente può anche ottenere i sottotitoli nel lettore video in altre lingue.
- **Moderazione del contenuto visivo**: questa tecnologia consente il rilevamento di contenuti per adulti e/o audaci presenti nel video e può essere usata per filtrare i contenuti. 
- **Estrazione di parole chiave**: Video Indexer estrare le parole chiave in base alla trascrizione del testo parlato e del testo riconosciuto dallo strumento di rilevamento del testo visivo.
- **Etichette**: Video Indexer fornisce etichette per oggetti quali gatti, cani, tavoli, auto e per azioni come stare in piedi, correre o volare.
- **Marchi**: Video Indexer estrae i marchi aziendali in base alla trascrizione del testo parlato e del testo riconosciuto dallo strumento di rilevamento del testo visivo.

Quando Video Indexer ha completato l'elaborazione e l'analisi, è possibile verificare, modificare, eseguire ricerche e pubblicare le informazioni dettagliate sui video.

Il servizio Video Indexer soddisfa le esigenze dei gestori di contenuti e degli sviluppatori. I gestori di contenuti possono usare il portale Web di Video Indexer per utilizzare il servizio senza scrivere una singola riga di codice. Vedere [Get started using the Video Indexer portal](video-indexer-get-started.md) (Iniziare a usare il portale di Video Indexer). Gli sviluppatori possono sfruttare le API per elaborare contenuti su larga scala. Vedere [Usare l'API REST Video Indexer](video-indexer-use-apis.md). Il servizio consente anche ai clienti di usare i widget per pubblicare i flussi video e le informazioni dettagliate estratte nelle rispettive applicazioni. Vedere [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione).

È possibile iscriversi al servizio con un account AAD, LinkedIn, Facebook, Google o MSA esistente. Per altre informazioni, vedere [Getting started](video-indexer-get-started.md) (Introduzione).

## <a name="scenarios"></a>Scenari

Ecco alcuni scenari in cui Video Indexer può risultare utile.

- Ricerca: le informazioni dettagliate estratte dal video possono essere usate per migliorare l'esperienza di ricerca in un catalogo video. L'indicizzazione del testo parlato e dei visi ad esempio può consentire all'esperienza di ricerca di individuare i momenti in un video in cui una persona specifica ha pronunciato determinate parole o in cui due persone sono visualizzate insieme. La ricerca basata su tali informazioni dettagliate estratte dai video è utile per agenzie di stampa, istituti di istruzione, operatori che trasmettono eventi live, proprietari di contenuti per l'intrattenimento, app line-of-business aziendali e in genere per qualsiasi settore che ha un catalogo video in cui gli utenti devono eseguire ricerche.

- Monetizzazione: Video Indexer può contribuire al miglioramento del valore dei video. I settori che si basano ad esempio sui profitti degli annunci pubblicitari (media, social media e così via) possono offrire annunci più rilevanti usando le informazioni dettagliate estratte come segnali aggiuntivi per il server di annunci. La presentazione di un annuncio relativo a una scarpa sportiva, ad esempio, è più rilevante durante una partita di calcio rispetto a una gara di nuoto.

- Engagement degli utenti: le informazioni dettagliate estratte dai video possono essere usate per migliorare l'engagement degli utenti tramite il posizionamento dei momenti rilevanti del video per gli utenti. Si consideri ad esempio un video formativo che illustra le sfere per i primi 30 minuti e le piramidi per i 30 minuti successivi. Uno studente che sta leggendo informazioni sulle piramidi otterrebbe maggiori vantaggi se il video venisse posizionato a partire dal marcatore dei 30 minuti.

Per altre informazioni, vedere questo [blog](http://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Passaggi successivi

È ora possibile iniziare a usare Video Indexer. Per altre informazioni, vedere gli articoli seguenti:

- [Get started with the Video Indexer portal](video-indexer-get-started.md) (Introduzione al portale di Video Indexer)
- [Elaborare contenuti con l'API REST Video Indexer](video-indexer-use-apis.md)
- [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Incorporare i widget visivi nell'applicazione)
