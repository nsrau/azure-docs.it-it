---
title: Esercitazione REST sulla messaggistica negoziata del Bus di servizio| Microsoft Azure | Documentazione Microsoft
description: Esercitazione di messaggistica negoziata REST.
services: service-bus
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9b7a8147-a1b1-42fc-b30e-f52e79a902b5
ms.service: service-bus
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/27/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 45b72037e2de01b9201edf3e4ebee7e80d996383


---
# <a name="service-bus-brokered-messaging-rest-tutorial"></a>Esercitazione sulla messaggistica negoziata del bus di servizio - REST
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Questa esercitazione illustra come creare una coda e un argomento/una sottoscrizione di base del bus di servizio di Azure basati su REST.

## <a name="create-a-namespace"></a>Creare uno spazio dei nomi
Il primo passaggio consiste nel creare uno spazio dei nomi del servizio e nell'ottenere una chiave di [firma di accesso condiviso](service-bus-sas-overview.md). Uno spazio dei nomi fornisce un limite per ogni applicazione esposta tramite il bus di servizio. Una chiave di firma di accesso condiviso viene generata dal sistema quando viene creato uno spazio dei nomi del servizio. La combinazione di spazio dei nomi servizio e chiave di firma di accesso condiviso fornisce le credenziali che consentono al bus di servizio di autenticare l'accesso a un'applicazione.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-console-client"></a>Creare un client console
Le code del Bus di servizio consentono di archiviare i messaggi in una coda First in, First-Out. Gli argomenti e le sottoscrizioni implementano un modello di pubblicazione/sottoscrizione; si crea un argomento e quindi una o più sottoscrizioni associate a tale argomento. Quando i messaggi vengono inviati all'argomento, vengono immediatamente inviati ai sottoscrittori dell'argomento.

Il codice contenuto in questa sezione effettua le seguenti operazioni:

* Usa lo spazio dei nomi e la chiave della [firma di accesso condiviso](service-bus-sas-overview.md) per accedere alle risorse dello spazio dei nomi del bus di servizio.
* Crea una coda, invia un messaggio alla coda e legge il messaggio dalla coda.
* Crea un argomento e una sottoscrizione all'argomento e invia e legge il messaggio dalla sottoscrizione.
* Recupera dal bus di servizio tutte le informazioni su coda, argomento e sottoscrizione, incluse le regole della sottoscrizione.
* Elimina le risorse della coda, dell'argomento e della sottoscrizione.

Poiché il servizio è un servizio Web basato su REST, non ci sono tipi speciali coinvolti, visto che l’intero processo di scambio implica l’utilizzo di stringhe. Ciò significa che il progetto di Visual Studio non deve creare riferimenti alle librerie del bus di servizio.

Dopo aver ottenuto lo spazio dei nomi e le credenziali nel primo passaggio, creare un'applicazione console di base di Visual Studio.

### <a name="create-a-console-application"></a>Creare un'applicazione console
1. Aprire Visual Studio come amministratore facendo clic con il pulsante destro del mouse sul programma nel menu **Start**, quindi scegliere **Esegui come amministratore**.
2. Creare un nuovo progetto di applicazione console. Fare clic sul menu **File**, selezionare **Nuovo** e fare clic su **Progetto**. Nella finestra di dialogo **Nuovo progetto** fare clic su **Visual C#**. Se **Visual C#** non è visibile, cercare in **Altri linguaggi**, selezionare il modello **Applicazione console** e denominarlo **Microsoft.ServiceBus.Samples**. Utilizzare il Percorso predefinito. Fare clic su **OK** per creare il progetto.
3. In Program.cs, assicurarsi che le istruzioni `using` appaiano come quanto segue:
   
    ```
    using System;
    using System.Globalization;
    using System.IO;
    using System.Net;
    using System.Security.Cryptography;
    using System.Text;
    using System.Xml;
    ```
4. Se necessario, rinominare lo spazio dei nomi per il programma dal valore predefinito di Visual Studio con `Microsoft.ServiceBus.Samples`.
5. All'interno della classe `Program` aggiungere le seguenti variabili globali:
   
    ```
    static string serviceNamespace;
    static string baseAddress;
    static string token;
    const string sbHostName = "servicebus.windows.net";
    ```
