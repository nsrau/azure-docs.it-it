## Visualizzare la telemetria del dispositivo nel dashboard

Il dashboard nella soluzione di monitoraggio remoto consente di visualizzare la telemetria che i dispositivi inviano all’hub IoT.

1. Nel browser, tornare al dashboard della soluzione di monitoraggio remoto, fare clic su **Dispositivi** nel pannello di sinistra per passare all’**Elenco di dispositivi**.

2. Nell’**Elenco di dispositivi**, si noterà che lo stato del dispositivo è ora **in esecuzione**.

    ![][18]

3. Nel dashboard, selezionare il dispositivo nell’elenco a discesa **Dispositivo da visualizzare** per visualizzare i relativi dati di telemetria. La telemetria dall'applicazione di esempio è di 50 unità per la temperatura interna, 55 unità per la temperatura esterna e 50 unità per l’umidità. Si noti che per impostazione predefinita il dashboard mostra solo i valori di temperatura e umidità.

    ![][img-telemetry]

## Inviare un comando al dispositivo

Il dashboard nella soluzione di monitoraggio remoto consente di richiedere l’hub IoT per inviare comandi ai dispositivi. Ad esempio, nella soluzione di monitoraggio remota, è possibile inviare un comando per impostare la temperatura interna di un dispositivo.

1. Nel dashboard della soluzione di monitoraggio remoto, fare clic su **Dispositivi** nel pannello di sinistra per passare all’**Elenco di dispositivi**.

2. Fare clic su **ID dispositivo** per il dispositivo nell’**Elenco di dispositivi**.

3. Nel pannello **Dettagli del dispositivo**, fare clic su **Comandi**.

    ![][13]

4. Nell’elenco a discesa **Comando**, selezionare **SetTemperature**, e poi in **Temperatura** inserire un nuovo valore di temperatura. Fare clic su **Inviare comando** per inviare il comando al dispositivo.

    ![][14]

    > [AZURE.NOTE]La cronologia dei comandi mostra inizialmente lo stato del comando come **In sospeso**. Quando il dispositivo riconosce il comando, lo stato passa a **Esito positivo**.

5. Nel dashboard, verificare che il dispositivo stia ora inviando 75 come nuovo valore di temperatura.

## Passaggi successivi

L'articolo [Personalizzazione delle soluzioni preconfigurate][lnk-customize] descrive alcuni modi per estendere questo esempio. Le estensioni possibili comprendono l'utilizzo di sensori reali e implementazione di comandi aggiuntivi.

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-dev-messaging]: ../articles/iot-hub/iot-hub-devguide.md#messaging

<!---HONumber=AcomDC_1203_2015-->