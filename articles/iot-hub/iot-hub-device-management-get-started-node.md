<properties
	pageTitle="Introduzione a Gestione dei dispositivi dell'hub IoT | Microsoft Azure"
	description="Esercitazione introduttiva all'hub IoT di Azure per la gestione dei dispositivi con C#. Usare l'hub IoT di Azure e C# con gli SDK IoT di Microsoft Azure per implementare la gestione dei dispositivi."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Introduzione a Gestione dei dispositivi dell'hub IoT di Azure con node.js (anteprima)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introduzione
Per iniziare a usare Gestione dei dispositivi dell'hub IoT di Azure, è necessario creare un hub IoT di Azure, effettuare il provisioning di dispositivi nell'hub IoT, avviare più dispositivi simulati e visualizzare questi dispositivi nell'interfaccia utente di esempio di Gestione dei dispositivi. Questa esercitazione descrive tali operazioni.

> [AZURE.NOTE]  Anche se è disponibile un hub IoT esistente, è necessario creare un nuovo hub IoT per abilitare le funzionalità di gestione dei dispositivi. Gli hub IoT esistenti non hanno ancora funzionalità di gestione dei dispositivi. Quando la gestione dei dispositivi sarà disponibile a livello generale, tutti gli hub IoT esistenti verranno aggiornati con funzionalità di gestione dei dispositivi.

## Prerequisiti

Questa esercitazione presuppone che si usi un computer di sviluppo Ubuntu Linux.

Per seguire la procedura qui descritta è necessario che sia installato il software seguente:

- Git.

- gcc (versione 4.9 o successiva). È possibile verificare la versione corrente installata nell'ambiente usando il comando `gcc --version`. Per informazioni su come aggiornare la versione di gcc su Ubuntu 14.04, vedere <http://askubuntu.com/questions/466651/how-do-i-use-the-latest-gcc-4-9-on-ubuntu-14-04>.

- [CMake](https://cmake.org/download/) (versione 2.8 o successiva). È possibile verificare la versione corrente installata nell'ambiente usando il comando `cmake --version`.

- Node.js 6.1.0 o versione successiva. Installare Node.js per la piattaforma specifica da <https://nodejs.org/>.

- Una sottoscrizione di Azure attiva. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk-free-trial].

## Creare un hub IoT abilitato per la gestione dei dispositivi

È necessario creare un hub IoT abilitato per la gestione dei dispositivi a cui connettere i dispositivi simulati. La procedura seguente illustra come completare questa attività con il portale di Azure.

1.  Accedere al [portale di Azure].
2.  Nell'indice fare clic su **Nuovo**, quindi su **Internet of Things** e infine su **Hub IoT di Azure**.

	![][img-new-hub]

3.  Nel pannello **Hub IoT** scegliere la configurazione per l'hub IoT.

	![][img-configure-hub]

  -   Nella casella **Nome** immettere un nome per l'hub IoT. Se il **Nome** è valido e disponibile, appare un segno di spunta verde nella casella **Nome**.
  -   Selezionare un **piano tariffario e un livello di scalabilità**. Per questa esercitazione non è necessario un livello specifico.
  -   **Gruppo di risorse**: creare un nuovo gruppo di risorse o selezionarne uno esistente. Per altre informazioni, vedere [Uso di Gruppi di risorse per gestire le risorse di Azure].
  -   Selezionare la casella **Abilita Gestione dei dispositivi**.
  -   In **Percorso** selezionare il percorso per ospitare l'hub IoT. Durante l'anteprima pubblica Gestione dei dispositivi dell'hub IoT è disponibile solo negli Stati Uniti orientali, in Europa settentrionale e in Asia orientale. In futuro sarà disponibile in tutte le aree.

  > [AZURE.NOTE]  Se non è selezionata la casella **Abilita Gestione dei dispositivi** gli esempi non funzioneranno.

4.  Dopo aver scelto le opzioni di configurazione dell'hub IoT, fare clic su **Crea**. La creazione dell'hub IoT da parte di Azure può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nella **Schermata iniziale** o nel pannello **Notifiche**.

	![][img-monitor]

5.  Al termine della creazione dell'hub IoT, aprire il pannello del nuovo hub IoT, prendere nota del **Nome host** e fare clic sull'icona **Chiavi**.

	![][img-keys]

