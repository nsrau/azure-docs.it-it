## <a name="create-a-device-identity"></a>Creare un'identità del dispositivo
In questa sezione si scriverà un'app console .NET che crea un'identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non abbia una voce nel registro di identità. Per altre informazioni, vedere la sezione "Registro di identità" della [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity]. Quando si esegue questa app console vengono generati un ID dispositivo univoco e una chiave con cui il dispositivo può identificarsi quando invia messaggi da dispositivo a cloud all'hub IoT. Gli ID dispositivo fanno distinzione tra maiuscole e minuscole.

1. In Visual Studio aggiungere un progetto desktop classico di Windows Visual C# a una nuova soluzione usando il modello di progetto **App console (.NET Framework)**. Verificare che la versione di .NET Framework sia 4.5.1 o successiva. Denominare il progetto **CreateDeviceIdentity** e denominare la soluzione **IoTHubGetStarted**.
   
    ![Nuovo progetto desktop di Windows classico in Visual C#][10]
2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **CreateDeviceIdentity** e quindi scegliere **Gestisci pacchetti NuGet**.
3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **microsoft.azure.devices**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices** e accettare le condizioni per l'uso. Questa procedura scarica, installa e aggiunge un riferimento al [pacchetto NuGet Azure IoT - SDK per dispositivi][lnk-nuget-service-sdk] e alle relative dipendenze.
   
    ![Finestra Gestione pacchetti NuGet][11]
4. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;
5. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione dell'hub IoT creato nella sezione precedente.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. Aggiungere il metodo seguente alla classe **Program** :
   
        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }
   
    Questo metodo crea un'identità del dispositivo con ID **myFirstDevice**. Se questo ID dispositivo è già disponibile nel registro delle identità, il codice recupera semplicemente le informazioni sul dispositivo esistenti. L'app visualizzerà quindi la chiave primaria per l'identità. Questa chiave verrà usata dall'app per dispositivo simulato per connettersi all'hub IoT.
7. Aggiungere infine le righe seguenti al metodo **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();
8. Eseguire l'applicazione e prendere nota della chiave del dispositivo.
   
    ![Chiave del dispositivo generata dall'applicazione][12]

> [!NOTE]
> Il registro di identità dell'hub IoT archivia solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity].
> 
> 

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp3.png


<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
