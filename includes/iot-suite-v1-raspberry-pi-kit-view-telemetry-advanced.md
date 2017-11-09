## <a name="view-the-telemetry"></a>Visualizzare i dati di telemetria

Raspberry Pi ora invia i dati di telemetria alla soluzione di monitoraggio remota. È possibile visualizzare i dati di telemetria nel dashboard della soluzione. È anche possibile inviare messaggi a Raspberry Pi dal dashboard della soluzione.

- Passare al dashboard della soluzione.
- Selezionare il dispositivo nell'elenco a discesa **Dispositivo da visualizzare**.
- I dati di telemetria da Raspberry Pi vengono visualizzati nel dashboard.

![Visualizzare i dati di telemetria da Raspberry Pi][img-telemetry-display]

## <a name="initiate-the-firmware-update"></a>Avviare l'aggiornamento del firmware

Il processo di aggiornamento del firmware scarica e installa una versione aggiornata dell'applicazione client del dispositivo in Raspberry Pi. Per altre informazioni sul processo di aggiornamento del firmware, vedere la descrizione del modello di aggiornamento del firmware in [Panoramica della gestione dei dispositivi con l'hub IoT][lnk-update-pattern].

Per avviare il processo di aggiornamento del firmware, richiamare un metodo nel dispositivo. Questo metodo è asincrono e viene restituito non appena inizia il processo di aggiornamento. Il dispositivo usa le proprietà segnalate per notificare la soluzione sullo stato dell'aggiornamento.

I metodi vengono richiamati in Raspberry Pi dal dashboard della soluzione. Quando Raspberry Pi si connette per la prima volta alla soluzione di monitoraggio remoto, invia le informazioni sui metodi supportati. 

[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced/telemetry.png
[lnk-update-pattern]: ../articles/iot-hub/iot-hub-device-management-overview.md
