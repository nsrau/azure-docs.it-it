---
title: Eseguire manualmente una funzione di Azure non attivata da HTTP
description: Usare una richiesta HTTP per l'esecuzione di una funzione di Azure non attivata da HTTP
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: fd7b0be967c7a0bbc605c51408448917b5222d36
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121766"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Eseguire manualmente una funzione non attivata da HTTP

Questo articolo illustra come eseguire manualmente una funzione non attivata da HTTP tramite una richiesta HTTP con formattazione speciale.

In alcuni contesti, potrebbe essere necessario eseguire una funzione di Azure su richiesta che viene attivata indirettamente.  Esempi di trigger indiretti includono [funzioni in base a una pianificazione specifica](./functions-create-scheduled-function.md) o le funzioni che vengono eseguite come risultato di un'[azione di un'altra risorsa](./functions-create-storage-blob-triggered-function.md). 

Nell'esempio seguente viene usato [Postman](https://www.getpostman.com/), ma è possibile usare [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) o qualsiasi altro strumento per inviare richieste HTTP.

## <a name="define-the-request-location"></a>Definire il percorso della richiesta

Per eseguire una funzione non attivata da HTTP, è necessario una modalità per inviare una richiesta in Azure per eseguire la funzione. L'URL usato per effettuare questa richiesta richiede un modulo specifica.

![Definire il percorso della richiesta: nome host, percorso della cartella + nome funzione](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Nome host:** La posizione pubblica dell'app per le funzioni che è costituita dal nome dell'app per le funzioni sul segno più *azurewebsites.net* o il dominio personalizzato.
- **Percorso cartella:** Per accedere alle funzioni non attivate da HTTP tramite una richiesta HTTP, è necessario inviare la richiesta tramite le cartelle *amministratore/funzioni*.
- **Nome della funzione:** Nome della funzione che si desidera eseguire.

Utilizzare questo percorso di richiesta in Postman insieme alla chiave master della funzione nella richiesta per Azure per eseguire la funzione.

> [!NOTE]
> Durante l'esecuzione in locale, la chiave della funzione non è necessaria. È possibile [chiamare la funzione](#call-the-function) direttamente omettendo l'intestazione `x-functions-key`.

## <a name="get-the-functions-master-key"></a>Ottenere la chiave master della funzione

1. Passare alla funzione nell'portale di Azure e selezionare **tasti funzione**. Quindi, selezionare il tasto funzione che si desidera copiare. 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="Individuare la chiave master da copiare." border="true":::

1. Nella sezione **modifica chiave** copiare il valore della chiave negli Appunti e quindi fare clic su **OK**.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="Copiare la chiave master negli Appunti." border="true":::

1. Dopo aver copiato la chiave di *_master* , selezionare **codice + test**, quindi selezionare **log**. Verranno visualizzati i messaggi dalla funzione registrati in questa posizione quando si esegue manualmente la funzione da Postman.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="Visualizzare i log per visualizzare i risultati del test della chiave master." border="true":::

> [!CAUTION]  
> Date le autorizzazioni elevate concesse dalla chiave master nell'app per le funzioni, è consigliabile non condividere questa chiave con terze parti o distribuirla in un'applicazione.

## <a name="call-the-function"></a>Chiamare la funzione

Aprire Postman e seguire questa procedura:

1. Immettere il **percorso della richiesta nella casella di testo dell'URL**.
1. Assicurarsi che il metodo HTTP sia impostato su **POST**.
1. Selezionare la scheda **Intestazioni**.
1. Digitare **x-Functions-Key** come prima chiave e incollare la chiave master (dagli Appunti) come valore.
1. Digitare **Content-Type** come seconda chiave e digitare **Application/JSON** come valore.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Impostazioni delle intestazioni del post." border="true":::

1. Selezionare la scheda **Corpo**.
1. Digitare **{"input": "test"}** come corpo della richiesta.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Impostazioni del corpo del post." border="true":::

1. Selezionare **Invia**.
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Invia una richiesta con postazione." border="true":::

    Postman indica quindi uno stato **202-Accettato**.

1. Quindi, ritornare alla funzione nel portale di Azure. Esaminare i log per visualizzare i messaggi provenienti dalla chiamata manuale alla funzione.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="Visualizzare i log per visualizzare i risultati del test della chiave master." border="true":::

## <a name="next-steps"></a>Passaggi successivi

- [Strategie per il test del codice in Funzioni di Azure](./functions-test-a-function.md)
- [Debug locale del trigger della Griglia di eventi di Funzioni di Azure](./functions-debug-event-grid-trigger-local.md)
