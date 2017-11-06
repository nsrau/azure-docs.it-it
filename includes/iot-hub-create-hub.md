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
<!-- Images -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md