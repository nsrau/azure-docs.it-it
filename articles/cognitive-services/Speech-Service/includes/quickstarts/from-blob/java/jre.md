---
title: 'Guida introduttiva: Riconoscimento della voce archiviata in archiviazione BLOB, Java - Servizio Voce'
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
ms.openlocfilehash: d4a436540460240ebd97c39d3406b2886586a6df
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828981"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Caricare un file di origine in un BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-eclipse"></a>Aprire il progetto in Eclipse

Il primo passaggio consiste nel verificare che il progetto sia aperto in Eclipse.

1. Avviare Eclipse
2. Caricare il progetto e aprire `Main.java`.

## <a name="add-a-reference-to-gson"></a>Aggiungere un riferimento a Gson
In questo argomento di avvio rapido verrà usato un serializzatore/deserializzatore JSON esterno. Per Java abbiamo scelto [Gson](https://github.com/google/gson).

Aprire il file pom.xml e aggiungere il seguente riferimento: [!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/pom.xml?range=19-25)]

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere codice che funge da scheletro del progetto.

[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=1-13,95-105,206-207)]
(Sarà necessario sostituire i valori di `YourSubscriptionKey`, `YourServiceRegion` e `YourFileUrl` con valori personalizzati.)

## <a name="json-wrappers"></a>Wrapper JSON

Poiché le API REST accettano le richieste in formato JSON e restituiscono i risultati in JSON, è possibile interagire usando solo stringhe, ma questa opzione non è consigliata.
Per semplificare la gestione delle richieste e delle risposte, verranno dichiarate alcune classi da usare per la serializzazione/deserializzazione di JSON.

Procedere e inserire le dichiarazioni prima di `Main`.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=15-93)]

## <a name="create-and-configure-an-http-client"></a>Creare e configurare il client HTTP
Per prima cosa è necessario un client HTTP con un URL di base e un set di autenticazione corretti.
Inserire questo codice in `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=106-113)]

## <a name="generate-a-transcription-request"></a>Generare una richiesta di trascrizione
Successivamente, verrà generata la richiesta di trascrizione. Aggiungere questo codice a `Main` [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=115-116)]

## <a name="send-the-request-and-check-its-status"></a>Inviare la richiesta e controllarne lo stato
A questo punto, viene inviata la richiesta al servizio Voce e si controlla il codice di risposta iniziale. Questo codice di risposta indicherà semplicemente se il servizio ha ricevuto la richiesta. Il servizio restituirà un URL nelle intestazioni della risposta, che è il percorso in cui verrà memorizzato lo stato della trascrizione.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=118-129)]

## <a name="wait-for-the-transcription-to-complete"></a>Attendere il completamento della trascrizione
Poiché il servizio elabora la trascrizione in modo asincrono, ogni tanto è necessario eseguirne il polling dello stato. La verifica avverrà ogni 5 secondi.

È possibile controllare lo stato recuperando il contenuto nell'URL ottenuto al momento della richiesta. Quando si ottiene il contenuto, questo viene deserializzato in una delle classi helper per renderne più facile l'interazione.

Di seguito è riportato il codice di polling con lo stato di visualizzazione di tutti gli elementi, ad eccezione di un completamento positivo, che verrà visto successivamente.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=131-159,192-204)]

## <a name="display-the-transcription-results"></a>Visualizzare i risultati della trascrizione
Una volta che il servizio ha completato correttamente la trascrizione, i risultati verranno memorizzati in un altro URL che è possibile ottenere dalla risposta di stato.

Verrà scaricato il contenuto di tale URL, deserializzato il file JSON ed eseguito il ciclo nei risultati stampando il testo visualizzato.
[!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java?range=6-160-190)]

## <a name="check-your-code"></a>Controllare il codice
A questo punto, il codice dovrà avere questo aspetto: (Sono stati aggiunti alcuni commenti a questa versione) [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-blob/src/quickstart/Main.java]

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

A questo punto è possibile compilare l'app e testare il riconoscimento vocale con il servizio Voce.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
