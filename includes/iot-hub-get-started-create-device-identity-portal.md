## <a name="create-a-device-identity"></a>Creare un'identità del dispositivo
In questa sezione si usa il [portale di Azure][lnk-azure-portal] per creare un'identità del dispositivo nel registro delle identità dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non abbia una voce nel registro di identità. Per altre informazioni, vedere la sezione "Registro di identità" della [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity]. Nel portale **Device Explorer** permette di generare una chiave e un ID dispositivo univoci con cui il dispositivo può identificarsi quando invia messaggi da dispositivo a cloud all'hub IoT. Gli ID dispositivo fanno distinzione tra maiuscole e minuscole.

1. Assicurarsi di aver eseguito l'accesso al [portale di Azure][lnk-azure-portal]. 

1. Nell'indice fare clic su **Tutte le risorse** e trovare la risorsa hub IoT. 
   
    ![Passare all'hub IoT][img-find-iothub]
1. Dopo aver aperto la risorsa hub IoT, fare clic sullo strumento **Device Explorer** e quindi su **Aggiungi** nella parte superiore. Specificare un nome per il nuovo dispositivo e fare clic su **Salva**. 

    ![Creare l'identità del dispositivo nel portale][img-create-device]

   In questo modo viene creata una nuova identità del dispositivo per l'hub IoT.
1. Nell'elenco dei dispositivi in **Device Explorer** fare clic sul dispositivo appena creato e prendere nota della **Stringa di connessione - chiave primaria**. 

    ![Stringa di connessione del dispositivo][img-connection-string]
> [!NOTE]
> Il registro di identità dell'hub IoT archivia solo le identità del dispositivo per abilitare l'accesso sicuro all'hub. Archivia gli ID dispositivo e le chiavi da usare come credenziali di sicurezza e un flag di attivazione/disattivazione che consente di disabilitare l'accesso per un singolo dispositivo. Se l'applicazione deve archiviare altri metadati specifici del dispositivo, dovrà usare un archivio specifico dell'applicazione. Per altre informazioni, vedere la [Guida per gli sviluppatori dell'hub IoT][lnk-devguide-identity].
> 
> 

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

