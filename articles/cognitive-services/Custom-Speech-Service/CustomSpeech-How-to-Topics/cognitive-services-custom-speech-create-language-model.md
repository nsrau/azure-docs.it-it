---
title: Esercitazione per creare un modello linguistico con il servizio di riconoscimento vocale personalizzato - Servizi cognitivi Microsoft | Microsoft Docs
description: In questa esercitazione si apprenderà come creare un modello linguistico con il servizio di riconoscimento vocale personalizzato in servizi cognitivi Microsoft.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ms.openlocfilehash: 6af2da9ffc7678a58fcf1c647ba89c586066d2ad
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339097"
---
# <a name="tutorial-create-a-custom-language-model"></a>Esercitazione: Creare un modello linguistico personalizzato

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

In questa esercitazione si crea un modello linguistico personalizzato per le query di testo o per le espressioni che si prevede vengano digitate o pronunciate dagli utenti in un'applicazione. È quindi possibile usare il modello insieme ai modelli conversione voce/testo Microsoft avanzati esistenti per aggiungere l'interazione vocale alla propria applicazione.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Preparare i dati
> * Importare il set di dati linguistici
> * Creare il modello linguistico personalizzato

Se non si ha un account Servizi cognitivi, crearne [uno gratuito](https://cris.ai) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi che l'account Servizi cognitivi sia collegato a una sottoscrizione aprendo la pagina [Sottoscrizioni Servizi cognitivi](https://cris.ai/Subscriptions).

Se non sono elencate sottoscrizioni, è possibile far creare un account a Servizi cognitivi facendo clic sul pulsante **Get free subscription (Ottieni sottoscrizione gratuita)**. In alternativa, è possibile connettersi a una sottoscrizione al servizio di ricerca personalizzato nel portale di Azure facendo clic sul pulsante **Connect existing subscription (Connetti a una sottoscrizione esistente)**.

Per informazioni sulla creazione di una sottoscrizione al servizio di ricerca personalizzata nel portale di Azure, vedere [Creare un account delle API Servizi cognitivi nel portale di Azure](../../cognitive-services-apis-create-account.md).

## <a name="prepare-the-data"></a>Preparare i dati

Per creare un modello linguistico personalizzato per l'applicazione, è necessario fornire un elenco di espressioni di esempio al sistema, ad esempio:

*   "Ha avuto l'orticaria per tutta la scorsa settimana."
*   "Il paziente presentava una cicatrice da erniorrafia guarita."

Le frasi non devono essere necessariamente complete o corrette da un punto di vista grammaticale, ma piuttosto corrispondere con precisione all'input vocale che si prevede il sistema riceverà durante la distribuzione. Questi esempi devono riflettere lo stile e il contenuto dell'attività che gli utenti effettueranno con l'applicazione.

I dati del modello linguistico devono essere scritti in un file di testo normale con la codifica US-ASCII o UTF-8, a seconda della lingua. Per en-US, sono supportate entrambe le codifiche. Per zh-CN, è supportata solo la codifica UTF-8 (BOM è opzionale). Il file di testo deve contenere un esempio (frase, espressione o query) per riga.

Se si desidera che alcuni frasi abbiano un peso (importanza) maggiore, è possibile aggiungerle più volte ai dati. Un buon numero di ripetizioni è compreso tra 10 e 100. Se si normalizzano le voci sulla cifra 100, è possibile pesare le frasi con facilità in relazione a questa cifra.

Nella tabella seguente sono riepilogati i requisiti principali per i dati linguistici.

| Proprietà | Valore |
|----------|-------|
| Codifica testo | en-US: US-ACSII o UTF-8 oppure zh-CN: UTF-8|
| N. di espressioni per riga | 1 |
| Massima dimensione di file | 200 MB |
| Osservazioni | evitare di ripetere i caratteri più di 4 volte, ad esempio "aaaaa"|
| Osservazioni | non inserire caratteri speciali come "\t" o altri caratteri UTF-8 successivi a U+00A1 nella [tabella dei caratteri Unicode](http://www.utf8-chartable.de/)|
| Osservazioni | anche gli URI verranno rifiutati in quanto non esiste un modo univoco per pronunciarli|

Quando viene importato, il testo viene normalizzato per poter essere elaborato dal sistema. Tuttavia, esistono alcune importanti normalizzazioni da effettuare _prima_ di caricare i dati. Vedere le [linee guida per la trascrizione](cognitive-services-custom-speech-transcription-guidelines.md) per determinare la lingua appropriata quando si preparano i dati linguistici.

## <a name="import-the-language-data-set"></a>Importare il set di dati linguistici

Fare clic sul pulsante "Importa" nella riga "Set di dati acustici", sul sito verrà visualizzata una pagina per caricare un nuovo set di dati.

Quando si è pronti per importare il set di dati linguistici, accedere al [portale del servizio di riconoscimento vocale personalizzato](https://cris.ai).  Quindi fare clic sul menu a discesa "Riconoscimento vocale personalizzato" nella barra multifunzione superiore e selezionare "Adaptation Data" (Dati di adattamento). Se è la prima volta che si caricano dati nel servizio di riconoscimento vocale personalizzato, si vedrà una tabella vuota chiamata "Set di dati".

Per importare un nuovo set di dati, fare clic sul pulsante "Importa" nella riga "Set di dati linguistici", sul sito verrà visualizzata una pagina per caricare un nuovo set di dati. Immettere un nome e una descrizione per identificare il set di dati in futuro. Quindi, usare il pulsante "Scegli file" per individuare il file di testo dei dati linguistici. Successivamente, fare clic su "Importa". Il set di dati verrà caricato. A seconda delle dimensioni del set di dati, l'operazione può richiedere diversi minuti.

![provare](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-import.png)

Una volta completata l'importazione, verrà nuovamente visualizzata la tabella dei dati linguistici insieme a una voce che corrisponde al set di dati linguistici. Si noti che al set è stato assegnato un ID univoco (GUID). I dati presenteranno anche uno stato che riflette lo stato corrente. Lo stato sarà "Waiting" (In attesa) mentre sono in coda per l'elaborazione, "Processing" (In elaborazione) durante la convalida e "Complete" (Completato) quando sono pronti all'uso. La convalida dei dati esegue una serie di controlli sul testo nel file e alcune normalizzazioni del testo dei dati.

Quando lo stato è "Complete" (Completato), è possibile scegliere "View Report" (Visualizza report) per visualizzare il report di verifica dei dati linguistici. Viene visualizzato il numero di espressioni che hanno passato o meno la verifica insieme ai dettagli per le espressioni non accettate. Nell'esempio seguente, due voci non hanno passato la verifica a causa di caratteri non corretti. In questo set di dati, la prima aveva due emoticon, mentre la seconda presentava diversi caratteri non presenti nel set di caratteri ASCII stampabili.

![provare](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-report.png)

Quando lo stato del set di dati linguistici è "Complete" (Completato), è possibile usare il set per creare un modello linguistico personalizzato.

![provare](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Creare un modello linguistico personalizzato

Quando i dati linguistici sono pronti, fare clic su "Language Models" (Modelli linguistici) dal menu a discesa "Menu" per avviare il processo di creazione del modello linguistico personalizzato. Questa pagina contiene una tabella denominata "Language Models" (Modelli linguistici) contenente i modelli linguistici personalizzati correnti. Se non sono stati ancora creati modelli, la tabella sarà vuota. Il titolo della tabella corrisponde alla lingua corrente. Se si desidera creare un modello per una lingua diversa, fare clic su "Change Locale" (Cambia lingua). È possibile trovare altre informazioni sulle lingue supportate nella sezione su come [cambiare la lingua](cognitive-services-custom-speech-change-locale.md). Per creare un nuovo modello, fare clic sul collegamento "Crea nuovo" sotto al titolo della tabella.

Nella pagina "Crea modello linguistico", immettere un "Nome" e una "Descrizione" per tenere traccia delle informazioni pertinenti sul modello, ad esempio il set di dati usato. Quindi, selezionare "Base Language Model" (Modello linguistico di base) dal menu a discesa. Questo modello sarà il punto di partenza per la personalizzazione. Esistono due modelli linguistici di base tra cui scegliere. _Microsoft Search and Dictation LM_ (Modello linguistico Microsoft per ricerca e dettatura) è appropriato per un parlato diretto a un'applicazione, ad esempio comandi, query di ricerca o dettatura. _Microsoft Conversational LM_ (Modello linguistico Microsoft per conversazione) è adatto per il riconoscimento vocale in uno stile di conversazione. Questo tipo di parlato è solitamente diretto a un'altra persona e si verifica nei call center o nelle riunioni.

Dopo aver specificato il modello linguistico di base, selezionare il set di dati linguistici da usare per la personalizzazione dal menu a discesa "Dati linguistici"

![provare](../../../media/cognitive-services/custom-speech-service/custom-speech-language-models-create2.png)

Come per la creazione del modello acustico, è possibile scegliere facoltativamente di eseguire il test offline del nuovo modello al termine dell'elaborazione. Poiché si tratta di una valutazione delle prestazioni del riconoscimento vocale, il test offline richiede un set di dati acustici.

Per eseguire il test offline del modello linguistico, selezionare la casella di controllo accanto a "Test offline". Selezionare quindi un modello acustico dal menu a discesa. Se non sono stati creati modelli acustici personalizzati, nel menu saranno presenti solo quelli Microsoft di base. Se è stato scelto un modello linguistico base colloquiale, sarà necessario usare qui un modello acustico colloquiale. Se invece è stato scelto un modello per ricerca e dettatura, sarà necessario selezionare il modello acustico equivalente.

Infine, selezionare il set di dati acustici da usare per eseguire la valutazione.

Quando si è pronti per avviare l'elaborazione, premere "Crea". Si verrà riportati alla tabella dei modelli linguistici. Sarà presente una nuova voce nella tabella, corrispondente a questo modello. Lo stato riflette quello del modello e cambierà diverse volte, passando ad esempio a “Waiting” (In attesa), “Processing” (Elaborazione) e “Complete” (Completato).

Quando il modello raggiunge lo stato "Complete" (Completato), può essere distribuito a un endpoint. Facendo clic su "View Result" (Visualizza risultato) vengono visualizzati i risultati del test offline, se eseguito.

Se in qualsiasi momento si desidera modificare il nome o la descrizione del modello, è possibile usare il collegamento "Edit" (Modifica) nella riga pertinente della tabella dei modelli linguistici.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato sviluppato un modello linguistico personalizzato da usare con un testo. Per creare un modello acustico personalizzato da usare con file e trascrizioni audio, procedere all'esercitazione su come creare un modello acustico.

> [!div class="nextstepaction"]
> [Creare un modello acustico personalizzato](cognitive-services-custom-speech-create-acoustic-model.md)
