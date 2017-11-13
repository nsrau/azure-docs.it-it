## <a name="create-an-iot-hub"></a>Creare un hub IoT
Creare un hub IoT per connettere l'app per dispositivo simulato. La procedura seguente illustra come completare questa attività usando il portale di Azure.

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Ora che è stato creato un hub IoT, individuare le informazioni importanti che consentono di connettere dispositivi e applicazioni all'hub IoT. 

1. Dopo avere creato l'hub IoT, fare clic sul nuovo riquadro dell'hub IoT nel portale di Azure per aprire la finestra delle proprietà. Annotare il **Nome host**, quindi fare clic su **Criteri di accesso condiviso**.
   
    ![Finestra del nuovo hub IoT][4]
1. In **Criteri di accesso condivisi** fare clic sul criterio **iothubowner**, quindi copiare e annotare la stringa di connessione dell'hub IoT nella finestra **iothubowner**. Per altre informazioni, vedere [Controllo di accesso][lnk-access-control] nella "Guida per gli sviluppatori dell'hub IoT".
   
    ![Criteri di accesso condivisi][5]

<!-- Images. -->
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
