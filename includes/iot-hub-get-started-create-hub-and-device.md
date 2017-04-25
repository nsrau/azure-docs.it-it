## <a name="create-an-iot-hub"></a>Creare un hub IoT

1. Nel [portale di Azure](https://portal.azure.com/) fare clic su **Nuovo** > **Internet delle cose** > **Hub IoT**.

   ![Creare un hub IoT nel portale di Azure](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
1. Nel riquadro **Hub IoT** immettere le informazioni seguenti per l'hub IoT:

   **Nome**: il nome dell'hub IoT. Se il nome immesso è valido, viene visualizzato un segno di spunta verde.

   **Piano tariffario e livello di scalabilità**: selezionare il livello F1 gratuito. Questa opzione è sufficiente per questa demo. Vedere [il piano tariffario e il livello di scalabilità](https://azure.microsoft.com/pricing/details/iot-hub/).

   **Gruppo di risorse**: creare un gruppo di risorse per ospitare l'hub IoT o usarne uno esistente. Vedere [Uso di Gruppi di risorse per gestire le risorse di Azure](../articles/azure-resource-manager/resource-group-portal.md).

   **Percorso**: selezionare la posizione più vicina all'utente in cui viene creato l'hub IoT.

   **Aggiungere al dashboard**: selezionare questa opzione per semplificare l'accesso all'hub IoT dal dashboard.

   ![Compilare i campi per la creazione dell'hub IoT di Azure](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

1. Fare clic su **Crea**. La creazione dell'hub IoT può richiedere alcuni minuti. È possibile visualizzare lo stato di avanzamento nel riquadro **Notifiche**.

   ![Vedere le notifiche relative allo stato della creazione dell'hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

1. Dopo aver creato l'hub IoT, selezionarlo dal dashboard. Annotare il **Nome host**, quindi fare clic su **Criteri di accesso condiviso**.

   ![Ottenere il nome host dell'hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. Nel riquadro **Criteri di accesso condivisi** fare clic sul criterio **iothubowner**, quindi copiare e annotare la **Stringa di connessione** dell'hub IoT. Per altre informazioni, vedere [Controllare l'accesso all'hub IoT](../articles/iot-hub/iot-hub-devguide-security.md).

   ![Ottenere la stringa di connessione dell'hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-the-your-device"></a>Registrare un dispositivo nell'hub IoT per il dispositivo

1. Nel [portale di Azure](https://portal.azure.com/), aprire l'hub IoT.
1. Fare clic su **Esplora dispositivi**.
1. Nel riquadro Esplora dispositivi fare clic su **Aggiungi** per aggiungere un dispositivo all'hub IoT.

   **ID dispositivo**: ID del nuovo dispositivo.

   **Tipo di autenticazione**: selezionare **Chiave simmetrica**.

   **Genera chiavi automaticamente**: selezionare questo campo.

   **Connetti dispositivo all'hub IoT**: fare clic su **Abilita**.

   ![Aggiungere un dispositivo a Esplora dispositivi nell'hub IoT](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

1. Fare clic su **Salva**.
1. Dopo la creazione del dispositivo, aprire il dispositivo nel riquadro **Esplora dispositivi**.
1. Annotare la chiave primaria della stringa di connessione.

   ![Ottenere la stringa di connessione del dispositivo](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)