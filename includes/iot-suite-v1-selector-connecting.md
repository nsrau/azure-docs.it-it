> [!div class="op_single_selector"]
> * [C su Windows](../articles/iot-suite/iot-suite-v1-connecting-devices.md)
> * [C su Linux](../articles/iot-suite/iot-suite-v1-connecting-devices-linux.md)
> * [Node.JS](../articles/iot-suite/iot-suite-v1-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>Panoramica dello scenario
In questo scenario, viene creato un dispositivo che invia la seguente telemetria alla [soluzione preconfigurata][lnk-what-are-preconfig-solutions] per il monitoraggio remoto:

* Temperatura esterna
* Temperatura interna
* Umidità

Per semplicità, il codice nel dispositivo genera valori di esempio, ma si consiglia di estendere l'esempio connettendo i sensori reali al dispositivo e inviando i dati di telemetria reali.

Inoltre, il dispositivo è in grado di rispondere ai metodi richiamati dal dashboard di soluzione e ai valori di proprietà desiderati impostati nel dashboard di soluzione.

Per completare l'esercitazione, è necessario un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

## <a name="before-you-start"></a>Prima di iniziare
Prima di scrivere un codice per il dispositivo, occorre eseguire la soluzione preconfigurata di monitoraggio remoto e poi effettuare il provisioning di un nuovo dispositivo personalizzato all'interno della soluzione in questione.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Eseguire il provisioning della soluzione preconfigurata per il monitoraggio remoto
Il dispositivo creato in questa esercitazione invia dati a un'istanza della soluzione preconfigurata per il [monitoraggio remoto][lnk-remote-monitoring]. Se nel proprio account Azure non è già stato eseguito il provisioning della soluzione preconfigurata per il monitoraggio remoto, attenersi alla procedura seguente:

1. Nella pagina <https://www.azureiotsuite.com/> fare clic su **+** per creare una soluzione.
2. Fare clic su **Seleziona** nel pannello **Remote monitoring** (Monitoraggio remoto) per creare la soluzione.
3. Nella pagina per la **creazione della soluzione di monitoraggio remoto** immettere il nome desiderato in **Nome soluzione** e selezionare l'**area** in cui eseguire la distribuzione e quindi la sottoscrizione di Azure da usare. Fare clic su **Crea soluzione**.
4. Attendere finché non viene completato il processo di provisioning.

> [!WARNING]
> Le soluzioni preconfigurate usano servizi di Azure fatturabili. Al termine, assicurarsi di rimuovere la soluzione preconfigurata dalla sottoscrizione per evitare eventuali addebiti non necessari. Per rimuovere completamente una soluzione preconfigurata dalla sottoscrizione, visitare la pagina <https://www.azureiotsuite.com/>.
> 
> 

Al termine del processo di provisioning della soluzione di monitoraggio remoto, fare clic su **Avvia** per aprire il dashboard della soluzione nel browser.

![Dashboard della soluzione][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Effettuare il provisioning del dispositivo nella soluzione di monitoraggio remoto
> [!NOTE]
> Se è già stato eseguito il provisioning di un dispositivo nella soluzione, è possibile saltare questo passaggio. È necessario conoscere le credenziali del dispositivo quando si crea l'applicazione client.
> 
> 

Per connettere un dispositivo alla soluzione preconfigurata, è necessario che identifichi se stesso nell'hub IoT mediante delle credenziali valide. È possibile recuperare le credenziali del dispositivo dal dashboard della soluzione. Le istruzioni per includere le credenziali del dispositivo nell'applicazione client sono illustrate più avanti in questa esercitazione.

Per aggiungere un dispositivo alla soluzione per il monitoraggio remoto, completare i passaggi seguenti nel dashboard della soluzione:

1. Nell'angolo inferiore sinistro del dashboard fare clic su **Aggiungi un dispositivo**.
   
   ![Aggiungere un dispositivo][1]
2. Nel pannello **Dispositivo personalizzato** fare clic su **Aggiungi nuovo**.
   
   ![Aggiungere un dispositivo personalizzato][2]
3. Scegliere **Let me define my own Device ID** (Desidero definire il mio ID dispositivo). Immettere un ID dispositivo, ad esempio **mydevice**, fare clic su **Controlla ID** per verificare che tale nome non sia già in uso e quindi fare clic su **Crea** per effettuare il provisioning del dispositivo.
   
   ![Aggiungere un ID dispositivo][3]
4. Annotare le credenziali del dispositivo (ID dispositivo, nome host dell'hub IoT e chiave dispositivo). Questi valori sono necessari per consentire all'applicazione client di connettersi alla soluzione per il monitoraggio remoto. Fare quindi clic su **Done**.
   
    ![Vedere le credenziali del dispositivo][4]
5. Selezionare il dispositivo nell'elenco dei dispositivi nel dashboard della soluzione. Nel pannello **Dettagli dispositivo** fare clic su **Attiva dispositivo**. Lo stato del dispositivo è ora **In esecuzione**. La soluzione per il monitoraggio remoto può ora ricevere i dati di telemetria dal dispositivo e richiamare i metodi sul dispositivo.

[img-dashboard]: ./media/iot-suite-v1-selector-connecting/dashboard.png
[1]: ./media/iot-suite-v1-selector-connecting/suite0.png
[2]: ./media/iot-suite-v1-selector-connecting/suite1.png
[3]: ./media/iot-suite-v1-selector-connecting/suite2.png
[4]: ./media/iot-suite-v1-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/