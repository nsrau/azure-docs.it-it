## <a name="view-device-telemetry"></a>Visualizzare la telemetria dei dispositivi

È possibile visualizzare i dati di telemetria inviati dal dispositivo nella pagina **Dispositivi** della soluzione.

1. Selezionare il dispositivo del quale è stato effettuato il provisioning nell'elenco dei dispositivi della pagina **Dispositivi**. Un pannello visualizza le informazioni sul dispositivo, incluso un tracciato dei dati di telemetria del dispositivo:

    ![Vedere i dettagli del dispositivo](media/iot-suite-visualize-connecting/devicesdetail.png)

1. Scegliere **Pressione** per modificare la visualizzazione dei dati di telemetria:

    ![Visualizzare i dati di telemetria per la pressione](media/iot-suite-visualize-connecting/devicespressure.png)

1. Per visualizzare le informazioni di diagnostica sul dispositivo, scorrere fino a **Diagnostica**:

    ![Visualizzazione della diagnostica del dispositivo](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>Agire sul dispositivo

Per richiamare i metodi nei dispositivi, usare la pagina **Dispositivi** della soluzione per il monitoraggio remoto. Nella soluzione per il monitoraggio remoto, i dispositivi **Chiller** implementano ad esempio un metodo **Reboot**.

1. Scegliere **Dispositivi** per passare alla pagina **Dispositivi** della soluzione.

1. Selezionare il dispositivo del quale è stato effettuato il provisioning nell'elenco dei dispositivi della pagina **Dispositivi**:

    ![Selezionare il dispositivo fisico](media/iot-suite-visualize-connecting/devicesselect.png)

1. Per visualizzare un elenco dei metodi che è possibile chiamare in un dispositivo, scegliere **Pianificazione**. Per pianificare un metodo per l'esecuzione in più dispositivi, è possibile selezionare più dispositivi nell'elenco. Il pannello **Pianificazione** mostra i tipi di metodi comuni a tutti i dispositivi selezionati.

1. Scegliere **Reboot**, impostare il nome processo su **RebootPhysicalChiller** e quindi fare clic su **Applica**:

    ![Pianificare il riavvio](media/iot-suite-visualize-connecting/deviceschedule.png)

1. Viene visualizzato un messaggio nella console che esegue il codice del dispositivo quando il dispositivo gestisce il metodo.

> [!NOTE]
> Per tenere traccia dello stato del processo nella soluzione, scegliere **Visualizza**.

## <a name="next-steps"></a>Passaggi successivi

L'articolo [Personalizzare la soluzione preconfigurata di monitoraggio remoto](../articles/iot-suite/iot-suite-remote-monitoring-customize.md) descrive alcuni modi per personalizzare la soluzione preconfigurata.