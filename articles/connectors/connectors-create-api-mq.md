---
title: Connetti a IBM MQ Server
description: Inviare e recuperare messaggi con un'istanza di Azure o un server IBM MQ locale e app per la logica di Azure
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 03/31/2020
tags: connectors
ms.openlocfilehash: 737c5b90b216156ca08346f4a64fd0b421ad6c19
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410149"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Connettersi a un server IBM MQ da app per la logica di Azure

Il connettore IBM MQ Invia e recupera i messaggi archiviati in un server IBM MQ locale o in Azure. Il connettore include un client MQ Microsoft che comunica con un server IBM MQ remoto tramite una rete TCP/IP. Questo articolo fornisce una guida introduttiva per l'uso del connettore MQ. È possibile iniziare visualizzando un singolo messaggio in una coda e quindi provare altre azioni.

Il connettore IBM MQ include queste azioni, ma non fornisce trigger:

- Sfogliare un singolo messaggio senza eliminare il messaggio dal server IBM MQ.
- Sfogliare un batch di messaggi senza eliminare i messaggi dal server IBM MQ.
- Ricevere un singolo messaggio ed eliminare il messaggio dal server IBM MQ.
- Ricevere un batch di messaggi ed eliminare i messaggi dal server IBM MQ.
- Inviare un singolo messaggio al server IBM MQ.

Di seguito sono riportate le versioni IBM WebSphere MQ supportate ufficialmente:

  * MQ 7.5
  * MQ 8.0
  * MQ 9,0

## <a name="prerequisites"></a>Prerequisiti

* Se si usa un server MQ locale, [installare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md) in un server all'interno della rete. Nel server in cui è installato il gateway dati locale deve essere installato anche .NET Framework 4,6 per il funzionamento del connettore MQ.

  Al termine dell'installazione del gateway, è necessario creare anche una risorsa in Azure per il gateway dati locale. Per altre informazioni, vedere [configurare la connessione al gateway dati](../logic-apps/logic-apps-gateway-connection.md).

  Se il server MQ è disponibile pubblicamente o disponibile in Azure, non è necessario usare il gateway dati.

* App per la logica in cui si vuole aggiungere l'azione MQ. Questa app per la logica deve usare lo stesso percorso della connessione gateway dati locale e deve avere già un trigger che avvia il flusso di lavoro.

  Il connettore MQ non contiene trigger, quindi è necessario aggiungere prima un trigger all'app per la logica. Ad esempio, è possibile usare il trigger di ricorrenza. Se non si ha familiarità con le app per la logica, provare questa [Guida introduttiva per creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Crea connessione MQ

Se non si dispone già di una connessione MQ quando si aggiunge un'azione MQ, viene chiesto di creare la connessione, ad esempio:

![Fornire le informazioni di connessione](media/connectors-create-api-mq/connection-properties.png)

1. Se ci si connette a un server MQ locale, selezionare **Connetti tramite gateway dati locale**.

