---
title: 'Guida introduttiva: Riconoscimento della voce archiviata in archiviazione BLOB, C# - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 77ab519ae966ab6b3dfc9fd309ce9a5740a5ce0f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505892"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Caricare un file di origine in un BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Aprire il progetto in Visual Studio

Il primo passaggio consiste nel verificare che il progetto sia aperto in Visual Studio.

1. Avviare Visual Studio 2019.
2. Caricare il progetto e aprire `Program.cs`.

## <a name="add-a-reference-to-newtonsoftjson"></a>Aggiungere un riferimento a NewtonSoftJSon

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **helloworld** e quindi scegliere **Gestisci pacchetti NuGet** per visualizzare Gestione pacchetti NuGet.

1. Nell'angolo in alto a destra individuare la casella a discesa **Origine dei pacchetti** e assicurarsi che sia selezionata l'opzione **`nuget.org`** .

1. Nell'angolo in alto a sinistra fare clic su **Sfoglia**.

1. Nella casella di ricerca digitare *newtonsoft.json* e premere **INVIO**.

1. Nei risultati della ricerca selezionare il pacchetto **Newtonsoft.Json** e quindi selezionare **Installa** per installare la versione stabile più recente.

1. Accettare tutti i contratti e le licenze per avviare l'installazione.

   Dopo aver installato il pacchetto, viene visualizzato un messaggio di conferma nella finestra della **Console di Gestione pacchetti**.

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere codice che funge da scheletro del progetto.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-43,138,277)]
(Sarà necessario sostituire i valori di `YourSubscriptionKey`, `YourServiceRegion` e `YourFileUrl` con valori personalizzati.)
## <a name="json-wrappers"></a>Wrapper JSON

Poiché le API REST accettano le richieste in formato JSON e restituiscono i risultati in JSON, è possibile interagire usando solo stringhe, ma questa opzione non è consigliata.
Per semplificare la gestione delle richieste e delle risposte, verranno dichiarate alcune classi da usare per la serializzazione/deserializzazione di JSON.

Procedere e inserire le dichiarazioni dopo `TranscribeAsync`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=140-276)]

## <a name="create-and-configure-an-http-client"></a>Creare e configurare il client HTTP
Per prima cosa è necessario un client HTTP con un URL di base e un set di autenticazione corretti.
Inserire questo codice in `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=46-50)]

## <a name="generate-a-transcription-request"></a>Generare una richiesta di trascrizione
Successivamente, verrà generata la richiesta di trascrizione. Aggiungere questo codice a `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=52-57)]

## <a name="send-the-request-and-check-its-status"></a>Inviare la richiesta e controllarne lo stato
A questo punto, viene inviata la richiesta al servizio Voce e si controlla il codice di risposta iniziale. Questo codice di risposta indicherà semplicemente se il servizio ha ricevuto la richiesta. Il servizio restituirà un URL nelle intestazioni della risposta, che è il percorso in cui verrà memorizzato lo stato della trascrizione.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=59-70)]

## <a name="wait-for-the-transcription-to-complete"></a>Attendere il completamento della trascrizione
Poiché il servizio elabora la trascrizione in modo asincrono, ogni tanto è necessario eseguirne il polling dello stato. La verifica avverrà ogni 5 secondi.

È possibile controllare lo stato recuperando il contenuto nell'URL ottenuto al momento della richiesta. Quando si ottiene il contenuto, questo viene deserializzato in una delle classi helper per renderne più facile l'interazione.

Di seguito è riportato il codice di polling con lo stato di visualizzazione di tutti gli elementi, ad eccezione di un completamento positivo, che verrà visto successivamente.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=72-106,121-137)]

## <a name="display-the-transcription-results"></a>Visualizzare i risultati della trascrizione
Una volta che il servizio ha completato correttamente la trascrizione, i risultati verranno memorizzati in un altro URL che è possibile ottenere dalla risposta di stato.

Effettuare ora una richiesta per scaricare i risultati in un file temporaneo prima di leggerli e deserializzarli.
Una volta caricati, i risultati possono essere stampati sulla console.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=107-120)]

## <a name="check-your-code"></a>Controllare il codice
A questo punto, il codice dovrà avere questo aspetto: (In questa versione sono stati aggiunti alcuni commenti) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-277)]

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

A questo punto è possibile compilare l'app e testare il riconoscimento vocale con il servizio Voce.

1. **Compilare il codice**: sulla barra dei menu di Visual Studio scegliere **Compila** > **Compila soluzione**.
2. **Avviare l'app**: sulla barra dei menu scegliere **Debug** > **Avvia debug** o premere **F5**.
3. **Avviare il riconoscimento**: verrà richiesto di pronunciare una frase in inglese. La voce viene inviata al servizio Voce, trascritta come testo e visualizzata sulla console.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
