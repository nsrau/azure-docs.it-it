---
title: Come creare un modello linguistico con il servizio di riconoscimento vocale - Servizi cognitivi Microsoft | Microsoft Docs
description: Informazioni su come creare un modello linguistico con il servizio di riconoscimento vocale in Servizi cognitivi Microsoft.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 85e67be406b3d9723476821adfb09fc4db8dc1d1
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068574"
---
# <a name="tutorial-create-a-custom-language-model"></a>Esercitazione: Creare un modello linguistico personalizzato

In questo documento viene spiegato come creare un modello linguistico personalizzato da usare insieme ai modelli conversione voce/testo Microsoft avanzati esistenti per aggiungere l'interazione vocale alla propria applicazione.

Nel documento viene illustrato come:
> [!div class="checklist"]
> * Preparare i dati
> * Importare il set di dati linguistici
> * Creare il modello linguistico personalizzato

Se non si ha un account Servizi cognitivi, crearne [uno gratuito](https://azure.microsoft.com/try/cognitive-services/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi che l'account Servizi cognitivi sia collegato a una sottoscrizione aprendo la pagina [Cognitive Services Subscriptions](https://customspeech.ai/Subscriptions) (Sottoscrizioni Servizi cognitivi).

È possibile connettersi a una sottoscrizione al servizio di riconoscimento vocale creata nel portale di Azure facendo clic sul pulsante **Connect existing subscription (Connetti a una sottoscrizione esistente)**.

Per informazioni sulla creazione di una sottoscrizione al servizio di riconoscimento vocale nel portale di Azure, vedere la pagina [Try the Speech service for free](get-started.md) (Prova gratuita del servizio di riconoscimento vocale).

## <a name="prepare-the-data"></a>Preparare i dati

Per creare un modello linguistico personalizzato per l'applicazione, è necessario fornire un elenco di espressioni di esempio al sistema, ad esempio:

*   "Il paziente ha l'orticaria da una settimana."
*   "Il paziente presentava una cicatrice da erniorrafia guarita."

Le frasi non devono essere necessariamente complete o corrette da un punto di vista grammaticale, ma piuttosto corrispondere con precisione all'input vocale che si prevede riceverà il sistema durante la distribuzione. Questi esempi devono riflettere lo stile e il contenuto dell'attività che gli utenti effettueranno con l'applicazione.

I dati del modello linguistico devono essere scritti in UTF-8 BOM. Il file di testo deve contenere un esempio (frase, espressione o query) per riga.

Se si desidera che alcuni termini abbiano un peso, cioè un'importanza, maggiore, è possibile aggiungere ai dati diverse espressioni che includano tali termini. 

Nella tabella seguente sono riassunti i requisiti principali per i dati linguistici.

| Proprietà | Valore |
|----------|-------|
| Codifica testo | UTF-8 BOM|
| N. di espressioni per riga | 1 |
| Massima dimensione di file | 1,5 GB |
| Osservazioni | evitare di ripetere i caratteri più di quattro volte, ad esempio "aaaaa"|
| Osservazioni | non inserire caratteri speciali come "\t" o altri caratteri UTF-8 successivi a U+00A1 nella [tabella dei caratteri Unicode](http://www.utf8-chartable.de/)|
| Osservazioni | Anche gli URI verranno rifiutati in quanto non esiste un modo univoco per pronunciarli|

Quando viene importato, il testo viene normalizzato per poter essere elaborato dal sistema. Tuttavia, esistono alcune importanti normalizzazioni da effettuare _prima_ di caricare i dati. Vedere [Transcription guidelines](prepare-transcription.md) (Linee guida per la trascrizione) per determinare il formato da rispettare per preparare i dati linguistici.

## <a name="language-support"></a>Supporto per le lingue

Per i modelli linguistici di **riconoscimento vocale** personalizzati sono supportate le lingue seguenti.

Fare clic su per vedere l'elenco completo delle [lingue supportate](supported-languages.md)

## <a name="import-the-language-data-set"></a>Importare il set di dati linguistici

Fare clic sul pulsante "Import"(Importa) nella riga "Language Datasets" (Set di dati linguistici). Sul sito verrà visualizzata una pagina per caricare un nuovo set di dati.

Quando si è pronti per importare il set di dati linguistici, accedere al [portale del servizio di riconoscimento vocale](https://customspeech.ai).  Quindi, fare clic sul menu a discesa "Custom Speech" (Riconoscimento vocale personalizzato) nella barra multifunzione in alto e selezionare "Adaptation Data" (Dati di adattamento). La prima volta che si caricano dati nel servizio di riconoscimento vocale, si vedrà una tabella vuota chiamata "Datasets" (Set di dati).

Per importare un nuovo set di dati, fare clic sul pulsante "Import"(Importa) nella riga "Language Datasets" (Set di dati linguistici). Sul sito verrà visualizzata una pagina per caricare un nuovo set di dati. Immettere un nome e una descrizione per identificare il set di dati in futuro e scegliere la lingua. Quindi, usare il pulsante "Choose File" (Scegli file) per individuare il file di testo dei dati linguistici. Successivamente, fare clic su "Import" (Importa) per caricare il set di dati. A seconda delle dimensioni del set di dati, l'importazione può richiedere diversi minuti.

![provare](media/stt/speech-language-datasets-import.png)

Una volta completata l'importazione, verrà nuovamente visualizzata la tabella dei dati linguistici insieme a una voce che corrisponde al set di dati linguistici. Si noti che al set è stato assegnato un ID univoco (GUID). I dati presenteranno anche uno stato che riflette lo stato corrente. Lo stato sarà "Waiting" (In attesa) mentre sono in coda per l'elaborazione, "Processing" (In elaborazione) durante la convalida e "Complete" (Completato) quando sono pronti all'uso. La convalida dei dati esegue una serie di controlli sul testo nel file e alcune normalizzazioni del testo dei dati.

Quando lo stato è "Complete" (Completato), è possibile scegliere "View Report" (Visualizza report) per visualizzare il report di verifica dei dati linguistici. Viene visualizzato il numero di espressioni che hanno superato o meno la verifica insieme ai dettagli per le espressioni non accettate. Nell'esempio seguente, due voci non hanno superato la verifica a causa di caratteri non corretti. In questo set di dati, la prima riga aveva due tabulazioni, la seconda presentava diversi caratteri non presenti nel set di caratteri ASCII stampabili mentre la terza era vuota.

![provare](media/stt/speech-language-datasets-report.png)

Quando lo stato del set di dati linguistici è "Complete" (Completato), è possibile usare il set per creare un modello linguistico personalizzato.

![provare](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Creare un modello linguistico personalizzato

Quando i dati linguistici sono pronti, fare clic su "Language Models" (Modelli linguistici) dal menu a discesa "Menu" per avviare il processo di creazione del modello linguistico personalizzato. Questa pagina contiene una tabella denominata "Language Models" (Modelli linguistici) contenente i modelli linguistici personalizzati correnti. Se non sono stati ancora creati modelli, la tabella sarà vuota. Le impostazioni locali correnti sono indicate nella tabella accanto alla voce di pertinenza.

Prima di intraprendere qualsiasi azione, è necessario selezionare le impostazioni locali appropriate. Le impostazioni locali correnti sono indicate nel titolo della tabella in tutti i dati, modelli e pagine della distribuzione. Per modificare le impostazioni locali, fare clic sul pulsante "Change Locale" (Modifica impostazioni locali) sotto il titolo della tabella per accedere a una pagina di conferma delle impostazioni locali. Fare clic su "OK" per tornare alla tabella.

Nella pagina "Create Language Model" (Crea modello linguistico) immettere "Name" (Nome) e "Description" (Descrizione) per tenere traccia delle informazioni pertinenti sul modello, ad esempio il set di dati usato. Quindi, selezionare "Base Language Model" (Modello linguistico di base) dal menu a discesa. Questo modello sarà il punto di partenza per la personalizzazione. Esistono due modelli linguistici di base tra cui scegliere. Il modello Search and Dictation (Modello per ricerca e dettatura) è appropriato per un parlato diretto a un'applicazione, ad esempio comandi, query di ricerca o dettatura. Il modello Conversational (Modello colloquiale) è adatto per il riconoscimento vocale di una conversazione. Questo tipo di parlato è solitamente diretto a un'altra persona e si verifica nei call center o nelle riunioni. Un nuovo modello denominato "Universal" (Universale) è anche disponibile per il pubblico. Il modello universale mira ad affrontare tutti gli scenari e a sostituire il modello di ricerca e dettatura e il modello colloquiale.

5.  Nell'esempio seguente, dopo aver specificato il modello linguistico di base, selezionare il set di dati linguistici da usare per la personalizzazione dal menu a discesa "Language Data" (Dati linguistici)

![provare](media/stt/speech-language-models-create2.png)

Come per la creazione del modello acustico, è possibile scegliere facoltativamente di eseguire il test offline del nuovo modello al termine dell'elaborazione. Le valutazioni del modello richiedono la presenza di un set di dati acustici.

Per eseguire il test offline del modello linguistico, selezionare la casella di controllo accanto a "Offline Testing" (Testing offline). Selezionare quindi un modello acustico dal menu a discesa. Se non sono stati creati modelli acustici personalizzati, nel menu saranno presenti solo quelli Microsoft di base. Se è stato scelto un modello linguistico base colloquiale, sarà necessario usare qui un modello acustico colloquiale. Se invece è stato scelto un modello per ricerca e dettatura, sarà necessario selezionare il modello acustico equivalente.

Infine, selezionare il set di dati acustici da usare per eseguire la valutazione.

Quando si è pronti per avviare l'elaborazione, premere "Create" (Crea) per accedere alla tabella dei modelli linguistici. Sarà presente una nuova voce nella tabella, corrispondente a questo modello. Lo stato riflette quello del modello e cambierà diverse volte, passando ad esempio a “Waiting” (In attesa), “Processing” (Elaborazione) e “Complete” (Completato).

Quando il modello raggiunge lo stato "Complete" (Completato), può essere distribuito a un endpoint. Facendo clic su "View Result" (Visualizza risultato) vengono visualizzati i risultati del test offline, se eseguito.

Se in qualsiasi momento si desidera modificare il nome o la descrizione del modello, è possibile usare il collegamento "Edit" (Modifica) nella riga pertinente della tabella dei modelli linguistici.

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
- [Dati di esempio in Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
