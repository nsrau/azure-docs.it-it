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
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="juanpere"/>

# Introduzione a Gestione dei dispositivi dell'hub IoT di Azure con C# (anteprima)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introduzione
Per iniziare a usare Gestione dei dispositivi dell'hub IoT di Azure, è necessario creare un hub IoT di Azure, effettuare il provisioning di dispositivi nell'hub IoT, avviare più dispositivi simulati e visualizzare questi dispositivi nell'interfaccia utente di esempio di Gestione dei dispositivi. Questa esercitazione descrive tali operazioni.

> [AZURE.NOTE]  Anche se è disponibile un hub IoT esistente, è necessario creare un nuovo hub IoT per abilitare le funzionalità di gestione dei dispositivi. Gli hub IoT esistenti non hanno ancora queste funzionalità. Quando la gestione dei dispositivi sarà disponibile a livello generale, tutti gli hub IoT esistenti verranno aggiornati con funzionalità di gestione dei dispositivi.

## Prerequisiti

Questa esercitazione presuppone che si usi un computer di sviluppo Windows.

Per seguire la procedura qui descritta è necessario che siano installati gli elementi seguenti:

- Microsoft Visual Studio 2015.

- Git.

- CMake versione 2.8 o successiva. Installare CMake da <https://cmake.org/download/>. Per PC Windows, scegliere l'opzione relativa a Windows Installer (file MSI). Selezionare la casella per aggiungere CMake alla variabile PATH dell'utente corrente.

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

    > [AZURE.NOTE]  Se non si seleziona la casella **Abilita Gestione dei dispositivi**, gli esempi non funzionano.<br/>Selezionando **Abilita Gestione dei dispositivi**, si crea un hub IoT di anteprima supportato solo negli Stati Uniti orientali, nell'Europa settentrionale e nell'Asia orientale e non destinato a scenari di produzione. Non è possibile eseguire la migrazione dei dispositivi all'interno e all'esterno degli hub abilitati per la gestione dei dispositivi.

4.  Dopo aver scelto le opzioni di configurazione dell'hub IoT, fare clic su **Crea**. La creazione dell'hub IoT da parte di Azure può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nella **Schermata iniziale** o nel pannello **Notifiche**.

	![][img-monitor]

5.  Al termine della creazione dell'hub IoT, aprire il pannello del nuovo hub IoT, prendere nota del **Nome host** e fare clic su **Criteri di accesso condiviso**.

	![][img-keys]

6.  Fare clic sui criteri **iothubowner**, quindi copiare e prendere nota della stringa di connessione nel pannello **iothubowner**. Copiarla in una posizione accessibile in un secondo momento, perché sarà necessaria per portare a termine l'esercitazione.

 	> [AZURE.NOTE] In scenari di produzione occorre evitare l'uso di credenziali **iothubowner**.

	![][img-connection]

Ora che è stato creato un hub IoT abilitato per la gestione dei dispositivi, per completare il resto dell'esercitazione è necessaria la stringa di connessione.

## Compilare esempi ed effettuare il provisioning di dispositivi nell'hub IoT

In questa sezione viene eseguito uno script che compila il dispositivo simulato e gli esempi ed effettua il provisioning di un set di nuove identità dispositivo nel registro dei dispositivi dell'hub IoT. Un dispositivo non può connettersi all'hub IoT a meno che non sia presente una voce nel registro dei dispositivi.

Per compilare gli esempi ed effettuare il provisioning dei dispositivi nell'hub IoT, seguire questa procedura:

1.  Aprire il **Prompt dei comandi per gli sviluppatori per VS2015**.

2.  Clonare il repository GitHub. **Assicurarsi di eseguire la clonazione in una directory senza spazi.**

	  ```
	  git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
	  ```

3.  Dalla cartella radice in cui è stato clonato il repository **azure-iot-sdks** passare alla cartella **\\azure-iot-sdks\\csharp\\service\\samples** e avviare l'esecuzione, sostituendo il valore del segnaposto con la stringa di connessione della sezione precedente:

	  ```
	  setup.bat <IoT Hub Connection String>
	  ```

Lo script esegue queste operazioni:

1.  Esegue **cmake** per creare una soluzione di Visual Studio 2015 per il dispositivo simulato. Il file di progetto è **azure-iot-sdks\\csharp\\service\\samples\\cmake\\iotdm\_client\\samples\\iotdm\_simple\_sample\\iotdm\_simple\_sample.vcxproj**. Si noti che i file di origine si trovano nella cartella ***azure-iot-sdks\\c\\iotdm\_client\\samples\\iotdm\_simple\_sample**.

2.  Compila il progetto di dispositivo simulato **iotdm\_simple\_sample.vcxproj**.

3.  Compila gli esempi di gestione dei dispositivi **azure-iot-sdks\\csharp\\service\\samples\\GetStartedWithIoTDM\\GetStartedWithIoTDM.sln**.

4.  Esegue **GenerateDevices.exe** per effettuare il provisioning delle identità dei dispositivi nell'hub IoT. I dispositivi sono descritti in **sampledevices.json**, nella cartella **azure-iot-sdks\\node\\service\\samples**. Dopo il provisioning dei dispositivi, le credenziali vengono archiviate nel file **devicecreds.txt**, nella cartella **azure-iot-sdks\\csharp\\service\\samples\\bin**.

