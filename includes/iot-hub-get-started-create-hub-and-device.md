## <a name="create-an-iot-hub"></a>Creare un hub IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Ora che è stato creato un hub IoT, individuare le informazioni importanti che consentono di connettere dispositivi e applicazioni all'hub IoT. 

1. Dopo aver creato l'hub IoT, selezionarlo nel dashboard. Annotare il **Nome host**, quindi fare clic su **Criteri di accesso condiviso**.

   ![Ottenere il nome host dell'hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. Nel riquadro **Criteri di accesso condivisi** fare clic sul criterio **iothubowner**, quindi copiare e annotare la **Stringa di connessione** dell'hub IoT. Per altre informazioni, vedere [Controllare l'accesso all'hub IoT](../articles/iot-hub/iot-hub-devguide-security.md).

> [!NOTE] 
Per questa esercitazione di configurazione non è necessaria la stringa di connessione iothubowner. Potrebbe essere tuttavia necessaria per alcune delle esercitazioni in altri scenari IoT, dopo aver completato questa configurazione.

   ![Ottenere la stringa di connessione dell'hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registrare un dispositivo nell'hub IoT per il dispositivo

1. Nel [portale di Azure](https://portal.azure.com/), aprire l'hub IoT.

2. Fare clic su **Esplora dispositivi**.
3. Nel riquadro Esplora dispositivi fare clic su **Aggiungi** per aggiungere un dispositivo all'hub IoT. Eseguire quindi le operazioni seguenti:

   **ID dispositivo**: immettere l'ID del nuovo dispositivo. Gli ID dispositivo fanno distinzione tra maiuscole e minuscole.

   **Tipo di autenticazione**: selezionare **Chiave simmetrica**.

   **Genera chiavi automaticamente**: selezionare questa casella di controllo.

   **Connetti dispositivo all'hub IoT**: fare clic su **Abilita**.

   ![Aggiungere un dispositivo a Device Explorer nell'hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Fare clic su **Save**.
5. Dopo la creazione del dispositivo, aprire il dispositivo nel riquadro **Esplora dispositivi**.
6. Annotare la chiave primaria della stringa di connessione.

   ![Ottenere la stringa di connessione del dispositivo](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)
