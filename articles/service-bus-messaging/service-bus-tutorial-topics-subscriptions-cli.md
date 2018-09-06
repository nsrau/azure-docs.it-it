---
title: Esercitazione - Aggiornare l'assortimento del magazzino al dettaglio usando i canali di pubblicazione/sottoscrizione e i filtri di argomento con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: In questa esercitazione viene illustrato come inviare e ricevere messaggi da un argomento e una sottoscrizione e come aggiungere e usare le regole di filtro con l'interfaccia della riga di comando di Azure
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: b68b3f311312a952ab55c15083ff5ed562443be2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702004"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Esercitazione: Aggiornare l'inventario usando l'interfaccia della riga di comando e argomenti/sottoscrizioni

Il bus di servizio di Microsoft Azure è un servizio di messaggistica cloud multi-tenant che invia informazioni tra applicazioni e servizi. Le operazioni asincrone offrono messaggistica negoziata flessibile, insieme a funzionalità di messaggistica e pubblicazione/sottoscrizione FIFO (First-In-First-Out) strutturate. Questa esercitazione illustra come usare gli argomenti del bus di servizio e le sottoscrizioni in uno scenario di magazzino al dettaglio, con i canali di pubblicazione/sottoscrizione tramite l'interfaccia della riga di comando di Azure e Java.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un argomento del bus di servizio e una o più sottoscrizioni a tale argomento usando l'interfaccia della riga di comando di Azure
> * Aggiungere filtri di argomento usando l'interfaccia della riga di comando di Azure
> * Creare due messaggi con contenuto differente
> * Inviare i messaggi e verificare che siano arrivati nelle sottoscrizioni previste
> * Ricevere messaggi dalle sottoscrizioni

Un esempio di questo scenario è un aggiornamento dell'assortimento di magazzino per più punti vendita al dettaglio. In questo scenario ogni negozio o set di negozi ottiene i messaggi a essi destinati per aggiornare gli assortimenti. Questa esercitazione illustra come implementare lo scenario usando filtri e sottoscrizioni. Si crea prima di tutto un argomento con 3 sottoscrizioni, si aggiungono alcune regole e filtri e quindi si inviano e ricevono messaggi dall'argomento e dalle sottoscrizioni.

![argomento](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito][] prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per sviluppare un'app per il bus di servizio con Java, è necessario che siano installati gli elementi seguenti:

- [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), versione più recente.
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, in questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Argomenti e sottoscrizioni del bus di servizio

Ogni [sottoscrizione a un argomento](service-bus-messaging-overview.md#topics) può ricevere una copia di ogni messaggio. Gli argomenti sono completamente compatibili a livello di protocollo e di semantica con le code del bus di servizio. Gli argomenti del bus di servizio supportano un'ampia gamma di regole di selezione con condizioni di filtro, con azioni facoltative per impostare o modificare le proprietà dei messaggi. Ogni volta che una regola corrisponde, genera un messaggio. Per altre informazioni su regole, filtri e azioni, seguire questo [collegamento](topic-filters.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Dopo che l'interfaccia della riga di comando è stata installata, aprire un prompt dei comandi ed eseguire i comandi seguenti per accedere ad Azure. Questi passaggi non sono necessari se si usa Cloud Shell:

1. Se si usa l'interfaccia della riga di comando di Azure in locale, eseguire il comando seguente per accedere ad Azure. Questo passaggio di accesso non è necessario se si eseguono questi comandi in Cloud Shell:

   ```azurecli-interactive
   az login
   ```

2. Impostare il contesto di sottoscrizione corrente sulla sottoscrizione di Azure da usare:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>Usare l'interfaccia della riga di comando per il provisioning delle risorse

Eseguire i comandi seguenti per il provisioning delle risorse del bus di servizio. Assicurarsi di sostituire tutti i segnaposto con i valori appropriati:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Dopo l'esecuzione dell'ultimo comando, copiare e incollare la stringa di connessione e il nome della coda selezionato in una posizione temporanea, ad esempio il Blocco note. Saranno necessari nel passaggio successivo.

## <a name="create-filter-rules-on-subscriptions"></a>Creare regole di filtro nelle sottoscrizioni

Dopo che è stato effettuato il provisioning dello spazio dei nomi e di argomento/sottoscrizioni e sono disponibili le credenziali necessarie, è possibile creare regole di filtro nelle sottoscrizioni, quindi inviare e ricevere messaggi. È possibile esaminare il codice in [questa cartella dell'esempio di GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/quickstarts-and-tutorials/tutorial-topics-subscriptions-filters-java/src/main/java/com/microsoft/azure/).

## <a name="send-and-receive-messages"></a>Inviare e ricevere messaggi

1. Assicurarsi che Cloud Shell sia aperto e visualizzi il prompt di Bash.

2. Clonare il [repository GitHub del bus di servizio](https://github.com/Azure/azure-service-bus/) eseguendo il comando seguente:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Passare alla cartella dell'esempio `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java`. Si noti che nella shell Bash i comandi rispettano la distinzione tra maiuscole e minuscole e i separatori di percorso devono essere barre.

3. Eseguire il comando seguente per compilare l'applicazione:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Per eseguire il programma, eseguire il comando seguente. Assicurarsi di sostituire i segnaposto con la stringa di connessione e il nome dell'argomento ottenuti nel passaggio precedente:

   ```shell
  java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   10 messaggi vengono inviati all'argomento e successivamente vengono ricevuti dalle singole sottoscrizioni:

   ![output programma](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire questo comando per rimuovere il gruppo di risorse, lo spazio dei nomi e tutte le risorse correlate:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Questa sezione contiene altri dettagli sulle operazioni eseguite dal codice di esempio.

### <a name="get-connection-string-and-queue"></a>Ottenere la stringa di connessione e la coda

Il codice dichiara prima di tutto un set di variabili, che determinano l'esecuzione rimanente del programma:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";    
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

La stringa di connessione e il nome dell'argomento sono gli unici valori aggiunti tramite i parametri della riga di comando e passati a `main()`. L'esecuzione effettiva del codice viene attivata nel metodo `run()`, invia i messaggi, quindi li riceve dall'argomento:

```java
public static void main(String[] args) {
        TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
        // Send sample messages.
        this.sendMessagesToTopic();

        // Receive messages from subscriptions.
        this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Creare il client dell'argomento per inviare i messaggi

Per inviare e ricevere i messaggi, il metodo `sendMessagesToTopic()` crea un'istanza del client dell'argomento, che usa la stringa di connessione e il nome dell'argomento, quindi chiama un altro metodo che invia i messaggi:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
         // Create client for the topic.
        TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

        // Create a message sender from the topic client.

        System.out.printf("\nSending orders to topic.\n");

        // Now we can start sending orders.
        CompletableFuture.allOf(
                SendOrders(topicClient,Store[0]),
                SendOrders(topicClient,Store[1]),
                SendOrders(topicClient,Store[2]),
                SendOrders(topicClient,Store[3]),
                SendOrders(topicClient,Store[4]),
                SendOrders(topicClient,Store[5]),
                SendOrders(topicClient,Store[6]),
                SendOrders(topicClient,Store[7]),
                SendOrders(topicClient,Store[8]),
                SendOrders(topicClient,Store[9])                
        ).join();

        System.out.printf("\nAll messages sent.\n");
    }

     public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));         
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8)); 
            // We always set the Sent to field
            message.setTo(store);    
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both. 
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});
                        
            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());            
            topicClient.sendAsync(message);
        }
               
        return new CompletableFuture().completedFuture(null);         
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Ricevere messaggi dalle singole sottoscrizioni

