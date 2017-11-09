---
title: Esercitazione per la sicurezza X.509 nell'hub IoT di Azure | Microsoft Docs
description: Introduzione alla sicurezza basata su X.509 nell'hub IoT di Azure in un ambiente simulato.
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: 99fb80d5cafc8fd20f5048de305fef99bf49c286
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2017
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Configurare la sicurezza X.509 nell'hub IoT di Azure

Questa esercitazione offre una simulazione delle operazioni necessarie per proteggere l'hub IoT di Azure usando l'*l'autenticazione del certificato X.509*. A scopo illustrativo, verrà descritta la procedura per usare lo strumento open source OpenSSL per creare i certificati in locale nel computer Windows. È consigliabile usare questa esercitazione solo a scopo di test. Per l'ambiente di produzione, è necessario acquistare i certificati da un'*autorità di certificazione (CA) radice*. 

## <a name="prerequisites"></a>Prerequisiti
Per questa esercitazione è necessario disporre delle risorse seguenti pronte:

- Un hub IoT creato con la propria sottoscrizione di Azure. Vedere [Creare un hub IoT usando il portale di Azure](iot-hub-create-through-portal.md) per istruzioni dettagliate. 
- [Visual Studio 2015 o Visual Studio 2017](https://www.visualstudio.com/vs/) installato nel computer in uso. 

<a id="getcerts"></a>

## <a name="get-x509-ca-certificates"></a>Ottenere certificati della CA X.509
La sicurezza basata su certificati X.509 nell'hub IoT richiede prima di tutto una [catena di certificati X.509](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), che include il certificato radice e i certificati intermedi fino al certificato foglia. 

È possibile scegliere uno dei modi seguenti per ottenere i certificati:
- Acquistare i certificati X.509 da un'*autorità di certificazione (CA) radice*. Questo è il modo consigliato per gli ambienti di produzione.
OPPURE
- Creare i propri certificati X.509 usando uno strumento di terze parti, ad esempio [OpenSSL](https://www.openssl.org/). Questo modo è appropriato per scopi di sviluppo e test. Le sezioni intitolate *Creare certificati X.509* e *Creare una catena di certificati X.509* nell'articolo [Script PowerShell per la gestione di certificati X.509 firmati da CA](iot-hub-security-x509-create-certificates.md) descrivono in dettaglio uno script di PowerShell di esempio per creare i certificati con OpenSSL e PowerShell. Se si preferisce usare la shell **Bash** anziché PowerShell, vedere le sezioni correlate di [Managing CA Certificates Sample](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (Gestione di un esempio di certificati della CA). Nel resto dell'esercitazione verrà usato l'ambiente OpenSSL configurato in questa *guida pratica* per esaminare in dettaglio tutti gli aspetti della sicurezza X.509 nell'hub IoT di Azure.


<a id="registercerts"></a>

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrare i certificati della CA X.509 nell'hub IoT

Questi passaggi illustrano come aggiungere una nuova autorità di certificazione all'hub IoT tramite il portale.

1. Nel portale di Azure passare all'hub IoT e aprire il menu **IMPOSTAZIONI** > **Certificati**. 
2. Fare clic su **Aggiungi** per aggiungere un nuovo certificato.
3. Immettere un nome visualizzato descrittivo per il certificato. Selezionare il file del certificato radice denominato *RootCA.cer* creato nella sezione precedente, dal computer. Fare clic su **Carica**.
4. Quando si riceve una notifica che il certificato è stato caricato correttamente, fare clic su **Salva**.

    ![Caricamento del certificato](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Il certificato verrà visualizzato nell'elenco **Esplora certificati**. Si noti che lo **STATO** di questo certificato è *Non verificato*.

5. Fare clic sul certificato aggiunto nel passaggio precedente.

6. Nel pannello **Dettagli certificato** fare clic su **Genera codice di verifica**.

7. Viene creato un **Codice di verifica** per convalidare la proprietà del certificato. Copiare il codice negli Appunti. 

   ![Verificare il certificato](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. A questo punto, è necessario firmare questo *codice di verifica* con la chiave privata associata al certificato della CA X.509, che genera una firma. Sono disponibili vari strumenti per eseguire questo processo di firma, ad esempio OpenSSL. Questa procedura è nota come [verifica del possesso](https://tools.ietf.org/html/rfc5280#section-3.1). Se sono stati usati gli script di esempio di PowerShell nella sezione precedente, eseguire lo script indicato nella sezione intitolata [Verifica del possesso del certificato della CA X.509](iot-hub-security-x509-create-certificates.md#signverificationcode).
 
9. Caricare la firma risultante dal passaggio 8 nell'hub IoT nel portale. Nel pannello **Dettagli certificato** nel portale di Azure passare a **File di certificato di verifica con estensione pem o cer** e selezionare la firma, ad esempio, il file *VerifyCert4.cer* creato dal comando di PowerShell di esempio usando l'icona _Esplora file_ accanto.

10. Al termine del caricamento del certificato, fare clic su **Verifica**. Lo **STATO** del certificato diventa **_Verificato_** nel pannello **Certificati**. Fare clic su **Aggiorna** se non viene aggiornato automaticamente.

   ![Caricare la verifica del certificato](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  


<a id="createdevice"></a>

## <a name="create-an-x509-device-for-your-iot-hub"></a>Creare un dispositivo X.509 per l'hub IoT

1. Nel portale di Azure passare a **Device Explorer** dell'hub IoT.

2. Fare clic su **Aggiungi** per aggiungere un nuovo dispositivo. 

3. Specificare un nome visualizzato descrittivo per **ID dispositivo** e selezionare **_Firmato da CA X.509_** come **Tipo di autenticazione**. Fare clic su **Salva**.

   ![Creare il dispositivo X.509 nel portale](./media/iot-hub-security-x509-get-started/create-x509-device.png)



<a id="authenticatedevice"></a>

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autenticare il dispositivo X.509 con i certificati X.509

Per autenticare il dispositivo X.509, è prima di tutto necessario firmare il dispositivo con il certificato della CA. La firma dei dispositivi foglia viene in genere eseguita nell'impianto di produzione, con strumenti di produzione abilitati di conseguenza. Man mano che il dispositivo passa da un produttore a un altro, ogni azione di firma del costruttore viene acquisita come certificato intermedio all'interno della catena. Il risultato finale è una catena di certificati dal certificato della CA al certificato foglia del dispositivo. Se sono stati usati gli script di PowerShell proposti nelle sezioni precedenti, per simulare il processo è possibile eseguire lo script indicato nella sezione *Creare il certificato X.509 foglia per il dispositivo* nell'articolo [Script PowerShell per la gestione di certificati X.509 con firma CA](iot-hub-security-x509-create-certificates.md).

In seguito verrà illustrato come creare un'applicazione C# per simulare il dispositivo X.509 registrato per l'hub IoT. Verranno inviati valori di temperatura e umidità dal dispositivo simulato all'hub. Si noti che in questa esercitazione verrà creata solo l'applicazione del dispositivo. Rimarrà come esercizio per i lettori la creazione dell'applicazione di servizio dell'hub IoT per inviare risposta agli eventi inviati da questo dispositivo simulato. L'applicazione C# presuppone che siano stati seguiti gli script PowerShell indicati nell'articolo [Script PowerShell per la gestione di certificati X.509 con firma CA](iot-hub-security-x509-create-certificates.md).

1. In Visual Studio creare un nuovo progetto desktop classico di Windows Visual C# usando il modello di progetto Applicazione console. Denominare il progetto **SimulateX509Device**.
   ![Creare il progetto di dispositivo X.509 in Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **SimulateX509Device** e quindi scegliere **Gestisci pacchetti NuGet**. Nella finestra Gestione pacchetti NuGet selezionare **Sfoglia** e cercare **microsoft.azure.devices.client**. Selezionare **Installa** per installare il pacchetto **microsoft.azure.devices.client** e accettare le condizioni d'uso. Questa procedura scarica, installa e aggiunge un riferimento al pacchetto NuGet Azure IoT SDK per dispositivi e alle relative dipendenze.
   ![Aggiungere il pacchetto NuGet SDK per dispositivi in Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Aggiungere le righe di codice seguenti all'inizio del file *Program.cs*:
    
    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Aggiungere le righe di codice seguenti all'interno della classe **Program**:
    
    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```
   Usare il nome del dispositivo descrittivo usato nella sezione precedente al posto del segnaposto _<your_device_id>_.

5. Aggiungere la funzione seguente per creare numeri casuali per temperatura e umidità e inviare tali valori all'hub:
    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

6. Infine, aggiungere le righe di codice seguenti alla funzione **Main**, sostituendo i segnaposto _device-id_, _your-iot-hub-name_ e _absolute-path-to-your-device-pfx-file_ in base ai requisiti della configurazione specifica.
    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "123");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
   Questo codice si connette all'hub IoT creando la stringa di connessione per il dispositivo X.509. Una volta stabilita la connessione, il codice invia quindi gli eventi di temperatura e umidità all'hub e attende la risposta. 
7. Dato che l'applicazione accede a un file *PFX*, è necessario eseguirla in modalità di *amministrazione*. Compilare la soluzione di Visual Studio. Aprire una nuova finestra di comando come **amministratore** e passare alla cartella che contiene questa soluzione. Passare al percorso *bin/Debug* all'interno della cartella della soluzione. Eseguire l'applicazione **SimulateX509Device.exe** dalla finestra di comando dell'_amministratore_. Il dispositivo dovrebbe connettersi correttamente all'hub e inviare eventi. 
   ![Eseguire l'app per dispositivi](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="see-also"></a>Vedere anche
Per altre informazioni sulla protezione della soluzione IoT, vedere:

* [Procedure consigliate per la sicurezza IoT][lnk-security-best-practices]
* [Architettura della sicurezza IoT][lnk-security-architecture]
* [Proteggere la distribuzione di IoT][lnk-security-deployment]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Simulazione di un dispositivo con Azure IoT Edge][lnk-iotedge]

[lnk-security-best-practices]: iot-hub-security-best-practices.md
[lnk-security-architecture]: iot-hub-security-architecture.md
[lnk-security-deployment]: iot-hub-security-deployment.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
