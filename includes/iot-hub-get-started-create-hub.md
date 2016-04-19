## Creare un hub IoT

È necessario creare un hub IoT a cui si connette il dispositivo simulato. La procedura seguente illustra come completare questa attività con il portale di Azure.

1. Accedere al [portale di Azure][lnk-portal].

2. Nell'indice fare clic su **Nuovo**, quindi su **Internet of Things** e infine su **Hub IoT di Azure**.

    ![][1]

3. Nel pannello **Hub IoT** scegliere la configurazione per l'hub IoT.

    ![][2]

    * Nella casella **Nome** immettere un nome per l'hub IoT. Se il **Nome** è valido e disponibile, appare un segno di spunta verde nella casella **Nome**.
    * Selezionare un [piano tariffario e un livello di scalabilità][lnk-pricing]. Per questa esercitazione non è necessario un livello specifico. Per questa esercitazione è consigliabile usare il livello F1 gratuito.
    * **Gruppo di risorse**: creare un nuovo gruppo di risorse o selezionarne uno esistente. Per altre informazioni, vedere [Uso di Gruppi di risorse per gestire le risorse di Azure][lnk-resource-groups].
    * In **Percorso** selezionare il percorso per ospitare l'hub IoT. Per questa esercitazione è consigliabile scegliere il percorso più vicino.

4. Dopo aver scelto le opzioni di configurazione dell'hub IoT, fare clic su **Crea**. La creazione dell'hub IoT da parte di Azure può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nella Schermata iniziale o nel pannello Notifiche.

    ![][3]

5. Dopo aver creato l'hub IoT, fare clic sull'icona del nuovo riquadro per l'hub IoT nel portale per aprire il pannello del nuovo hub IoT. Prendere nota del **nome host** e quindi fare clic sull'icona delle **chiavi**.

    ![][4]

6. Nel pannello **Criteri di accesso condiviso** Fare clic sui criteri **iothubowner**, quindi copiare e prendere nota della stringa di connessione nel pannello **iothubowner**. Per altre informazioni, vedere la sezione [Controllo di accesso][lnk-access-control] dell'articolo "Guida per gli sviluppatori dell'hub IoT di Azure".

    ![][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide.md#accesscontrol

<!---HONumber=AcomDC_0413_2016-->