1. Fornire le informazioni di connessione per il server MQ.

   * In **Server** è possibile immettere il nome del server MQ o immettere l'indirizzo IP seguito da due punti e dal numero di porta.

   * Per usare Secure Sockets Layer (SSL), selezionare **Abilita SSL?**.

     Il connettore MQ supporta attualmente solo l'autenticazione server e non l'autenticazione client. Per ulteriori informazioni, vedere [problemi di connessione e di autenticazione](#connection-problems).

1. Nella sezione **gateway** seguire questa procedura:

   1. Dall'elenco **sottoscrizione** selezionare la sottoscrizione di Azure associata alla risorsa del gateway di Azure.

   1. Dall'elenco **gateway di connessione** selezionare la risorsa del gateway di Azure che si vuole usare.

1. Al termine, selezionare **Crea**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problemi di connessione e autenticazione

Quando l'app per la logica tenta la connessione al server MQ locale, è possibile che venga ricevuto questo errore:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Se si usa il connettore MQ direttamente in Azure, il server MQ deve usare un certificato emesso da un' [autorità di certificazione](https://www.ssl.com/faqs/what-is-a-certificate-authority/)attendibile.

* Se si usa il gateway dati locale, provare a usare un certificato emesso da un' [autorità di certificazione](https://www.ssl.com/faqs/what-is-a-certificate-authority/) attendibile, quando possibile. Tuttavia, se questa opzione non è possibile, è possibile usare un certificato autofirmato che non viene emesso da un' [autorità di certificazione](https://www.ssl.com/faqs/what-is-a-certificate-authority/) attendibile ed è considerato meno sicuro.

  Per installare il certificato autofirmato del server, è possibile utilizzare lo strumento **Gestione certificati Windows** (certmgr. msc). Per questo scenario, nel computer locale in cui è in esecuzione il servizio gateway dati locale, è necessario installare il certificato nell'archivio certificati del **computer locale** a livello di **autorità di certificazione radice attendibili** .

  1. Nel computer in cui è in esecuzione il servizio gateway dati locale, aprire il menu Start, trovare e selezionare **Gestisci certificati utente**.

  1. Dopo aver aperto lo strumento Gestione certificati Windows, passare alla cartella **certificati-computer** >  locale**attendibile autorità di certificazione radice** e installare il certificato.

     > [!IMPORTANT]
     > Assicurarsi di installare il certificato nell'archivio **certificati-** > **autorità di certificazione radice attendibili** del computer locale.

* Il server MQ richiede la definizione della specifica di crittografia che si desidera utilizzare per le connessioni SSL. Tuttavia, SsLStream in .NET non consente di specificare l'ordine per le specifiche di crittografia. Per ovviare a questa limitazione, è possibile modificare la configurazione del server MQ in modo che corrisponda alla prima specifica di crittografia nel gruppo inviato dal connettore nella negoziazione SSL.

  Quando si tenta di eseguire la connessione, il server MQ registra un messaggio di evento che indica che la connessione non è riuscita perché l'altra estremità usava la specifica di crittografia non corretta. Il messaggio di evento contiene la specifica di crittografia visualizzata per prima nell'elenco. Aggiornare la specifica di crittografia nella configurazione del canale in modo che corrisponda alla specifica di crittografia nel messaggio dell'evento.

## <a name="browse-single-message"></a>Sfoglia singolo messaggio

1. Nell'app per la logica, sotto il trigger o un'altra azione, selezionare **nuovo passaggio**.

1. Nella casella di ricerca immettere `mq`e selezionare l'azione **Sfoglia messaggio** .

   ![Selezionare l'azione "Sfoglia messaggio"](media/connectors-create-api-mq/browse-message.png)

1. Se non è già stata creata una connessione MQ, viene richiesto di [creare tale connessione](#create-connection).

1. Dopo aver creato la connessione, impostare le proprietà dell'azione **Sfoglia messaggio** :

   | Proprietà | Descrizione |
   |----------|-------------|
   | **Coda** | Se diverso dalla coda specificata nella connessione, specificare la coda. |
   | **MessageID**, **CorrelationId**, **GroupID**e altre proprietà | Cerca un messaggio basato sulle diverse proprietà dei messaggi MQ |
   | **IncludeInfo** | Per includere informazioni aggiuntive sul messaggio nell'output, selezionare **true**. Per omettere ulteriori informazioni sui messaggi nell'output, selezionare **false**. |
   | **Timeout** | Immettere un valore per determinare il tempo di attesa dell'arrivo di un messaggio in una coda vuota. Se non si specifica alcun valore, viene recuperato il primo messaggio nella coda e non trascorre alcun tempo di attesa prima della visualizzazione di un messaggio. |
   |||

   Ad esempio:

   ![Proprietà per l'azione "Sfoglia messaggio"](media/connectors-create-api-mq/browse-message-properties.png)

1. Al termine, fare clic su **Salva**nella barra degli strumenti della finestra di progettazione. Per testare l'app, selezionare **Esegui**.

   Al termine dell'esecuzione, nella finestra di progettazione vengono visualizzati i passaggi del flusso di lavoro e il relativo stato, in modo da poter esaminare l'output.

1. Per visualizzare i dettagli di ogni passaggio, fare clic sulla barra del titolo del passaggio. Per esaminare altre informazioni sull'output di un passaggio, selezionare **Mostra output non elaborati**.

   ![Visualizzare l'output del messaggio](media/connectors-create-api-mq/browse-message-output.png)

   Di seguito è riportato un esempio di output non elaborato:

   ![Visualizzare l'output non elaborato del messaggio](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Se si imposta **IncludeInfo** su **true**, viene visualizzato un output aggiuntivo:

   ![Visualizzare le informazioni incluse del messaggio](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Visualizzare più messaggi

L'azione **Sfoglia messaggi** include un'opzione **BatchSize** per indicare il numero di messaggi da restituire dalla coda. Se **BatchSize** non ha alcun valore, vengono restituiti tutti i messaggi. L'output restituito è una matrice di messaggi.

1. Seguire i passaggi precedenti, ma aggiungere invece l'azione **Sfoglia messaggi** .

1. Se non è già stata creata una connessione MQ, viene richiesto di [creare tale connessione](#create-connection). In caso contrario, per impostazione predefinita viene utilizzata la prima connessione configurata in precedenza. Per creare una nuova connessione, selezionare **Cambia connessione**. In alternativa, selezionare una connessione diversa.

1. Fornire le informazioni per l'azione.

1. Salvare ed eseguire l'app per la logica.

   Al termine dell'esecuzione dell'app per la logica, di seguito è riportato un esempio di output dell'azione **Sfoglia messaggi** :

   ![Esempio di output "Sfoglia messaggi"](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Ricezione messaggio singolo

L'azione **Receive message** (Ricevi messaggio) ha gli stessi input e output dell'azione **Browse message** (Visualizza messaggio). Quando si utilizza **Receive Message**, il messaggio viene eliminato dalla coda.

## <a name="receive-multiple-messages"></a>Ricevere più messaggi

L'azione **Receive messages** (Ricevi messaggi) ha gli stessi input e output dell'azione **Browse messages** (Visualizza messaggi). Quando si utilizza **receive messages**, i messaggi vengono eliminati dalla coda.

> [!NOTE]
> Quando si esegue un'azione browse o Receive in una coda che non contiene messaggi, l'azione ha esito negativo con l'output seguente:
>
> ![MQ "nessun messaggio" errore](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Send message

1. Attenersi ai passaggi precedenti, ma aggiungere invece l'azione **Invia messaggio** .

1. Se non è già stata creata una connessione MQ, viene richiesto di [creare tale connessione](#create-connection). In caso contrario, per impostazione predefinita viene utilizzata la prima connessione configurata in precedenza. Per creare una nuova connessione, selezionare **Cambia connessione**. In alternativa, selezionare una connessione diversa.

1. Fornire le informazioni per l'azione. Per **MessageType**selezionare un tipo di messaggio valido, ovvero **datagramma**, **risposta**o **richiesta** .

   ![Proprietà per "Invia azione messaggio"](media/connectors-create-api-mq/send-message-properties.png)

1. Salvare ed eseguire l'app per la logica.

   Al termine dell'esecuzione dell'app per la logica, di seguito è riportato un esempio di output dell'azione **Invia messaggio** :

   ![Esempio di output di "Invia messaggio"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche su azioni e limiti, descritti dalla descrizione di spavalderia del connettore, vedere la [pagina di riferimento](/connectors/mq/)del connettore.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
