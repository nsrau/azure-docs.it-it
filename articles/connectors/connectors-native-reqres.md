---
title: Usare le azioni di richiesta e risposta | Documentazione Microsoft
description: Panoramica del trigger e dell'azione di richiesta e risposta in un'app per la logica di Azure
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 58210db585befd7ce915d4579d4d0303eb15bff3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-request-and-response-components"></a>Introduzione ai componenti di richiesta e risposta
Con i componenti di richiesta e risposta in un'app per la logica è possibile rispondere in tempo reale agli eventi.

Ad esempio, è possibile:

* Rispondere a una richiesta HTTP con i dati di un database locale tramite un'app per la logica.
* Attivare un'app per la logica da un evento webhook esterno.
* Chiamare un'app per la logica con un'azione di richiesta e risposta dall'interno di un'altra app per la logica.

Per informazioni su come iniziare a usare le azioni di richiesta e risposta in un'app per la logica, vedere [Creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-request-trigger"></a>Usare il trigger di richiesta HTTP
Un trigger è un evento che può essere usato per avviare il flusso di lavoro definito in un'app per la logica. [Altre informazioni sui trigger](connectors-overview.md).

Ecco una sequenza di esempio di come configurare una richiesta HTTP nella finestra di progettazione dell'app per la logica.

1. Aggiungere il trigger **Richiesta - Alla ricezione di una richiesta HTTP** all'app per la logica. È possibile facoltativamente fornire uno schema JSON (usando uno strumento come [JSONSchema.net](http://jsonschema.net)) per il corpo della richiesta. In questo modo la finestra di progettazione potrà generare i token per le proprietà nella richiesta HTTP.
2. Aggiungere un'altra azione per poter salvare l'app per la logica.
3. Dopo il salvataggio dell'app per la logica, è possibile ottenere l'URL della richiesta HTTP dalla scheda di richiesta.
4. Un HTTP POST (è possibile usare uno strumento come [Postman](https://www.getpostman.com/)) all'URL attiva l'app per la logica.

> [!NOTE]
> Se non viene definita un'azione di risposta, una risposta `202 ACCEPTED` viene restituita immediatamente al chiamante. È possibile usare l'azione di risposta per personalizzare una risposta.
> 
> 

![Trigger di risposta](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Usare l'azione di risposta HTTP
L'azione di risposta HTTP è valida solo quando viene usata in un flusso di lavoro attivato da una richiesta HTTP. Se non viene definita un'azione di risposta, una risposta `202 ACCEPTED` viene restituita immediatamente al chiamante.  È possibile aggiungere un'azione di risposta in qualsiasi passaggio nel flusso di lavoro. L'app per la logica mantiene aperta la richiesta in ingresso per una risposta solo per un minuto.  Dopo un minuto, se non è stata inviata alcuna risposta dal flusso di lavoro (e nella definizione è presente un'azione di risposta), viene restituito un `504 GATEWAY TIMEOUT` al chiamante.

Ecco come aggiungere un'azione di risposta HTTP:

1. Fare clic sul pulsante **Nuovo passaggio** .
2. Selezionare **Aggiungi un'azione**.
3. Nella casella di ricerca azione digitare **response** per elencare l'azione di risposta.
   
    ![Selezionare l'azione di risposta](./media/connectors-native-reqres/using-action-1.png)
4. Aggiungere i parametri richiesti per il messaggio di risposta HTTP.
   
    ![Completare l'azione di risposta](./media/connectors-native-reqres/using-action-2.png)
5. Fare clic sull'angolo in alto a sinistra della barra degli strumenti per salvare e pubblicare (attivare) l'app per la logica.

## <a name="request-trigger"></a>Trigger di richiesta
Ecco i dettagli per il trigger supportato da questo connettore. È disponibile un solo trigger di richiesta.

| Trigger | DESCRIZIONE |
| --- | --- |
| Richiesta |Si verifica quando viene ricevuta una richiesta HTTP |

## <a name="response-action"></a>Azione di risposta
Ecco i dettagli per l'azione supportata da questo connettore. Esiste una sola azione di risposta che può essere usata solo quando è accompagnata da un trigger di richiesta.

| Azione | DESCRIZIONE |
| --- | --- |
| response |Restituisce una risposta alla richiesta HTTP correlata |

### <a name="trigger-and-action-details"></a>Dettagli sui trigger e le azioni
Le tabelle seguenti descrivono i campi di input per il trigger e l'azione e i corrispondenti dettagli di output.

#### <a name="request-trigger"></a>Trigger di richiesta
Di seguito è riportato un campo di input per il trigger da una richiesta HTTP in ingresso.

| Nome visualizzato | Nome proprietà | DESCRIZIONE |
| --- | --- | --- |
| Schema JSON |schema |Lo schema JSON del corpo della richiesta HTTP |

<br>

**Dettagli dell'output**

Di seguito sono indicati i dettagli di output per la richiesta.

| Nome proprietà | Tipo di dati | DESCRIZIONE |
| --- | --- | --- |
| headers |object |Intestazioni della richiesta |
| Corpo |object |Oggetto della richiesta |

#### <a name="response-action"></a>Azione di risposta
Di seguito sono riportati i campi di input per l'azione di risposta HTTP. Un asterisco (*) indica che è un campo obbligatorio.

| Nome visualizzato | Nome proprietà | DESCRIZIONE |
| --- | --- | --- |
| Codice di stato* |statusCode |Il codice di stato HTTP |
| Headers |headers |Un oggetto JSON delle intestazioni HTTP da includere |
| Corpo |Corpo |Il corpo della risposta |

## <a name="next-steps"></a>Passaggi successivi
Provare ora a usare la piattaforma e [creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). È possibile esplorare gli altri connettori disponibili nelle app per la logica esaminando l' [elenco di API](apis-list.md).

