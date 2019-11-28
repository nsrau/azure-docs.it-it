---
title: Eseguire manualmente una funzione di Azure non attivata da HTTP
description: Usare una richiesta HTTP per l'esecuzione di una funzione di Azure non attivata da HTTP
author: craigshoemaker
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 8198ff6579aff839ff9aacb729e2f3f8d3472fae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230466"
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

Passare alla funzione nel portale di Azure e fare clic su **Gestisci** e trovare la sezione **chiavi Host**. Fare clic sul pulsante **copia** nella riga *_master* per copiare la chiave master negli Appunti.

![Copiare la chiave master dalla schermata di gestione delle funzioni](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Dopo aver copiato la chiave master, fare clic sul nome della funzione per tornare alla finestra del file di codice. Quindi, fare clic sulla scheda **Log**. Verranno visualizzati i messaggi dalla funzione registrati in questa posizione quando si esegue manualmente la funzione da Postman.

> [!CAUTION]  
> Date le autorizzazioni elevate concesse dalla chiave master nell'app per le funzioni, è consigliabile non condividere questa chiave con terze parti o distribuirla in un'applicazione.

## <a name="call-the-function"></a>Chiamare la funzione

Aprire Postman e seguire questa procedura:

1. Immettere il **percorso della richiesta nella casella di testo dell'URL**.
2. Assicurarsi che il metodo HTTP sia impostato su **POST**.
3. **Fare clic** sulla scheda **Intestazioni**.
4. Immettere **x-functions-key** come prima **chiave** e incollare la chiave master (dagli appunti) nella casella **valore**.
5. Immettere **Content-Type** come seconda **chiave** e immettere **application/json** come **valore**.

    ![Impostazioni delle intestazioni di Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Fare clic** sulla scheda **Corpo**.
7. Immettere **{"input": "test"}** come corpo della richiesta.

    ![Impostazioni dei corpi di Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Fare clic su **Send**.

    ![Inviare una richiesta con Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Postman indica quindi uno stato **202-Accettato**.

Quindi, ritornare alla funzione nel portale di Azure. Individuare la finestra *Log* e verranno visualizzati i messaggi provenienti dalla chiamata manuale alla funzione.

![Risultati del registro delle funzioni da una chiamata manuale](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Passaggi successivi

- [Strategie per il test del codice in Funzioni di Azure](./functions-test-a-function.md)
- [Debug locale del trigger della Griglia di eventi di Funzioni di Azure](./functions-debug-event-grid-trigger-local.md)
