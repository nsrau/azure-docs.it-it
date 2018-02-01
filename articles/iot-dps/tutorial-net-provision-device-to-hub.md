---
title: Effettuare il provisioning di un dispositivo con il servizio Device Provisioning in hub IoT di Azure (.NET) | Microsoft Docs
description: Effettuare il provisioning del dispositivo in un singolo hub IoT con il servizio Device Provisioning in hub IoT di Azure (.NET)
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 6919f962d853faa572ee7ad5d0cb9aeacd3bd2b6
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Registrare il dispositivo in un hub IoT usando il client del servizio Device Provisioning in hub IoT di Azure (.NET)

Nell'esercitazione precedente abbiamo appreso come configurare un dispositivo per la connessione al servizio Device Provisioning. In questa esercitazione viene illustrato come usare questo servizio per eseguire il provisioning del dispositivo a un singolo hub IoT, usando la **_registrazione singola_** e i **_gruppi di registrazioni_**. Questa esercitazione illustra come:

> [!div class="checklist"]
> * Registrare il dispositivo
> * Avviare il dispositivo
> * Verificare che il dispositivo sia registrato

## <a name="prerequisites"></a>prerequisiti

Prima di procedere, assicurarsi di configurare il dispositivo e il relativo *modulo di protezione hardware* come descritto nell'esercitazione [Configurare un dispositivo per il provisioning usando il servizio Device Provisioning in hub IoT di Azure](./tutorial-set-up-device.md).

* Visual Studio 2015 o Visual Studio 2017

> [!NOTE]
> Visual Studio non è necessario. L'installazione di [.NET](https://www.microsoft.com/net) è sufficiente e gli sviluppatori possono usare il proprio editor preferito in Windows o Linux.  

Questa esercitazione simula il periodo durante o immediatamente dopo il processo di produzione hardware, quando le informazioni del dispositivo vengono aggiunte al servizio di provisioning. Questo codice viene in genere eseguito su un computer o su un dispositivo factory che possono eseguire codice .NET e non deve essere aggiunto ai dispositivi stessi.


## <a name="enroll-the-device"></a>Registrare il dispositivo

Questo passaggio prevede l'aggiunta di elementi di sicurezza esclusivi del dispositivo al servizio Device Provisioning. Di seguito sono indicati gli elementi di sicurezza.

