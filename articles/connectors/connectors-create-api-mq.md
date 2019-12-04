---
title: Connetti a IBM MQ Server
description: Inviare e recuperare messaggi con un'istanza di Azure o un server IBM MQ locale e app per la logica di Azure
services: logic-apps
ms.suite: integration
author: valrobb
ms.author: valthom
ms.reviewer: chrishou, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: ef9e91b526055ece58ce283572deb98cff951653
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789585"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Connettersi a un server IBM MQ da app per la logica di Azure

Il connettore IBM MQ Invia e recupera i messaggi archiviati in un server IBM MQ locale o in Azure. Il connettore include un client MQ Microsoft che comunica con un server IBM MQ remoto tramite una rete TCP/IP. Questo articolo fornisce una guida introduttiva per l'uso del connettore MQ. È possibile iniziare visualizzando un singolo messaggio in una coda e quindi provare altre azioni.

Il connettore IBM MQ include queste azioni, ma non fornisce trigger:

- Sfogliare un singolo messaggio senza eliminare il messaggio dal server IBM MQ
- Sfogliare un batch di messaggi senza eliminare i messaggi dal server IBM MQ
- Ricevere un singolo messaggio ed eliminare il messaggio dal server IBM MQ
- Ricevere un batch di messaggi ed eliminare i messaggi dal server IBM MQ
- Invia un singolo messaggio al server IBM MQ

## <a name="prerequisites"></a>Prerequisiti

* Se si usa un server MQ locale, [installare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md) in un server all'interno della rete. Nel server in cui è installato il gateway dati locale deve essere installato anche .NET Framework 4,6 per il funzionamento del connettore MQ. È anche necessario creare una risorsa in Azure per il gateway dati locale. Per altre informazioni, vedere [configurare la connessione al gateway dati](../logic-apps/logic-apps-gateway-connection.md).

  Tuttavia, se il server MQ è disponibile pubblicamente o disponibile in Azure, non è necessario usare il gateway dati.

* Versioni supportate ufficialmente di IBM WebSphere MQ:

  * MQ 7.5
  * MQ 8.0
  * MQ 9,0

* App per la logica in cui si vuole aggiungere l'azione MQ. Questa app per la logica deve usare lo stesso percorso della connessione gateway dati locale e deve avere già un trigger che avvia il flusso di lavoro. 

  Il connettore MQ non contiene trigger, quindi è necessario aggiungere prima un trigger all'app per la logica. Ad esempio, è possibile usare il trigger di ricorrenza. Se non si ha familiarità con le app per la logica, provare questa [Guida introduttiva per creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## <a name="browse-a-single-message"></a>Visualizzare un singolo messaggio

1. Nell'app per la logica, sotto il trigger o un'altra azione, scegliere **nuovo passaggio**. 

1. Nella casella di ricerca digitare "mq" e selezionare questa azione: **Sfoglia messaggio**

   ![Sfoglia messaggio](media/connectors-create-api-mq/Browse_message.png)

1. Se non si dispone di una connessione MQ esistente, creare la connessione:  

   1. Nell'azione selezionare **Connetti tramite gateway dati locale**.
   
   1. Immettere le proprietà per il server MQ.  

      In **Server** è possibile immettere il nome del server MQ o immettere l'indirizzo IP seguito da due punti e dal numero di porta.
    
   1. Aprire l'elenco **gateway** , che Mostra tutte le connessioni gateway configurate in precedenza. Selezionare il gateway.
    
   1. Al termine dell'operazione, scegliere **Crea**. 
   
      La connessione ha un aspetto simile a questo esempio:

      ![Proprietà connessione](media/connectors-create-api-mq/Connection_Properties.png)

1. Impostare le proprietà dell'azione:

   * **Queue**: specificare una coda diversa dalla connessione.

   * **MessageID**, **CorrelationId**, **GroupID**e altre proprietà: cercare un messaggio in base alle diverse proprietà dei messaggi mq

   * **IncludeInfo**: specificare **true** per includere informazioni aggiuntive sul messaggio nell'output. In alternativa, specificare **false** per non includere informazioni aggiuntive sul messaggio nell'output.

   * **Timeout**: immettere un valore per determinare il tempo di attesa dell'arrivo di un messaggio in una coda vuota. Se non si specifica alcun valore, viene recuperato il primo messaggio nella coda e non trascorre alcun tempo di attesa prima della visualizzazione di un messaggio.

     ![Sfoglia proprietà messaggio](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Salvare** le modifiche e quindi **eseguire** l'app per la logica.

   ![Salva ed Esegui](media/connectors-create-api-mq/Save_Run.png)

   Al termine dell'esecuzione, vengono visualizzati i passaggi dell'esecuzione ed è possibile esaminare l'output.

1. Per esaminare i dettagli di ogni passaggio, scegliere il segno di spunta verde. Per esaminare ulteriori informazioni sui dati di output, scegliere **Mostra output non elaborati**.

   ![Sfoglia output messaggio](media/connectors-create-api-mq/Browse_message_output.png)  

   Di seguito è riportato un esempio di output non elaborato:

   ![Visualizzare l'output non elaborato del messaggio](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Se si imposta **IncludeInfo** su true, viene visualizzato l'output seguente:

   ![Sfoglia informazioni di inclusione messaggio](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Visualizzare più messaggi

L'azione **Browse messages** (Visualizza messaggi) include un'opzione **BatchSize** per indicare il numero di messaggi da restituire dalla coda.  In assenza di un valore per **BatchSize**, vengono restituiti tutti i messaggi. L'output restituito è una matrice di messaggi.

1. Quando si aggiunge l'azione **Sfoglia messaggi** , per impostazione predefinita viene selezionata la prima connessione configurata in precedenza. Per creare una nuova connessione, scegliere **Cambia connessione**. In alternativa, selezionare una connessione diversa.

1. Al termine dell'esecuzione dell'app per la logica, di seguito è riportato un esempio di output dell'azione **Sfoglia messaggi** :

   ![Output dell'azione Browse messages (Visualizza messaggi)](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Ricezione messaggio singolo

L'azione **Receive message** (Ricevi messaggio) ha gli stessi input e output dell'azione **Browse message** (Visualizza messaggio). Quando si usa l'azione **Receive message** (Ricevi messaggio), il messaggio viene eliminato dalla coda.

## <a name="receive-multiple-messages"></a>Ricevere più messaggi

L'azione **Receive messages** (Ricevi messaggi) ha gli stessi input e output dell'azione **Browse messages** (Visualizza messaggi). Quando si usa l'azione **Receive messages** (Ricevi messaggi), i messaggi vengono eliminati dalla coda.

Se non sono presenti messaggi nella coda durante l'esecuzione di una ricerca o di una ricezione, il passaggio ha esito negativo con l'output seguente:  

![Errore di nessun messaggio in MQ](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Send message

Quando si aggiunge l'azione **Invia messaggi** , per impostazione predefinita viene selezionata la prima connessione configurata in precedenza. Per creare una nuova connessione, scegliere **Cambia connessione**. In alternativa, selezionare una connessione diversa.

1. Selezionare un tipo di messaggio valido: **datagramma**, **risposta**o **richiesta**  

   ![Invia le prop del messaggio](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Al termine dell'esecuzione dell'app per la logica, di seguito è riportato un esempio di output dell'azione **Invia messaggio** :

   ![Output dell'azione Send message (Invia messaggio)](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su azioni e limiti, descritti dalla descrizione del connettore OpenAPI (in precedenza spavalderia), vedere la [pagina di riferimento](/connectors/mq/)del connettore.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
