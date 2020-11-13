---
title: Valutazione e miglioramento della Riconoscimento vocale personalizzato accuratezza del servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: In questo documento si apprenderà come misurare quantitativamente e migliorare la qualità del modello di sintesi vocale o del modello personalizzato. Per il test dell'accuratezza sono necessari dati di trascrizione audio e con etichetta umana. è necessario fornire da 30 minuti a 5 ore di audio rappresentativo.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: affbf57fcda5ff9fb56e148c2fa8769e7aa775e6
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555805"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>Valutazione e miglioramento dell'accuratezza Riconoscimento vocale personalizzato

Questo articolo illustra come misurare quantitativamente e migliorare l'accuratezza dei modelli di sintesi vocale di Microsoft o di modelli personalizzati. Per il test dell'accuratezza sono necessari dati di trascrizione audio e con etichetta umana. è necessario fornire da 30 minuti a 5 ore di audio rappresentativo.

## <a name="evaluate-custom-speech-accuracy"></a>Valutare l'accuratezza di Riconoscimento vocale personalizzato

Lo standard di settore per misurare l'accuratezza del modello è la *frequenza degli errori di Word* (WER). WER conta il numero di parole non corrette identificate durante il riconoscimento, quindi divide in base al numero totale di parole fornite nella trascrizione con etichetta umana (mostrata di seguito come N). Infine, tale numero viene moltiplicato per il 100% per calcolare il WER.

![Formula WER](./media/custom-speech/custom-speech-wer-formula.png)

Le parole identificate in modo errato rientrano in tre categorie:

* Inserimento (I): parole erroneamente aggiunte nella trascrizione di ipotesi
* Eliminazione (D): parole che non sono state rilevate nella trascrizione dell'ipotesi
* Sostituzioni: parole sostituite tra i riferimenti e le ipotesi

Ecco un esempio:

![Esempio di parole erroneamente identificate](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Risolvere gli errori e migliorare WER

È possibile usare il WER dai risultati del riconoscimento del computer per valutare la qualità del modello usato con l'app, lo strumento o il prodotto. Un WER del 5%-10% è considerato una qualità ottima ed è pronto per l'uso. Un WER del 20% è accettabile, tuttavia è opportuno prendere in considerazione una formazione aggiuntiva. Un numero di messaggi pari al 30% indica una scarsa qualità e richiede la personalizzazione e la formazione.

Il modo in cui vengono distribuiti gli errori è importante. Quando vengono rilevati molti errori di eliminazione, è in genere dovuto a un livello di attendibilità del segnale audio debole. Per risolvere questo problema, è necessario raccogliere i dati audio più vicino all'origine. Gli errori di inserimento indicano che l'audio è stato registrato in un ambiente rumoroso ed è possibile che sia presente l'area interattiva, causando problemi di riconoscimento. Gli errori di sostituzione vengono spesso rilevati quando un campione insufficiente di termini specifici del dominio è stato fornito come trascrizioni con etichetta umana o testo correlato.

Analizzando i singoli file è possibile determinare il tipo di errore e gli errori che sono univoci per un file specifico. La comprensione dei problemi a livello di file consentirà di individuare i miglioramenti.

## <a name="create-a-test"></a>Creare un test

Se si vuole testare la qualità del modello di base di sintesi vocale di Microsoft o di un modello personalizzato che è stato sottoposto a training, è possibile confrontare due modelli affiancati per valutare l'accuratezza. Il confronto include i risultati di WER e il riconoscimento. In genere, un modello personalizzato viene confrontato con il modello di base di Microsoft.

Per valutare i modelli side-by-Side:

1. Accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).
2. Passare a **riconoscimento vocale > Riconoscimento vocale personalizzato > [nome del progetto] > test**.
3. Fare clic su **Aggiungi test**.
4. Selezionare **valuta accuratezza**. Assegnare al test un nome, una descrizione e selezionare il set di dati di trascrizione audio + con etichetta umana.
5. Selezionare fino a due modelli che si desidera testare.
6. Fare clic su **Crea**.

Una volta creato correttamente il test, è possibile confrontare i risultati affiancati.

### <a name="side-by-side-comparison"></a>Confronto affiancato

Una volta completato il test, indicato dalla modifica dello stato a *succeeded* , si troverà un numero wer per entrambi i modelli inclusi nel test. Fare clic sul nome del test per visualizzare la pagina dei dettagli del test. Questa pagina di dettaglio elenca tutte le espressioni del set di dati, indicando i risultati del riconoscimento dei due modelli insieme alla trascrizione dal set di dati inviato. Per esaminare il confronto affiancato, è possibile abilitare o disabilitare vari tipi di errore, tra cui inserimento, eliminazione e sostituzione. Ascoltando l'audio e confrontando i risultati del riconoscimento in ogni colonna, che mostra la trascrizione con etichetta umana e i risultati per due modelli di riconoscimento vocale, è possibile decidere quale modello soddisfi le proprie esigenze e dove siano necessari ulteriori training e miglioramenti.

## <a name="improve-custom-speech-accuracy"></a>Migliorare l'accuratezza di Riconoscimento vocale personalizzato

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
| Assistente vocale | Alta (può essere < il 10% WER) | Bassa | Bassa | Media, a causa di titoli di canzoni, nomi di prodotti o località |
| Dettatura | Alta (può essere < il 10% WER) | Bassa | Basso | Alto |
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

Le parole che sono composte o altamente specializzate possono avere pronunce univoche. Queste parole possono essere riconosciute se la parola può essere suddivisa in parole più piccole per la relativa dichiarazione. Ad esempio, per riconoscere **Xbox** , pronunciare come **X Box**. Questo approccio non aumenterà l'accuratezza complessiva, ma potrà aumentare il riconoscimento delle parole chiave.

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

* [Eseguire il training di un modello e distribuirlo](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
