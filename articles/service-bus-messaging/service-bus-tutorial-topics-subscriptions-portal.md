---
title: Esercitazione - Aggiornare l'assortimento del magazzino al dettaglio usando i canali di pubblicazione/sottoscrizione e i filtri di argomento con il portale di Azure | Microsoft Docs
description: In questa esercitazione viene illustrato come inviare e ricevere messaggi da un argomento e una sottoscrizione e come aggiungere e usare le regole di filtro con .NET
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.author: sethm
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: f504f3bf513a20d8590d9907106b1fd12f907877
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651480"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Esercitazione: Aggiornare l'inventario usando il portale di Azure e argomenti/sottoscrizioni

Il bus di servizio di Microsoft Azure è un servizio di messaggistica cloud multi-tenant che invia informazioni tra applicazioni e servizi. Le operazioni asincrone offrono messaggistica negoziata flessibile, insieme a funzionalità di messaggistica e pubblicazione/sottoscrizione FIFO (First-In-First-Out) strutturate. Questa esercitazione illustra come usare gli argomenti del bus di servizio e le sottoscrizioni in uno scenario di magazzino al dettaglio, con i canali di pubblicazione/sottoscrizione tramite il portale di Azure e .NET.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare un argomento del bus di servizio e una o più sottoscrizioni a tale argomento usando il portale di Azure
> * Aggiungere filtri di argomento usando il codice .NET
> * Creare due messaggi con contenuto differente
> * Inviare i messaggi e verificare che siano arrivati nelle sottoscrizioni previste
> * Ricevere messaggi dalle sottoscrizioni

Un esempio di questo scenario è un aggiornamento dell'assortimento di magazzino per più punti vendita al dettaglio. In questo scenario ogni negozio o set di negozi ottiene i messaggi a essi destinati per aggiornare gli assortimenti. Questa esercitazione illustra come implementare lo scenario usando filtri e sottoscrizioni. Si crea prima di tutto un argomento con 3 sottoscrizioni, si aggiungono alcune regole e filtri e quindi si inviano e ricevono messaggi dall'argomento e dalle sottoscrizioni.

![argomento](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito][] prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, accertarsi di avere installato:

