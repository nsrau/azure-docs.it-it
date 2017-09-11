---
title: Informazioni su come usare il connettore MQ nelle app per la logica di Azure | Microsoft Docs
description: Connettersi a un server MQ locale o in Azure dal flusso di lavoro delle app per la logica per visualizzare, ricevere e inviare messaggi a WebSphere MQ
services: logic-apps
author: valthom
manager: anneta
documentationcenter: 
editor: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 06/01/2017
ms.author: valthom; ladocs
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 9fb0258df3f45d121cda49d3b5c274bd9548c832
ms.contentlocale: it-it
ms.lasthandoff: 09/08/2017

---

# <a name="connect-to-an-ibm-mq-server-from-logic-apps-using-the-mq-connector"></a>Connettersi a un server IBM MQ da app per la logica usando il connettore MQ 

Microsoft Connector per MQ consente di inviare e recuperare i messaggi archiviati in un server MQ locale o in Azure. Il connettore include un client MQ Microsoft che comunica con un server IBM MQ remoto tramite una rete TCP/IP. Questo documento è una guida introduttiva all'uso del connettore MQ. Si consiglia di iniziare con la visualizzazione di un singolo messaggio in una coda, per poi provare altre azioni.    

Il connettore MQ include le azioni seguenti. Non sono disponibili trigger.

-   Visualizzare un singolo messaggio senza eliminare il messaggio dal server IBM MQ
-   Visualizzare un gruppo di messaggi senza eliminarli dal server IBM MQ
-   Ricevere un singolo messaggio ed eliminarlo dal server IBM MQ
-   Ricevere un gruppo di messaggi ed eliminarli dal server IBM MQ
-   Inviare un singolo messaggio al server IBM MQ 

## <a name="prerequisites"></a>Prerequisiti

* Se si usa un server MQ locale, [installare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md) in un server all'interno della rete. Se il server MQ è disponibile pubblicamente o all'interno di Azure, il gateway dati non viene usato o non è necessario.

    > [!NOTE]
    > Per il corretto funzionamento del connettore MQ, nel server in cui è installato il gateway dati locale deve essere installato anche .NET Framework 4.6.

* Creare una risorsa di Azure per il gateway dati locale - [Configurare la connessione al gateway dati](../logic-apps/logic-apps-gateway-connection.md).

* Versioni supportate ufficialmente di IBM WebSphere MQ:
   * MQ 7.5
   * MQ 8.0

## <a name="create-a-logic-app"></a>Creare un'app per la logica

