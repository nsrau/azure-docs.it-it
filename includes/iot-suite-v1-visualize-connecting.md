## <a name="view-device-telemetry-in-the-dashboard"></a>Visualizzare la telemetria del dispositivo nel dashboard
Il dashboard nella soluzione di monitoraggio remoto consente di visualizzare la telemetria che i dispositivi inviano all'hub IoT.

1. Nel browser tornare al dashboard della soluzione di monitoraggio remoto e fare clic su **Dispositivi** nel pannello di sinistra per passare a **Elenco dispositivi**.
2. In **Devices list** (Elenco dispositivi) si noterà che lo stato del dispositivo è ora **In esecuzione**. Se non lo è, nel pannello **Dettagli dispositivo** fare clic su **Attiva dispositivo**.
   
    ![Visualizzare lo stato dei dispositivi][18]
3. Fare clic su **Dashboard** per tornare al dashboard e selezionare il dispositivo nell'elenco a discesa **Dispositivo da visualizzare** per visualizzarne i dati di telemetria. La telemetria dall'applicazione di esempio è di 50 unità per la temperatura interna, 55 unità per la temperatura esterna e 50 unità per l’umidità.
   
    ![Visualizzare la telemetria dei dispositivi][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>Richiamare un metodo sul dispositivo
Il dashboard nella soluzione di monitoraggio remoto consente di richiamare metodi sui dispositivi tramite l'hub IoT. Nella soluzione di monitoraggio remota, ad esempio, è possibile richiamare un metodo per simulare il riavvio di un dispositivo.

1. Nel dashboard della soluzione di monitoraggio remoto fare clic su **Dispositivi** nel pannello di sinistra per passare a **Elenco dispositivi**.
2. In **Elenco dispositivi** fare clic sull'**ID** del proprio dispositivo.
3. Nel pannello **Dettagli dispositivo** fare clic su **Metodi**.
   
    ![Metodi per il dispositivo][13]
4. Nel menu a discesa **Metodi**, selezionare **InitiateFirmwareUpdate**, quindi in **FWPACKAGEURI** immettere un URL fittizio. Fare clic su **Richiama metodo** per chiamare il metodo sul dispositivo.
   
    ![Richiamare un metodo per il dispositivo][14]
   

5. Viene visualizzato un messaggio nella console che esegue il codice del dispositivo quando il dispositivo gestisce il metodo. I risultati del metodo vengono aggiunti alla cronologia nel portale delle soluzioni:

    ![Visualizzare la cronologia del metodo][img-method-history]

## <a name="next-steps"></a>Passaggi successivi
L'articolo [Personalizzazione delle soluzioni preconfigurate][lnk-customize] descrive alcuni modi per estendere questo esempio. Le estensioni possibili comprendono l'utilizzo di sensori reali e implementazione di comandi aggiuntivi.

Per altre informazioni sulle [autorizzazioni visitare il sito azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-v1-visualize-connecting/suite4.png
[14]: ./media/iot-suite-v1-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-v1-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-v1-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-v1-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
