<properties
	pageTitle="Gestione dei dispositivi con Gateway SDK | Microsoft Azure"
	description="Procedura dettagliata con IoT Hub Gateway SDK che illustra come implementare la gestione dei dispositivi usando Gateway SDK"
	services="iot-hub"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>


# IoT Gateway SDK (beta): gestione dei dispositivi con Gateway SDK

Questa esercitazione illustra come usare le funzionalità di [gestione dei dispositivi][lnk-device-management] dell'hub IoT insieme ad [Azure IoT Hub Gateway SDK][lnk-gateway-sdk]. L'esercitazione usa un modulo di calcolo Intel Edison per ospitare un gateway con dispositivi simulati che invia dati di telemetria all'hub IoT. Si useranno le funzionalità di gestione dei dispositivi dell'hub IoT per eseguire un aggiornamento remoto del firmware nella scheda Edison.

Contenuto dell'esercitazione:

- **Architettura**: informazioni importanti sull'architettura relative all'esempio di gestione dei dispositivi.

- **Compilazione ed esecuzione**: i passaggi richiesti per compilare ed eseguire l'esempio.

## Architettura

In questa esercitazione si effettua il provisioning di una scheda Intel Edison da usare come gateway dei dispositivi IoT connessi all'hub IoT. Viene configurata anche la scheda Edison per poterla gestire tramite l'hub IoT. Il diagramma seguente mostra gli elementi chiave della soluzione compilata in questa esercitazione:

![Architettura di gestione dei dispositivi e del gateway][img-architecture]

### Dispositivi

Questa soluzione include tre dispositivi IoT connessi all'hub IoT:

- La scheda Edison è un dispositivo denominato **GW-device**. Nell'esercitazione si usano le funzionalità di gestione dei dispositivi dell'hub IoT per aggiornare il firmware di questo dispositivo fisico.

- Due dispositivi simulati, ovvero **GW-ble1-demo** e **GW-ble2-demo**. Questi dispositivi simulano i dispositivi Bluetooth a basso consumo che si connettono all'hub IoT tramite il gateway e inviano all'hub dati di telemetria sulla temperatura.

### Software del gateway

Il software del gateway viene eseguito come servizio nella scheda Edison. Due dispositivi simulati generano dati di telemetria sulla temperatura. Il modulo di mapping associa questi dispositivi simulati ai dispositivi registrati con l'hub IoT e il modulo dell'hub IoT gestisce la comunicazione con l'endpoint dell'hub IoT. L'articolo[IoT Gateway SDK (beta): inviare messaggi da dispositivo a cloud con un dispositivo simulato usando Linux][lnk-gateway-scenario] descrive dettagliatamente questo scenario.

### Client di gestione dei dispositivi

Il [client di gestione dei dispositivi][lnk-device-management] viene eseguito come servizio nella scheda Edison. Ciò consente di eseguire processi remoti nella scheda Edison, ad esempio [aggiornamenti del firmware][lnk-dm-jobs], riavvii e aggiornamenti della configurazione, nonché esecuzione di query sulle proprietà del dispositivo. In questa esercitazione il client di gestione dei dispositivi riceve ed elabora una richiesta per eseguire un aggiornamento del firmware nella scheda Edison.

### Hub IoT

[Hub IoT di Azure][lnk-iot-hub] è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e il back-end di una soluzione. In questa esercitazione l'hub IoT:

- Consente ai tre dispositivi di connettersi al cloud.
- Riceve i dati di telemetria generati dai dispositivi simulati dal gateway.
- Consente di inviare una richiesta di aggiornamento del firmware alla scheda Edison.
- Monitora lo stato di avanzamento del processo di aggiornamento del firmware.

### Interfaccia utente di esempio di gestione dei dispositivi

L'[interfaccia utente di esempio di gestione dei dispositivi ][lnk-dm-sample-ui] è un'applicazione Web di esempio che consente di usare le funzionalità di gestione dei dispositivi dell'hub IoT in un'interfaccia utente Web interattiva. Ad esempio, è possibile usare l'interfaccia utente di esempio per eseguire query sui dispositivi connessi all'hub IoT e inviare processi di aggiornamento del firmware ai dispositivi. In questa esercitazione si userà l'interfaccia utente di esempio per inviare un processo di aggiornamento del firmware alla scheda Edison e monitorare l'avanzamento del processo fino al completamento.