1. Nella **schermata iniziale di Azure** selezionare **+** (segno più), **Web e dispositivi mobili** e quindi **App per la logica**. 
2. Immettere **Nome**, ad esempio, MQTestApp **Sottoscrizione**, **Gruppo di risorse** e **Percorso** (usare il percorso in cui è configurata la connessione al gateway dati locale). Selezionare **Aggiungi al dashboard** e selezionare **Crea**.  
![Creare un'app per la logica](media/connectors-create-api-mq/Create_Logic_App.png)

## <a name="add-a-trigger"></a>Aggiungere un trigger

> [!NOTE]
> Il connettore MQ non include trigger. Usare quindi un altro trigger per avviare l'app per la logica, ad esempio il trigger **Ricorrenza**. 

1. Verrà aperta **Progettazione app per la logica**. Selezionare **Ricorrenza** nell'elenco dei trigger comuni.
2. Selezionare **Modifica** all'interno del trigger Ricorrenza. 
3. Impostare **Frequenza** su **Giorno** e **Intervallo** su **7**. 

## <a name="browse-a-single-message"></a>Visualizzare un singolo messaggio
1. Selezionare **+ Nuovo passaggio** e quindi **Aggiungi un'azione**.
2. Nella casella di ricerca digitare `mq` e quindi selezionare **MQ - Browse message** (MQ - Visualizza messaggio).  
![Browse message](media/connectors-create-api-mq/Browse_message.png) (Visualizza messaggio)

3. Se non è disponibile una connessione MQ esistente, creare la connessione:  

    1. Selezionare **Connect via on-premises data gateway** (Connetti tramite gateway dati locale) e immettere le proprietà del server MQ.  
    In **Server** è possibile immettere il nome del server MQ o immettere l'indirizzo IP seguito da due punti e dal numero di porta. 
    2. Nell'elenco a discesa **Gateway** sono elencate le eventuali connessioni gateway esistenti configurate. Selezionare il gateway.
    3. Selezionare **Create** (Crea) al termine. La connessione sarà simile alla seguente:   
    ![Proprietà connessione](media/connectors-create-api-mq/Connection_Properties.png)

4. Nelle proprietà dell'azione è possibile:  

    * Usare la proprietà **Queue** per accedere a un nome diverso rispetto a quello definito nella connessione.
    * Usare le proprietà **MessageId**, **CorrelationId**, **GroupId** e altre proprietà per visualizzare un messaggio in base alle diverse proprietà dei messaggi MQ.
    * Impostare **IncludeInfo** su **True** per includere informazioni aggiuntive sul messaggio nell'output. Oppure impostarla su **False** per non includere informazioni aggiuntive sul messaggio nell'output.
    * Immettere un valore **Timeout** per impostare l'attesa per l'arrivo di un messaggio in una coda vuota. Se non si specifica alcun valore, viene recuperato il primo messaggio nella coda e non trascorre alcun tempo di attesa prima della visualizzazione di un messaggio.  
    ![Visualizzare le proprietà del messaggio](media/connectors-create-api-mq/Browse_message_Props.png)

5. **Salvare** le modifiche e quindi **eseguire** l'app per la logica:  
![Salvare ed eseguire](media/connectors-create-api-mq/Save_Run.png)

6. Dopo alcuni secondi vengono visualizzati i passaggi dell'esecuzione ed è possibile esaminare l'output. Selezionare il segno di spunta verde per visualizzare i dettagli di ogni passaggio. Selezionare **Mostra output non elaborati** per visualizzare dettagli aggiuntivi sui dati di output.  
![Visualizzare l'output del messaggio](media/connectors-create-api-mq/Browse_message_output.png)  

    Output non elaborato:  
    ![Visualizzare l'output non elaborato del messaggio](media/connectors-create-api-mq/Browse_message_raw_output.png)

7. Quando l'opzione **IncludeInfo** è impostata su true, viene visualizzato l'output seguente:  
![Visualizzare le informazioni incluse del messaggio](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Visualizzare più messaggi
L'azione **Browse messages** (Visualizza messaggi) include un'opzione **BatchSize** per indicare il numero di messaggi da restituire dalla coda.  In assenza di un valore per **BatchSize**, vengono restituiti tutti i messaggi. L'output restituito è una matrice di messaggi.

1. Quando si aggiunge l'azione **Browse messages** (Visualizza messaggi), viene selezionata per impostazione predefinita la prima connessione configurata. Selezionare **Cambia connessione** per creare una nuova connessione oppure selezionare una connessione diversa.

2. L'output dell'azione Browse messages (Visualizza messaggi) è il seguente:  
![Output dell'azione Browse messages (Visualizza messaggi)](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-a-single-message"></a>Ricevere un singolo messaggio
L'azione **Receive message** (Ricevi messaggio) ha gli stessi input e output dell'azione **Browse message** (Visualizza messaggio). Quando si usa l'azione **Receive message** (Ricevi messaggio), il messaggio viene eliminato dalla coda.

## <a name="receive-multiple-messages"></a>Ricevere più messaggi
L'azione **Receive messages** (Ricevi messaggi) ha gli stessi input e output dell'azione **Browse messages** (Visualizza messaggi). Quando si usa l'azione **Receive messages** (Ricevi messaggi), i messaggi vengono eliminati dalla coda.

Se non sono presenti messaggi nella coda quando si esegue un'azione di visualizzazione o ricezione, il passaggio ha esito negativo con l'output seguente:  
![Errore di nessun messaggio in MQ](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-a-message"></a>Inviare un messaggio
1. Quando si aggiunge l'azione **Send message** (Invia messaggio), viene selezionata per impostazione predefinita la prima connessione configurata. Selezionare **Cambia connessione** per creare una nuova connessione oppure selezionare una connessione diversa. I valori validi per **MessageType** sono **Datagram**, **Reply** o **Request**.  
![Proprietà dell'azione Send message (Invia messaggio)](media/connectors-create-api-mq/Send_Msg_Props.png)

2. L'output dell'azione Send message (Invia messaggio) è simile al seguente:  
![Output dell'azione Send message (Invia messaggio)](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per visualizzare eventuali azioni e trigger definiti in Swagger ed eventuali limiti, vedere i [dettagli del connettore](/connectors/mq/).

## <a name="next-steps"></a>Passaggi successivi
[Creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle app per la logica nell' [elenco delle API](apis-list.md).