## Avviare i dispositivi simulati

Ora che i dispositivi sono stati aggiunti al registro dei dispositivi, è possibile avviare i dispositivi simulati gestiti. Viene avviato un dispositivo simulato per ogni identità del dispositivo di cui viene effettuato il provisioning nell'hub IoT di Azure.

Al prompt dei comandi per gli sviluppatori nella cartella **\\azure-iot-sdks\\csharp\\service\\samples\\bin** eseguire:

  ```
  simulate.bat
  ```

Questo script esegue un'istanza di **iotdm\_simple\_sample.exe** per ogni dispositivo elencato nel file **devicecreds.txt**. L'esecuzione del dispositivo simulato continua fino a quando non viene chiusa la finestra di comando.

L'applicazione di esempio **iotdm\_simple\_sample** viene compilata con la libreria client di Gestione dei dispositivi dell'hub IoT di Azure per C, che consente di creare dispositivi IoT che possono essere gestiti dall'hub IoT di Azure. I produttori di dispositivi possono usare questa libreria per creare report sulle proprietà dei dispositivi e implementare le azioni di esecuzione richieste dai processi dei dispositivi. La libreria è un componente fornito insieme agli SDK open source per hub IoT di Azure.

Quando si esegue **simulate.bat**, viene visualizzato un flusso di dati nella finestra di output. L'output mostra il traffico in ingresso e in uscita e le istruzioni **printf** nelle funzioni di callback specifiche dell'applicazione. Questo output consente di visualizzare il traffico in ingresso e in uscita nonché la gestione dei pacchetti decodificati da parte dell'applicazione di esempio. Quando il dispositivo si connette all'hub IoT, il servizio viene avviato automaticamente per osservare le risorse nel dispositivo. La libreria client di Gestione dei dispositivi dell'hub IoT richiama quindi i callback del dispositivo per recuperare i valori più recenti da quest'ultimo.

Il seguente è l'output dell'applicazione di esempio **iotdm\_simple\_sample**. Nella parte superiore viene visualizzato il messaggio **REGISTERED**, che mostra il dispositivo con ID **Device11-7ce4a850** che si connette all'hub IoT.

> [AZURE.NOTE]  Per avere un output meno dettagliato, compilare ed eseguire la configurazione finale.

![][img-output]

Assicurarsi di lasciare tutti i dispositivi simulati in esecuzione durante il completamento delle sezioni seguenti.

## Eseguire l'interfaccia utente di esempio di Gestione dei dispositivi

Dopo avere effettuato il provisioning di un hub IoT e quando sono disponibili alcuni dispositivi simulati in esecuzione e registrati per la gestione, è possibile distribuire l'interfaccia utente di esempio di Gestione dei dispositivi. L'interfaccia utente di esempio di Gestione dei dispositivi fornisce un esempio funzionante relativo a come usare le API di Gestione dei dispositivi per offrire un'esperienza di interfaccia utente interattiva. Per altre informazioni sull'interfaccia utente di esempio di Gestione dei dispositivi, inclusi i [problemi noti](https://github.com/Azure/azure-iot-device-management#knownissues), vedere il repository [Azure IoT device management UI][lnk-dm-github] \(Interfaccia utente di Gestione dei dispositivi di Azure IoT) di GitHub.

Per recuperare, compilare ed eseguire l'interfaccia utente di esempio di Gestione dei dispositivi, seguire questa procedura:

1. Aprire **Command Prompt**.

2. Confermare di avere installato Node.js 6.1.0 o versione successiva, come richiesto nella sezione dei prerequisiti, digitando `node --version`.

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

6. Usare un editor di testo per aprire il file user-config.json nella radice della cartella clonata. Sostituire il testo "&lt;YOUR CONNECTION STRING HERE&gt;" con la stringa di connessione dell'hub IoT dalla sezione precedente, quindi salvare il file.

7. Nel prompt dei comandi eseguire il comando seguente per avviare l'app dell'esperienza utente di Gestione dei dispositivi:

	```
	npm run start
	```

8. Quando il prompt dei comandi segnala che i servizi sono stati avviati, aprire un Web browser (sono attualmente supportati Microsoft Edge/Internet Explorer 11 e versioni successive/Safari/Chrome) e passare all'app di gestione dei dispositivi disponibile nell'URL seguente per visualizzare i dispositivi simulati: <http://127.0.0.1:3003>.

	![][img-dm-ui]

Lasciare in esecuzione i dispositivi simulati e l'app di gestione dei dispositivi durante l'esercitazione successiva sulla gestione dei dispositivi.


## Passaggi successivi

Per altre informazioni sulle attività iniziali con l'hub IoT, vedere [Introduzione a Gateway SDK][lnk-gateway-SDK].

Per altre informazioni sulle funzionalità di gestione dei dispositivi dell'hub IoT di Azure, vedere l'esercitazione [Esplorare la gestione dei dispositivi dell'hub IoT di Azure usando l'interfaccia utente di esempio][lnk-sample-ui].

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portale di Azure]: https://portal.azure.com/
[Uso di Gruppi di risorse per gestire le risorse di Azure]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

<!---HONumber=AcomDC_0817_2016-->