- Per i dispositivi basati su TPM:
    - *Chiave di verifica dell'autenticità*, univoca per ogni chip TPM o simulazione. Per altre informazioni leggere [Informazioni sulla chiave di verifica dell'autenticità del TPM](https://technet.microsoft.com/library/cc770443.aspx).
    - *ID di registrazione*, usato per identificare in modo univoco un dispositivo nell'ambito o nello spazio dei nomi. Può coincidere o meno con l'ID dispositivo. L'ID è obbligatorio per ogni dispositivo. Per i dispositivi basati su TPM, l'ID di registrazione può essere derivato dal TPM stesso, ad esempio un hash SHA-256 della chiave di verifica dell'autenticità del TPM.

- Per i dispositivi basati su X.509:
    - [Certificato X.509 rilasciato al dispositivo](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx), sotto forma di file con estensione *pem* o *cer*. Per la registrazione individuale è necessario usare il *certificato foglia* per il sistema X.509, mentre per i gruppi di registrazione è necessario usare il *certificato radice* o un *certificato del firmatario* equivalente.
    - *ID di registrazione*, usato per identificare in modo univoco un dispositivo nell'ambito o nello spazio dei nomi. Può coincidere o meno con l'ID dispositivo. L'ID è obbligatorio per ogni dispositivo. Per i dispositivi basati su X.509, l'ID di registrazione viene derivato dal nome comune del certificato. Per altre informazioni su questi requisiti, vedere [Concetti relativi ai dispositivi](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-device).

È possibile registrare il dispositivo nel servizio Device Provisioning in due modi:

- **Individual Enrollments** (Registrazioni individuali) Rappresenta una voce per un singolo dispositivo che può eseguire la registrazione al servizio Device Provisioning. Le registrazioni individuali possono usare certificati X.509 o token di firma di accesso condiviso (in un TPM reale o virtuale) come meccanismo di attestazione. È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche oppure per i dispositivi che possono usare solo token di firma di accesso condiviso tramite TPM come meccanismo di attestazione. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

- **Enrollment Groups** (Gruppi di registrazione) Rappresenta un gruppo di dispositivi che condividono un meccanismo di attestazione specifico. È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant. I gruppi di registrazione sono basati solo su X.509 e condividono tutti un certificato di firma nella rispettiva catena di certificati X.509.

### <a name="enroll-the-device-using-individual-enrollments"></a>Registrare il dispositivo usando le registrazioni singole

1. In Visual Studio creare un progetto Applicazione console di Visual C# usando il modello di progetto **Applicazione console**. Specificare **DeviceProvisioning** come nome per il progetto.
    
1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **DeviceProvisioning** e quindi scegliere **Gestisci pacchetti NuGet**.

1. Nella finestra **Gestione pacchetti NuGet** selezionare **Sfoglia** e cercare **microsoft.azure.devices.provisioning.service**. Selezionare la voce e fare clic su **Installa** per installare il pacchetto **Microsoft.Azure.Devices.Provisioning.Service** e accettare le condizioni per l'utilizzo. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet [Azure IoT Device Provisioning Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) e alle relative dipendenze.

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con la stringa di connessione DPS annotata nella sezione precedente.
   
    ```csharp
    static readonly string ServiceConnectionString = "{DPS connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Aggiungere quanto segue per implementare la registrazione per il dispositivo:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Aggiungere infine il codice seguente al metodo **Main** per aprire la connessione all'hub IoT e iniziare la registrazione:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. In Esplora soluzioni in Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Imposta progetti di avvio...**. Selezionare **Progetto di avvio singolo**, quindi selezionare il progetto **DeviceProvisioning** nel menu a discesa.  

1. Eseguire l'app **DeviceProvisiong** del dispositivo .NET. Dovrebbe configurare il provisioning per il dispositivo: 

    ![Esecuzione della registrazione singola](./media/tutorial-net-provision-device-to-hub/individual.png)

Quando la registrazione del dispositivo è completata, verrà visualizzato nel portale come riportato di seguito:

   ![Registrazione riuscita nel portale](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Registrare il dispositivo tramite i gruppi di registrazioni

> [!NOTE]
> L'esempio di gruppo di registrazioni richiede un certificato X.509.

1. In Esplora soluzioni di Visual Studio aprire il progetto **DeviceProvisioning** creato in precedenza. 

1. Aggiungere le istruzione `using` seguenti all'inizio del file **Program.cs** :
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Aggiungere i campi seguenti alla classe **Program** . Sostituire il valore del segnaposto con il percorso del certificato X.509.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Aggiungere quanto segue al file **Program.cs** per implementare la registrazione per il gruppo:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Sostituire infine il codice seguente nel metodo **Main** per aprire la connessione all'hub IoT e iniziare la registrazione del gruppo:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Eseguire l'app **DeviceProvisiong** del dispositivo .NET. Dovrebbe essere configurato il provisioning del gruppo per il dispositivo: 

    ![Esecuzione della registrazione del gruppo](./media/tutorial-net-provision-device-to-hub/group.png)

    Quando la registrazione del gruppo di dispositivi è completata, il gruppo verrà visualizzato nel portale come riportato di seguito:

   ![Registrazione riuscita del gruppo nel portale](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Avviare il dispositivo

A questo punto la configurazione seguente è pronta per la registrazione del dispositivo:

1. Il dispositivo o il gruppo di dispositivi sono registrati per il servizio Device Provisioning e 
2. Il dispositivo è pronto con la sicurezza configurata ed è accessibile tramite l'applicazione usando Device Provisioning Service client SDK.

Avviare il dispositivo per consentire all'applicazione client di eseguire la registrazione al servizio Device Provisioning.  


## <a name="verify-the-device-is-registered"></a>Verificare che il dispositivo sia registrato

In seguito all'avvio del dispositivo dovrebbero verificarsi le azioni seguenti. Vedere l'applicazione di esempio del simulatore TPM [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) per altri dettagli. 

1. Il dispositivo invia una richiesta di registrazione al servizio Device Provisioning.
2. Per i dispositivi TPM il servizio Device Provisioning invia di nuovo una richiesta di registrazione a cui risponde il dispositivo. 
3. Al termine della registrazione il servizio Device Provisioning invia l'URI dell'hub IoT, l'ID dispositivo e la chiave crittografata di nuovo al dispositivo. 
4. L'applicazione client dell'hub IoT nel dispositivo si connette quindi all'hub. 
5. Successivamente alla connessione all'hub il dispositivo dovrebbero essere visualizzato in **Device Explorer** dell'hub IoT. 

    ![Successful connection to hub in the portal (Connessione all'hub nel portale riuscita)](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Registrare il dispositivo
> * Avviare il dispositivo
> * Verificare che il dispositivo sia registrato

Passare all'esercitazione successiva per informazioni su come eseguire il provisioning di più dispositivi tra hub con bilanciamento del carico. 

> [!div class="nextstepaction"]
> [Eseguire il provisioning dei dispositivi in più hub IoT con bilanciamento del carico](./tutorial-provision-multiple-hubs.md)