6. All'interno di `Main()`, incollare il codice seguente:
   
    ```
    Console.Write("Enter your service namespace: ");
    serviceNamespace = Console.ReadLine();
   
    Console.Write("Enter your SAS key: ");
    string SASKey = Console.ReadLine();
   
    baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
    try
    {
        token = GetSASToken("RootManageSharedAccessKey", SASKey);
   
        string queueName = "Queue" + Guid.NewGuid().ToString();
   
        // Create and put a message in the queue
        CreateQueue(queueName, token);
        SendMessage(queueName, "msg1");
        string msg = ReceiveAndDeleteMessage(queueName);
   
        string topicName = "Topic" + Guid.NewGuid().ToString();
        string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
        CreateTopic(topicName);
        CreateSubscription(topicName, subscriptionName);
        SendMessage(topicName, "msg2");
   
        Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));
   
        // Get an Atom feed with all the queues in the namespace
        Console.WriteLine(GetResources("$Resources/Queues"));
   
        // Get an Atom feed with all the topics in the namespace
        Console.WriteLine(GetResources("$Resources/Topics"));
   
        // Get an Atom feed with all the subscriptions for the topic we just created
        Console.WriteLine(GetResources(topicName + "/Subscriptions"));
   
        // Get an Atom feed with all the rules for the topic and subscription we just created
        Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));
   
        // Delete the queue we created
        DeleteResource(queueName);
   
        // Delete the topic we created
        DeleteResource(topicName);
   
        // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
        Console.WriteLine(GetResources("$Resources/Topics"));
   
        // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
        Console.WriteLine(GetResources("$Resources/Queues"));
    }
    catch (WebException we)
    {
        using (HttpWebResponse response = we.Response as HttpWebResponse)
        {
            if (response != null)
            {
                Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
            }
            else
            {
                Console.WriteLine(we.ToString());
            }
        }
    }
   
    Console.WriteLine("\nPress ENTER to exit.");
    Console.ReadLine();
    ```

## <a name="create-management-credentials"></a>Creazione delle credenziali di gestione
Il passaggio successivo consiste nello scrivere un metodo che elabori lo spazio dei nomi e la chiave SAS immessi nel passaggio precedente e restituisca un token SAS. In questo esempio viene creato un token SAS  valido per un'ora.

### <a name="create-a-getsastoken-method"></a>Creare un metodo GetSASToken()
Incollare il codice seguente dopo il metodo `Main()`, nella classe `Program`:

```
private static string GetSASToken(string SASKeyName, string SASKeyValue)
{
  TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
  string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
  string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
  HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

  string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
  string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
      WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
  return sasToken;
}
```
## <a name="create-the-queue"></a>Creare la coda
Il passaggio successivo consiste nello scrivere un metodo che utilizzi il comando HTTP PUT di tipo REST per creare una coda.

Incollare il seguente codice subito dopo il codice `GetSASToken()` aggiunto nel passaggio precedente:

