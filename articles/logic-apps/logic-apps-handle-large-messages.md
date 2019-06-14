---
title: Gestire messaggi di grandi dimensioni - App per la logica di Azure | Microsoft Docs
description: Informazioni su come gestire messaggi di grandi dimensioni con la suddivisione in blocchi in App per la logica di Azure
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 4/27/2018
ms.author: shhurst
ms.openlocfilehash: 5aa5ea2a39a0fb9f969e965fed14063522197cda
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60303791"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Gestire messaggi di grandi dimensioni con la suddivisione in blocchi in App per la logica di Azure

Per la gestione di messaggi, App per la logica di Azure impone delle dimensioni massime per il contenuto dei messaggi. Tale limite consente di ridurre il sovraccarico creato archiviando ed elaborando messaggi di grandi dimensioni. Per gestire messaggi superiori a questo limite, App per la logica di Azure può *suddividere in blocchi* un messaggio di grandi dimensioni in messaggi più piccoli. In questo modo è comunque possibile trasferire file di grandi dimensioni tramite App per la logica di Azure in condizioni specifiche. Quando si comunica con altri servizi tramite connettori o HTTP, App per la logica di Azure è in grado di usare messaggi di grandi dimensioni, ma *solo* in blocchi. Questa condizione indica che i connettori devono supportare anche la suddivisione in blocchi, oppure che lo scambio di messaggi HTTP sottostante tra App per la logica di Azure e questi servizi deve usare la suddivisione in blocchi.

Questo articolo illustra la procedura di configurazione della suddivisione in blocchi per azioni relative alla gestione di messaggi di dimensioni maggiori del limite. I trigger dell'app per la logica non supportano la suddivisione in blocchi a causa dell'aumento del sovraccarico dovuto allo scambio di più messaggi. 

## <a name="what-makes-messages-large"></a>Che cosa rende un messaggio "di grandi dimensioni"?

