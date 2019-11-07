---
title: 'Guida introduttiva: Riconoscimento della voce archiviata in archiviazione BLOB, C++ - Servizio Voce'
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
ms.openlocfilehash: 2173dbabc83ff0a03c0cfd18e02a6f3183ef90e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506092"
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
2. Caricare il progetto e aprire `helloworld.cpp`.

## <a name="add-a-references"></a>Aggiungere i riferimenti

Per velocizzare lo sviluppo del codice, verranno usati un paio di componenti esterni:
* [CPP REST SDK](https://github.com/microsoft/cpprestsdk): una libreria client per eseguire chiamate REST a un servizio REST.
* [nlohmann/JSON](https://github.com/nlohmann/json): una pratica libreria di analisi/serializzazione/deserializzazione JSON.

Entrambi possono essere installati usando [vcpkg](https://github.com/Microsoft/vcpkg/).

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere codice che funge da scheletro del progetto.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]
(Sarà necessario sostituire i valori di `YourSubscriptionKey`, `YourServiceRegion` e `YourFileUrl` con valori personalizzati.)

## <a name="json-wrappers"></a>Wrapper JSON

Poiché le API REST accettano le richieste in formato JSON e restituiscono i risultati in JSON, è possibile interagire usando solo stringhe, ma questa opzione non è consigliata.
Per semplificare la gestione delle richieste e delle risposte, verranno dichiarate alcune classi da usare per la serializzazione/deserializzazione di JSON e alcuni metodi per assistere nlohmann/json.

Procedere e inserire le dichiarazioni prima di `recognizeSpeech`.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Creare e configurare il client HTTP
Per prima cosa è necessario un client HTTP con un URL di base e un set di autenticazione corretti.
Inserire questo codice in `recognizeSpeech` [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>Generare una richiesta di trascrizione
Successivamente, verrà generata la richiesta di trascrizione. Aggiungere questo codice a `recognizeSpeech` [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>Inviare la richiesta e controllarne lo stato
A questo punto, viene inviata la richiesta al servizio Voce e si controlla il codice di risposta iniziale. Questo codice di risposta indicherà semplicemente se il servizio ha ricevuto la richiesta. Il servizio restituirà un URL nelle intestazioni della risposta, che è il percorso in cui verrà memorizzato lo stato della trascrizione.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>Attendere il completamento della trascrizione
Poiché il servizio elabora la trascrizione in modo asincrono, ogni tanto è necessario eseguirne il polling dello stato. La verifica avverrà ogni 5 secondi.

È possibile controllare lo stato recuperando il contenuto nell'URL ottenuto al momento della richiesta. Quando si ottiene il contenuto, questo viene deserializzato in una delle classi helper per renderne più facile l'interazione.

Di seguito è riportato il codice di polling con lo stato di visualizzazione di tutti gli elementi, ad eccezione di un completamento positivo, che verrà visto successivamente.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>Visualizzare i risultati della trascrizione
Una volta che il servizio ha completato correttamente la trascrizione, i risultati verranno memorizzati in un altro URL che è possibile ottenere dalla risposta di stato.

Verrà scaricato il contenuto di tale URL, deserializzato il file JSON ed eseguito il ciclo nei risultati stampando il testo visualizzato.
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>Controllare il codice
A questo punto, il codice dovrà avere questo aspetto: (In questa versione sono stati aggiunti alcuni commenti) [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

A questo punto è possibile compilare l'app e testare il riconoscimento vocale con il servizio Voce.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]