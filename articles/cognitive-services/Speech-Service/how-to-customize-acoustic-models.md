---
title: 'Esercitazione: Creare un modello acustico con il servizio Voce'
titlesuffix: Azure Cognitive Services
description: Informazioni su come creare un modello acustico con il servizio Voce in Servizi cognitivi di Azure.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 70fc9c34599f27eb5d67b79ef823f8037ae55ba9
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215243"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Esercitazione: Creare un modello acustico personalizzato

La creazione di un modello acustico personalizzato è utile se la propria applicazione è progettata per essere usata in un ambiente specifico, ad esempio un'automobile, in presenza di specifici dispositivi di registrazione o condizioni o da parte di una particolare popolazione di utenti. Sono esempi di queste situazioni un eloquio con un forte accento, particolari rumori di sottofondo o l'uso di uno speciale microfono per la registrazione.

In questo articolo viene spiegato come:
> [!div class="checklist"]
> * Preparare i dati
> * Importare il set di dati acustico
> * Creare il modello acustico personalizzato

Se non si ha un account Servizi cognitivi di Azure, crearne [uno gratuito](https://azure.microsoft.com/try/cognitive-services) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi che l'account Servizi cognitivi sia collegato a una sottoscrizione aprendo la pagina [Cognitive Services Subscriptions](https://cris.ai/Subscriptions) (Sottoscrizioni Servizi cognitivi).

Per connettersi a una sottoscrizione del servizio Voce creata nel portale di Azure, selezionare **Connect existing subscription** (Connetti alla sottoscrizione esistente).

Per informazioni sulla creazione di una sottoscrizione al servizio Voce nel portale di Azure, vedere [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md).

## <a name="prepare-the-data"></a>Preparare i dati

Per personalizzare un modello acustico per un particolare contesto, è necessario disporre di una raccolta di dati di riconoscimento vocale. Tale raccolta può includere da alcune espressioni a centinaia di ore di parlato. La raccolta è costituita da un set di file audio con i dati per il riconoscimento vocale e da un file di testo con le trascrizioni di ciascun file audio. I dati audio devono essere rappresentativi del contesto in cui si vuole usare il servizio di riconoscimento.

Ad esempio: 

* Se si intende usare il riconoscimento vocale in un ambiente rumoroso, i file audio devono contenere persone che parlano in tale ambiente.
* Se si è interessati a ottimizzare le prestazioni per un unico parlante, ad esempio, si vogliono trascrivere i famosi discorsi radiofonici del presidente Franklin D. Roosevelt, i file audio devono essere costituiti da numerosi esempi di tale voce parlante.

Un set di dati acustico per la personalizzazione del modello acustico si compone di due parti: (1) un set di file audio contenente i dati per il riconoscimento vocale e (2) un file con le trascrizioni di tutti i file audio.

### <a name="audio-data-recommendations"></a>Consigli per i dati audio

* Tutti i file audio nel set di dati devono essere archiviati nel formato audio WAV (RIFF).
* L'audio deve avere una frequenza di campionamento di 8 kilohertz (KHz) o 16 KHz e i valori del campione devono essere archiviati come interi con segno a 16 bit PCM (Pulse Code Modulation) non compressi.
* Sono supportati solo i file audio a canale singolo (mono).
* I file audio possono avere una durata compresa tra 100 microsecondi e 1 minuto, anche se idealmente dovrebbero essere di circa 10-12 secondi. In teoria, ogni file audio deve iniziare e terminare con almeno 100 microsecondi di silenzio. Il valore più comune è compreso tra 500 microsecondi e un secondo.
* Se nei dati è presente rumore di sottofondo nei dati, è consigliabile che siano presenti anche alcuni esempi con segmenti di silenzio più lunghi, ad esempio, di alcuni secondi, prima e/o dopo il contenuto per il riconoscimento vocale.
* Ogni file audio deve essere costituito da una singola espressione, ad esempio una singola frase per la dettatura, una sola domanda o una sola battuta di un dialogo.
* A ogni file audio nel set di dati deve essere assegnato un nome file univoco e l'estensione wav.
* Il set di file audio deve risiedere in un'unica cartella senza sottodirectory e l'intero set di file audio deve essere compresso in un unico archivio ZIP.

> [!NOTE]
> Le importazioni di dati tramite il portale Web sono attualmente limitate a 2 GB, di conseguenza questa è la dimensione massima di un set di dati acustico. Questa dimensione corrisponde circa a 17 ore di audio registrato a 16 kHz oppure a 34 ore di audio registrato a 8 kHz. Nella tabella seguente sono riassunti i requisiti principali per i dati audio:
>

| Proprietà | Valore |
|---------- |----------|
| Formato file | RIFF (WAV) |
| Frequenza di campionamento | 8.000 Hertz (Hz) o 16.000 Hz |
| Canali | 1 (mono) |
| Formato del campione | PCM, interi a 16 bit |
| Durata del file | 0,1 secondi < durata < 12 secondi | 
| Intervallo di silenzio | > 0,1 secondi |
| Formato di archiviazione | zip |
| Dimensione massima del file | 2 GB |

> [!NOTE]
> I nomi file devono essere composti solo da caratteri dell'alfabeto latino ed essere conformi al formato 'nomefile.estensione'

## <a name="language-support"></a>Supporto per le lingue

Per l'elenco completo delle lingue supportate per i modelli linguistici personalizzati di **riconoscimento vocale**, vedere l'[elenco delle lingue supportate per il servizio Voce](language-support.md#speech-to-text).

### <a name="transcriptions-for-the-audio-dataset"></a>Trascrizioni per i set di dati audio

Le trascrizioni di tutti i file WAV devono essere contenute in un unico file di testo normale. Ogni riga del file delle trascrizioni deve contenere il nome di uno dei file audio ed essere seguita dalla trascrizione corrispondente. Il nome del file deve essere separato dalla trascrizione mediante un carattere di tabulazione (\t).

  Ad esempio: 
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> La trascrizione deve presentare la codifica UTF-8 BOM (byte order mark).

Alle trascrizioni viene applicata la normalizzazione del testo in modo che possano essere elaborate dal sistema. Tuttavia, esistono alcune importanti normalizzazioni da effettuare _prima_ di caricare i dati nel servizio di riconoscimento vocale personalizzato. Per la lingua appropriata da usare durante la preparazione delle trascrizioni, vedere [Linee guida per la trascrizione per l'utilizzo del servizio Voce](prepare-transcription.md).

Per eseguire i passaggi delle sezioni successive, usare il [portale del servizio Voce](https://cris.ai).

## <a name="import-the-acoustic-dataset"></a>Importare il set di dati acustico

Dopo la preparazione, i file audio e le trascrizioni sono pronti per l'importazione nel portale Web del servizio.

Per importarli, assicurarsi prima di aver eseguito l'accesso al [portale del servizio Voce](https://cris.ai). Nell'elenco a discesa **Riconoscimento vocale personalizzato** della barra multifunzione selezionare **Adaptation Data** (Dati di adattamento). Se è la prima volta che si caricano dati nel servizio Riconoscimento vocale personalizzato, viene visualizzata una tabella vuota denominata **Datasets** (Set di dati). 

Nella riga **Acoustic Datasets** (Set di dati acustici) selezionare il pulsante **Import** (Importa). Il sito visualizza una pagina per il caricamento di un nuovo set di dati.

![Pagina Import Acoustic Data (Importa dati acustici)](media/stt/speech-acoustic-datasets-import.png)

Nelle caselle **Name** (Nome) e **Description** (Descrizione) immettere le informazioni appropriate. Per tenere traccia dei diversi set di dati caricati, è utile aggiungere brevi descrizioni. 

Nelle caselle **Transcriptions file (.txt)** (File di trascrizioni (.txt)) e **Audio files (.zip)** (File audio (.zip)) selezionare **Sfoglia** e quindi il file di trascrizione in formato testo normale e l'archivio ZIP dei file WAV. Al termine della preparazione, fare clic su **Import** (Importa) per caricare i dati. I dati verranno caricati. Per i set di dati di grandi dimensioni, il processo di importazione può richiedere alcuni minuti.

Al termine del caricamento, tornare alla tabella **Acoustic Datasets** (Set di dati acustici). Viene visualizzata una voce corrispondente al set di dati acustico caricato. Tenere presente che alla voce è stato assegnato un ID univoco (GUID). Nei dati è indicato lo stato corrente, ovvero *NotStarted* (Non avviato) se è ancora in coda per l'elaborazione, *Running* (In esecuzione) se è in corso la convalida e *Complete* (Completato) quando i dati sono pronti per l'uso.

La convalida dei dati consiste in una serie di controlli sui file audio per verificare il formato file, la lunghezza e la frequenza di campionamento e sul file delle trascrizioni per verificare il formato file ed eseguire alcune normalizzazione del testo.

Quando lo stato è *Succeeded* (Riuscito), è possibile selezionare **Details** (Dettagli) per visualizzare il report della verifica dei dati acustici. Viene visualizzato il numero di espressioni che hanno superato o meno la verifica insieme ai dettagli per le espressioni non accettate. Nell'esempio nell'immagine seguente la verifica di due file WAV non è riuscita perché il formato audio non è corretto. In questo set di dati la frequenza di campionamento di un file è errata e il formato dell'altro file non è corretto.

![Pagina Adaptation Data Details (Dettagli dei dati di adattamento)](media/stt/speech-acoustic-datasets-report.png)

Se si vuole modificare il nome o la descrizione del set di dati, è possibile selezionare il collegamento **Edit** (Modifica) e modificarne le voci. Non è possibile modificare le voci relative a file audio o trascrizioni.

## <a name="create-a-custom-acoustic-model"></a>Creare un modello acustico personalizzato

Quando lo stato del set di dati è *Complete* (Completato), è possibile usare il set di dati per creare un modello acustico personalizzato. A questo scopo, selezionare **Acoustic Models** (Modelli acustici) nell'elenco a discesa **Custom Speech** (Riconoscimento vocale personalizzato). Tutti i modelli acustici personalizzati sono elencati in una tabella denominata **Your models** (Modelli). Al primo utilizzo questa tabella è vuota. Nel titolo della tabella sono indicate le impostazioni locali correnti. Attualmente, è possibile creare modelli acustici solo per la lingua inglese (Stati Uniti).

Per creare un nuovo modello, selezionare **Create New** (Crea nuovo) sotto il titolo della tabella. Come prima, immettere un nome e una descrizione per identificare il modello. Ad esempio, è possibile usare il campo **Description** (Descrizione) per registrare il modello e il set di dati acustici iniziali usati per creare il modello. 

Selezionare quindi un modello di base nell'elenco a discesa **Base Acoustic Model** (Modello acustico di base). Questo modello di base sarà il punto di partenza per la personalizzazione. Esistono due modelli acustici di base tra cui scegliere:
* Il modello **Microsoft Search and Dictation AM** (Modello acustico Microsoft per ricerca e dettatura) è appropriato per un parlato diretto a un'applicazione, ad esempio comandi, query di ricerca o dettatura. 
* Il modello **Microsoft Conversational Model** (Modello acustico colloquiale Microsoft) è adatto per il riconoscimento vocale di una conversazione. Questo tipo di parlato è solitamente diretto a un'altra persona e viene usato nei call center o nelle riunioni. 

La latenza per i risultati parziali nei modelli colloquiali è maggiore rispetto a quella nei modelli di ricerca e dettatura.

> [!NOTE]
> Al momento viene distribuito un nuovo modello **Universal** (Universale) che consente di gestire tutti gli scenari. I modelli indicati in precedenza rimarranno comunque disponibili.

Nell'elenco a discesa **Acoustic Data** (Dati acustici) selezionare i dati acustici da usare per eseguire la personalizzazione.

![Pagina Create Acoustic Model (Crea modello acustico)](media/stt/speech-acoustic-models-create2.png)

Al termine dell'elaborazione, è possibile scegliere facoltativamente di eseguire il test di accuratezza del nuovo modello. Il test valuta la conversione della voce in testo scritto per un set di dati acustici specificato usando il modello acustico personalizzato e restituisce i risultati. Per eseguire questo test, selezionare la casella di controllo **Accuracy Testing** (Test dell'accuratezza). Selezionare un modello linguistico nell'elenco a discesa. Se non sono stati creati modelli linguistici personalizzati, nell'elenco a discesa verranno visualizzati solo i modelli linguistici di base. Per selezionare il modello linguistico più appropriato, vedere [Esercitazione: Creare un modello linguistico personalizzato](how-to-customize-language-model.md).

Selezionare infine il set di dati acustico da usare per valutare il modello personalizzato. Se si esegue il test dell'accuratezza, è importante selezionare un set di dati acustico diverso da quello usato per la creazione del modello per avere un'idea realistica delle prestazioni. Il test dell'accuratezza sui dati di training non consente di valutare le prestazioni del modello adattato in condizioni reali. Il risultato sarebbe troppo ottimistico. Si noti, inoltre, che il test di accuratezza è limitato a 1000 espressioni. Se il set di dati acustico per il test ne contiene di più, verranno valutate solo le prime 1.000 espressioni.

Quando si è pronti per avviare l'esecuzione del processo di personalizzazione, selezionare **Create** (Crea).

Nella tabella dei modelli acustici viene visualizzata una nuova voce che corrisponde al nuovo modello. La tabella visualizza anche lo stato del processo, ovvero *Waiting* (In attesa), *Processing* (In fase di elaborazione) o *Complete* (Completato).

![Pagina Acoustic Models (Modelli acustici)](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di prova al servizio Voce](https://azure.microsoft.com/try/cognitive-services/)
- [Riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
- [Dati di esempio in Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