## Compilare ed eseguire

Per eseguire questo esempio è necessario creare un'immagine personalizzata per la scheda Edison che includa il software del gateway dell'hub IoT e il client di gestione dei dispositivi.

Prima di iniziare, verificare che sia possibile connettere la scheda Edison alla rete wireless. Per configurare la scheda Edison, è necessario connetterla a un computer host. In seguito si userà il computer host per eseguire il flashing della scheda Edison con l'immagine personalizzata creata. Intel offre un set di guide introduttive che includono guide per i sistemi operativi seguenti:

- [Get Started with the Intel Edison Development Board on Windows 64-bit][lnk-setup-win64] \(Introduzione alla scheda di sviluppo Intel Edison in Windows a 64 bit).
- [Get Started with the Intel Edison Development Board on Windows 32-bit][lnk-setup-win32] \(Introduzione alla scheda di sviluppo Intel Edison in Windows a 32 bit).
- [Getting Started with the Intel® Edison Board on Linux][lnk-setup-linux] \(Introduzione alla scheda Intel® Edison in Linux).

Per configurare la scheda Edison e acquisire familiarità con questo dispositivo, è consigliabile eseguire tutti i passaggi descritti in questi articoli introduttivi tranne:

- L'esecuzione del flashing del firmware più recente, perché viene aggiornato come parte di questa esercitazione, quindi non è necessario completare il passaggio in questa fase.
- L'ultimo passaggio, relativo alla scelta dell'IDE, che non è necessario per l'esercitazione corrente.

Dopo avere configurato la scheda Edison e installato i driver necessari nel computer host, assicurarsi che sia possibile connettersi alla scheda Edison con un terminale seriale. La pagina [Setting up a serial terminal][lnk-serial-connection] \(Configurazione di un terminale seriale) nel sito Web Intel include collegamenti a istruzioni di configurazione per i sistemi operativi host, ad esempio Windows e Linux.

È necessario completare anche queste attività

- [Creare un hub IoT][lnk-create-hub] nella sottoscrizione di Azure. Per completare questa esercitazione, è necessario il nome dell'hub. Se non si ha ancora una sottoscrizione di Azure, è possibile creare un [account gratuito][lnk-free-trial].
- Aggiungere tre dispositivi, ovvero**GW-ble1-demo**, **GW-ble2-demo** e **GW dispositivo**, all'hub IoT e prendere nota dei relativi ID e chiavi di dispositivo. Per aggiungere i dispositivi all'hub IoT creato nel passaggio precedente e recuperarne le chiavi, è possibile usare lo strumento [Device Explorer o iothub-explorer][lnk-explorer-tools]. Si useranno due di questi dispositivi (**GW-ble1-demo** e **GW-ble2-demo**) come dispositivi BLE (Bluetooth Low Energy) simulati connessi al gateway e un dispositivo (**GW-device**) per identificare il dispositivo gateway Edison come client di gestione dei dispositivi gestibile dall'hub IoT.

### Preparare l'ambiente di compilazione e verificare che sia possibile creare un'immagine personalizzata

Per creare un'immagine personalizzata per la scheda Edison, è necessario un ambiente Linux. Questi passaggi presuppongono l'uso di Ubuntu 14.04 che, al momento della stesura di questo articolo, è la piattaforma consigliata. Sono necessari almeno 40 GB di spazio disponibile nella partizione principale.

> [AZURE.NOTE] L'esecuzione dello script che crea l'immagine personalizzata può richiedere fino a 6 ore in un computer con 4 core CPU. È possibile ridurre il tempo usando un computer più potente con più core CPU.

