> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-how-to-configure.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-twin-how-to-configure.md)
> 
> 

## <a name="introduction"></a>Introduzione
Nell'[introduzione ai dispositivi gemelli dell'hub IoT][lnk-twin-tutorial] è stato illustrato come impostare i metadati dei dispositivi dal back-end della soluzione usando *tag*, come segnalare le condizioni dei dispositivi da un'app per dispositivi con le *proprietà segnalate* e come eseguire query su tali informazioni con un linguaggio simile a SQL.

In questa esercitazione verrà illustrato come usare le *proprietà desiderate* del dispositivo gemello in combinazione con le *proprietà segnalate* per configurare in remoto le app per dispositivi. In particolare, questa esercitazione descrive come le proprietà segnalate e desiderate di un dispositivo gemello supportano la configurazione in più passaggi dell'impostazione di un'applicazione per dispositivi e come ottenere nel back-end della soluzione la visibilità dello stato di tale operazione in tutti i dispositivi.

A livello generale, per la gestione dei dispositivi questa esercitazione segue il *modello dello stato desiderato*, il cui principio di base prevede che il back-end della soluzione specifichi lo stato desiderato per i dispositivi gestiti anziché inviare comandi specifici. Il dispositivo ha quindi la responsabilità di stabilire il modo migliore per raggiungere lo stato desiderato, molto importante negli scenari IoT in cui condizioni specifiche del dispositivo influiscono sulla possibilità di eseguire immediatamente determinati comandi, segnalando continuamente al back-end lo stato corrente e le potenziali condizioni di errore. Il modello dello stato desiderato è determinante per gestire grandi set di dispositivi, perché offre al back-end la visibilità completa dello stato del processo di configurazione in tutti i dispositivi.
Per altre informazioni sul ruolo del modello dello stato desiderato nella gestione dei dispositivi, vedere [Panoramica della gestione dei dispositivi con l'hub IoT][lnk-dm-overview].

> [!NOTE]
> Negli scenari in cui i dispositivi sono controllati in modo più interattivo (come nel caso dell'attivazione di una ventola da un'app controllata dall'utente), può essere opportuno usare [metodi diretti][lnk-methods].
> 
> 

In questa esercitazione, il back-end dell'applicazione modifica la configurazione della telemetria di un dispositivo di destinazione e di conseguenza l'app per dispositivi segue un processo in più passaggi per applicare un aggiornamento della configurazione (che richiede ad esempio un riavvio del modulo software che nell'esercitazione viene simulato con un semplice ritardo).

Il back-end archivia la configurazione nelle proprietà desiderate del dispositivo gemello nel modo seguente:

        {
            ...
            "properties": {
                ...
                "desired": {
                    "telemetryConfig": {
                        "configId": "{id of the configuration}",
                        "sendFrequency": "{config}"
                    }
                }
                ...
            }
            ...
        }

> [!NOTE]
> Dato che le configurazioni possono essere oggetti complessi, per semplificarne il confronto vengono in genere assegnati ID univoci (hash o [GUID][lnk-guid]).
> 
> 

L'app per dispositivi segnala la propria configurazione corrente eseguendo il mirroring della proprietà desiderata **telemetryConfig** nelle proprietà segnalate:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Success",
                    }
                }
                ...
            }
        }

Si noti che la proprietà segnalata **telemetryConfig** ha una proprietà **status** aggiuntiva usata per segnalare lo stato del processo di aggiornamento della configurazione.

Quando viene ricevuta una nuova configurazione desiderata, l'app per dispositivi segnala una configurazione in sospeso modificando le informazioni:

        {
            "properties": {
                ...
                "reported": {
                    "telemetryConfig": {
                        "changeId": "{id of the current configuration}",
                        "sendFrequency": "{current configuration}",
                        "status": "Pending",
                        "pendingConfig": {
                            "changeId": "{id of the pending configuration}",
                            "sendFrequency": "{pending configuration}"
                        }
                    }
                }
                ...
            }
        }

In un secondo momento, l'app per dispositivi segnalerà l'esito positivo o negativo dell'operazione aggiornando la proprietà sopra indicata.
Si noti che il back-end può eseguire in qualsiasi momento query sullo stato del processo di configurazione in tutti i dispositivi.

Questa esercitazione illustra come:

* Creare un dispositivo simulato che riceve aggiornamenti della configurazione dal back-end e segnala più aggiornamenti come *proprietà segnalate* nel processo di aggiornamento della configurazione.
* Creare un'app back-end che aggiorna la configurazione desiderata di un dispositivo e quindi esegue query sul processo di aggiornamento della configurazione.

<!-- links -->

[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: ../articles/iot-hub/iot-hub-device-management-overview.md
[lnk-twin-tutorial]: ../articles/iot-hub/iot-hub-node-node-twin-getstarted.md
[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

<!--HONumber=Nov16_HO3-->


