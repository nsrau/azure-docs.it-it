### <a name="create-a-console-application"></a>Creare un'applicazione console

In Visual Studio creare un nuovo progetto **App console (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Aggiungere il pacchetto NuGet di inoltro

1. Fare clic con il pulsante destro del mouse sul progetto appena creato e quindi scegliere **Gestisci pacchetti NuGet**.
2. Selezionare **Sfoglia** e quindi cercare **Microsoft.Azure.Relay**. Nei risultati della ricerca selezionare **Inoltro di Microsoft Azure**. 
3. Selezionare **Installa** per completare l'installazione. Chiudere la finestra di dialogo.

### <a name="write-code-to-receive-messages"></a>Scrivere codice per ricevere messaggi

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
3. Aggiungere il metodo `ProcessMessagesOnConnection` alla classe `Program`:
   
    ```csharp
    // The method initiates the connection.
    private static async void ProcessMessagesOnConnection(HybridConnectionStream relayConnection, CancellationTokenSource cts)
    {
        Console.WriteLine("New session");
   
        // The connection is a fully bidrectional stream. 
        // Put a stream reader and a stream writer over it.  
        // This allows you to read UTF-8 text that comes from 
        // the sender, and to write text replies back.
        var reader = new StreamReader(relayConnection);
        var writer = new StreamWriter(relayConnection) { AutoFlush = true };
        while (!cts.IsCancellationRequested)
        {
            try
            {
                // Read a line of input until a newline is encountered.
                var line = await reader.ReadLineAsync();
   
                if (string.IsNullOrEmpty(line))
                {
                    // If there's no input data, signal that 
                    // you will no longer send data on this connection,
                    // and then break out of the processing loop.
                    await relayConnection.ShutdownAsync(cts.Token);
                    break;
                }
   
                // Write the line on the console.
                Console.WriteLine(line);
   
                // Write the line back to the client, prepended with "Echo:"
                await writer.WriteLineAsync($"Echo: {line}");
            }
            catch (IOException)
            {
                // Catch an I/O exception. This likely occurred when
                // the client disconnected.
                Console.WriteLine("Client closed connection");
                break;
            }
        }
   
        Console.WriteLine("End session");
   
        // Close the connection.
        await relayConnection.CloseAsync(cts.Token);
    }
    ```
4. Aggiungere il metodo `RunAsync` alla classe `Program`:
   
    ```csharp
    private static async Task RunAsync()
    {
        var cts = new CancellationTokenSource();
   
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Subscribe to the status events.
        listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
        listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
        listener.Online += (o, e) => { Console.WriteLine("Online"); };
   
        // Opening the listener establishes the control channel to
        // the Azure Relay service. The control channel is continuously 
        // maintained, and is reestablished when connectivity is disrupted.
        await listener.OpenAsync(cts.Token);
        Console.WriteLine("Server listening");
   
        // Provide callback for the cancellation token that will close the listener.
        cts.Token.Register(() => listener.CloseAsync(CancellationToken.None));
   
        // Start a new thread that will continuously read the console.
        new Task(() => Console.In.ReadLineAsync().ContinueWith((s) => { cts.Cancel(); })).Start();
   
        // Accept the next available, pending connection request. 
        // Shutting down the listener allows a clean exit. 
        // This method returns null.
        while (true)
        {
            var relayConnection = await listener.AcceptConnectionAsync();
            if (relayConnection == null)
            {
                break;
            }
   
            ProcessMessagesOnConnection(relayConnection, cts);
        }
   
        // Close the listener after you exit the processing loop.
        await listener.CloseAsync(cts.Token);
    }
    ```
5. Aggiungere la riga di codice seguente al metodo `Main` nella classe `Program`:
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Il file Program.cs completato avrà l'aspetto seguente:
   
    ```csharp
    namespace Server
    {
        using System;
        using System.IO;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Relay;
   
        public class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            public static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                var cts = new CancellationTokenSource();
   
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
                // Subscribe to the status events.
                listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
                listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
                listener.Online += (o, e) => { Console.WriteLine("Online"); };
   
                // Opening the listener establishes the control channel to
                // the Azure Relay service. The control channel is continuously 
                // maintained, and is reestablished when connectivity is disrupted.
                await listener.OpenAsync(cts.Token);
                Console.WriteLine("Server listening");
   
                // Provide callback for a cancellation token that will close the listener.
                cts.Token.Register(() => listener.CloseAsync(CancellationToken.None));
   
                // Start a new thread that will continuously read the console.
                new Task(() => Console.In.ReadLineAsync().ContinueWith((s) => { cts.Cancel(); })).Start();
   
                // Accept the next available, pending connection request. 
                // Shutting down the listener allows a clean exit. 
                // This method returns null.
                while (true)
                {
                    var relayConnection = await listener.AcceptConnectionAsync();
                    if (relayConnection == null)
                    {
                        break;
                    }
   
                    ProcessMessagesOnConnection(relayConnection, cts);
                }
   
                // Close the listener after you exit the processing loop.
                await listener.CloseAsync(cts.Token);
            }
   
            private static async void ProcessMessagesOnConnection(HybridConnectionStream relayConnection, CancellationTokenSource cts)
            {
                Console.WriteLine("New session");
   
                // The connection is a fully bidrectional stream. 
                // Put a stream reader and a stream writer over it.  
                // This allows you to read UTF-8 text that comes from 
                // the sender, and to write text replies back.
                var reader = new StreamReader(relayConnection);
                var writer = new StreamWriter(relayConnection) { AutoFlush = true };
                while (!cts.IsCancellationRequested)
                {
                    try
                    {
                        // Read a line of input until a newline is encountered.
                        var line = await reader.ReadLineAsync();
   
                        if (string.IsNullOrEmpty(line))
                        {
                            // If there's no input data, signal that 
                            // you will no longer send data on this connection.
                            // Then, break out of the processing loop.
                            await relayConnection.ShutdownAsync(cts.Token);
                            break;
                        }
   
                        // Write the line on the console.
                        Console.WriteLine(line);
   
                        // Write the line back to the client, prepended with "Echo:"
                        await writer.WriteLineAsync($"Echo: {line}");
                    }
                    catch (IOException)
                    {
                        // Catch an I/O exception. This likely occurred when
                        // the client disconnected.
                        Console.WriteLine("Client closed connection");
                        break;
                    }
                }
   
                Console.WriteLine("End session");
   
                // Close the connection.
                await relayConnection.CloseAsync(cts.Token);
            }
        }
    }
    ```