6.  Fare clic sui criteri **iothubowner**, quindi copiare e prendere nota della stringa di connessione nel pannello **iothubowner**. Copiarla in una posizione accessibile in un secondo momento, perché sarà necessaria per portare a termine l'esercitazione.

 	> [AZURE.NOTE] In scenari di produzione occorre evitare l'uso di credenziali **iothubowner**.

	![][img-connection]

Ora che è stato creato un hub IoT abilitato per la gestione dei dispositivi, per completare il resto dell'esercitazione è necessaria la stringa di connessione.

## Compilare esempi ed effettuare il provisioning di dispositivi nell'hub IoT

In questa sezione viene eseguito uno script che compila il dispositivo simulato e gli esempi ed effettua il provisioning di un set di nuove identità dispositivo nel registro dei dispositivi dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non sia presente una voce nel registro dei dispositivi.

Per compilare gli esempi ed effettuare il provisioning dei dispositivi nell'hub IoT, seguire questa procedura:

1.  Aprire una shell.

2.  Clonare il repository GitHub. **Assicurarsi di eseguire la clonazione in una directory senza spazi.**

	  ```
	  git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
	  ```

3.  Dalla cartella radice in cui è stato clonato il repository **azure-iot-sdks** passare alla directory **azure-iot-sdks/c/build\_all/linux** ed eseguire il comando seguente per installare i pacchetti di prerequisiti e le librerie dipendenti:

	  ```
	  ./setup.sh
	  ```


4.  Dalla cartella radice in cui è stato clonato il repository **azure-iot-sdks** passare alla directory **azure-iot-sdks/node/service/samples** ed eseguire il comando seguente, sostituendo il valore del segnaposto con la stringa di connessione della sezione precedente:

	  ```
	  ./setup.sh <IoT Hub Connection String>
	  ```

Lo script esegue queste operazioni:

1.  Esegue **cmake** per creare i file make necessari per compilare il dispositivo simulato. L'eseguibile si trova in **azure-iot-sdks/node/service/samples/cmake/iotdm\_client/samples/iotdm\_simple\_sample**. Si noti che i file di origine sono nella cartella **azure-iot-sdks/c/iotdm\_client/samples/iotdm\_simple\_sample**.

2.  Compila l'eseguibile del dispositivo simulato **iotdm\_simple\_sample**.

3.  Esegue `npm install` per installare i pacchetti necessari.

4.  Esegue `node generate_devices.js` per effettuare il provisioning delle identità dei dispositivi nell'hub IoT. I dispositivi sono descritti in **sampledevices.json**. Al completamento del provisioning dei dispositivi, le credenziali vengono archiviate nel file **devicecreds.txt**, disponibile nella directory **azure-iot-sdks/node/service/samples**.

## Avviare i dispositivi simulati

Ora che i dispositivi sono stati aggiunti al registro dei dispositivi, è possibile avviare i dispositivi simulati gestiti. È necessario avviare un dispositivo simulato per ogni identità del dispositivo di cui viene effettuato il provisioning nell'hub IoT di Azure.

Usando una shell, passare alla directory **azure-iot-sdks/node/service/samples** ed eseguire il comando seguente:

  ```
  ./simulate.sh
  ```

Questo script genera i comandi che è necessario eseguire per avviare **iotdm\_simple\_sample** per ogni dispositivo elencato nel file **devicecreds.txt**. Eseguire singolarmente i comandi da una finestra del terminale separata per ogni dispositivo simulato. L'esecuzione del dispositivo simulato continua fino a quando non viene chiusa la finestra di comando.

L'applicazione **iotdm\_simple\_sample** viene compilata con la libreria client di Gestione dei dispositivi dell'hub IoT di Azure per C, che consente di creare dispositivi IoT gestibili dall'hub IoT di Azure. I produttori di dispositivi possono usare questa libreria per creare report sulle proprietà dei dispositivi e implementare le azioni di esecuzione richieste dai processi dei dispositivi. La libreria è un componente fornito insieme agli SDK open source per hub IoT di Azure.

