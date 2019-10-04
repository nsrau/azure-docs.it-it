---
title: Guida introduttiva - Usare Azure PowerShell per creare una coda del bus di servizio | Microsoft Docs
description: In questa guida introduttiva si apprenderà come usare Azure PowerShell per creare una coda del bus di servizio. Quindi si userà un'applicazione di esempio per inviare e ricevere messaggi dalla coda.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: 80cef3efd65a3f68dba1856a892a9a120f7d95cd
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990289"
---
# <a name="quickstart-use-azure-powershell-to-create-a-service-bus-queue"></a>Guida introduttiva: Usare Azure PowerShell per creare una coda del bus di servizio
Questa guida introduttiva illustra come inviare e ricevere messaggi verso e da una coda del bus di servizio, usando PowerShell per creare uno spazio dei nomi di messaggistica e una coda in tale spazio dei nomi e come ottenere le credenziali di autorizzazione per tale spazio dei nomi. La procedura illustra quindi come inviare e ricevere messaggi da questa coda usando la [libreria .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere installato:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito][] prima di iniziare. 
- [Visual Studio 2017 Update 3 (versione 15.3, 26730.01)](https://www.visualstudio.com/vs) o versioni successive. Si usa Visual Studio per compilare un esempio che invia messaggi a e riceve messaggi da una coda. L'esempio consiste nel testare la coda creata nel portale. 
- [NET Core SDK](https://www.microsoft.com/net/download/windows) versione 2.0 o successiva.

Ai fini di questa guida introduttiva, è necessario eseguire la versione più recente di Azure PowerShell. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell][]. Se si ha familiarità con Azure Cloud Shell, è possibile usarlo senza installare Azure PowerShell nel computer. Per informazioni su Azure Cloud Shell, vedere [Panoramica di Azure Cloud Shell](../cloud-shell/overview.md)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

1. Installare prima di tutto il modulo PowerShell del bus di servizio, se non è già stato fatto:

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. Eseguire questo comando per accedere ad Azure:

   ```azurepowershell-interactive
   Login-AzAccount
   ```

3. Eseguire i comandi seguenti per impostare il contesto corrente di sottoscrizione o per visualizzare la sottoscrizione attualmente attiva:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>Effettuare il provisioning delle risorse

Dal prompt di PowerShell eseguire i comandi seguenti per il provisioning delle risorse del bus di servizio. Assicurarsi di sostituire tutti i segnaposto con i valori appropriati:

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Dopo l'esecuzione del cmdlet `Get-AzServiceBusKey`, copiare e incollare la stringa di connessione e il nome della coda selezionato in una posizione temporanea, ad esempio il Blocco note. Saranno necessari nel passaggio successivo.

## <a name="send-and-receive-messages"></a>Inviare e ricevere messaggi

Dopo che lo spazio dei nomi e la coda sono stati creati e sono disponibili le credenziali necessarie, è possibile inviare e ricevere messaggi. È possibile esaminare il codice in [questa cartella dell'esempio di GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Per eseguire il codice, seguire questa procedura:

1. Clonare il [repository GitHub del bus di servizio](https://github.com/Azure/azure-service-bus/) eseguendo il comando seguente:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. Passare alla cartella dell'esempio `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Se non è già stato fatto, ottenere la stringa di connessione usando il cmdlet di PowerShell seguente. Assicurarsi di sostituire `my-resourcegroup`  e  `namespace-name` con i valori specifici: 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5. Al prompt di PowerShell, digitare il comando seguente:

   ```shell
   dotnet build
   ```

6. Passare alla cartella `bin\Debug\netcoreapp2.0`.

7. Digitare il comando seguente per eseguire il programma. Assicurarsi di sostituire `myConnectionString` con il valore ottenuto in precedenza e `myQueueName` con il nome della coda creata:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. 10 messaggi vengono inviati alla coda e successivamente vengono ricevuti dalla coda:

   ![output programma](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Eseguire questo comando per rimuovere il gruppo di risorse, lo spazio dei nomi e tutte le risorse correlate:

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
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

Il metodo MainAsync() crea un client di accodamento con gli argomenti della riga di comando, chiama un gestore di messaggi ricevente denominato `RegisterOnMessageHandlerAndReceiveMessages()` e invia il set di messaggi:

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

Il metodo `RegisterOnMessageHandlerAndReceiveMessages()` imposta semplicemente alcune opzioni del gestore di messaggi, quindi chiama il metodo `RegisterMessageHandler()` del client di accodamento, che avvia la ricezione:

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

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati creati uno spazio dei nomi del bus di servizio e altre risorse necessarie per inviare e ricevere messaggi da una coda. Per altre informazioni sulla scrittura di codice per inviare e ricevere messaggi, procedere alle esercitazioni nella sezione **Inviare e ricevere messaggi**. 

> [!div class="nextstepaction"]
> [Inviare e ricevere messaggi](service-bus-dotnet-get-started-with-queues.md)

[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Installare e configurare Azure PowerShell]: /powershell/azure/install-Az-ps