Un messaggio è "di grandi dimensioni" sulla base del servizio che lo gestisce. Il limite esatto dei messaggi di grandi dimensioni varia tra App per la logica di Azure e i connettori. Sia App per la logica di Azure che i connettori non sono in grado di elaborare direttamente messaggi di grandi dimensioni, che devono quindi essere suddivisi in blocchi. Per le dimensioni massime dei messaggi di App per la logica di Azure, vedere [Limiti e configurazione per App per la logica](../logic-apps/logic-apps-limits-and-config.md).
Per le dimensioni massime dei messaggi di un connettore, vedere i [dettagli tecnici specifici di tale connettore](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Gestione di messaggi in blocchi per App per la logica di Azure

App per la logica di Azure non è in grado di usare direttamente gli output di messaggi suddivisi in blocchi maggiori delle dimensioni massime del messaggio. Solo le azioni che supportano la suddivisione in blocchi possono accedere al contenuto dei messaggi in questi output. In tal caso, un'azione che gestisce messaggi di grandi dimensioni deve soddisfare *entrambi* questi criteri:

* Supporto nativo della suddivisione in blocchi quando tale azione appartiene a un connettore. 
* Supporto della suddivisione in blocchi abilitato nella configurazione di esecuzione dell'azione. 

In caso contrario, quando si tenta di accedere ad output di contenuti di grandi dimensioni si verificherà un errore di runtime. Per abilitare la suddivisione in blocchi, vedere [Set up chunking support](#set-up-chunking) (Configurare il supporto della suddivisione in blocchi).

### <a name="chunked-message-handling-for-connectors"></a>Gestione dei messaggi in blocchi per i connettori

I servizi che comunicano con App per la logica di Azure possono avere un proprio limite di dimensione dei messaggi. Tale limite è spesso inferiore rispetto al limite di App per la logica di Azure. Ad esempio, supponendo che supporti la suddivisione in blocchi, un connettore può considerare un messaggio di 30 MB come di grandi dimensioni, diversamente da App per la logica di Azure. Per rispettare il limite del connettore, App per la logica di Azure divide qualsiasi messaggio di dimensioni superiori a 30 MB in blocchi più piccoli.

Per i connettori che supportano la suddivisione in blocchi, il protocollo di suddivisione in blocchi sottostante è invisibile agli utenti finali. Tuttavia, non tutti i connettori supportano la suddivisione in blocchi; pertanto, questi connettori generano errori di runtime quando i messaggi in arrivo superano i limiti di dimensione dei connettori.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Configurare la suddivisione in blocchi su HTTP

In scenari HTTP generici è possibile dividere download e upload di contenuti di grandi dimensioni tramite HTTP, in modo che l'app per la logica e un endpoint possano scambiare messaggi di grandi dimensioni. I messaggi devono tuttavia essere suddivisi in blocchi nel modo previsto da App per la logica di Azure. 

Se un endpoint ha abilitato la suddivisione in blocchi per download o upload, le azioni HTTP nell'app per la logica suddividono automaticamente i messaggi di grandi dimensioni. In caso contrario, è necessario configurare il supporto della suddivisione in blocchi nell'endpoint. Se non si possiede o controlla l'endpoint o il connettore, l'opzione per configurare la suddivisione in blocchi non sarà disponibile.

Se un'azione HTTP non abilita già la suddivisione in blocchi, è necessario configurare anche la suddivisione in blocchi nella proprietà `runTimeConfiguration` dell'azione. È possibile impostare questa proprietà all'interno dell'azione, sia direttamente nell'editor della visualizzazione codice, come descritto più avanti, sia nella finestra di progettazione di App per la logica, come descritto di seguito:

1. Nell'angolo superiore destro dell'azione HTTP, scegliere il pulsante con puntini di sospensione ( **...** ), quindi scegliere **Impostazioni**.

   ![Nell'azione, aprire il menu Impostazioni](./media/logic-apps-handle-large-messages/http-settings.png)

2. In **Trasferimento di contenuto**, impostare **Consenti la divisione in blocchi** su **On**.

   ![Attivare la suddivisione in blocchi](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Per continuare a configurare la suddivisione in blocchi per download o upload, proseguire con le sezioni seguenti.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Download di contenuti in blocchi

Molti endpoint inviano automaticamente messaggi di grandi dimensioni in blocchi quando vengono scaricati tramite una richiesta HTTP GET. Per scaricare messaggi in blocchi da un endpoint tramite HTTP, l'endpoint deve supportare le richieste di contenuto parziale o i *download in blocchi*. Quando l'app per la logica invia una richiesta HTTP GET a un endpoint per il download di contenuti e l'endpoint risponde con un codice di stato "206", la risposta contiene contenuti in blocchi. App per la logica di Azure non è in grado di controllare se un endpoint supporta richieste parziali. Tuttavia, quando l'app per la logica ottiene la prima risposta "206", invia automaticamente più richieste per scaricare tutto il contenuto.

Per controllare se un endpoint è in grado di supportare contenuti parziali, inviare una richiesta HEAD. Tale richiesta consente di determinare se la risposta contiene l'intestazione `Accept-Ranges`. In questo modo, se l'endpoint supporta i download in blocchi ma non invia contenuti in blocchi, è possibile *suggerire* questa opzione impostando l'intestazione `Range` nella richiesta HTTP GET. 

Questa procedura descrive il processo dettagliato che App per la logica di Azure usa per il download di contenuti in blocchi da un endpoint all'app per la logica:

1. L'app per la logica invia una richiesta HTTP GET all'endpoint.

   L'intestazione della richiesta può includere facoltativamente un campo `Range` che descrive un intervallo di byte per la richiesta di blocchi di contenuti.

2. L'endpoint risponde con il codice di stato "206" e un corpo del messaggio HTTP.

    I dettagli del contenuto di questo blocco compaiono nell'intestazione `Content-Range` della risposta, incluse informazioni che consentono a App per la logica di Azure di determinare l'inizio e la fine del blocco, oltre alle dimensioni totali dell'intero contenuto prima della suddivisione in blocchi.

3. L'app per la logica invia automaticamente richieste HTTP GET di follow-up.

    L'app per la logica invia richieste GET di follow-up fino a quando l'intero contenuto non viene recuperato.

Ad esempio, questa definizione di azione mostra una richiesta HTTP GET che imposta l'intestazione `Range`. L'intestazione *suggerisce* che l'endpoint deve rispondere con un contenuto suddiviso in blocchi:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

La richiesta GET imposta l'intestazione "Range" (Intervallo) su "bytes= 0-1023", ovvero sull'intervallo di byte. Se l'endpoint supporta richieste di contenuto parziale, risponde con un blocco di contenuto nell'intervallo richiesto. In base all'endpoint, il formato esatto per il campo di intestazione "Range" può variare.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Upload di contenuti in blocchi

Per caricare contenuti in blocchi da un'azione HTTP, nell'azione deve essere abilitato il supporto della suddivisione in blocchi tramite la proprietà `runtimeConfiguration`. Questa impostazione consente all'azione di avviare il protocollo di suddivisione in blocchi. L'app per la logica può quindi inviare un messaggio POST o PUT iniziale all'endpoint di destinazione. Quando l'endpoint risponde con le dimensioni del blocco suggerite, l'app per la logica invia richieste HTTP PATCH di follow-up contenenti i blocchi di contenuto.

Questa procedura descrive il processo dettagliato che App per la logica di Azure usa per l'upload di contenuti in blocchi dall'app per la logica a un endpoint:

1. L'app per la logica invia una richiesta HTTP POST o PUT iniziale con corpo del messaggio vuoto. L'intestazione della richiesta include queste informazioni sul contenuto di cui l'app per la logica eseguirà il caricamento in blocchi:

   | Campo intestazione della richiesta di App per la logica di Azure | Value | Type | Descrizione |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | suddiviso in blocchi | String | Indica che il contenuto viene caricato in blocchi |
   | **x-ms-content-length** | <*content-length*> | Integer | Dimensioni dell'intero contenuto in byte prima della suddivisione in blocchi |
   ||||

2. L'endpoint risponde con il codice di stato di esito positivo"200" e queste informazioni facoltative:

   | Campo intestazione della risposta dell'endpoint | Type | Obbligatorio | Descrizione |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | Integer | No | Dimensioni del blocco suggerite in byte |
   | **Posizione** | String | No | Percorso URL a cui inviare i messaggi HTTP PATCH |
   ||||

3. L'app per la logica crea e invia messaggi HTTP PATCH di follow-up, ognuno contenente le informazioni seguenti:

   * Un blocco di contenuto basato su **x-ms-chunk-size** o su dimensioni calcolate internamente finché tutto il contenuto corrispondente a **x-ms-content-length** non viene caricato in sequenza

   * Questa intestazione descrive in dettaglio il blocco di contenuto inviato in ogni messaggio PATCH:

     | Campo intestazione della richiesta di App per la logica di Azure | Value | Type | Descrizione |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*range*> | String | Intervallo in byte del blocco di contenuto corrente, incluso il valore iniziale, il valore finale e le dimensioni totali del contenuto, ad esempio: "bytes=0-1023/10100" |
     | **Content-Type** | <*content-type*> | String | Tipo di contenuto in blocchi |
     | **Content-Length** | <*content-length*> | String | Lunghezza della dimensione in byte del blocco corrente |
     |||||

4. Dopo ogni richiesta PATCH, l'endpoint conferma la ricezione di ogni blocco rispondendo con il codice di stato "200".

Ad esempio, questa definizione di azione mostra una richiesta HTTP POST per il caricamento di contenuti in blocchi a un endpoint. Nella proprietà dell'azione `runTimeConfiguration`, la proprietà `contentTransfer` imposta `transferMode` su `chunked`:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```