Quando si esegue **simulate.sh**, viene visualizzato un flusso di dati nella finestra di output. L'output mostra il traffico in ingresso e in uscita, nonché istruzioni **printf** nelle funzioni di callback specifiche dell'applicazione. In questo modo è possibile visualizzare il traffico in ingresso e in uscita nonché la gestione dei pacchetti decodificati da parte dell'applicazione di esempio. Quando il dispositivo si connette all'hub IoT, il servizio viene avviato automaticamente per osservare le risorse nel dispositivo. La libreria client di Gestione dei dispositivi dell'hub IoT richiama quindi i callback del dispositivo per recuperare i valori più recenti da quest'ultimo.

Di seguito è riportato l'output dell'applicazione di esempio **iotdm\_simple\_sample**. Nella parte superiore viene visualizzato il messaggio **REGISTERED**, che mostra il dispositivo con ID **Device11-7ce4a850** che si connette all'hub IoT.

> [AZURE.NOTE]  Per avere un output meno dettagliato, compilare ed eseguire la configurazione finale.

![][img-output]

Assicurarsi di lasciare tutti i dispositivi simulati in esecuzione durante il completamento delle sezioni seguenti.

## Eseguire l'interfaccia utente di esempio di Gestione dei dispositivi

Dopo avere effettuato il provisioning di un hub IoT e quando sono disponibili alcuni dispositivi simulati in esecuzione e registrati per la gestione, è possibile distribuire l'interfaccia utente di esempio di Gestione dei dispositivi. L'interfaccia utente di esempio di Gestione dei dispositivi fornisce un esempio funzionante relativo a come usare le API di Gestione dei dispositivi per offrire un'esperienza di interfaccia utente interattiva. Per altre informazioni sull'interfaccia utente di esempio di Gestione dei dispositivi, inclusi i [problemi noti](https://github.com/Azure/azure-iot-device-management#knownissues), vedere il repository [Azure IoT device management UI][lnk-dm-github] (Interfaccia utente di Gestione dei dispositivi di Azure IoT ) di GitHub.

Per recuperare, compilare ed eseguire l'interfaccia utente di esempio di Gestione dei dispositivi, seguire questa procedura:

1. Aprire una shell.

2. Confermare di avere installato Node.js 6.1.0 o versione successiva, come richiesto nella sezione dei prerequisiti, digitando `node --version`.

3. Clonare il repository di GitHub per l'interfaccia utente di Gestione dei dispositivi eseguendo il comando seguente nella shell:

	```
	git clone https://github.com/Azure/azure-iot-device-management.git
	```
	
4. Nella cartella radice della copia clonata del repository per l'interfaccia utente di Gestione dei dispositivi di Azure IoT eseguire il comando seguente per recuperare i pacchetti dipendenti:

	```
	npm install
	```

5. Al termine dell'esecuzione del comando npm install, eseguire il comando seguente nella shell per compilare il codice:

	```
	npm run build
	```

6. Usare un editor di testo per aprire il file user-config.json nella radice della cartella clonata. Sostituire il testo "&lt;YOUR CONNECTION STRING HERE&gt;" con la stringa di connessione dell'hub IoT dalla sezione precedente, quindi salvare il file.

7. Nella shell eseguire il comando seguente per avviare l'app dell'esperienza utente di Gestione dei dispositivi:

	```
	npm run start
	```

8. Quando il prompt dei comandi segnala che i servizi sono stati avviati, aprire un Web browser e passare all'app di gestione dei dispositivi disponibile nell'URL seguente per visualizzare i dispositivi simulati: <http://127.0.0.1:3003>.

	![][img-dm-ui]

Lasciare in esecuzione i dispositivi simulati e l'app di gestione dei dispositivi durante l'esercitazione successiva sulla gestione dei dispositivi.


## Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT, vedere [Introduzione a Gateway SDK][lnk-gateway-SDK].

Per altre informazioni sulle funzionalità di gestione dei dispositivi dell'hub IoT di Azure, vedere l'esercitazione [Esplorare la gestione dei dispositivi dell'hub IoT di Azure usando l'interfaccia utente di esempio][lnk-sample-ui].

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started-node/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started-node/image2.png
[img-monitor]: media/iot-hub-device-management-get-started-node/image3.png
[img-keys]: media/iot-hub-device-management-get-started-node/image4.png
[img-connection]: media/iot-hub-device-management-get-started-node/image5.png
[img-output]: media/iot-hub-device-management-get-started-node/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started-node/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portale di Azure]: https://portal.azure.com/
[Uso di Gruppi di risorse per gestire le risorse di Azure]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

<!---HONumber=AcomDC_0713_2016-->