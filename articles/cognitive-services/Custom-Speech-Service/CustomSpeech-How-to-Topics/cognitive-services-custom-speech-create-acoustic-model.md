---
title: Esercitazione per creare un modello acustico con Servizio di riconoscimento vocale personalizzato - Servizi cognitivi Microsoft | Microsoft Docs
description: In questa esercitazione si apprenderà come creare un modello acustico con Servizio di riconoscimento vocale personalizzato in Servizi cognitivi Microsoft.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ms.openlocfilehash: 0e4c21a064cdb0a60aef49482eee4b768112b899
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216422"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Esercitazione: Creare un modello acustico personalizzato

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

In questa esercitazione si crea un modello acustico personalizzato per i dati del parlato che l'applicazione dovrà riconoscere. La creazione di un modello acustico personalizzato è utile se l'applicazione è progettata per essere usata in un ambiente specifico, ad esempio una fabbrica rumorosa, o da una particolare popolazione di utenti.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Preparare i dati
> * Importare il set di dati acustico
> * Creare il modello acustico personalizzato

Se non si dispone di un account per Servizi cognitivi, crearne [uno gratuito](https://cris.ai) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi che l'account Servizi cognitivi sia collegato a una sottoscrizione aprendo la pagina [Sottoscrizioni Servizi cognitivi](https://cris.ai/Subscriptions).

Se non sono elencate sottoscrizioni, è possibile far creare un account a Servizi cognitivi facendo clic sul pulsante **Get free subscription (Ottieni sottoscrizione gratuita)**. In alternativa, è possibile connettersi a una sottoscrizione al servizio di ricerca personalizzato nel portale di Azure facendo clic sul pulsante **Connect existing subscription (Connetti a una sottoscrizione esistente)**.

Per informazioni sulla creazione di una sottoscrizione al servizio di ricerca personalizzata nel portale di Azure, vedere [Creare un account delle API Servizi cognitivi nel portale di Azure](../../cognitive-services-apis-create-account.md).

## <a name="prepare-the-data"></a>Preparare i dati

Per personalizzare il modello acustico per un particolare contesto, è necessario disporre di una raccolta di dati di riconoscimento vocale. Questa raccolta è costituita da un set di file audio con i dati per il riconoscimento vocale e da un file di testo con le trascrizioni di ciascun file audio. I dati audio devono essere rappresentativi del contesto in cui si desidera usare il servizio di riconoscimento.

Ad esempio:

*   Se si intende utilizzare il riconoscimento vocale in una fabbrica rumorosa, i file audio devono contenere persone che parlano in una fabbrica rumorosa.
<a name="Preparing data to customize the acoustic model"></a>
*   Se si è interessati a ottimizzare le prestazioni per un unico parlante, ad esempio, si vogliono trascrivere i famosi discorsi radiofonici del presidente Franklin D. Roosevelt, i file audio devono consistere di numerosi esempi di tale voce parlante.

Un set di dati acustici per la personalizzazione del modello acustico si compone di due parti: (1) un set di file audio contenente i dati per il riconoscimento vocale e (2) un file con le trascrizioni di tutti i file audio.

### <a name="audio-data-recommendations"></a>Consigli per i dati audio

*   Tutti i file audio nel set di dati devono essere salvati nel formato audio WAV (RIFF).
*   L'audio deve avere una frequenza di campionamento di 8 kHz o 16 kHz e i valori del campione devono essere salvati come interi con segno a 16 bit PCM non compressi.
*   Sono supportati solo i file audio a canale singolo (mono).
*   I file audio devono avere una lunghezza compresa tra 100 ms e 1 minuto. Ciascun file audio deve idealmente iniziare e terminare con almeno 100 ms di silenzio. I valori comuni sono compresi tra 500 ms e 1 secondo.
*   Se c'è un rumore di sottofondo nei dati, è consigliabile che siano anche presenti alcuni esempi con segmenti di silenzio più lunghi, ad esempio, di alcuni secondi, prima e/o dopo il contenuto per il riconoscimento vocale.
*   Ciascun file audio deve essere costituito da una singola espressione, ad esempio, una singola frase per la dettatura, una sola domanda o una sola battuta di un dialogo.
*   Ciascun file audio nel set di dati deve avere un nome file univoco e l'estensione "wav".
*   Il set di file audio deve risiedere in un'unica cartella senza sottodirectory e l'intero set di file audio deve essere compresso in un unico archivio ZIP.

> [!NOTE]
> Le importazioni di dati tramite il portale Web devono attualmente sottostare al limite di 2 GB, pertanto, questa è la dimensione massima che può avere del set di dati acustici. Questa dimensione corrisponde circa a 17 ore di registrazioni audio a 16 kHz o a 34 ore di registrazioni audio a 8 kHz. Nella tabella seguente sono riassunti i requisiti principali per i dati audio.
>

| Proprietà | Valore |
|---------- |----------|
| Formato file | RIFF (WAV) |
| Frequenza di campionamento | 8000 Hz o 16000 Hz |
| Canali | 1 (mono) |
| Formato del campione | PCM, interi a 16 bit |
| Durata del file | 0,1 secondi < durata < 60 secondi |
| Intervallo di silenzio | > 0,1 secondi |
| Formato di archiviazione | Zip |
| Dimensione massima del file | 2 GB |

### <a name="transcriptions"></a>Trascrizioni

Le trascrizioni di tutti i file WAV devono essere contenute in un unico file di testo normale. Ogni riga del file delle trascrizioni deve chiamarsi come uno dei file audio ed essere seguita dalla trascrizione corrispondente. Il nome del file deve essere separato dalla trascrizione mediante un carattere di tabulazione (\t).

  Ad esempio: 
```
  speech01.wav  speech recognition is awesome

  speech02.wav  the quick brown fox jumped all over the place

  speech03.wav  the lazy dog was not amused
```

Alle trascrizioni verrà applicata la normalizzazione del testo in modo che possano essere elaborate dal sistema. Tuttavia, esistono alcune importanti normalizzazioni da effettuare _prima_ di caricare i dati nel servizio di riconoscimento vocale personalizzato. Per preparare le trascrizioni, consultare la sezione [Transcription guidelines](cognitive-services-custom-speech-transcription-guidelines.md) (Linee guida per la trascrizione) per la lingua appropriata.

I passaggi seguenti vanno eseguiti nel [portale di Servizio di riconoscimento vocale personalizzato](https://cris.ai). 

## <a name="import-the-acoustic-data-set"></a>Importare il set di dati acustici

Dopo essere stati preparati, i file audio e le trascrizioni sono pronti per essere importati nel portale Web di servizio.

A tale scopo, assicurarsi prima di tutto di avere eseguito l'accesso al [portale di Servizio di riconoscimento vocale personalizzato](https://cris.ai). Quindi, fare clic sul menu a discesa "Custom Speech" (Riconoscimento vocale personalizzato) nella barra multifunzione in alto e selezionare "Adaptation Data" (Dati di adattamento). Se è la prima volta che si caricano dati nel servizio di riconoscimento vocale personalizzato, si vedrà una tabella vuota chiamata "Datasets" (Set di dati). 

Fare clic sul pulsante "Import" (Importa) nella riga "Acoustic Datasets" (Set di dati acustici). Sul sito verrà visualizzata una pagina per caricare un nuovo set di dati.

![provare](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-datasets-import.png)

Immettere _Name_ (Nome) e _Description_ (Descrizione) nelle appropriate caselle di testo. Queste informazioni sono utili per tenere traccia dei diversi set di dati caricati. Successivamente, fare clic su "Choose File" (Scegli file) per "Transcription File" (File trascrizioni) e "WAV Files" (File WAV) e selezionare il proprio file delle trascrizioni di testo e l'archivio ZIP contenente i file WAV, rispettivamente. Al termine della preparazione, fare clic su "Import" (Importa) per caricare i dati. I dati vengono caricati. Per i set di dati grandi, l'operazione potrebbe richiedere alcuni minuti.

Una volta completato il caricamento, verrà nuovamente visualizzata la tabella "Acoustic Datasets" (Set di dati acustici) insieme a una voce che corrisponde al proprio set di dati acustici. Si noti che al set è stato assegnato un ID univoco (GUID). I dati presenteranno anche uno stato che riflette lo stato corrente. Lo stato sarà "Waiting" (In attesa) mentre sono in coda per l'elaborazione, "Processing" (In elaborazione) durante la convalida e "Complete" (Completato) quando sono pronti all'uso.

La convalida dei dati consiste in una serie di controlli sui file audio per verificare il formato file, la lunghezza e la frequenza di campionamento e sul file delle trascrizioni per verificare il formato file ed eseguire alcune normalizzazione del testo.

Quando lo stato è "Complete" (Completato), è possibile fare clic su "Details" (Dettagli) per visualizzare il report di verifica dei dati acustici. Viene visualizzato il numero di espressioni che hanno superato o meno la verifica insieme ai dettagli sulle espressioni non accettate. Nell'esempio seguente, due file WAV non hanno superato la verifica a causa del formato audio non corretto. In questo set di dati, uno aveva una frequenza di campionamento non corretta e uno un formato file non corretto.

![provare](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-datasets-report.png)

Se a un certo punto si desidera modificare il nome o la descrizione del set di dati, è possibile fare clic sul collegamento "Edit" (Modifica) e modificare queste voci. Si noti che non è possibile modificare le trascrizioni o i file audio.

## <a name="create-a-custom-acoustic-model"></a>Creare un modello acustico personalizzato

Quando lo stato del set di dati acustici è "Complete" (Completato), è possibile usare il set per creare un modello acustico personalizzato. A tale scopo, fare clic su "Acoustic Models" (Modelli acustici) nel menu a discesa "Custom Speech" (Riconoscimento vocale personalizzato). Si noterà una tabella denominata "Your models" (Modelli) contenente tutti i modelli acustici personalizzati dell'utente. Al primo utilizzo questa tabella sarà vuota. Il titolo della tabella riporta la lingua corrente. Attualmente, è possibile creare modelli acustici solo per Inglese (Stati Uniti).

Per creare un nuovo modello, fare clic su "Create New" (Crea nuovo) sotto il titolo della tabella. Come prima, immettere un nome e una descrizione per identificare il modello. Ad esempio, il campo "Description" (Descrizione) può essere usato per specificare il modello di partenza e il set di dati acustici usati per creare il modello. In seguito, selezionare "“Base Acoustic Model” (Modello acustico di base) dal menu a discesa. Il modello di base è il modello che rappresenta il punto di partenza per la personalizzazione. Esistono due modelli acustici di base tra cui scegliere. _Microsoft Search and Dictation LM_ (Modello linguistico Microsoft per ricerca e dettatura) è appropriato per un parlato diretto a un'applicazione, ad esempio comandi, query di ricerca o dettatura. _Microsoft Conversational model_ (Modello acustico colloquiale Microsoft) è adatto per il riconoscimento vocale di una conversazione. Questo tipo di parlato è solitamente diretto a un'altra persona e si verifica nei call center o nelle riunioni. La latenza per i risultati parziali nei modelli colloquiali è maggiore rispetto a quella nei modelli di ricerca e dettatura.

In seguito, selezionare i dati acustici da usare per eseguire la personalizzazione tramite il menu di scelta rapida.

![provare](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-models-create2.png)

Al termine dell'elaborazione, è possibile scegliere di eseguire test offline del nuovo modello. Il test valuta la trasposizione da parlato a scritto sul set di dati acustici specificato usando il modello acustico personalizzato e segnala i risultati. Per eseguire questo test, selezionare la casella di controllo "Accuracy Testing" (Test dell'accuratezza). Selezionare quindi un modello linguistico dal menu a discesa. Se non sono stati creati modelli linguistici personalizzati, nell'elenco a discesa saranno disponibili solo i modelli linguistici di base. Vedere la [descrizione](cognitive-services-custom-speech-create-language-model.md) dei modelli linguistici di base nella guida e selezionare quello più appropriato.

Infine, selezionare il set di dati acustici da usare per eseguire la valutazione del modello personalizzato. Se si esegue il test dell'accuratezza, è importante selezionare dati acustici diversi da quelli usati per la creazione del modello per farsi un'idea realistica delle prestazioni del modello. Si noti, inoltre, che i test offline sono limitati a 1000 espressioni. Se il set di dati acustici per i test ha dimensioni maggiori, verranno valutate solo le prime 1000 espressioni.

Quando si è pronti per avviare l'esecuzione del processo di personalizzazione, premere "Create" (Crea).

Ora verrà visualizzata una nuova voce nella tabella dei modelli acustici corrispondente a questo nuovo modello. Lo stato del processo è indicato nella tabella. Gli stati possono essere “Waiting” (In attesa), “Processing” (Elaborazione) e “Complete” (Completato).

![provare](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato sviluppato un modello acustico personalizzato da usare con trascrizioni e file audio. Per creare un file linguistico personalizzato da usare con i file di testo, passare all'esercitazione sulla creazione di un modello linguistico personalizzato.

> [!div class="nextstepaction"]
> [Creare un modello linguistico personalizzato](cognitive-services-custom-speech-create-language-model.md)
