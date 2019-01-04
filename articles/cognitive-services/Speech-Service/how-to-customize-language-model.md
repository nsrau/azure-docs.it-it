---
title: 'Esercitazione: Come creare un modello linguistico con il servizio di riconoscimento vocale'
titlesuffix: Azure Cognitive Services
description: Informazioni su come creare un modello linguistico con il servizio Voce. Usare il modello linguistico personalizzato insieme ai modelli conversione voce/testo Microsoft avanzati esistenti per aggiungere l'interazione vocale alla propria applicazione.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: e65fa21eefcc103f553f8e1bc47792ac372c59e3
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53975441"
---
# <a name="tutorial-create-a-custom-language-model"></a>Esercitazione: Creare un modello linguistico personalizzato

In questo documento, si crea un modello di lingua personalizzato. È quindi possibile usare il modello insieme ai modelli conversione voce/testo Microsoft avanzati esistenti per aggiungere l'interazione vocale alla propria applicazione.

Nel documento viene illustrato come:
> [!div class="checklist"]
> * Preparare i dati
> * Importare il set di dati linguistici
> * Creare il modello linguistico personalizzato

Se non si ha un account Servizi cognitivi, crearne [uno gratuito](https://azure.microsoft.com/try/cognitive-services/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi che l'account Servizi cognitivi sia collegato a una sottoscrizione aprendo la pagina [Sottoscrizioni Servizi cognitivi](https://customspeech.ai/Subscriptions).

È possibile connettersi a una sottoscrizione al servizio di riconoscimento vocale creata nel portale di Azure selezionando il pulsante **Connetti a una sottoscrizione esistente**.

Per informazioni sulla creazione di una sottoscrizione al servizio di riconoscimento vocale nel portale di Azure, consultare la pagina [Come iniziare](get-started.md).

## <a name="prepare-the-data"></a>Preparare i dati

Per creare un modello linguistico personalizzato per l'applicazione, è necessario fornire un elenco di espressioni di esempio al sistema, ad esempio:

*   "Il paziente ha l'orticaria da una settimana."
*   "Il paziente presentava una cicatrice da erniorrafia guarita."

Le frasi non devono essere necessariamente complete o corrette da un punto di vista grammaticale, ma devono corrispondere con precisione all'input vocale che si prevede riceverà il sistema durante la distribuzione. Questi esempi devono riflettere lo stile e il contenuto dell'attività che gli utenti effettueranno con l'applicazione.

I dati del modello linguistico devono essere scritti in UTF-8 BOM. Il file di testo deve contenere un esempio (frase, espressione o query) per riga.

Se si vuole che alcuni termini abbiano un peso (importanza) maggiore, è possibile aggiungere ai dati diverse espressioni che includano tali termini.

Nella tabella seguente sono riassunti i requisiti principali per i dati linguistici.

| Proprietà | Valore |
|----------|-------|
| Codifica testo | UTF-8 BOM|
| N. di espressioni per riga | 1 |
| Dimensione massima dei file | 1,5 GB |
| Osservazioni | Evitare di ripetere i caratteri più di quattro volte, ad esempio "aaaaa"|
| Osservazioni | Non inserire caratteri speciali come "\t" o altri caratteri UTF-8 successivi a U+00A1 nella [tabella dei caratteri Unicode](http://www.utf8-chartable.de/)|
| Osservazioni | anche gli URI verranno rifiutati in quanto non esiste un modo univoco per pronunciarli|

Quando viene importato, il testo viene normalizzato per poter essere elaborato dal sistema. Tuttavia, esistono alcune importanti normalizzazioni da effettuare _prima_ di caricare i dati. Vedere [Linee guida per la trascrizione](prepare-transcription.md) per determinare la lingua corretta da usare per preparare i dati linguistici.

## <a name="language-support"></a>Supporto per le lingue

Consultare l’elenco completo delle [lingue supportate](language-support.md#text-to-speech) per i modelli linguistici di **riconoscimento vocale** personalizzati.



## <a name="import-the-language-data-set"></a>Importare il set di dati linguistici

Selezionare il pulsante **Importa** nella riga **Set di dati linguistici**. Sul sito verrà visualizzata una pagina per caricare un nuovo set di dati.

Quando si è pronti per importare il set di dati linguistici, accedere al [portale del Servizio di riconoscimento vocale](https://customspeech.ai). Prima di tutto, selezionare il menu a discesa **Riconoscimento vocale personalizzato** nella barra multifunzione in alto. Quindi selezionare **Dati adattamento**. La prima volta che si caricano dati nel Servizio di riconoscimento vocale, si vedrà una tabella vuota chiamata **Set di dati**.

Per importare un nuovo set di dati, selezionare il tasto **Importa** nella riga **Set di dati linguistici**. A questo punto, il sito visualizza una pagina per il caricamento di un nuovo set di dati. Immettere un **Nome** e una **Descrizione** per identificare il set di dati in futuro, quindi scegliere le impostazioni locali.

Successivamente, usare il pulsante **Scegli file** per individuare il file di testo dei dati linguistici. Dopodiché, fare clic su **Importa** per caricare il set di dati. A seconda delle dimensioni del set di dati, l'importazione può richiedere diversi minuti.

![Prova](media/stt/speech-language-datasets-import.png)

Una volta completata l'importazione, i dati linguistici hanno una voce che corrisponde al set di dati linguistici. Si noti che al set è stato assegnato un ID univoco (GUID). I dati presenteranno anche uno stato che riflette lo stato corrente. Lo stato è **In attesa** mentre sono in coda per l'elaborazione, **In elaborazione** durante la convalida e **Completato** quando sono pronti all'uso. La convalida dei dati esegue una serie di controlli sul testo nel file. Inoltre, esegue alcune normalizzazioni del testo dei dati.

Quando lo stato è **Completato**, è possibile selezionare **Visualizza report** per visualizzare il report di verifica dei dati linguistici. Viene visualizzato il numero di espressioni che hanno superato o meno la verifica insieme ai dettagli per le espressioni non accettate. Nell'esempio seguente, due casi di verifica non riuscita a causa di caratteri non validi. (In questo set di dati, la prima riga ha due caratteri di tabulazione, la seconda ha diversi caratteri che non fanno parte set di caratteri ASCII stampabili, mentre la terza riga è vuota).

![Prova](media/stt/speech-language-datasets-report.png)

Quando lo stato del set di dati linguistici è **Completato**, può essere usato per creare un modello linguistico personalizzato.

![Prova](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Creare un modello linguistico personalizzato

Quando i dati linguistici sono pronti, selezionare **Modelli linguistici** dal menu a discesa **Menu** per avviare il processo di creazione del modello linguistico personalizzato. Questa pagina contiene una tabella denominata **Modelli linguistici** contenente i modelli linguistici personalizzati correnti. Se non sono stati ancora creati modelli, la tabella sarà vuota. Le impostazioni locali correnti sono indicate nella tabella accanto alla voce di pertinenza.

Prima di intraprendere qualsiasi azione, è necessario selezionare le impostazioni locali appropriate. Le impostazioni locali correnti sono indicate nel titolo della tabella in tutti i dati, modelli e pagine della distribuzione. Per modificare le impostazioni locali, selezionare il pulsante **Modifica impostazioni locali** sotto il titolo della tabella.  Verrà visualizzata una pagina di conferma delle impostazioni locali. Selezionare **OK** per tornare alla tabella.

Nella pagina Crea modello linguistico immettere **Nome** e **Descrizione** per tenere traccia delle informazioni pertinenti sul modello, ad esempio il set di dati usato. Quindi, selezionare **Modello linguistico di base** dal menu a discesa. Questo modello sarà il punto di partenza per la personalizzazione.

Esistono due modelli linguistici di base tra cui scegliere. Il modello Search and Dictation (Ricerca e dettatura) è appropriato per un parlato diretto a un'applicazione, ad esempio comandi, query di ricerca o dettatura. Il modello Conversational (Colloquiale) è adatto per il riconoscimento vocale di una conversazione. Questo tipo di parlato è solitamente diretto a un'altra persona e si verifica nei call center o nelle riunioni. È inoltre disponibile al pubblico un nuovo modello chiamato "Universal" (Universale). Il modello universale mira ad affrontare tutti gli scenari e a sostituire il modello di ricerca e dettatura e il modello colloquiale.

Come mostrato nell'esempio seguente, dopo aver specificato il modello linguistico di base, usare menu a discesa **Dati Linguistici** per selezionare il set di dati linguistici che si desidera usare per la personalizzazione.

![Prova](media/stt/speech-language-models-create2.png)

Come per la creazione del modello acustico, è possibile scegliere facoltativamente di eseguire il test offline del nuovo modello al termine dell'elaborazione. Le valutazioni del modello richiedono la presenza di un set di dati acustici.

Per eseguire il test offline del modello linguistico, selezionare la casella di controllo accanto a **Testing offline**. Selezionare quindi un modello acustico dal menu a discesa. Se non sono stati creati modelli acustici personalizzati, nel menu saranno presenti solo quelli Microsoft di base. Se è stato scelto un modello linguistico base colloquiale, sarà necessario usare qui un modello acustico colloquiale. Se invece è stato scelto un modello per ricerca e dettatura, sarà necessario selezionare il modello acustico equivalente.

Infine, selezionare il set di dati acustici che si desidera usare per eseguire la valutazione.

Quando si è pronti per avviare l'elaborazione, selezionare **Crea**. Successivamente, verrà visualizzata la tabella dei modelli linguistici. Sarà presente una nuova voce nella tabella, corrispondente a questo modello. Lo stato riflette lo stato del modello e cambierà diverse volte: da **In attesa** a **In elaborazione**, quindi a **Completato**.

Quando il modello raggiunge lo stato **Completato**, può essere distribuito a un endpoint. Facendo clic su **Visualizza risultato** vengono visualizzati i risultati del test offline, se eseguito.

Se in qualsiasi momento si desidera modificare il **Nome** o la **Descrizione** del modello, è possibile usare il collegamento **Modifica** nell’apposita riga della tabella dei modelli linguistici.

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di prova gratuita al Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
- [Dati di esempio in Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