```
// Uses HTTP PUT to create the queue
private static string CreateQueue(string queueName, string token)
{
    // Create the URI of the new queue, note that this uses the HTTPS scheme
    string queueAddress = baseAddress + queueName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating queue {0}", queueAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                          <title type=""text"">" + queueName + @"</title>
                          <content type=""application/xml"">
                            <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                          </content>
                        </entry>";

    byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## <a name="send-a-message-to-the-queue"></a>Inviare un messaggio alla coda
In questo passaggio si aggiunge un metodo che utilizza il comando HTTP POST di tipo REST per inviare un messaggio alla coda creata nel passaggio precedente.

1. Incollare il seguente codice subito dopo il codice `CreateQueue()` aggiunto nel passaggio precedente:
   
    ```
    // Sends a message to the "queueName" queue, given the name and the value to enqueue
    // Uses an HTTP POST request.
    private static void SendMessage(string queueName, string body)
    {
        string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
        Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
        WebClient webClient = new WebClient();
        webClient.Headers[HttpRequestHeader.Authorization] = token;
   
        webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));
    }
    ```
2. Le proprietà dei messaggi negoziati standard vengono inserite in una  intestazione HTTP `BrokerProperties`. Le proprietà del broker devono essere serializzate in formato JSON. Per specificare un valore **TimeToLive** di 30 secondi e aggiungere un'etichetta "M1" al messaggio, aggiungere il codice seguente immediatamente prima della chiamata `webClient.UploadData()` illustrata nell'esempio precedente:
   
    ```
    // Add brokered message properties "TimeToLive" and "Label"
    webClient.Headers.Add("BrokerProperties", "{ \"TimeToLive\":30, \"Label\":\"M1\"}");
    ```
   
    Si noti che le proprietà dei messaggi negoziati sono state e saranno aggiunte. Pertanto, la richiesta di invio deve specificare una versione API che supporti tutte le proprietà dei messaggi negoziati che fanno parte della richiesta. Se la versione API specificata non supporta una proprietà del messaggio negoziato, tale proprietà viene ignorata.
3. Le proprietà dei messaggi personalizzate sono definite come un set di coppie chiave-valore. Ogni proprietà personalizzata viene archiviata nella relativa intestazione TPPT. Per aggiungere le proprietà personalizzate "Priority" e "Customer", aggiungere il codice seguente immediatamente prima della chiamata `webClient.UploadData()` illustrata nell'esempio precedente:
   
    ```
    // Add custom properties "Priority" and "Customer".
    webClient.Headers.Add("Priority", "High");
    webClient.Headers.Add("Customer", "12345");
    ```

## <a name="receive-and-delete-a-message-from-the-queue"></a>Ricezione ed eliminazione di un messaggio dalla coda
Il passaggio successivo consiste nell'aggiungere un metodo che utilizzi il comando HTTP DELETE di tipo REST per ricevere ed eliminare un messaggio dalla coda.

Incollare il seguente codice subito dopo il codice `SendMessage()` aggiunto nel passaggio precedente:

```
// Receives and deletes the next message from the given resource (queue, topic, or subscription)
// using the resourceName and an HTTP DELETE request
private static string ReceiveAndDeleteMessage(string resourceName)
{
    string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
    Console.WriteLine("\nRetrieving message from {0}", fullAddress);
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
    string responseStr = Encoding.UTF8.GetString(response);

    Console.WriteLine(responseStr);
    return responseStr;
}
```

## <a name="create-a-topic-and-subscription"></a>Creare un argomento e una sottoscrizione
Il passaggio successivo consiste nello scrivere un metodo che usa il comando HTTP PUT di tipo REST per creare un argomento. Verrà quindi scritto un metodo che crea una sottoscrizione all'argomento.

### <a name="create-a-topic"></a>Creare un argomento
Incollare il seguente codice subito dopo il codice `ReceiveAndDeleteMessage()` aggiunto nel passaggio precedente:

```
// Using an HTTP PUT request.
private static string CreateTopic(string topicName)
{
    var topicAddress = baseAddress + topicName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating topic {0}", topicAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

### <a name="create-a-subscription"></a>Creare una sottoscrizione
Il codice seguente crea una sottoscrizione per l'argomento creato nel passaggio precedente. Aggiungere il codice seguente subito dopo la definizione `CreateTopic()`:

```
private static string CreateSubscription(string topicName, string subscriptionName)
{
    var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## <a name="retrieve-message-resources"></a>Recuperare le risorse di messaggistica
In questo passaggio, aggiungere il codice che recupera le proprietà dei messaggi, e che elimina le risorse di messaggistica create nei passaggi precedenti.

### <a name="retrieve-an-atom-feed-with-the-specified-resources"></a>Recuperare un feed Atom contenente le risorse specificate
Incollare il seguente codice subito dopo il metodo `CreateSubscription()` aggiunto nel passaggio precedente:

```
private static string GetResources(string resourceAddress)
{
    string fullAddress = baseAddress + resourceAddress;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;
    Console.WriteLine("\nGetting resources from {0}", fullAddress);
    return FormatXml(webClient.DownloadString(fullAddress));
}
```

### <a name="delete-messaging-entities"></a>Eliminare entità di messaggistica
Incollare il seguente codice subito dopo il codice aggiunto nel passaggio precedente:

```
private static string DeleteResource(string resourceName)
{
    string fullAddress = baseAddress + resourceName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nDeleting resource at {0}", fullAddress);
    byte[] response = webClient.UploadData(fullAddress, "DELETE", newbyte[0]);
    return Encoding.UTF8.GetString(response);
}
```

### <a name="format-the-atom-feed"></a>Formattare il feed Atom
Il metodo `GetResources()` contiene una chiamata per il metodo `FormatXml()` che riformatta il feed Atom recuperato per migliorarne la leggibilità. Di seguito è possibile trovare la definizione di `FormatXml()`; aggiungere questo codice subito dopo il codice `DeleteResource()` aggiunto nella sezione precedente:

```
// Formats the XML string to be more human-readable; intended for display purposes
private static string FormatXml(string inputXml)
{
    XmlDocument document = new XmlDocument();
    document.Load(new StringReader(inputXml));

    StringBuilder builder = new StringBuilder();
    using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
    {
        writer.Formatting = Formatting.Indented;
        document.Save(writer);
    }

    return builder.ToString();
}
```

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione
È ora possibile compilare ed eseguire l'applicazione. In Visual Studio scegliere **Compila soluzione** dal menu **Compila** o premere **CTRL+MAIUSC+B**.

### <a name="run-the-application"></a>Eseguire l'applicazione
Se non sono presenti errori, premere F5 per eseguire l'applicazione. Quando richiesto, immettere lo spazio dei nomi, il nome della chiave SAS e il valore della chiave SAS ottenuti nel primo passaggio.

### <a name="example"></a>Esempio
L'esempio seguente rappresenta il codice completo, come dovrebbe apparire dopo aver seguito tutti i passaggi di questa esercitazione.

```
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Security.Cryptography;
using System.Text;
using System.Xml;

namespace Microsoft.ServiceBus.Samples
{
    class Program
    {
        static string serviceNamespace;
        static string baseAddress;
        static string token;
        const string sbHostName = "servicebus.windows.net";

        static void Main(string[] args)
        {
            Console.Write("Enter your service namespace: ");
            serviceNamespace = Console.ReadLine();

            Console.Write("Enter your SAS key: ");
            string SASKey = Console.ReadLine();

            baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
            try
            {
                token = GetSASToken("RootManageSharedAccessKey", SASKey);

                string queueName = "Queue" + Guid.NewGuid().ToString();

                // Create and put a message in the queue
                CreateQueue(queueName, token);
                SendMessage(queueName, "msg1");
                string msg = ReceiveAndDeleteMessage(queueName);

                string topicName = "Topic" + Guid.NewGuid().ToString();
                string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
                CreateTopic(topicName);
                CreateSubscription(topicName, subscriptionName);
                SendMessage(topicName, "msg2");

                Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));

                // Get an Atom feed with all the queues in the namespace
                Console.WriteLine(GetResources("$Resources/Queues"));

                // Get an Atom feed with all the topics in the namespace
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the subscriptions for the topic we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions"));

                // Get an Atom feed with all the rules for the topic and subscription we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));

                // Delete the queue we created
                DeleteResource(queueName);

                // Delete the topic we created
                DeleteResource(topicName);

                // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Queues"));
            }
            catch (WebException we)
            {
                using (HttpWebResponse response = we.Response as HttpWebResponse)
                {
                    if (response != null)
                    {
                        Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
                    }
                    else
                    {
                        Console.WriteLine(we.ToString());
                    }
                }
            }

            Console.WriteLine("\nPress ENTER to exit.");
            Console.ReadLine();
        }

        private static string GetSASToken(string SASKeyName, string SASKeyValue)
        {
            TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
            string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
            string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
            HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

            string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
            string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
                WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
            return sasToken;
        }

        // Uses HTTP PUT to create the queue
        private static string CreateQueue(string queueName, string token)
        {
            // Create the URI of the new queue, note that this uses the HTTPS scheme
            string queueAddress = baseAddress + queueName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating queue {0}", queueAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + queueName + @"</title>
                                  <content type=""application/xml"">
                                    <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        // Sends a message to the "queueName" queue, given the name and the value to enqueue
        // Uses an HTTP POST request.
        private static void SendMessage(string queueName, string body)
        {
            string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
            Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            // Add brokered message properties “TimeToLive” and “Label”.
            webClient.Headers.Add("BrokerProperties", "{ \"TimeToLive\":30, \"Label\":\"M1\"}");
            // Add custom properties “Priority” and “Customer”.
            webClient.Headers.Add("Priority", "High");
            webClient.Headers.Add("Customer", "12345");
            webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));

        }

        // Receives and deletes the next message from the given resource (queue, topic, or subscription)
        // using the resourceName and an HTTP DELETE request.
        private static string ReceiveAndDeleteMessage(string resourceName)
        {
            string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
            Console.WriteLine("\nRetrieving message from {0}", fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            string responseStr = Encoding.UTF8.GetString(response);

            Console.WriteLine(responseStr);
            return responseStr;
        }

        // Using an HTTP PUT request.
        private static string CreateTopic(string topicName)
        {
            var topicAddress = baseAddress + topicName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating topic {0}", topicAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string CreateSubscription(string topicName, string subscriptionName)
        {
            var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string GetResources(string resourceAddress)
        {
            string fullAddress = baseAddress + resourceAddress;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            Console.WriteLine("\nGetting resources from {0}", fullAddress);
            return FormatXml(webClient.DownloadString(fullAddress));
        }

        private static string DeleteResource(string resourceName)
        {
            string fullAddress = baseAddress + resourceName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nDeleting resource at {0}", fullAddress);
            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            return Encoding.UTF8.GetString(response);
        }

        // Formats the XML string to be more human-readable; intended for display purposes
        private static string FormatXml(string inputXml)
        {
            XmlDocument document = new XmlDocument();
            document.Load(new StringReader(inputXml));

            StringBuilder builder = new StringBuilder();
            using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
            {
                writer.Formatting = Formatting.Indented;
                document.Save(writer);
            }

            return builder.ToString();
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti per ulteriori informazioni:

* [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
* [Dati fondamentali del bus di servizio](service-bus-fundamentals-hybrid-solutions.md)
* [Esercitazione su REST di Inoltro del bus di servizio](../service-bus-relay/service-bus-relay-rest-tutorial.md)




<!--HONumber=Nov16_HO2-->


