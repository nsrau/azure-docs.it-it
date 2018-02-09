---
title: Registrare un dispositivo TPM nel servizio Azure Device Provisioning con C# | Microsoft Docs
description: Guida introduttiva di Azure - Registrare un dispositivo TPM nel servizio Device Provisioning in hub IoT di Azure con C# Service SDK
services: iot-dps
keywords: 
author: JimacoMS2
ms.author: v-jamebr
ms.date: 01/16/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 93ad83c6c89ce065cf905d28e20093bae4fdfa96
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Registrare un dispositivo TPM nel servizio Device Provisioning in hub IoT con C# Service SDK
> [!div class="op_single_selector"]
> * [Java](quick-enroll-device-tpm-java.md)
> * [C#](quick-enroll-device-tpm-csharp.md)
> * [Node.JS](quick-enroll-device-tpm-node.md)

Questi passaggi illustrano come creare una registrazione singola a livello di codice per un dispositivo TPM nel servizio Device Provisioning in hub IoT di Azure usando [C# Service SDK](https://github.com/Azure/azure-iot-sdk-csharp) e un'applicazione C# .NET Core di esempio. È anche possibile registrare un dispositivo TPM simulato nel servizio di provisioning usando questa voce di registrazione singola. Anche se questi passaggi funzionano su computer sia Windows che Linux, questo articolo usa un computer di sviluppo Windows.

## <a name="prepare-the-development-environment"></a>Preparare l'ambiente di sviluppo

1. Assicurarsi che [Visual Studio 2017](https://www.visualstudio.com/vs/) sia installato nel computer. 
2. Verificare che [.NET Core SDK](https://www.microsoft.com/net/download/windows) sia installato nel computer. 
3. Assicurarsi di completare la procedura descritta in [Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](./quick-setup-auto-provision.md) prima di continuare.
4. (Facoltativo) Se si vuole registrare un dispositivo simulato al termine di questa guida introduttiva, seguire la procedura descritta in [Creare ed effettuare il provisioning di un dispositivo TPM simulato tramite C# Device SDK](quick-create-simulated-device-tpm-csharp.md) fino al passaggio in cui si ottiene una chiave di verifica dell'autenticità per il dispositivo. Annotare la chiave di verifica dell'autenticità, l'ID di registrazione e, facoltativamente, l'ID dispositivo. Sarà necessario usare questi valori più avanti nella guida introduttiva. **Non seguire i passaggi per la creazione di una registrazione singola nel portale di Azure.**

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Ottenere la stringa di connessione del servizio di provisioning

Per l'esempio in questa guida introduttiva è necessaria la stringa di connessione del servizio di provisioning.
1. Accedere al portale di Azure, fare clic sul pulsante **Tutte le risorse** nel menu a sinistra e aprire il servizio Device Provisioning. 
2. Fare clic su **Criteri di accesso condivisi** e quindi sui criteri di accesso da usare per aprirne le proprietà. Nella finestra **Criteri di accesso** copiare e annotare la stringa di connessione della chiave primaria. 

    ![Ottenere la stringa di connessione del servizio di provisioning dal portale](media/quick-enroll-device-tpm-csharp/get-service-connection-string.png)

## <a name="create-the-individual-enrollment-sample"></a>Creare l'esempio di registrazione singola 

La procedura disponibile in questa sezione mostra come creare un'app console di .NET Core che aggiunge una singola registrazione per un dispositivo TPM al servizio di provisioning. Con alcune modifiche, è anche possibile seguire questa procedura per creare un'app console di [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) per aggiungere la singola registrazione. Per altre informazioni sullo sviluppo con IoT Core, vedere la [documentazione di Windows IoT Core per sviluppatori](https://docs.microsoft.com/en-us/windows/iot-core/).
1. In Visual Studio aggiungere un progetto di app console di Visual C# .NET Core a una nuova soluzione usando il modello di progetto **Console App (.NET Core)** (App console - .NET Core). Verificare che la versione di .NET Framework sia 4.5.1 o successiva. Denominare il progetto **CreateTpmEnrollment**.

    ![Nuovo progetto desktop di Windows classico in Visual C#](media//quick-enroll-device-tpm-csharp/create-app.png)

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **CreateTpmEnrollment**, quindi scegliere **Gestisci pacchetti NuGet**.
3. Nella finestra **Gestione pacchetti NuGet** selezionare **Esplora**, cercare **Microsoft.Azure.Devices.Provisioning.Service**, selezionare **Installa** per installare il pacchetto **Microsoft.Azure.Devices.Provisioning.Service** e accettare le condizioni per l'utilizzo. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet [Azure IoT Provisioning Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) e alle relative dipendenze.

    ![Finestra Gestione pacchetti NuGet](media//quick-enroll-device-tpm-csharp/add-nuget.png)

4. Aggiungere le istruzioni `using` seguenti dopo le altre istruzioni `using` nella parte superiore del file **Program.cs**:
   
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```
    
5. Aggiungere i campi seguenti alla classe **Program** .  
   - Sostituire il valore del segnaposto **ProvisioningConnectionString** con la stringa di connessione del servizio di provisioning per cui si vuole creare la registrazione.
   - È facoltativamente possibile cambiare l'ID di registrazione, la chiave di verifica dell'autenticità, l'ID dispositivo e lo stato del provisioning. 
   - Se si usa questa guida introduttiva insieme alla guida introduttiva [Creare ed effettuare il provisioning di un dispositivo TPM simulato tramite C# Device SDK](quick-create-simulated-device-tpm-csharp.md) per effettuare il provisioning di un dispositivo simulato, sostituire la chiave di verifica dell'autenticità e l'ID di registrazione con i valori annotati in tale guida introduttiva. È possibile sostituire l'ID dispositivo con il valore suggerito in tale guida introduttiva, usare il valore personalizzato o usare il valore predefinito in questo esempio.
        
   ```csharp
   private static string ProvisioningConnectionString = "{Your provisioning service connection string}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```
    
6. Aggiungere il metodo seguente alla classe **Program**.  Questo codice crea una singola voce di registrazione e quindi chiama il metodo **CreateOrUpdateIndividualEnrollmentAsync** su **ProvisioningServiceClient** per aggiungere la singola registrazione al servizio di provisioning.
   
   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```
       
7. Sostituire infine il corpo del metodo **Main** con le righe seguenti:
   
   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```
        
8. Compilare la soluzione.

## <a name="run-the-individual-enrollment-sample"></a>Eseguire l'esempio di registrazione singola
  
1. Eseguire l'esempio in Visual Studio per creare la registrazione singola per il dispositivo TPM.
 
2. Al termine della creazione, la finestra di comando visualizza le proprietà della nuova registrazione singola.

    ![Proprietà delle registrazioni nell'output del comando](media/quick-enroll-device-tpm-csharp/output.png)

3. Per assicurarsi che la registrazione singola sia stata creata, nel pannello di riepilogo del servizio Device Provisioning nel portale di Azure selezionare **Gestisci registrazioni**, quindi selezionare la scheda **Registrazioni singole**. Dovrebbe essere visualizzata una nuova voce di registrazione che corrisponde all'ID di registrazione usato nell'esempio. Fare clic sulla voce per verificare la chiave di verifica dell'autenticità e altre proprietà per la voce.

    ![Proprietà delle registrazioni nel portale](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal.png)
 
4. (Facoltativo) Se è stata seguita la procedura nella guida introduttiva [Creare ed effettuare il provisioning di un dispositivo TPM simulato tramite C# Device SDK](quick-create-simulated-device-tpm-csharp.md), è possibile completare i passaggi rimanenti nella guida introduttiva per registrare il dispositivo simulato. Assicurarsi di ignorare i passaggi per la creazione di una registrazione singola nel portale di Azure.

## <a name="clean-up-resources"></a>Pulire le risorse
Se si prevede di esplorare l'esempio di servizio C#, non eliminare le risorse create in questa guida introduttiva. Se non si prevede di continuare, usare i passaggi seguenti per eliminare tutte le risorse create da questa guida introduttiva.

1. Chiudere la finestra di output di esempio di C# sul computer.
2. Passare al servizio Device Provisioning nel portale di Azure, fare clic su **Gestisci registrazioni** e quindi selezionare la scheda **Registrazioni singole**. Selezionare l'*ID registrazione* della voce di registrazione creata usando questa guida introduttiva e fare clic sul pulsante **Elimina** nella parte superiore del pannello. 
3. Se è stata seguita la procedura nella guida introduttiva [Creare ed effettuare il provisioning di un dispositivo TPM simulato tramite C# Device SDK](quick-create-simulated-device-tpm-csharp.md) per creare un dispositivo TPM simulato: 

    1. Chiudere la finestra del simulatore TPM e la finestra dell'output dell'esempio per il dispositivo simulato.
    2. Nel portale di Azure passare all'hub IoT in cui è stato effettuato il provisioning del dispositivo. Nel menu a sinistra in **Explorers** (Strumenti di esplorazione) fare clic su **IoT Devices** (Dispositivi IoT), selezionare la casella di controllo accanto al dispositivo e quindi fare clic su **Elimina** nella parte superiore della finestra.
 
## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva è stata creata a livello di codice una voce di registrazione singola per un dispositivo TPM e, facoltativamente, è stato creato un dispositivo TPM simulato nel computer e ne è stato effettuato il provisioning nell'hub IoT usando il servizio Device Provisioning in hub IoT di Azure. Per informazioni approfondite sul provisioning del dispositivo, passare all'esercitazione sulla configurazione del servizio Device Provisioning nel portale di Azure. 
 
> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service tutorials (Esercitazioni sul servizio Device Provisioning in hub IoT di Azure)](./tutorial-set-up-cloud.md)

