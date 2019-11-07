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
ms.openlocfilehash: 40b226796b4dfb9aced3c6b00eba1a12bad66894
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506156"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Caricare un file di origine in un BLOB di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md)

## <a name="download-and-install-the-api-client-library"></a>Scaricare e installare la libreria client dell'API

Per eseguire l'esempio, è necessario generare la libreria Python per l'API REST tramite [Swagger](https://swagger.io).

Seguire questa procedura per l'installazione:

1. Passare a https://editor.swagger.io.
1. Fare clic su **File** e quindi su **Import URL** (URL di importazione).
1. Immettere l'URL di Swagger che include l'area della sottoscrizione dei servizi Voce: `https://<your-region>.cris.ai/docs/v2.0/swagger`.
1. Fare clic su **Generate Client** (Genera client) e selezionare **Python**.
1. Salvare la libreria client.
1. Estrarre il file python-client-generated.zip scaricato in un punto qualsiasi del file system.
1. Installare il modulo python-client estratto nell'ambiente Python usando pip: `pip install path/to/package/python-client`.
1. Il nome del pacchetto installato è `swagger_client`. Per verificare se l'installazione è stata completata correttamente, usare il comando `python -c "import swagger_client"`.

> **Nota:** A causa di un [bug noto nella generazione automatica di Swagger](https://github.com/swagger-api/swagger-codegen/issues/7541), è possibile che si verifichino errori durante l'importazione del pacchetto `swagger_client`.
> Questi errori possono essere corretti eliminando la riga con il contenuto
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> dal file `swagger_client/models/model.py` e la riga con il contenuto
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> dal file `swagger_client/models/inner_error.py` all'interno del pacchetto installato. Il messaggio di errore indica la posizione in cui si trovano questi file per l'installazione.

## <a name="install-other-dependencies"></a>Installare altre dipendenze

L'esempio usa la libreria `requests`. È possibile installarla con il comando

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere codice che funge da scheletro del progetto.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]
(Sarà necessario sostituire i valori di `YourSubscriptionKey`, `YourServiceRegion` e `YourFileUrl` con valori personalizzati.)

## <a name="create-and-configure-an-http-client"></a>Creare e configurare il client HTTP
Per prima cosa è necessario un client HTTP con un URL di base e un set di autenticazione corretti.
Inserire questo codice in `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Generare una richiesta di trascrizione
Successivamente, verrà generata la richiesta di trascrizione. Aggiungere questo codice a `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Inviare la richiesta e controllarne lo stato
A questo punto, viene inviata la richiesta al servizio Voce e si controlla il codice di risposta iniziale. Questo codice di risposta indicherà semplicemente se il servizio ha ricevuto la richiesta. Il servizio restituirà un URL nelle intestazioni della risposta, che è il percorso in cui verrà memorizzato lo stato della trascrizione.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Attendere il completamento della trascrizione
Poiché il servizio elabora la trascrizione in modo asincrono, ogni tanto è necessario eseguirne il polling dello stato. La verifica avverrà ogni 5 secondi.

Enumerare tutte le trascrizioni elaborate da questa risorsa del servizio Voce e cercare quella creata.

Di seguito è riportato il codice di polling con lo stato di visualizzazione di tutti gli elementi, ad eccezione di un completamento positivo, che verrà visto successivamente.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Visualizzare i risultati della trascrizione
Una volta che il servizio ha completato correttamente la trascrizione, i risultati verranno memorizzati in un altro URL che è possibile ottenere dalla risposta di stato.

Si ottiene e si visualizza il risultato JSON.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>Controllare il codice
A questo punto, il codice dovrà avere questo aspetto: (In questa versione sono stati aggiunti alcuni commenti) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Compilare ed eseguire l'app

A questo punto è possibile compilare l'app e testare il riconoscimento vocale con il servizio Voce.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