- [Visual Studio 2017 Update 3 (versione 15.3, 26730.01)](http://www.visualstudio.com/vs) o versioni successive.
- [NET Core SDK](https://www.microsoft.com/net/download/windows) versione 2.0 o successiva.

## <a name="service-bus-topics-and-subscriptions"></a>Argomenti e sottoscrizioni del bus di servizio

Ogni [sottoscrizione a un argomento](service-bus-messaging-overview.md#topics) può ricevere una copia di ogni messaggio. Gli argomenti sono completamente compatibili a livello di protocollo e di semantica con le code del bus di servizio. Gli argomenti del bus di servizio supportano un'ampia gamma di regole di selezione con condizioni di filtro, con azioni facoltative per impostare o modificare le proprietà dei messaggi. Ogni volta che una regola corrisponde, genera un messaggio. Per altre informazioni su regole, filtri e azioni, seguire questo [collegamento](topic-filters.md).

## <a name="log-on-to-the-azure-portal"></a>Accedere al portale di Azure

Aprire e accedere prima di tutto al [portale di Azure][Azure portal] con la propria sottoscrizione di Azure. Il primo passaggio è la creazione di uno spazio dei nomi del bus di servizio di tipo **Messaggistica**.

## <a name="create-a-service-bus-namespace"></a>Creare uno spazio dei nomi del bus di servizio

Uno spazio dei nomi di messaggistica del bus di servizio fornisce un contenitore di ambito univoco, a cui fa riferimento il [FQDN][], in cui si crea una o più code, argomenti e sottoscrizioni. L'esempio seguente crea uno spazio dei nomi di messaggistica del bus di servizio in un [gruppo di risorse](/azure/azure-resource-manager/resource-group-portal) nuovo o esistente:

1. Nel riquadro di spostamento sinistro del portale fare clic su **+ Crea una risorsa**, quindi su **Enterprise Integration** e infine su **Bus di servizio**.
2. Nella finestra di dialogo **Crea spazio dei nomi** immettere un nome per lo spazio dei nomi. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.
3. Dopo aver verificato che il nome dello spazio dei nomi sia disponibile, scegliere il piano tariffario, ovvero Standard o Premium.
4. Nel campo **Sottoscrizione** scegliere una sottoscrizione di Azure in cui creare lo spazio dei nomi.
5. Nel campo **Gruppo di risorse** scegliere un gruppo di risorse esistente nel quale risiederà lo spazio dei nomi oppure crearne uno nuovo.      
6. In **Località**scegliere il paese o l'area in cui deve essere ospitato lo spazio dei nomi.
7. Fare clic su **Crea**. A questo punto, lo spazio dei nomi verrà creato e abilitato nel sistema. Potrebbero essere necessari alcuni minuti per consentire al sistema di effettuare il provisioning delle risorse per lo spazio dei nomi creato.

  ![namespace](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>Ottenere le credenziali di gestione

Con la creazione di un nuovo spazio dei nomi viene generata automaticamente una regola di firma di accesso condiviso iniziale con una coppia associata di chiavi primaria e secondaria, ognuna delle quali concede il controllo completo su tutti gli aspetti dello spazio dei nomi. Per copiare la regola iniziale seguire questa procedura:

1. Fare clic su **Tutte le risorse**, quindi sul nome dello spazio dei nomi appena creato.
2. Nella finestra dello spazio dei nomi fare clic su **Criteri di accesso condiviso**.
3. Nella schermata **Criteri di accesso condiviso** fare clic su **RootManageSharedAccessKey**.
4. Nella finestra **Criteri: RootManageSharedAccessKey** fare clic sul pulsante **Copia** accanto a **Stringa di connessione primaria** per copiare la stringa di connessione negli Appunti e usarla in un secondo momento. Incollare questo valore nel Blocco note o in un'altra posizione temporanea.

    ![connection-string][connection-string]
5. Ripetere il passaggio precedente e copiare e incollare il valore della **chiave primaria** in un percorso temporaneo per usarlo in seguito.

## <a name="create-a-topic-and-subscriptions"></a>Creare un argomento e le sottoscrizioni

Per creare un argomento del bus di servizio, specificare lo spazio dei nomi in cui crearlo. L'esempio seguente mostra come creare un argomento nel portale:

1. Nel riquadro di spostamento a sinistra del portale fare clic su **Bus di servizio**. Se l'opzione **Bus di servizio** non è visualizzata, fare clic su **Tutti i servizi**.
2. Fare clic sullo spazio dei nomi in cui si vuole creare l'argomento.
3. Nella finestra dello spazio dei nomi fare clic su **Argomenti** e quindi nella finestra **Argomenti** fare clic su **+ Argomenti**.
4. Immettere il nome dell'argomento in **Nome** e lasciare le impostazioni predefinite per gli altri valori.
5. Nella parte inferiore della finestra fare clic su **Crea**.
6. Prendere nota del nome dell'argomento.
7. Selezionare l'argomento appena creato.
8. Fare clic su **+ Sottoscrizione**, immettere il nome della sottoscrizione **S1** e lasciare le impostazioni predefinite per tutti gli altri valori.
9. Ripetere due volte il passaggio precedente, creando le sottoscrizioni denominate **S2** e **S3**.

## <a name="create-filter-rules-on-subscriptions"></a>Creare regole di filtro nelle sottoscrizioni

Dopo che è stato effettuato il provisioning dello spazio dei nomi e di argomento/sottoscrizioni e sono disponibili le credenziali necessarie, è possibile creare regole di filtro nelle sottoscrizioni, quindi inviare e ricevere messaggi. È possibile esaminare il codice in [questa cartella dell'esempio di GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/GettingStarted\BasicSendReceiveTutorialwithFilters).

### <a name="send-and-receive-messages"></a>Inviare e ricevere messaggi

Per eseguire il codice, seguire questa procedura:

1. In un prompt dei comandi o di PowerShell clonare il [repository GitHub del bus di servizio](https://github.com/Azure/azure-service-bus/) eseguendo il comando seguente:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Passare alla cartella dell'esempio `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters`.

3. Ottenere la stringa di connessione copiata nel Blocco note nella sezione [Ottenere le credenziali di gestione](#obtain-the-management-credentials) di questa esercitazione. È anche necessario il nome dell'argomento creato nella sezione precedente.

4. Al prompt dei comandi digitare il comando seguente:

   ```shell
   dotnet build
   ```

5. Passare alla cartella `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0`.

6. Digitare il comando seguente per eseguire il programma. Assicurarsi di sostituire `myConnectionString` con il valore ottenuto in precedenza e `myTopicName` con il nome dell'argomento creato:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Seguire le istruzioni nella console per selezionare prima la creazione dei filtri. Parte della creazione di filtri consiste nel rimuovere i filtri predefiniti. Quando si usa PowerShell o l'interfaccia della riga di comando, non è necessario rimuovere i filtri predefiniti, ma, se si esegue questa operazione nel codice, è necessario rimuoverli. I comandi della console 1 e 3 consentono di gestire i filtri nelle sottoscrizioni create in precedenza:

   - Esecuzione 1: per rimuovere i filtri predefiniti.
   - Esecuzione 2: per aggiungere filtri personalizzati.
   - Esecuzione 3: per rimuovere facoltativamente i filtri personalizzati. Si noti che questa operazione non ricreerà i filtri predefiniti.

    ![Output di 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Dopo la creazione del filtro, è possibile inviare messaggi. Premere 4 e osservare l'invio di 10 messaggi all'argomento:

    ![Inviare l'output](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Premere 5 e osservare la ricezione dei messaggi. Se non si ottengono nuovamente 10 messaggi, premere "m" per visualizzare il menu, quindi premere ancora 5.

    ![Output di ricezione](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare lo spazio dei nomi e la coda. A tale scopo, selezionare queste risorse nel portale e fare clic su **Elimina**.

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Questa sezione contiene altri dettagli sulle operazioni eseguite dal codice di esempio.

### <a name="get-connection-string-and-topic"></a>Ottenere la stringa di connessione e l'argomento

Il codice dichiara prima di tutto un set di variabili, che determinano l'esecuzione rimanente del programma.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

La stringa di connessione e il nome dell'argomento vengono passati tramite i parametri della riga di comando come illustrato e quindi vengono letti nel metodo `Main()`:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Rimuovere i filtri predefiniti

Quando si crea una sottoscrizione, il bus di servizio crea un filtro predefinito per ogni sottoscrizione. Questo filtro consente di ricevere ogni messaggio inviato all'argomento. Per usare i filtri personalizzati, è possibile rimuovere il filtro predefinito, come illustrato nel codice seguente:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Creare filtri

Il codice seguente aggiunge i filtri personalizzati definiti in questa esercitazione:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Rimuovere i filtri personalizzati creati

Se si vogliono rimuovere tutti i filtri nella sottoscrizione, il codice seguente illustra come eseguire questa operazione:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Inviare messaggi

L'invio di messaggi a un argomento è simile all'invio di messaggi a una coda. Questo esempio mostra come inviare messaggi, usando un elenco di attività e l'elaborazione asincrona:

```csharp
public async Task SendMessages()
{
    try
    {
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Ricevere messaggi

I messaggi vengono ricevuti nuovamente tramite un elenco di attività e il codice usa l'invio in batch. È possibile inviare e ricevere messaggi usando l'invio in batch, ma questo esempio mostra solo la ricezione in batch. Anche se di fatto il ciclo non verrà interrotto, continuare a eseguirlo e impostare un intervallo di tempo superiore, ad esempio un minuto. La chiamata di ricezione al broker viene lasciata aperta per questo periodo di tempo e, se arrivano messaggi, vengono immediatamente restituiti e viene effettuata una nuova chiamata di ricezione. Questo concetto è denominato *polling di lunga durata*. L'uso della distribuzione della ricezione che è possibile visualizzare nella [guida introduttiva](service-bus-quickstart-portal.md) e in diversi altri esempi del repository, è un'opzione più comune.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }

    await receiver.CloseAsync();
}
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato effettuato il provisioning delle risorse usando il portale di Azure, quindi sono stati inviati e ricevuti messaggi da un argomento del bus di servizio e dalle relative sottoscrizioni. Si è appreso come:

> [!div class="checklist"]
> * Creare un argomento del bus di servizio e una o più sottoscrizioni a tale argomento usando il portale di Azure
> * Aggiungere filtri di argomento usando il codice .NET
> * Creare due messaggi con contenuto differente
> * Inviare i messaggi e verificare che siano arrivati nelle sottoscrizioni previste
> * Ricevere messaggi dalle sottoscrizioni

Per altri esempi di invio e ricezione dei messaggi, vedere gli [esempi del bus di servizio su GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Passare alla prossima esercitazione per altre informazioni su come usare le funzionalità di pubblicazione/sottoscrizione del bus di servizio.

> [!div class="nextstepaction"]
> [Aggiornare l'inventario usando PowerShell e argomenti/sottoscrizioni](service-bus-tutorial-topics-subscriptions-powershell.md)

[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[FQDN]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png