## <a name="create-an-iot-hub"></a>Creare un hub IoT
Creare un hub IoT per connettere l'app per dispositivo simulato. La procedura seguente illustra come completare questa attività usando il portale di Azure.

1. Accedere al [portale di Azure][lnk-portal].

1. Selezionare **Nuovo** > **Internet delle cose** > **Hub IoT**.
   
    ![Indice del portale di Azure][1]

1. Nel riquadro **Hub IoT** immettere le informazioni seguenti per l'hub IoT:

   * **Nome**: creare un nome per l'hub IoT. Se il nome immesso è valido, viene visualizzato un segno di spunta verde.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **Piano tariffario e livello di scalabilità**: per questa esercitazione selezionare il livello **F1 gratuito**. Per altre informazioni, vedere la pagina relativa a [piano tariffario e livello di scalabilità][lnk-pricing].

   * **Gruppo di risorse**: creare un gruppo di risorse per ospitare l'hub IoT o usarne uno esistente. Per altre informazioni, vedere l'articolo su come [usare i gruppi di risorse per gestire le risorse di Azure][lnk-resource-groups].

   * **Località**: selezionare la località più vicina.

   * **Aggiungi al dashboard**: selezionare questa opzione per semplificare l'accesso all'hub IoT dal dashboard.

    ![Finestra Hub IoT][2]

1. Fare clic su **Crea**. La creazione dell'hub IoT può richiedere alcuni minuti. È possibile monitorare lo stato di avanzamento nel riquadro **Notifiche**.

1. Quando il nuovo hub IoT è pronto, fare clic sul suo riquadro nel portale di Azure per aprire la finestra delle proprietà. Ora che è stato creato un hub IoT, individuare le informazioni importanti che consentono di connettere dispositivi e applicazioni all'hub IoT. Annotare il **Nome host**, quindi fare clic su **Criteri di accesso condiviso**.
   
    ![Finestra del nuovo hub IoT][4]

1. In **Criteri di accesso condivisi** fare clic sul criterio **iothubowner**, quindi annotare la stringa di connessione dell'hub IoT nella finestra **iothubowner**. Per altre informazioni, vedere [Controllo di accesso][lnk-access-control] nella "Guida per gli sviluppatori dell'hub IoT".
   
    ![Criteri di accesso condivisi][5]

<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md
