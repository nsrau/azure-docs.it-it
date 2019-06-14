---
title: Installare Azure IoT Edge in Windows | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: f67f24cab907c3fe9998704e0a0a85d5b29f60a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808856"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installare il runtime di Azure IoT Edge in Windows

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud. 

Per altre informazioni sul funzionamento del runtime di IoT Edge, vedere [Informazioni sul runtime di Azure IoT Edge e sulla relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge su x64 (Intel/AMD) di Windows system usando i contenitori di Windows.

> [!NOTE]
> Un problema noto relativo sistema operativo Windows impedisce la transizione per sospensione e ibernazione gli stati di alimentazione, quando si eseguono i moduli di IoT Edge (contenitori di Windows Nano Server con isolamento di processo). Questo problema influisce sulla durata della batteria del dispositivo.
>
> In alternativa, usare il comando `Stop-Service iotedge` arrestare tutti i moduli IoT Edge in esecuzione prima di usare questi stati di alimentazione. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

Una configurazione con contenitori Linux su sistemi Windows non è una configurazione di produzione consigliata o supportata per Azure IoT Edge. È tuttavia possibile usarla a scopo di sviluppo e test. Per altre informazioni, vedere [usare IoT Edge in Windows per eseguire contenitori Linux](how-to-install-iot-edge-windows-with-linux.md).

Per informazioni su cosa è incluso nella versione più recente di IoT Edge, vedere [Azure IoT Edge rilascia](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Prerequisiti

Usare questa sezione per verificare se il dispositivo Windows in uso può supportare IoT Edge e per prepararlo per un motore del contenitore prima dell'installazione. 

### <a name="supported-windows-versions"></a>Versioni supportate di Windows

Per gli scenari di sviluppo e test, Azure IoT Edge con i contenitori di Windows può essere installato in qualsiasi versione di Windows 10 o Windows Server 2019 (build 17763) che supporta la funzionalità dei contenitori. Per informazioni su quali sistemi operativi sono attualmente supportati per gli scenari di produzione, vedere [sistemi supportati di Azure IoT Edge](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Preparare il dispositivo per un motore del contenitore 

Azure IoT Edge si basa su un motore del contenitore [compatibile con OCI](https://www.opencontainers.org/). Per gli scenari di produzione, usare il motore Moby incluso nello script di installazione per eseguire contenitori Windows nel dispositivo Windows in uso. 

## <a name="install-iot-edge-on-a-new-device"></a>Installare IoT Edge in un nuovo dispositivo

>[!NOTE]
>I pacchetti software di Azure IoT Edge sono soggetti alle condizioni di licenza riportate nei pacchetti, all'interno della directory LICENSE. Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

Uno script di PowerShell scarica e installa il daemon di sicurezza di Azure IoT Edge. Il daemon di sicurezza avvia quindi il primo di due moduli di runtime, l'agente IoT Edge, che consente distribuzioni remote di altri moduli. 

Quando si installa il runtime di IoT Edge per la prima volta in un dispositivo, è necessario effettuare il provisioning del dispositivo con un'identità da un hub IoT. Un singolo dispositivo IoT Edge può eseguirne manualmente usando una stringa di connessione dispositivo fornita dall'IoT Hub. In alternativa, è possibile usare il servizio Device Provisioning per effettuare automaticamente il provisioning dei dispositivi. Ciò è utile quando si dispone di molti dispositivi da configurare. A seconda del provisioning, scegliere lo script di installazione appropriato. 

Le sezioni seguenti descrivono i casi d'uso comuni e i parametri per lo script di installazione di IoT Edge in un nuovo dispositivo. 

### <a name="option-1-install-and-manually-provision"></a>Opzione 1: Effettuare il provisioning e l'installazione manualmente

In questo caso, prima è fornire una **stringa di connessione del dispositivo** generati dall'IoT Hub per il provisioning del dispositivo. 

Questo esempio viene illustrata un'installazione manuale con i contenitori di Windows:

1. Se hai già fatto, registrare un nuovo dispositivo IoT Edge e recuperare il **stringa di connessione del dispositivo**. Copiare la stringa di connessione da usare in seguito in questa sezione. È possibile completare questo passaggio con gli strumenti seguenti:

   * [Portale di Azure](how-to-register-device-portal.md)
   * [Interfaccia della riga di comando di Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Eseguire PowerShell come amministratore.

   >[!NOTE]
   >Usare una sessione AMD64 di PowerShell per installare IoT Edge, non PowerShell (x86). Se non si conosce il tipo di sessione in uso, eseguire questo comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Il **Distribuisci IoTEdge** comando verifica che nel computer Windows si trova in una versione supportata, viene attivata la funzionalità del contenitore e quindi scarica il runtime moby e runtime di IoT Edge. Impostazione predefinita il comando Usa i contenitori di Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. A questo punto, i dispositivi IoT Core potrebbe essere riavviato automaticamente. Altri dispositivi Windows 10 o Windows Server possono essere necessario riavviare. In questo caso, riavviare il dispositivo. Quando il dispositivo è pronto, eseguire PowerShell come amministratore di nuovo.

5. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando richiesto, specificare la stringa di connessione dispositivo recuperato nel passaggio 1. La stringa di connessione consente di associare il dispositivo fisico con un ID dispositivo nell'IoT Hub. 

   La stringa di connessione viene espresso nel formato seguente e non deve includere le virgolette: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Usare la procedura descritta in [verificare l'installazione ha esito positivo](#verify-successful-installation) per controllare lo stato di IoT Edge nel dispositivo. 

Quando si installa e si effettua il provisioning di un dispositivo manualmente, è possibile usare ulteriori parametri per modificare l'installazione tra cui:
* Indirizzare il traffico attraverso un server proxy
* Fare in modo che il programma di installazione punti a una directory offline
* Dichiarare un'immagine del contenitore di un agente specifico e fornire le credenziali se si trova in un registro privato

Per altre informazioni su queste opzioni di installazione, proseguire con il passaggio per apprendere [tutti i parametri di installazione](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opzione 2: Installare ed effettuare automaticamente il provisioning

In questa seconda opzione viene effettuato il provisioning del dispositivo con il servizio Device Provisioning in hub IoT. Fornire l'**ID ambito** da un'istanza del servizio Device Provisioning e l'**ID registrazione** dal dispositivo.

L'esempio seguente illustra un'installazione automatica con i contenitori di Windows:

1. Seguire i passaggi descritti in [crea ed effettuare il provisioning di un dispositivo TPM IoT Edge simulato in Windows](how-to-auto-provision-simulated-device-windows.md) per configurare il servizio Device Provisioning e il recupero relativo **ID ambito**, simulare un dispositivo TPM e il recupero relativo **ID registrazione**, quindi creare una registrazione singola. Dopo aver registrato il dispositivo nell'hub IoT, continuare con la procedura di installazione.  

   >[!TIP]
   >Mantenere aperta la finestra di esecuzione del simulatore TPM durante l'installazione e il test. 

2. Eseguire PowerShell come amministratore.

   >[!NOTE]
   >Usare una sessione AMD64 di PowerShell per installare IoT Edge, non PowerShell (x86). Se non si conosce il tipo di sessione in uso, eseguire questo comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Il **Distribuisci IoTEdge** comando verifica che nel computer Windows si trova in una versione supportata, viene attivata la funzionalità del contenitore e quindi scarica il runtime moby e runtime di IoT Edge. Impostazione predefinita il comando Usa i contenitori di Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. A questo punto, i dispositivi IoT Core potrebbe essere riavviato automaticamente. Altri dispositivi Windows 10 o Windows Server possono essere necessario riavviare. In questo caso, riavviare il dispositivo. Quando il dispositivo è pronto, eseguire PowerShell come amministratore di nuovo.

6. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows. Usare il `-Dps` flag da utilizzare il servizio Device Provisioning invece di provisioning manuale.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

7. Quando richiesto, fornire l'ID ambito dal servizio Device Provisioning e l'ID di registrazione dal dispositivo, che dovrebbe aver recuperato nel passaggio 1.

8. Usare la procedura descritta in [verificare l'installazione ha esito positivo](#verify-successful-installation) per controllare lo stato di IoT Edge nel dispositivo. 

Quando si installa e si effettua il provisioning di un dispositivo manualmente, è possibile usare ulteriori parametri per modificare l'installazione tra cui:
* Indirizzare il traffico attraverso un server proxy
* Fare in modo che il programma di installazione punti a una directory offline
* Dichiarare un'immagine del contenitore di un agente specifico e fornire le credenziali se si trova in un registro privato

Per altre informazioni su queste opzioni di installazione, continuare a leggere questo articolo oppure passare direttamente a [Tutti i parametri di installazione](#all-installation-parameters).

## <a name="offline-installation"></a>Installazione offline

Durante l'installazione vengono scaricati i due file: 
* File cab Microsoft Azure IoT Edge, che contiene il daemon di sicurezza IoT Edge (iotedged), motore contenitore Moby e Moby CLI.
* MSI di Visual C++ Redistributable Package (runtime di VC)

È possibile scaricare uno o entrambi questi file anticipatamente al dispositivo, quindi scegliere lo script di installazione nella directory che contiene i file. Il programma di installazione verifica innanzitutto il contenuto della directory e quindi scarica solo i componenti che non sono già presenti. Se tutti i file sono disponibili offline, è possibile installare senza connessione internet. È anche possibile usare questa funzionalità per installare una versione specifica dei componenti.  

Per i file di installazione IoT Edge più recenti insieme a versioni precedenti, vedere [Azure IoT Edge rilascia](https://github.com/Azure/azure-iotedge/releases).

Per installare i componenti offline, usare il `-OfflineInstallationPath` parametro durante la distribuzione-IoTEdge comando e specificare il percorso assoluto per la directory del file. Ad esempio,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

È anche possibile usare il parametro di percorso di installazione offline con il comando Update-IoTEdge, introdotto più avanti in questo articolo. 

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Verificare lo stato del servizio IoT Edge. Si dovrebbe essere elencato come in esecuzione.  

```powershell
Get-Service iotedge
```

Esaminare i log del servizio generati negli ultimi cinque minuti. Se è stata completata l'installazione del runtime di IoT Edge, è possibile visualizzare un elenco degli errori dal momento in cui un programma in esecuzione **Distribuisci IoTEdge** e **Initialize-IoTEdge**. Questi errori sono previsti, come il servizio sta tentando di avviare prima della configurazione in corso. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Elencare i moduli in esecuzione. Dopo una nuova installazione, il modulo solo dovrebbe essere in esecuzione **edgeAgent**. Dopo aver [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md), si noterà ad altri utenti. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gestire i contenitori di modulo

Il servizio di IoT Edge richiede un motore contenitore in esecuzione nel dispositivo. Quando si distribuisce un modulo in un dispositivo, il runtime di IoT Edge Usa il motore di contenitori per eseguire il pull dell'immagine del contenitore da un registro nel cloud. Il servizio di IoT Edge consente di interagire con i moduli e recuperare i log, ma a volte è consigliabile usare il motore del contenitore per interagire con il contenitore stesso. 

Per altre informazioni sui concetti di modulo, vedere [moduli comprendere Azure IoT Edge](iot-edge-modules.md). 

Se si eseguono i contenitori di Windows nel dispositivo Windows IoT Edge, l'installazione di IoT Edge incluso il motore contenitore Moby. Il motore Moby si basava sugli stessi standard di Docker ed è stato progettato per l'esecuzione in parallelo nello stesso computer Desktop di Docker. Per questo motivo, se si desidera contenitori destinazione gestiti dal motore Moby, è necessario scegliere come destinazione in modo specifico tale motore invece di Docker. 

Ad esempio, per elencare tutte le immagini Docker, usare il comando seguente:

```powershell
docker images
```

Per elencare tutte le immagini Moby, modificare lo stesso comando con un puntatore al motore di Moby: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

Il motore di URI è elencato nell'output dello script di installazione oppure è possibile trovarlo nella sezione Impostazioni di runtime contenitore per il file config. yaml. 

![uri moby_runtime in config. yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Per altre informazioni sui comandi è possibile usare per interagire con i contenitori e immagini in esecuzione nel dispositivo, vedere [interfacce della riga di comando di Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Aggiornare un'installazione esistente

Se hai già installato il runtime di IoT Edge in un dispositivo prima ed effettuato il provisioning con un'identità dall'IoT Hub, è possibile aggiornare il runtime senza la necessità di immettere nuovamente le informazioni sul dispositivo. 

Per altre informazioni, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).

Questo esempio mostra un'installazione che punta a un file di configurazione esistente e usa i contenitori Windows: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Quando si aggiorna IoT Edge, è possibile usare parametri aggiuntivi per modificare l'aggiornamento, tra cui:
* Indirizzare il traffico passi attraverso un server proxy, o
* Fare in modo che il programma di installazione punti a una directory offline 
* Il riavvio senza un prompt dei comandi, se necessario

È possibile dichiarare un'immagine del contenitore dell'agente IoT Edge con i parametri degli script in quanto tali informazioni sono già impostate nel file di configurazione dall'installazione precedente. Se si vuole modificare l'immagine del contenitore dell'agente, eseguire questa operazione nel file config.yaml. 

Per altre informazioni su queste opzioni di aggiornamento, usare il comando `Get-Help Update-IoTEdge -full` o fare riferimento a [tutti i parametri di installazione](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Disinstallare IoT Edge

Per rimuovere l'installazione di IoT Edge da un dispositivo Windows, usare il comando seguente da una finestra amministrativa di PowerShell. Questo comando rimuove il runtime di IoT Edge, insieme alla configurazione esistente e ai dati del motore Moby. 

```powershell
Uninstall-IoTEdge
```

Il comando Disinstalla IoTEdge non funziona in Windows IoT Core. Per rimuovere IoT Edge da dispositivi Windows IoT Core, è necessario ridistribuire l'immagine di Windows IoT Core. 

Per altre informazioni sulle opzioni di disinstallazione, usare il comando `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Tutti i parametri di installazione

Nelle sezioni precedenti sono stati introdotti alcuni scenari di installazione comuni con esempi di come usare i parametri per modificare lo script di installazione. In questa sezione fornisce le tabelle di riferimento dei parametri comuni usati per installare, aggiornare o disinstallare IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Il comando Distribuisci IoTEdge Scarica e distribuisce il Daemon di sicurezza di IoT Edge e le relative dipendenze. Il comando di distribuzione accetta i parametri comuni, tra gli altri. Per un elenco completo, usare il comando `Get-Help Deploy-IoTEdge -full`.  

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo contenitore, Windows è il valore predefinito.<br><br>Per i contenitori di Windows, IoT Edge Usa il motore di contenitore moby incluso nell'installazione. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **Proxy** | URL proxy | Includere questo parametro se il dispositivo deve passare attraverso un server proxy per accedere a Internet. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Percorso directory | Se questo parametro è incluso, il programma di installazione verificherà la directory elencata per il file cab di IoT Edge e i file MSI di Runtime VC necessari per l'installazione. Vengono scaricati tutti i file non trovati nella directory. Se entrambi i file si trovano nella directory, è possibile installare IoT Edge senza una connessione internet. È anche possibile usare questo parametro per usare una versione specifica. |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Nessuno | Questo flag consente lo script di distribuzione riavviare la macchina senza chiedere conferma, se necessario. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Il comando Initialize-IoTEdge Configura IoT Edge con la stringa di connessione del dispositivo e i dettagli operativi. Gran parte delle informazioni generate da questo comando viene quindi archiviata nel file iotedge\config.yaml. Il comando di inizializzazione accetta i parametri comuni, tra gli altri. Per un elenco completo, usare il comando `Get-Help Initialize-IoTEdge -full`. 

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **Manuale** | Nessuna | **Parametro opzionale**. Se non viene specificato alcun tipo di provisioning, manuale è il valore predefinito.<br><br>Dichiara che si fornirà una stringa di connessione del dispositivo per effettuare il provisioning del dispositivo manualmente. |
| **Dps** | Nessuna | **Parametro opzionale**. Se non viene specificato alcun tipo di provisioning, manuale è il valore predefinito.<br><br>Dichiara che si forniranno un ID ambito del servizio Device Provisioning (DPS) e l'ID registrazione del dispositivo per effettuare il provisioning tramite DPS.  |
| **DeviceConnectionString** | Una stringa di connessione da un dispositivo IoT Edge registrato in un hub IoT, tra virgolette singole | **Obbligatorio** per l'installazione manuale. Se non si specifica una stringa di connessione nei parametri di script, ne verrà richiesta una durante l'installazione. |
| **ScopeId** | Un ID ambito da un'istanza del servizio Device Provisioning associata all'hub IoT. | **Obbligatorio** per l'installazione di DPS. Se non si specifica un ID ambito nei parametri di script, ne verrà richiesto uno durante l'installazione. |
| **RegistrationId** | Un ID registrazione generato dal dispositivo | **Obbligatorio** per l'installazione di DPS. Se non si specifica un ID registrazione nei parametri di script, ne verrà richiesto uno durante l'installazione. |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo contenitore, Windows è il valore predefinito.<br><br>Per i contenitori di Windows, IoT Edge Usa il motore di contenitore moby incluso nell'installazione. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI dell'immagine dell'agente IoT Edge | Per impostazione predefinita, una nuova installazione di IoT Edge usa il tag di versioni più recente per l'immagine dell'agente IoT Edge. Usare questo parametro per impostare un tag specifico per la versione dell'immagine o per fornire la propria immagine dell'agente. Per altre informazioni, vedere [Informazioni sui tag di IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome utente** | Nome utente del registro contenitori | Usare questo parametro solo se si imposta il parametro -AgentImage su un contenitore in un registro privato. Specificare un nome utente con accesso al registro. |
| **Password** | Stringa della password di protezione | Usare questo parametro solo se si imposta il parametro -AgentImage su un contenitore in un registro privato. Specificare la password per accedere al registro. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | Se un sistema operativo è specificato alcun contenitore, Windows è il valore predefinito. Per i contenitori Windows, nell'installazione verrà incluso un motore del contenitore. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **Proxy** | URL proxy | Includere questo parametro se il dispositivo deve passare attraverso un server proxy per accedere a Internet. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Percorso directory | Se questo parametro è incluso, il programma di installazione verificherà la directory elencata per il file cab di IoT Edge e i file MSI di Runtime VC necessari per l'installazione. Vengono scaricati tutti i file non trovati nella directory. Se entrambi i file si trovano nella directory, è possibile installare IoT Edge senza una connessione internet. È anche possibile usare questo parametro per usare una versione specifica. |
| **RestartIfNeeded** | Nessuno | Questo flag consente lo script di distribuzione riavviare la macchina senza chiedere conferma, se necessario. |


### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **Force** | Nessuno | Questo flag forza la disinstallazione, nel caso in cui il tentativo precedente di disinstallazione non è riuscito. 
| **RestartIfNeeded** | Nessuno | Questo flag consente lo script di disinstallazione riavviare la macchina senza chiedere conferma, se necessario. |


## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

In caso di problemi che impediscono di installare correttamente IoT Edge, vedere la pagina relativa alla [risoluzione dei problemi](troubleshoot.md).

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