Per i passaggi in questa sezione, sono stati consultati gli articoli seguenti: [Intel Edison Board Support Package][lnk-inteledison-bsp] \(Pacchetto per il supporto della scheda Intel Edison), [Manually Building Yocto Images for the Intel Edison Board from Source][lnk-hackgnar] \(Creazione manuale delle immagini Yocto per la scheda Intel Edison dall'origine) e [Creating a Custom Linux Kernel for the Edison (release 2.1)][lnk-shawnhymel] \(Creazione di un kernel Linux personalizzato per Edison - versione 2.1).

1. Accedere al computer Ubuntu 14.04 ed eseguire il comando seguente nella cartella principale per scaricare il pacchetto di origine Edison:
    
    ```
    curl -O http://downloadmirror.intel.com/25028/eng/edison-src-ww25.5-15.tgz
    ```

2. Decomprimere il pacchetto di origine per creare una cartella **edison src** nella cartella principale con il comando seguente e passare alla nuova cartella **edison src**:
    
    ```
    tar xfvz edison-src-ww25.5-15.tgz
    cd edison-src/
    ```

3. Installare i pacchetti prerequisiti:
    
    ```
    sudo apt-get -y install build-essential git diffstat gawk chrpath texinfo libtool gcc-multilib libsdl1.2-dev u-boot-tools
    ```

4. Creare le due nuove directory nella cartella **edison src**:
    
    ```
    mkdir bitbake_download_dir
    mkdir bitbake_sstate_dir 
    ```

5. Eseguire lo script seguente per scaricare l'ambiente di compilazione. Se sono disponibili più di 4 core CPU, impostare gli argomenti dello script **--parallel\_make** e **--bb\_number\_thread** sul numero di core disponibili:
    
    ```
    ./meta-intel-edison/setup.sh --dl_dir=bitbake_download_dir  --sstate_dir=bitbake_sstate_dir 
    ```

6. Aggiornare il percorso del repository Git Paho. Modificare il file **~/edison-src/out/linux64/poky/meta-intel-iot-middleware/recipes-connectivity/paho-mqtt/paho-mqtt\_3.1.bb** e sostituire l'URL `git://git.eclipse.org/gitroot/paho/org.eclipse.paho.mqtt.c.git/` con `git://github.com/eclipse/paho.mqtt.c.git`.

7. Inizializzare l'ambiente di compilazione con i comandi seguenti:
    
    ```
    cd out/linux64/
    source poky/oe-init-build-env
    ```

8. Usare il comando seguente per creare l'immagine personalizzata. La prima esecuzione di questo comando può richiedere fino a 6 ore in un computer con 4 core CPU. Le ricompilazioni successive, dopo avere aggiunto le personalizzazioni, saranno molto più rapide:
    
    ```
    bitbake edison-image
    ```

9. Finalizzare la compilazione eseguendo questi comandi:
    
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

I file necessari per eseguire il flashing della scheda Edison si trovano ora nella cartella **~/edison-src/out/linux64/build/toFlash/**.

### Aggiungere Gateway SDK all'immagine personalizzata

I passaggi descritti in questa sezione illustrano come eseguire il processo per l'aggiunta di Gateway SDK all'immagine personalizzata, per poter usare la scheda Edison come gateway IoT all'avvio. Per questa esercitazione il gateway include un modulo che simula due dispositivi Bluetooth a basso consumo (BLE, Bluetooth Low Energy) che generano dati di telemetria sulla temperatura che il gateway inoltrerà all'hub IoT.

Completare i passaggi seguenti nello stesso computer Ubuntu 14.04 usato per creare un'immagine di Edison nella sezione precedente.

1. Clonare Gateway SDK nella cartella principale:
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-gateway-sdk.git --recursive
    ```

2. Configurare il gateway per la connessione all'hub IoT e simulare due dispositivi. Modificare il file **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_intel\_edison.json** e sostituire i segnaposto **IoTHubName**, **IoTHubSuffix**, **deviceID** e **deviceKey** con i valori annotati durante la configurazione dell'hub IoT. Usare i dispositivi **GW-ble1-demo** e **GW-ble2-demo** creati in precedenza.

3. Creare una cartella in cui inserire il nuovo file recipe:
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk
    ```

4. Copiare il file recipe denominato **azure-iot-field-gateway-sdk.bb** dalla cartella **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/** alla cartella **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk/** appena creata. Modificare il file sostituendo le due occorrenze di `<<userName>>` con il nome utente corrente.

5. Modificare il file **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** aggiungendo una voce per il nuovo file recipe. Aggiungere la riga seguente alla fine del file:
    
    ```
    IMAGE_INSTALL += "azure-iot-field-gateway-sdk"
    ```

6. A questo punto è possibile testare le modifiche eseguendo il comando **bitbake** per compilare solo il nuovo file recipe:
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake azure-iot-field-gateway-sdk
    ```

### Aggiungere il client di gestione dei dispositivi dell'hub IoT di Azure all'immagine personalizzata

I passaggi descritti in questa sezione illustrano come eseguire il processo per l'aggiunta del client di gestione dei dispositivi dell'hub IoT all'immagine personalizzata per poter gestire il dispositivo gateway Edison dall'hub IoT. Per questa esercitazione il client di gestione dei dispositivi include il codice di esempio per abilitare un aggiornamento del firmware nel dispositivo gateway Edison.

Completare i passaggi seguenti nello stesso computer Ubuntu 14.04 usato nella sezione precedente per aggiungere il gateway all'immagine di Edison.

1. Clonare il ramo **dmpreview** del repository di SDK dell'hub IoT nella cartella principale:
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-sdks -b dmpreview --recursive
    ```

2. Creare le cartelle seguenti in cui inserire il nuovo file recipe:
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files
    ```

3. Copiare il file **iotdm-edison-sample.bb** dalla cartella **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** alla cartella **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample**.

4. Modificare il file **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/iotdm-edison-sample.bb** e sostituire `-Duse_http:BOOL=OFF` con `-Duse_http:BOOL=ON`.

5. Copiare il file nella cartella **iotdm\_edison\_sample.service** from the **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** nella cartella **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files**.

6. Modificare il file **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** aggiungendo una voce per il nuovo file recipe. Aggiungere la riga seguente alla fine del file:
    
    ```
    IMAGE_INSTALL += "iotdm-edison-sample"
    ```

7. Poiché Gateway SDK e il client di gestione dei dispositivi condividono alcune librerie, è necessario modificare il file **~/edison-src/out/linux64/poky/meta/classes/sstate.bbclass**. Aggiungere le righe seguenti alla fine del file. Assicurarsi di sostituire `<your user>` con il nome utente corrente:
    
    ```
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/lib/libaziotsharedutil.a"
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/include/azureiot"
    ```

8. Configurare la rete Wi-Fi per l'avvio automatico nella scheda Edison modificando il file **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-connectivity/wpa\_supplicant/wpa-supplicant/wpa\_supplicant.conf-sane** e aggiungendo le righe seguenti alla fine del file. Assicurarsi di sostituire `<your wifi ssid>` e `<your wifi password>` con i valori corretti per la rete Wi-Fi:
    
    ```
    network={
      ssid="<your wifi ssid>"
      key_mgmt=WPA-PSK
      pairwise=CCMP TKIP
      group=CCMP TKIP WEP104 WEP40
      eap=TTLS PEAP TLS
      psk="<your wifi password>"
    }
    ```

9. È possibile creare l'immagine per la scheda Edison che contiene Gateway SDK e il client di gestione dei dispositivi. Il comando **bitbake** verrà eseguito molto più rapidamente rispetto all'esecuzione precedente, perché ora deve solo creare il nuovo file recipe e aggiungerlo all'immagine:
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake edison-image
    ```

10. Finalizzare la compilazione eseguendo questi comandi:
  
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

I file necessari per eseguire il flashing della scheda Edison si trovano ora nella cartella **~/edison-src/out/linux64/build/toFlash/**.

### Eseguire il flashing della scheda Intel Edison con l'immagine personalizzata

A questo punto è possibile eseguire il flashing della scheda Edison con l'immagine personalizzata che contiene sia il client di gestione dei dispositivi dell'hub IoT, sia il software del gateway.

Copiare i file dalla cartella **toFlash** del computer Ubuntu usato per creare l'immagine personalizzata nel computer che si connette alla scheda Edison con un cavo USB.

Se si usa un computer Windows per connettersi alla scheda Edison con un cavo USB, è necessario eseguire lo script **flashall.bat** per eseguire il flashing di Edison. Se si usa un computer Linux per connettersi alla scheda Edison con un cavo USB, è necessario eseguire lo script **flashall.bat** per eseguire il flashing di Edison.

Una volta completato il processo di flashing, connettere la scheda Edison usando una [connessione seriale][lnk-serial-connection] dal computer host e accedere come utente **ROOT**. È consigliabile verificare che ora la scheda Edison sia connessa alla rete Wi-Fi.

### Eseguire l'esempio

È necessario configurare il client di gestione dei dispositivi nella scheda Edison perché si connetta come dispositivo **GW-device** all'hub IoT. Usare un editor di testo, ad esempio **vi** o **nano**, per creare un file con estensione **cs** nella cartella /home/root nella scheda Edison. Questo file deve contenere solo la stringa di connessione di **GW-device**. Se non si è preso nota di questa stringa di connessione in precedenza, è possibile usare lo strumento [Device Explorer o iothub-explorer][lnk-explorer-tools] per recuperare la stringa di connessione del dispositivo dal Registro di sistema del dispositivo dell'hub IoT.

Dopo aver creato il file con estensione **cs**, riavviare la scheda Edison usando il comando seguente:

```
reboot -h now
```

Quando si riavvia la scheda Edison, verificare che il servizio del gateway e il servizio di gestione dei dispositivi vengano entrambi avviati con uno stato **OK**:

```
[  OK  ] Started Daemon to receive the wpa_supplicant event.
         Starting PulseAudio Sound System...
[  OK  ] Started WPA supplicant service.
[  OK  ] Started Azure IoT DM as a service..
[  OK  ] Started Azure Iot Gateway as a service..
         Stopping Daemon to receive the wpa_supplicant event...
[  OK  ] Stopped Daemon to receive the wpa_supplicant event.

```

Per risolvere gli eventuali problemi del servizio gateway IoT, esaminare le voci presenti nel file **/deviceCloudUploadGatewaylog.log** nella scheda Edison. È anche possibile usare il comando seguente per visualizzare l'output del servizio:

```
systemctl status simulated_device_cloud_upload.service
```

Per risolvere gli eventuali problemi del servizio di gestione di dispositivi IoT, usare il comando seguente per visualizzare l'output:

```
systemctl status iotdm_edison_sample.service
```

### Avviare il processo di aggiornamento del firmware

Un aggiornamento del firmware nella scheda Edison, richiesto dal servizio di gestione dei dispositivi IoT, di solito scarica un file ZIP che contiene il firmware da un URL. Per semplificare questa esercitazione, copiare manualmente il file ZIP nella scheda Edison e quindi usare un URL **file://** invece di un URL **http://** quando si richiede l'aggiornamento.

Sempre per semplificare l'esercitazione, l'aggiornamento del firmware riapplica la stessa immagine del firmware invece di usarne una nuova. Si potrà vedere che l'immagine viene applicata alla scheda Edison.

Creare un file ZIP denominato **edison.zip** contenente tutti i file e le sottocartelle dalla cartella **toFlash** nel computer Ubuntu usato per creare l'immagine personalizzata. Assicurarsi che i file della cartella **toFlash** si trovino nella radice del file ZIP. Per copiare il file **edison.zip** nella cartella/home/root della scheda Edison, usare uno strumento come SCP oppure PSCP se si usa Putty.

Per inviare il processo di aggiornamento del firmware e monitorarne lo stato di avanzamento, usare l'[interfaccia utente di esempio di gestione dei dispositivi][lnk-dm-sample-ui] con Node.js. Questo esempio può essere eseguito in Windows o in Linux e richiede [Node. js][lnk-nodejs] 6.1.0 o versione successiva. Per recuperare, compilare ed eseguire l'interfaccia utente di esempio di gestione dei dispositivi nel computer desktop, seguire questa procedura:

1. Aprire **Command Prompt**.

2. Verificare di avere installato Node.js 6.1.0 o versione successiva digitando `node --version`.

3. Clonare il repository di GitHub per l'interfaccia utente di Gestione dei dispositivi eseguendo il comando seguente:

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. Nella cartella radice della copia clonata del repository per l'interfaccia utente di Gestione dei dispositivi di Azure IoT eseguire il comando seguente per recuperare i pacchetti dipendenti:

    ```
    npm install
    ```

5. Al termine dell'esecuzione del comando npm install, eseguire il comando seguente per compilare il codice:

    ```
    npm run build
    ```

6. Usare un editor di testo per aprire il file user-config.json nella radice della cartella clonata. Sostituire il testo "&lt;YOUR CONNECTION STRING HERE&gt;" con la stringa di connessione dell'hub IoT. Si può trovare la stringa di connessione nel [portale][lnk-azure-portal] di Azure.

7. Nel prompt dei comandi eseguire il comando seguente per avviare l'app dell'esperienza utente di Gestione dei dispositivi:

    ```
    npm run start
    ```

8. Quando il prompt dei comandi indica che i servizi sono stati avviati, aprire un Web browser e passare all'app di gestione dei dispositivi disponibile tramite l'URL seguente per visualizzare i dispositivi: <http://127.0.0.1:3003>.

    ![Interfaccia utente di Gestione dei dispositivi][img-dm-ui]

9. Selezionare il dispositivo **GW-device** nell'elenco a discesa **Device Jobs** (Processi del dispositivo), selezionare **Firmware Update** (Aggiornamento del firmware) e quindi fare clic su **Start** (Avvia).

10. Nel campo **Package URI** (URI del pacchetto) immettere **file:///home/root/edison.zip** per usare il file di immagine copiato in precedenza nella scheda Edison. Fare clic su **Submit** (Invia), fare clic su **Yes** (Sì) e quindi fare clic sul collegamento**Job History** (Cronologia processi) per visualizzare i nuovi processi padre e figlio in esecuzione:

    ![Collegamento alla cronologia dei processi][img-history-link]

11. Dopo alcuni minuti, nel terminale seriale connesso alla scheda Edison verranno visualizzati il riavvio della scheda Edison e l'applicazione delle modifiche del firmware:

    ```
    reading ota_update.scr
    14747 bytes read in 17 ms (846.7 KiB/s)
    ## Executing script at 00100000
    === OTA update script ===
    Validating Ota package
    part find mmc 0 label:update u_part_num;
    ota drive is mmc 0:9

    Validating edison_ifwi-dbg-00-dfu.bin hash for boot0 and boot1 partitions

    fatload mmc 0:9 0x6400000 edison_ifwi-dbg-00-dfu.bin;
    reading edison_ifwi-dbg-00-dfu.bin
    ...
    ```

12. Al termine del riavvio della scheda Edison aggiornare la pagina nell'interfaccia utente di esempio di gestione dei dispositivi. Come si vedrà, lo stato del processo è **completed** (completato) per i due processi di aggiornamento del firmware:

    ![Stato del processo completato][img-job-status]

A questo punto è stata completata l'esercitazione che illustra come usare il client di gestione dei dispositivi e il software gateway dell'hub IoT in una scheda Intel Edison. Come parte di questa esercitazione sono state eseguite queste operazioni:

- È stata creata un'immagine di Intel Edison personalizzata che include il software del gateway e il client di gestione dei dispositivi dell'hub IoT configurati per l'avvio ogni volta che viene avviata la scheda Edison.
- Sono stati configurati il client di gestione dei dispositivi e la scheda Edison per la connessione all'hub IoT.
- È stato avviato un processo di gestione dei dispositivi dall'interfaccia utente di esempio che causa il riavvio della scheda Edison e l'applicazione di una nuova immagine del firmware.

## Passaggi successivi

Per altre informazioni sulla gestione dei dispositivi con l'hub IoT, vedere l'articolo [Panoramica di Gestione dei dispositivi dell'hub IoT di Azure][lnk-device-management].

Per ottenere informazioni più avanzate sul Gateway SDK e provare alcuni esempi di codice, vedere il [Gateway SDK IoT di Azure][lnk-gateway-sdk].

Per esplorare ulteriormente le funzionalità dell'hub IoT, vedere:

- [Progettare una soluzione][lnk-design]
- [Guida per sviluppatori][lnk-devguide]
- [Informazioni sulla gestione dei dispositivi tramite l'interfaccia utente di esempio][lnk-dmui]
- [Gestire hub IoT tramite il portale di Azure][lnk-portal]



<!-- Links and images -->

[img-dm-ui]: media/iot-hub-gateway-sdk-device-management/image1.png
[img-history-link]: media/iot-hub-gateway-sdk-device-management/image2.png
[img-job-status]: media/iot-hub-gateway-sdk-device-management/image3.png
[img-architecture]: media/iot-hub-gateway-sdk-device-management/architecture.png

[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-device-management]: iot-hub-device-management-overview.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-serial-connection]: https://software.intel.com/setting-up-serial-terminal-intel-edison-board
[lnk-inteledison-bsp]: http://www.intel.com/content/dam/support/us/en/documents/edison/sb/edisonbsp_ug_331188007.pdf
[lnk-hackgnar]: http://www.hackgnar.com/2016/01/manually-building-yocto-images-for.html
[lnk-shawnhymel]: http://shawnhymel.com/724/creating-a-custom-linux-kernel-for-the-edison-yocto-2-1/
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-nodejs]: https://nodejs.org/
[lnk-azure-portal]: https://portal.azure.com
[lnk-dm-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-physical]: iot-hub-gateway-sdk-physical-device.md
[lnk-gateway-scenario]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0928_2016-->