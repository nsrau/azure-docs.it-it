## <a name="view-device-telemetry-in-the-dashboard"></a>Visualizzare la telemetria del dispositivo nel dashboard
Il dashboard nella soluzione di monitoraggio remoto consente di visualizzare la telemetria che i dispositivi inviano all’hub IoT.

1. Nel browser tornare al dashboard della soluzione di monitoraggio remoto e fare clic su **Dispositivi** nel pannello di sinistra per passare a **Elenco dispositivi**.
2. In **Elenco dispositivi** si noterà che lo stato del dispositivo è ora **In esecuzione**.
   
    ![][18]
3. Fare clic su **Dashboard** per tornare al dashboard e selezionare il dispositivo nell'elenco a discesa **Dispositivo da visualizzare** per visualizzarne i dati di telemetria. La telemetria dall'applicazione di esempio è di 50 unità per la temperatura interna, 55 unità per la temperatura esterna e 50 unità per l’umidità. Si noti che per impostazione predefinita il dashboard mostra solo i valori di temperatura e umidità.
   
    ![][img-telemetry]

## <a name="send-a-command-to-your-device"></a>Inviare un comando al dispositivo
Il dashboard nella soluzione di monitoraggio remoto consente di inviare comandi ai dispositivi tramite l'hub IoT. Ad esempio, nella soluzione di monitoraggio remota, è possibile inviare un comando per impostare la temperatura interna di un dispositivo.

1. Nel dashboard della soluzione di monitoraggio remoto fare clic su **Dispositivi** nel pannello di sinistra per passare a **Elenco dispositivi**.
2. In **Elenco dispositivi** fare clic sull'**ID** del proprio dispositivo.
3. Nel pannello **Dettagli dispositivo** fare clic su **Comandi**.
   
    ![][13]
4. Nell'elenco a discesa **Comando** selezionare **SetTemperature** e quindi in **Temperatura** immettere un nuovo valore di temperatura. Fare clic su **Inviare comando** per inviare il comando al dispositivo.
   
    ![][14]
   
   > [!NOTE]
   > La cronologia dei comandi mostra inizialmente lo stato del comando come **In sospeso**. Quando il dispositivo riconosce il comando, lo stato passa a **Esito positivo**.
   > 
   > 
5. Nel dashboard, verificare che il dispositivo stia ora inviando 75 come nuovo valore di temperatura.

## <a name="next-steps"></a>Passaggi successivi
L'articolo sulla [personalizzazione delle soluzioni preconfigurate][lnk-customize] descrive alcuni modi per estendere questo esempio. Le estensioni possibili comprendono l'utilizzo di sensori reali e implementazione di comandi aggiuntivi.

Per altre informazioni, vedere [Autorizzazioni per il sito azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md


<!--HONumber=Nov16_HO3-->


