---
title: Connettersi al server IBM MQ - App per la logica di Azure
description: Inviare e recuperare i messaggi con un server IBM MQ Azure o in locale e Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: a2894799946d069916b27a4f5bcc7bd3244705b2
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273132"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Connettersi a un server IBM MQ da App per la logica di Azure

Il connettore IBM MQ Invia e recupera i messaggi archiviati in un server IBM MQ in locale o in Azure. Il connettore include un client MQ Microsoft che comunica con un server IBM MQ remoto tramite una rete TCP/IP. Questo articolo fornisce una Guida di avvio per usare il connettore MQ. È possibile iniziare selezionando un singolo messaggio in una coda e quindi provare a eseguire altre azioni.

Il connettore IBM MQ include queste azioni, ma non fornisce alcun trigger:

- Visualizzare un singolo messaggio senza eliminare il messaggio dal server IBM MQ
- Sfogliare un batch di messaggi senza eliminarli dal server IBM MQ
- Un singolo messaggio di ricezione ed eliminare il messaggio dal server IBM MQ
- Ricevere un batch di messaggi ed eliminare i messaggi dal server IBM MQ
- Inviare un singolo messaggio al server IBM MQ

## <a name="prerequisites"></a>Prerequisiti

* Se si usa un server MQ locale, [installare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md) in un server all'interno della rete. Il server in cui è installato il gateway dati locale deve disporre anche .NET Framework 4.6 installato per il connettore MQ a funzionare. È anche necessario creare una risorsa in Azure per il gateway dati locale. Per altre informazioni, vedere [configurare la connessione al gateway dati](../logic-apps/logic-apps-gateway-connection.md).

  Tuttavia, se il server MQ è disponibile pubblicamente o all'interno di Azure, non devi usare il gateway dati.

* Versioni supportate ufficialmente di IBM WebSphere MQ:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* L'app per la logica in cui si desidera aggiungere l'azione MQ. Questa app per la logica deve usare la stessa posizione come la connessione al gateway dati locale e deve avere già un trigger che avvia il flusso di lavoro. 

  Il connettore MQ non include trigger, pertanto è necessario aggiungere un trigger per app per la logica prima di tutto. Ad esempio, è possibile utilizzare il trigger di ricorrenza. Se si ha familiarità con App per la logica, provare [Guida introduttiva per creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Visualizzare un singolo messaggio

1. Nell'app per la logica, sotto il trigger o un'altra azione, scegliere **nuovo passaggio**. 

1. Nella casella di ricerca, digitare "mq" e selezionare l'azione: **Browse message** (Visualizza messaggio)

   ![Sfoglia messaggio](media/connectors-create-api-mq/Browse_message.png)

1. Se non si dispone di una connessione MQ esistente, creare la connessione:  

   1. Nell'azione, selezionare **Connetti tramite gateway dati locale**.
   
   1. Immettere le proprietà per il server MQ.  

      In **Server** è possibile immettere il nome del server MQ o immettere l'indirizzo IP seguito da due punti e dal numero di porta.
    
   1. Aprire il **gateway** elenco, che mostra uno configurata in precedenza le connessioni del gateway. Selezionare il gateway.
    
   1. Al termine dell'operazione, scegliere **Crea**. 
   
      La connessione è simile a questo esempio:

      ![Proprietà connessione](media/connectors-create-api-mq/Connection_Properties.png)

1. Impostare le proprietà dell'azione:

   * **coda**: Specificare una coda diversa dalla connessione.

   * **MessageId**, **CorrelationId**, **GroupId**e altre proprietà: Visualizzare un messaggio in base alle proprietà dei messaggi MQ diversi

   * **IncludeInfo**: Specificare **True** per includere informazioni aggiuntive sul messaggio nell'output. In alternativa, specificare **False** per non includere informazioni aggiuntive sul messaggio nell'output.

   * **Timeout**: Immettere un valore per determinare quanto tempo aspettare per un messaggio in arrivo in una coda vuota. Se non si specifica alcun valore, viene recuperato il primo messaggio nella coda e non trascorre alcun tempo di attesa prima della visualizzazione di un messaggio.

     ![Individuare le proprietà del messaggio](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Salvare** le modifiche e quindi **eseguire** app per la logica.

   ![Salva ed Esegui](media/connectors-create-api-mq/Save_Run.png)

   Al termine dell'esecuzione, vengono illustrati i passaggi dell'esecuzione, ed è possibile esaminare l'output.

1. Per esaminare i dettagli per ogni passaggio, scegliere il segno di spunta verde. Per esaminare altre informazioni sui dati di output, scegli **Mostra output non elaborati**.

   ![Visualizzare l'output di messaggi](media/connectors-create-api-mq/Browse_message_output.png)  

   Ecco un esempio di output non elaborati:

   ![Visualizzare l'output non elaborato del messaggio](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Se si imposta **IncludeInfo** su true, di seguito viene visualizzato l'output:

   ![Messaggio Browse includono informazioni](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Visualizzare più messaggi

L'azione **Browse messages** (Visualizza messaggi) include un'opzione **BatchSize** per indicare il numero di messaggi da restituire dalla coda.  In assenza di un valore per **BatchSize**, vengono restituiti tutti i messaggi. L'output restituito è una matrice di messaggi.

1. Quando si aggiunge il **visualizzare messaggi** azione, in precedenza la prima connessione configurata è selezionata per impostazione predefinita. Per creare una nuova connessione, scegliere **Cambia connessione**. In alternativa, selezionare una connessione diversa.

1. Al termine dell'esecuzione di app per la logica, ecco un esempio di output dal **visualizzare messaggi** azione:

   ![Output dell'azione Browse messages (Visualizza messaggi)](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Singolo messaggio di ricezione

L'azione **Receive message** (Ricevi messaggio) ha gli stessi input e output dell'azione **Browse message** (Visualizza messaggio). Quando si usa l'azione **Receive message** (Ricevi messaggio), il messaggio viene eliminato dalla coda.

## <a name="receive-multiple-messages"></a>Ricevere più messaggi

L'azione **Receive messages** (Ricevi messaggi) ha gli stessi input e output dell'azione **Browse messages** (Visualizza messaggi). Quando si usa l'azione **Receive messages** (Ricevi messaggi), i messaggi vengono eliminati dalla coda.

Se non sono presenti messaggi nella coda quando si esegue una ricerca o un'operazione di ricezione, il passaggio ha esito negativo con il seguente output:  

![Errore di nessun messaggio in MQ](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Send message

Quando si aggiunge il **inviare messaggi** azione, in precedenza la prima connessione configurata è selezionata per impostazione predefinita. Per creare una nuova connessione, scegliere **Cambia connessione**. In alternativa, selezionare una connessione diversa.

1. Selezionare un tipo di messaggio valido: **Datagramma**, **Reply**, o **richiesta**  

   ![Invia messaggio Props](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Al termine dell'esecuzione dell'app per la logica, ecco un esempio di output dal **Invia messaggio** azione:

   ![Output dell'azione Send message (Invia messaggio)](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche dettagliate sulle azioni e limiti, che sono descritte da OpenAPI del connettore (in precedenza Swagger) descrizione, esaminare il connettore [pagina di riferimento](/connectors/mq/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
