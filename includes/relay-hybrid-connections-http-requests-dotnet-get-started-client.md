---
title: File di inclusione
description: File di inclusione
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 4a3f38e1423db0755d8c76f8850e41173d250f43
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
---
### <a name="create-a-console-application"></a>Creare un'applicazione console

Se è stata disabilitata l'opzione "Richiede l'autorizzazione client" durante la creazione del servizio di inoltro, è possibile inviare le richieste all'URL di Connessioni ibride con qualsiasi browser. Per l'accesso a endpoint protetti, è necessario creare e passare un token nell'intestazione `ServiceBusAuthorization`, come illustrato di seguito.

In Visual Studio creare un nuovo progetto **App console (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Aggiungere il pacchetto NuGet di inoltro

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e quindi scegliere **Gestisci pacchetti NuGet**.
2. Selezionare **Sfoglia** e quindi cercare **Microsoft.Azure.Relay**. Nei risultati della ricerca selezionare **Inoltro di Microsoft Azure**. 
3. Selezionare **Installa** per completare l'installazione. Chiudere la finestra di dialogo.

### <a name="write-code-to-send-requests"></a>Scrivere codice per inviare richieste

1. All'inizio del file Program.cs sostituire le istruzioni `using` esistenti con le istruzioni `using` seguenti:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. Aggiungere costanti alla classe `Program` per i dettagli della connessione ibrida. Sostituire i segnaposto tra parentesi con i valori ottenuti durante la creazione della connessione ibrida. Usare il nome completo dello spazio dei nomi.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Aggiungere il metodo seguente alla classe `Program`:
   
    ```csharp
    private static async Task RunAsync()
    {
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());
    }
    ```
4. Aggiungere la riga di codice seguente al metodo `Main` nella classe `Program`.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Program.cs dovrebbe avere l'aspetto seguente:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```

