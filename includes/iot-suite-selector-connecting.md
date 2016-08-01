> [AZURE.SELECTOR]
- [C su Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C su Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C su mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.JS](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## Panoramica dello scenario

In questo scenario, viene creato un dispositivo che invia la seguente telemetria per il monitoraggio remoto [soluzione preconfigurata][lnk-what-are-preconfig-solutions]\:

- Temperatura esterna
- Temperatura interna
- Umidità

Per semplicità, il codice nel dispositivo genera valori di esempio, ma si consiglia di estendere l'esempio connettendo i sensori reali al dispositivo e inviando i dati di telemetria reali.

Per completare l'esercitazione, è necessario un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

## Prima di iniziare

Prima di scrivere un codice per il dispositivo, occorre eseguire il provisioning della soluzione preconfigurata di monitoraggio remoto e poi effettuare il provisioning di un nuovo dispositivo personalizzato all'interno della soluzione in questione.

### Eseguire il provisioning della soluzione preconfigurata per il monitoraggio remoto

Il dispositivo creato in questa esercitazione invia dati a un'istanza della soluzione preconfigurata del [monitoraggio remoto][lnk-remote-monitoring]. Se nel proprio account Azure non è già stato eseguito il provisioning della soluzione preconfigurata per il monitoraggio remoto, attenersi alla procedura seguente:

1. Nella pagina <https://www.azureiotsuite.com/> fare clic su **+** per creare una nuova soluzione.

2. Fare clic su **Seleziona** nel pannello **Monitoraggio remoto** per creare la nuova soluzione.

3. Nella pagina di **creazione della soluzione di monitoraggio remoto**, immettere un **nome per la soluzione**, selezionare l'**area** in cui eseguire la distribuzione e scegliere la sottoscrizione di Azure che si desidera usare. Fare clic su **Crea soluzione**.

4. Attendere finché non viene completato il processo di provisioning.

> [AZURE.WARNING] Le soluzioni preconfigurate usano servizi di Azure fatturabili. Al termine, assicurarsi di rimuovere la soluzione preconfigurata dalla sottoscrizione per evitare eventuali addebiti non necessari. È possibile rimuovere completamente una soluzione proconfigurata dalla sottoscrizione visitando la pagina <https://www.azureiotsuite.com/>.

Al termine del processo di provisioning della soluzione di monitoraggio remoto, fare clic su **Avvia** per aprire il dashboard della soluzione nel browser.

![][img-dashboard]

### Effettuare il provisioning del dispositivo nella soluzione di monitoraggio remoto

> [AZURE.NOTE] Se è già stato eseguito il provisioning di un dispositivo nella soluzione, è possibile saltare questo passaggio. È necessario conoscere le credenziali del dispositivo quando si crea l'applicazione client.

Per connettere un dispositivo alla soluzione preconfigurata, è necessario che identifichi se stesso nell'hub IoT mediante delle credenziali valide. È possibile recuperare le credenziali del dispositivo dal dashboard della soluzione. Le istruzioni per includere le credenziali del dispositivo nell'applicazione client sono illustrate più avanti in questa esercitazione.

Per aggiungere un nuovo dispositivo alla soluzione di monitoraggio remoto, completare i passaggi seguenti nel dashboard della soluzione:

1.  Nell'angolo inferiore sinistro del dashboard fare clic su **Aggiungi un dispositivo**.

    ![][1]

2.  Nel pannello **Dispositivo personalizzato**, fare clic su **Aggiungi nuovo**.

    ![][2]

3.  Scegliere **Consentire la definizione del proprio ID del dispositivo**, immettere un ID del dispositivo, ad esempio **miodispositivo**, fare clic su **ID di controllo** per verificare che tale nome non sia già in uso, quindi fare clic su **Crea** per eseguire il provisioning del dispositivo.

    ![][3]

5. Annotare le credenziali del dispositivo (ID del dispositivo, nome host di Hub IoT e chiave del dispositivo), che serviranno all'applicazione client per eseguire la connessione alla soluzione di monitoraggio remoto. Fare quindi clic su **Done**.

    ![][4]

6. Assicurarsi che il dispositivo venga visualizzato nella sezione dei dispositivi. Lo stato del dispositivo è **In sospeso** fino a quando il dispositivo non stabilirà una connessione a una soluzione di monitoraggio remoto.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0720_2016-->