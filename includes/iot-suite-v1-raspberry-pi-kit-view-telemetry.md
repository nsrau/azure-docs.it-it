## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

Raspberry Pi ora invia i dati di telemetria alla soluzione di monitoraggio remota. È possibile visualizzare i dati di telemetria nel dashboard della soluzione. È anche possibile inviare messaggi a Raspberry Pi dal dashboard della soluzione.

- Passare al dashboard della soluzione.
- Selezionare il dispositivo nell'elenco a discesa **Dispositivo da visualizzare**.
- I dati di telemetria da Raspberry Pi vengono visualizzati nel dashboard.

![Visualizzare i dati di telemetria da Raspberry Pi][img-telemetry-display]

## <a name="act-on-the-device"></a>Agire sul dispositivo

Dal dashboard della soluzione è possibile richiamare i metodi in Raspberry Pi. Quando Raspberry Pi si connette alla soluzione di monitoraggio remoto, invia le informazioni sui metodi supportati.

- Nel dashboard della soluzione fare clic su **Dispositivi** per passare alla pagina **Dispositivi**. Selezionare Raspberry Pi in **Elenco dispositivi**. Quindi scegliere **Metodi**:

    ![Elencare i dispositivi nel dashboard][img-list-devices]

- Nella pagina **Richiama metodo** scegliere **LightBlink** nell'elenco a discesa **Metodo**.

- Scegliere **InvokeMethod**. Il LED connesso a Raspberry Pi lampeggia più volte. L'app in Raspberry Pi invia un acknowledgment al dashboard della soluzione:

    ![Visualizzare la cronologia del metodo][img-method-history]

- È possibile accendere e spegnere il LED usando il metodo **ChangeLightStatus**, con **LightStatusValue** impostato su **1** l'accensione o su **0** per lo spegnimento.

> [!WARNING]
> Se si lascia la soluzione di monitoraggio remoto in esecuzione nell'account Azure, verrà addebitato il tempo dell'esecuzione. Per altre informazioni sulla riduzione del consumo durante l'esecuzione della soluzione di monitoraggio remoto, vedere [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Configurazione di soluzioni preconfigurate di Azure IoT Suite per scopi dimostrativi). Al termine, eliminare la soluzione preconfigurata dall'account Azure.


[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md