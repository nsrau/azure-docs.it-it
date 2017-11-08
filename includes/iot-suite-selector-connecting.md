> [!div class="op_single_selector"]
> * [C su Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [C su Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (generico)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Node.js su Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [C su Raspberry Pi](../articles/iot-suite/iot-suite-connecting-pi-c.md)

In questa esercitazione viene implementato un dispositivo **Chiller** che invia i dati di telemetria seguenti alla soluzione di [monitoraggio remoto](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md) preconfigurata:

* Temperatura
* Pressione
* Umidità

Per semplicità, il codice genera valori di telemetria di esempio per il **chiller**. È possibile estendere l'esempio connettendo sensori reali al dispositivo e inviando dati di telemetria reali.

Il dispositivo di esempio inoltre:

* Invia i metadati alla soluzione per descrivere le proprie funzionalità.
* Risponde alle azioni attivate dalla pagina **Dispositivi** della soluzione.
* Risponde alle modifiche di configurazione inviate dalla pagina **Dispositivi** della soluzione.

Per completare l'esercitazione, è necessario un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

## <a name="before-you-start"></a>Prima di iniziare

Prima di scrivere un codice per il dispositivo, occorre eseguire la soluzione preconfigurata di monitoraggio remoto e poi effettuare il provisioning di un nuovo dispositivo personalizzato all'interno della soluzione in questione.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Eseguire il provisioning della soluzione preconfigurata per il monitoraggio remoto

Il dispositivo **Chiller** creato in questa esercitazione invia dati a un'istanza della soluzione preconfigurata per il [monitoraggio remoto](../articles/iot-suite/iot-suite-remote-monitoring-explore.md). Se nel proprio account Azure non è già stato effettuato il provisioning della soluzione preconfigurata per il monitoraggio remoto, vedere [Distribuire la soluzione preconfigurata di monitoraggio remoto](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)

Al termine del processo di provisioning della soluzione di monitoraggio remoto, fare clic su **Avvia** per aprire il dashboard della soluzione nel browser.

![Dashboard della soluzione](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Effettuare il provisioning del dispositivo nella soluzione di monitoraggio remoto

> [!NOTE]
> Se è già stato eseguito il provisioning di un dispositivo nella soluzione, è possibile saltare questo passaggio. Sono necessarie le credenziali del dispositivo quando si crea l'applicazione client.

Per connettere un dispositivo alla soluzione preconfigurata, è necessario che identifichi se stesso nell'hub IoT mediante delle credenziali valide. È possibile recuperare le credenziali del dispositivo dalla pagina **Dispositivi** della soluzione. Le istruzioni per includere le credenziali del dispositivo nell'applicazione client sono illustrate più avanti in questa esercitazione.

Per aggiungere un dispositivo alla soluzione per il monitoraggio remoto, completare i passaggi seguenti nella pagina **Dispositivi** della soluzione:

1. Passare alla pagina **Provisioning** e scegliere **Fisico** per **Tipo di dispositivo**:

    ![Effettuare il provisioning di un dispositivo fisico](media/iot-suite-selector-connecting/devicesprovision.png)

1. Immettere **Fisico-chiller** come ID del dispositivo. Scegliere le opzioni **Chiave simmetrica** e **Genera chiavi automaticamente**:

    ![Scegliere le opzioni per il dispositivo](media/iot-suite-selector-connecting/devicesoptions.png)

Per individuare le credenziali che il dispositivo deve usare per la connessione alla soluzione preconfigurata, passare al portale di Azure nel browser. Accedere alla sottoscrizione.

1. Individuare il gruppo di risorse che contiene i servizi di Azure usati dalla soluzione per il monitoraggio remoto. Il nome del gruppo di risorse coincide con quello della soluzione per il monitoraggio remoto della quale si è effettuato il provisioning.

1. Passare all'hub IoT in questo gruppo di risorse. Scegliere quindi **Esplora dispositivi**:

    ![Esplora dispositivi](media/iot-suite-selector-connecting/deviceexplorer.png)

1. Scegliere l'**ID dispositivo**creato nella pagina **Dispositivi** della soluzione per il monitoraggio remoto.

1. Prendere nota dei valori di **ID dispositivo** e **Chiave dispositivo**. Questi valori vengono usati quando si aggiunge codice per connettere il dispositivo alla soluzione.

È stato ora effettuato il provisioning di un dispositivo fisico nella soluzione preconfigurata per il monitoraggio remoto. Nelle sezioni seguenti si implementerà l'applicazione client che usa le credenziali del dispositivo per connettersi alla soluzione.

L'applicazione client implementa il modello di dispositivo **Chiller** predefinito. Un modello di dispositivo per la soluzione preconfigurata specifica le informazioni seguenti per il dispositivo:

* Le proprietà che il dispositivo segnala alla soluzione. Ad esempio, un dispositivo **Chiller** segnala informazioni su firmware e posizione.
* Tipi di dati di telemetria che il dispositivo invia alla soluzione. Ad esempio, un dispositivo **Chiller** invia valori di temperatura, umidità e pressione.
* I metodi che possono essere pianificati nella soluzione per l'esecuzione nel dispositivo. Un dispositivo **Chiller** deve ad esempio implementare i metodi **Reboot**, **FirmwareUpdate**, **EmergencyValveRelease** e **IncreasePressure**.