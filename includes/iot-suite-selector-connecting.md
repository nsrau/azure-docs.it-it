> [AZURE.SELECTOR]
- [C on Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C on Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C on mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## Panoramica dello scenario

In questo scenario, si creerà un dispositivo che invia la seguente telemetria per il monitoraggio remoto [soluzione preconfigurata][lnk-what-are-preconfig-solutions]\:

- Temperatura esterna
- Temperatura interna
- Umidità

Per semplicità, il codice nel dispositivo genera valori di esempio, ma si consiglia di estendere l'esempio connettendo i sensori reali al dispositivo e inviando i dati di telemetria reali.

Per completare l'esercitazione, è necessario un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

## Prima di iniziare

Prima di scrivere un codice per il dispositivo, occorre eseguire il provisioning della soluzione preconfigurata di monitoraggio remoto e poi effettuare il provisioning di un dispositivo all'interno di quella soluzione.

### Eseguire il provisioning della soluzione preconfigurata per il monitoraggio remoto

Il dispositivo creato invierà dati a un'istanza della soluzione preconfigurata del [monitoraggio remoto][lnk-remote-monitoring]. Visitare [Introduzione ad Azure IoT Suite][lnk-getstarted] per effettuare il provisioning della soluzione preconfigurata di monitoraggio remoto nel proprio account Azure:

1. Nella pagina https://www.azureiotsuite.com/ fare clic su **+** per creare una nuova soluzione.

2. Fare clic su **Seleziona** nel pannello **Monitoraggio remoto** per creare la nuova soluzione.

3. Nella pagina di **creazione della soluzione di monitoraggio remoto**, immettere un **nome per la soluzione**, selezionare l'**area** in cui eseguire la distribuzione e scegliere la sottoscrizione di Azure che si desidera usare. Fare clic su **Crea soluzione**.

4. Attendere finché non viene completato il processo di provisioning.

> [AZURE.WARNING] Le soluzioni preconfigurate usano servizi di Azure fatturabili. Al termine, assicurarsi di rimuovere la soluzione preconfigurata dalla sottoscrizione per evitare eventuali addebiti non necessari. È possibile rimuovere completamente una soluzione proconfigurata dalla sottoscrizione visitando la pagina https://www.azureiotsuite.com/.

Dopo aver eseguito il provisioning della soluzione di monitoraggio remoto, fare clic su **Avviare** per aprire il dashboard della soluzione nel browser.

![][img-dashboard]

### Effettuare il provisioning del dispositivo nella soluzione di monitoraggio remoto

> [AZURE.NOTE] Se è già stato eseguito il provisioning di un dispositivo nella soluzione, è possibile saltare questo passaggio. È necessario conoscere le credenziali del dispositivo quando si crea l'applicazione client.

Per connettere un dispositivo alla soluzione preconfigurata, è necessario che esso sia in grado di identificare se stesso mediante delle credenziali valide. È possibile ottenere le credenziali del dispositivo dal dashboard della soluzione e poi includerle nell'applicazione client.

Per aggiungere un nuovo dispositivo alla soluzione di monitoraggio remoto, completare i passaggi seguenti nel dashboard della soluzione:

1.  Nell'angolo inferiore sinistro del dashboard fare clic su **Aggiungi un dispositivo**.

    ![][1]

2.  Nel pannello **Dispositivo personalizzato**, fare clic su **Aggiungi nuovo**.

    ![][2]

3.  Scegliere **Consentire la definizione della propria ID del dispositivo**, immettere un’ID del dispositivo, ad esempio **mydevice**, fare clic su **ID di controllo** per verificare che tale nome non sia in uso, quindi fare clic su **Crea** per eseguire il provisioning del dispositivo.

    ![][3]

5. Annotare le credenziali del dispositivo (ID del dispositivo, nome host di Hub IoT e chiave del dispositivo), serviranno all'applicazione client per connettere il dispositivo alla soluzione di monitoraggio remoto. Fare quindi clic su **Done**.

    ![][4]

6. Assicurarsi che il dispositivo venga visualizzato correttamente nella sezione dei dispositivi. Lo stato è **in sospeso** fino a quando il dispositivo non stabilirà una connessione a una soluzione di monitoraggio remoto.

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-getstarted]: https://www.azureiotsuite.com/
[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

<!---HONumber=AcomDC_0211_2016-->