Il metodo `receiveAllMessages()` chiama il metodo `receiveAllMessageFromSubscription()`, che crea quindi un client di sottoscrizione per ogni chiamata e riceve i messaggi dalle singole sottoscrizioni:

```java
public void receiveAllMessages() throws Exception {     
    System.out.printf("\nStart Receiving Messages.\n");
    
    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2]) 
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {
        
        int receivedMessages = 0;

        // Create subscription client.
        IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

        // Create a receiver from the subscription client and receive all messages.
        System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

        while (true)
        {
            // This will make the connection wait for N seconds if new messages are available. 
            // If no additional messages come we close the connection. This can also be used to realize long polling.
            // In case of long polling you would obviously set it more to e.g. 60 seconds.
            IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
            if (receivedMessage != null)
            {
                if ( receivedMessage.getProperties() != null ) {                                                                                
                    System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));                                                                                          
                    
                    // Show the label modified by the rule action
                    if(receivedMessage.getLabel() != null)
                        System.out.printf("Label=%s\n", receivedMessage.getLabel());   
                }
                
                byte[] body = receivedMessage.getBody();
                Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
                System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());                          
                
                subscriptionClient.complete(receivedMessage.getLockToken());
                receivedMessages++;
            }
            else
            {
                // No more messages to receive.
                subscriptionClient.close();
                break;
            }
        }
        System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
        
        return new CompletableFuture().completedFuture(null);
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato effettuato il provisioning delle risorse usando l'interfaccia della riga di comando di Azure, quindi sono stati inviati e ricevuti messaggi da un argomento del bus di servizio e dalle relative sottoscrizioni. Si è appreso come:

> [!div class="checklist"]
> * Creare un argomento del bus di servizio e una o più sottoscrizioni a tale argomento usando il portale di Azure
> * Aggiungere filtri di argomento usando il codice .NET
> * Creare due messaggi con contenuto differente
> * Inviare i messaggi e verificare che siano arrivati nelle sottoscrizioni previste
> * Ricevere messaggi dalle sottoscrizioni

Per altri esempi di invio e ricezione dei messaggi, vedere gli [esempi del bus di servizio su GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Passare alla prossima esercitazione per altre informazioni su come usare le funzionalità di pubblicazione/sottoscrizione del bus di servizio.

> [!div class="nextstepaction"]
> [Aggiornare l'inventario usando PowerShell e argomenti/sottoscrizioni](service-bus-tutorial-topics-subscriptions-portal.md)

[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install Azure CLI 2.0]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create