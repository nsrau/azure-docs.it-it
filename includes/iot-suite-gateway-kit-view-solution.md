## <a name="view-the-solution-dashboard"></a>Visualizzare il dashboard della soluzione

Il dashboard della soluzione consente di gestire la soluzione distribuita. Ad esempio, è possibile visualizzare dati di telemetria, aggiungere dispositivi e richiamare metodi.

1. Al termine del provisioning, quando il riquadro della soluzione preconfigurata indica **Pronto**, scegliere **Avvia** per aprire il portale della soluzione di monitoraggio remoto in una nuova scheda.

    ![Avviare la soluzione preconfigurata][img-launch-solution]

1. Per impostazione predefinita, il portale della soluzione visualizza il *dashboard*. Passare ad altre aree del portale della soluzione usando il menu sul lato sinistro della pagina.

    ![Dashboard della soluzione preconfigurata per il monitoraggio remoto][img-menu]

## <a name="add-a-device"></a>Aggiungere un dispositivo

Per connettere un dispositivo alla soluzione preconfigurata, è necessario che identifichi se stesso nell'hub IoT mediante delle credenziali valide. È possibile recuperare le credenziali del dispositivo dal dashboard della soluzione. Le istruzioni per includere le credenziali del dispositivo nell'applicazione client sono illustrate più avanti in questa esercitazione.

Per aggiungere un dispositivo alla soluzione per il monitoraggio remoto, completare i passaggi seguenti nel dashboard della soluzione:

1. Nell'angolo inferiore sinistro del dashboard fare clic su **Aggiungi un dispositivo**.

   ![Aggiungere un dispositivo][1]

1. Nel pannello **Dispositivo personalizzato** fare clic su **Aggiungi nuovo**.

   ![Aggiungere un dispositivo personalizzato][2]

1. Scegliere **Let me define my own Device ID** (Desidero definire il mio ID dispositivo). Immettere un ID dispositivo, ad esempio **device01**, fare clic su **Verifica ID** per verificare che il nome non sia già stato usato nella soluzione e quindi fare clic su **Crea** per effettuare il provisioning del dispositivo.

   ![Aggiungere un ID dispositivo][3]

1. Annotare le credenziali del dispositivo (**ID dispositivo**, **Nome host hub IoT** e **Chiave dispositivo**). Questi valori sono necessari per consentire all'applicazione client in Raspberry Pi di connettersi alla soluzione per il monitoraggio remoto. Fare quindi clic su **Done**.

    ![Vedere le credenziali del dispositivo][4]

1. Selezionare il dispositivo nell'elenco dei dispositivi nel dashboard della soluzione. Nel pannello **Dettagli dispositivo** fare clic su **Attiva dispositivo**. Lo stato del dispositivo è ora **In esecuzione**. La soluzione per il monitoraggio remoto può ora ricevere i dati di telemetria dal dispositivo e richiamare i metodi sul dispositivo.

[img-launch-solution]: media/iot-suite-gateway-kit-view-solution/launch.png
[img-menu]: media/iot-suite-gateway-kit-view-solution/menu.png
[1]: media/iot-suite-gateway-kit-view-solution/suite0.png
[2]: media/iot-suite-gateway-kit-view-solution/suite1.png
[3]: media/iot-suite-gateway-kit-view-solution/suite2.png
[4]: media/iot-suite-gateway-kit-view-solution/suite3.png