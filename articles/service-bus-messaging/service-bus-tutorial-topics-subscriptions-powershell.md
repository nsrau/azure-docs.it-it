---
title: "Esercitazione: Aggiornare l'assortimento del magazzino al dettaglio usando i canali di pubblicazione/sottoscrizione e i filtri di argomento con Azure PowerShell | Microsoft Docs"
description: In questa esercitazione viene illustrato come inviare e ricevere messaggi da un argomento e una sottoscrizione e come aggiungere e usare le regole di filtro con Azure PowerShell
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.author: sethm
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 05c30504eb9b4440694f78ee979d4b25f30f65dc
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237965"
---
# <a name="tutorial-update-inventory-using-powershell-and-topicssubscriptions"></a>Esercitazione: Aggiornare l'inventario usando PowerShell e argomenti/sottoscrizioni

Il bus di servizio di Microsoft Azure è un servizio di messaggistica cloud multi-tenant che invia informazioni tra applicazioni e servizi. Le operazioni asincrone offrono messaggistica negoziata flessibile, insieme a funzionalità di messaggistica e pubblicazione/sottoscrizione FIFO (First-In-First-Out) strutturate. 

Questa esercitazione illustra come inviare e ricevere messaggi verso e da una coda del bus di servizio, usando PowerShell per creare uno spazio dei nomi di messaggistica e una coda in tale spazio dei nomi e come ottenere le credenziali di autorizzazione per tale spazio dei nomi. La procedura illustra quindi come inviare e ricevere messaggi da questa coda usando la [libreria .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un argomento del bus di servizio e una o più sottoscrizioni a tale argomento usando Azure PowerShell
> * Aggiungere filtri di argomento usando PowerShell
> * Creare due messaggi con contenuto differente
> * Inviare i messaggi e verificare che siano arrivati nelle sottoscrizioni previste
> * Ricevere messaggi dalle sottoscrizioni

Un esempio di questo scenario è un aggiornamento dell'assortimento di magazzino per più punti vendita al dettaglio. In questo scenario ogni negozio o set di negozi ottiene i messaggi a essi destinati per aggiornare gli assortimenti. Questa esercitazione illustra come implementare lo scenario usando filtri e sottoscrizioni. Si crea prima di tutto un argomento con 3 sottoscrizioni, si aggiungono alcune regole e filtri e quindi si inviano e ricevono messaggi dall'argomento e dalle sottoscrizioni.

![coda](./media/service-bus-quickstart-powershell/quick-start-queue.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito][] prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere installato:

1. [Visual Studio 2017 Update 3 (versione 15.3, 26730.01)](http://www.visualstudio.com/vs) o versioni successive.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows) versione 2.0 o successiva.

Ai fini di questa esercitazione, è necessario eseguire la versione più recente di Azure PowerShell. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Accedere ad Azure

Eseguire i comandi seguenti per accedere ad Azure. Questi passaggi non sono necessario se si eseguono i comandi di PowerShell in Cloud Shell: 

1. Installare il modulo PowerShell del bus di servizio:

   ```azurepowershell-interactive
   Install-Module AzureRM.ServiceBus
   ```

2. Eseguire questo comando per accedere ad Azure:

   ```azurepowershell-interactive
   Login-AzureRmAccount
   ```

4. Impostare il contesto corrente di sottoscrizione o visualizzare la sottoscrizione attualmente attiva:

   ```azurepowershell-interactive
   Select-AzureRmSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzureRmContext
   ```

## <a name="provision-resources"></a>Effettuare il provisioning delle risorse

Dopo l'accesso ad Azure, eseguire i comandi seguenti per il provisioning delle risorse del bus di servizio. Assicurarsi di sostituire tutti i segnaposto con i valori appropriati:

```azurepowershell-interactive
# Create a resource group 
New-AzureRmResourceGroup –Name my-resourcegroup –Location westus2

# Create a Messaging namespace
New-AzureRmServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location westus2

# Create a queue 
New-AzureRmServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Dopo l'esecuzione del cmdlet `Get-AzureRmServiceBusKey`, copiare e incollare la stringa di connessione e il nome della coda selezionato in una posizione temporanea, ad esempio il Blocco note. Saranno necessari nel passaggio successivo.

## <a name="send-and-receive-messages"></a>Inviare e ricevere messaggi

Dopo che lo spazio dei nomi e la coda sono stati creati e sono disponibili le credenziali necessarie, è possibile inviare e ricevere messaggi. È possibile esaminare il codice in [questa cartella dell'esempio di GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Per eseguire il codice, seguire questa procedura:

1. Clonare il [repository GitHub del bus di servizio](https://github.com/Azure/azure-service-bus/) eseguendo il comando seguente:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Aprire un prompt di PowerShell.

3. Passare alla cartella dell'esempio `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Se non è già stato fatto, ottenere la stringa di connessione usando il cmdlet di PowerShell seguente. Assicurarsi di sostituire `my-resourcegroup` e `namespace-name` con i valori specifici: 

   ```azurepowershell-interactive
   Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```
5.  Al prompt di PowerShell, digitare il comando seguente:

   ```shell
   dotnet build
   ```
6.  Passare alla cartella `\bin\Debug\netcoreapp2.0`.
7.  Digitare il comando seguente per eseguire il programma. Assicurarsi di sostituire `myConnectionString` con il valore ottenuto in precedenza e `myQueueName` con il nome della coda creata:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 
8. 10 messaggi vengono inviati alla coda e successivamente vengono ricevuti dalla coda:

   ![output programma](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire questo comando per rimuovere il gruppo di risorse, lo spazio dei nomi e tutte le risorse correlate:

```powershell-interactive
Remove-AzureRmResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Questa sezione contiene altri dettagli sulle operazioni eseguite dal codice di esempio. 

### <a name="get-connection-string-and-queue"></a>Ottenere la stringa di connessione e la coda

La stringa di connessione e il nome della coda vengono passati al metodo `Main()` come argomenti della riga di comando. `Main()` dichiara due variabili di stringa per contenere tali valori:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
Il metodo `Main()` avvia quindi il ciclo di messaggi asincrono, `MainAsync()`.

### <a name="message-loop"></a>Ciclo di messaggi

Il metodo `MainAsync()` crea un client di accodamento con gli argomenti della riga di comando, chiama un gestore di messaggi ricevente denominato `RegisterOnMessageHandlerAndReceiveMessages()` e invia il set di messaggi:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

Il metodo `RegisterOnMessageHandlerAndReceiveMessages()` imposta alcune opzioni del gestore di messaggi, quindi chiama il metodo `RegisterMessageHandler()` del client di accodamento, che avvia la ricezione:

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Inviare messaggi

Le operazioni di creazione e invio dei messaggi vengono eseguite nel metodo `SendMessagesAsync()`:

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Elaborare i messaggi

Il metodo `ProcessMessagesAsync()` riconosce, elabora e completa la ricezione dei messaggi:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato effettuato il provisioning delle risorse usando Azure PowerShell, quindi sono stati inviati e ricevuti messaggi da un argomento del bus di servizio e dalle relative sottoscrizioni. Si è appreso come:

> [!div class="checklist"]
> * Creare un argomento del bus di servizio e una o più sottoscrizioni a tale argomento usando il portale di Azure
> * Aggiungere filtri di argomento usando il codice .NET
> * Creare due messaggi con contenuto differente
> * Inviare i messaggi e verificare che siano arrivati nelle sottoscrizioni previste
> * Ricevere messaggi dalle sottoscrizioni

Per altri esempi di invio e ricezione dei messaggi, vedere gli [esempi del bus di servizio su GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Passare alla prossima esercitazione per altre informazioni su come usare le funzionalità di pubblicazione/sottoscrizione del bus di servizio.

> [!div class="nextstepaction"]
> [Aggiornare l'inventario usando PowerShell e argomenti/sottoscrizioni](service-bus-tutorial-topics-subscriptions-cli.md)

[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Installare e configurare Azure PowerShell]: /powershell/azure/install-azurerm-ps