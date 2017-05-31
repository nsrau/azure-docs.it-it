## <a name="customize-and-extend-the-device-management-actions"></a>Personalizzare ed estendere le operazioni di gestione dei dispositivi

Le soluzioni IoT possono espandere il set definito di modelli di gestione dei dispositivi o abilitare modelli personalizzati mediante l'uso di primitive di metodo da cloud a dispositivo e del dispositivo gemello. Altri esempi di operazioni di gestione dei dispositivi includono il ripristino delle informazioni predefinite, l'aggiornamento del firmware, l'aggiornamento del software, il risparmio energia, la gestione di rete e connettività e la crittografia dei dati.

## <a name="device-maintenance-windows"></a>Finestre di manutenzione del dispositivo

Configurare i dispositivi in modo che eseguano le azioni in un momento che riduce al minimo le interruzioni e i tempi di inattività. Le finestre di manutenzione del dispositivo costituiscono un modello comunemente usato per definire il momento in cui un dispositivo deve eseguire l'aggiornamento della configurazione. Le soluzioni di back-end possono usare le proprietà desiderate del dispositivo gemello per definire e attivare un criterio sul dispositivo che attiva una finestra di manutenzione. Quando un dispositivo riceve il criterio della finestra di manutenzione, può usare la proprietà segnalata del dispositivo gemello per segnalare lo stato del criterio. L'applicazione back-end può quindi usare le query del dispositivo gemello per attestare la conformità dei dispositivi e di tutti i criteri.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato usato un metodo diretto per attivare un riavvio remoto su un dispositivo. Sono state usate le proprietà segnalate per indicare l'ora dell'ultimo riavvio dal dispositivo ed è stata eseguita una query sul dispositivo gemello per ottenere l'ora dell'ultimo riavvio del dispositivo dal cloud.

Per altre informazioni sull'hub IoT e sui modelli di gestione dei dispositivi, ad esempio in modalità remota tramite l'aggiornamento del firmware air, vedere:

[Esercitazione: Come eseguire un aggiornamento del firmware][lnk-fwupdate]

Per informazioni su come estendere la soluzione IoT e pianificare le chiamate al metodo su più dispositivi, vedere l'esercitazione [Pianificare e trasmettere processi][lnk-tutorial-jobs].

Per altre informazioni sulle attività iniziali con l'hub IoT, vedere [Getting started with IoT Edge][lnk-iot-edge] (Introduzione a IoT Edge).

[lnk-fwupdate]: ../articles/iot-hub/iot-hub-node-node-firmware-update.md
[lnk-tutorial-jobs]: ../articles/iot-hub/iot-hub-node-node-schedule-jobs.md
[lnk-iot-edge]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md