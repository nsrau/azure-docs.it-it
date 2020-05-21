---
title: Migliorare un modello per il servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: Tipi particolari di trascrizioni con etichetta umana e testo correlato possono migliorare l'accuratezza del riconoscimento per un modello di sintesi vocale basato sullo scenario di pronuncia.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: v-demjoh
ms.openlocfilehash: bb904482f3cb5900b724803816269f1b10ab3720
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727878"
---
# <a name="improve-custom-speech-accuracy"></a>Migliorare l'accuratezza Riconoscimento vocale personalizzato

Questo articolo illustra come migliorare la qualità del modello personalizzato aggiungendo le trascrizioni audio, con etichetta umana e il testo correlato.

## <a name="accuracy-in-different-scenarios"></a>Accuratezza in scenari diversi

Gli scenari di riconoscimento vocale variano in base alla qualità audio e al linguaggio (vocabolario e stile di lingua). Nella tabella seguente vengono esaminati quattro scenari comuni:

| Scenario | Qualità audio | Vocabolario | Stile di pronuncia |
|----------|---------------|------------|----------------|
| Call center | Con una bassa 8 kHz, potrebbero essere presenti 2 persone su 1 canale audio, che potrebbero essere compressi | Narrow, univoco per dominio e prodotti | Conversazione, strutturata in maniera debole |
| Assistente vocale (ad esempio, Cortana o una finestra dell'unità) | Alta, 16 kHz | Entità Heavy (titoli di canzoni, prodotti, posizioni) | Parole e frasi chiaramente dichiarate |
| Dettatura (messaggio istantaneo, note, ricerca) | Alta, 16 kHz | Diversi | Prendere nota |
| Sottotitoli video codificati | Vario, incluso l'uso di diversi microfoni, aggiunta di musica | Vario, da riunioni, sintesi vocale, testi musicali | Lettura, preparazione o struttura a regime di controllo libero |

Diversi scenari producono risultati qualitativi diversi. Nella tabella seguente viene esaminato il modo in cui il contenuto di questi quattro scenari viene calcolato nella [frequenza degli errori di parole](how-to-custom-speech-evaluate-data.md). Nella tabella sono indicati i tipi di errore più comuni in ogni scenario.

| Scenario | Qualità riconoscimento vocale | Errori di inserimento | Errori di eliminazione | Errori di sostituzione |
|----------|----------------------------|------------------|-----------------|---------------------|
| Call center | Media (< 30% WER) | Bassa, tranne quando altri utenti discutono in background | Può essere elevato. I Call Center possono essere rumorosi e gli altoparlanti sovrapposti possono confondere il modello | Media. I nomi di prodotti e persone possono causare questi errori |
| Assistente vocale | Alta (può essere < il 10% WER) | Basso | Basso | Media, a causa di titoli di canzoni, nomi di prodotti o località |
| Dettatura | Alta (può essere < il 10% WER) | Basso | Basso | Alta |
| Sottotitoli video codificati | Dipende dal tipo di video (può essere < 50% WER) | Basso | Può essere elevato a causa di musica, rumori, qualità del microfono | Il gergo potrebbe causare questi errori |

La determinazione dei componenti di WER (numero di errori di inserimento, eliminazione e sostituzione) consente di determinare il tipo di dati da aggiungere per migliorare il modello. Usare il [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech) per visualizzare la qualità di un modello di base. Il portale riporta le frequenze degli errori di inserimento, sostituzione ed eliminazione combinate nel tasso di qualità di WER.

## <a name="improve-model-recognition"></a>Migliorare il riconoscimento del modello

È possibile ridurre gli errori di riconoscimento aggiungendo dati di training nel [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech). 

Pianificare la gestione del modello personalizzato aggiungendo periodicamente i materiali di origine. Il modello personalizzato necessita di formazione aggiuntiva per tenere conto delle modifiche apportate alle entità. Ad esempio, potrebbe essere necessario aggiornare i nomi dei prodotti, i nomi di canzoni o le nuove posizioni dei servizi.

Le sezioni seguenti descrivono in che modo ogni tipo di dati di training aggiuntivi può ridurre gli errori.

### <a name="add-related-text-sentences"></a>Aggiungere frasi di testo correlate

Le frasi di testo correlate aggiuntive possono principalmente ridurre gli errori di sostituzione correlati a errori di riconoscimento di parole comuni e parole specifiche del dominio visualizzandoli nel contesto. Le parole specifiche del dominio possono essere parole non comuni o composte, ma la loro pronuncia deve essere semplice da riconoscere.

> [!NOTE]
> Evitare le frasi di testo correlate che includono rumori come caratteri o parole non riconoscibili.

### <a name="add-audio-with-human-labeled-transcripts"></a>Aggiungere audio con trascrizioni con etichetta umana

L'audio con trascrizioni con etichetta umana offre i miglioramenti più accurati se l'audio deriva dal caso d'uso di destinazione. Gli esempi devono coprire l'ambito completo del riconoscimento vocale. Un Call Center per un negozio al dettaglio, ad esempio, può ottenere la maggior parte delle chiamate su swimwear e occhialini durante i mesi estivi. Assicurarsi che l'esempio includa l'ambito completo del riconoscimento vocale che si vuole rilevare.

Considerare i seguenti dettagli:

* Riconoscimento vocale personalizzato possibile acquisire solo il contesto di Word per ridurre gli errori di sostituzione, non gli errori di inserimento o eliminazione.
* Evitare esempi che includono errori di trascrizione, ma includere una varietà di qualità audio.
* Evitare frasi non correlate al dominio del problema. Le frasi non correlate possono danneggiare il modello.
* Quando la qualità delle trascrizioni varia, è possibile duplicare frasi eccezionalmente valide, come le trascrizioni eccezionali che includono frasi chiave, per aumentarne il peso.

### <a name="add-new-words-with-pronunciation"></a>Aggiungi nuove parole con la pronuncia

Le parole che sono composte o altamente specializzate possono avere pronunce univoche. Queste parole possono essere riconosciute se la parola può essere suddivisa in parole più piccole per la relativa dichiarazione. Ad esempio, per riconoscere **Xbox**, pronunciare come **X Box**. Questo approccio non aumenterà l'accuratezza complessiva, ma potrà aumentare il riconoscimento delle parole chiave.

> [!NOTE]
> Questa tecnica è disponibile solo per alcune lingue al momento. Per informazioni dettagliate, vedere Personalizzazione per la pronuncia nella [tabella speech-to-text](language-support.md) .

## <a name="sources-by-scenario"></a>Origini per scenario

La tabella seguente illustra gli scenari di riconoscimento vocale ed elenca i materiali di origine da considerare nelle tre categorie di contenuto di training elencate in precedenza.

| Scenario | Frasi testuali correlate | Trascrizioni audio + con etichetta umana | Nuove parole con pronuncia |
|----------|------------------------|------------------------------|------------------------------|
| Call center             | documenti di marketing, siti Web, revisioni di prodotti correlati all'attività del Call Center | chiamate al Call Center trascritte dagli utenti | termini con pronunce ambigue (vedere la versione precedente di Xbox) |
| Assistente vocale         | elencare le frasi usando tutte le combinazioni di comandi ed entità | registrare i comandi in lingua vocale nel dispositivo e trascriverli in testo | nomi (filmati, canzoni, prodotti) con pronunce univoche |
| Dettatura               | input scritto, ad esempio messaggi istantanei o messaggi di posta elettronica | simile a sopra | simile a sopra |
| Sottotitoli video codificati | TV Show scripts, movies, content marketing, video riepiloghi | trascrizioni esatte dei video | simile a sopra |

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training del modello](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Preparare e testare i dati](how-to-custom-speech-test-data.md)
- [Esaminare i dati](how-to-custom-speech-inspect-data.md)