---
title: Installare Azure IoT Edge in Windows | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: kgremban
ms.openlocfilehash: 80ce962ac6977fcce2455c8e2ef29af448a44075
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133150"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installare il runtime di Azure IoT Edge in Windows

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud.

Per altre informazioni sul funzionamento del runtime di IoT Edge, vedere [Informazioni sul runtime di Azure IoT Edge e sulla relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge nel sistema Windows x64 (AMD/Intel) usando i contenitori di Windows.This article lists the steps to install the Azure IoT Edge runtime on your Windows x64 (AMD/Intel) system using Windows containers.

> [!NOTE]
> Un problema noto del sistema operativo Windows impedisce la transizione agli stati di alimentazione di sospensione e ibernazione quando i moduli Edge IoT (contenitori di Windows Nano Server isolati dal processo) sono in esecuzione. Questo problema influisce sulla durata della batteria del dispositivo.
>
> Per risolvere il problema, utilizzare il comando `Stop-Service iotedge` per arrestare tutti i moduli IoT Edge in esecuzione prima di utilizzare questi stati di risparmio energia.

Una configurazione con contenitori Linux su sistemi Windows non è una configurazione di produzione consigliata o supportata per Azure IoT Edge. È tuttavia possibile usarla a scopo di sviluppo e test. Per altre informazioni, vedere [Usare Edge IoT in Windows per eseguire contenitori Linux.](how-to-install-iot-edge-windows-with-linux.md)

Per informazioni sugli elementi inclusi nella versione più recente di IoT Edge, vedere Versioni di [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Prerequisiti

Usare questa sezione per verificare se il dispositivo Windows in uso può supportare IoT Edge e per prepararlo per un motore del contenitore prima dell'installazione.

### <a name="supported-windows-versions"></a>Versioni supportate di Windows

IoT Edge per Windows richiede la versione 1809/build 17763 di Windows, che è la build di [supporto a lungo termine](https://docs.microsoft.com/windows/release-information/)di Windows più recente. Per il supporto dello SKU di Windows, vedere gli elementi supportati in base al fatto che si stia preparando per scenari di produzione o scenari di sviluppo e test:For Windows SKU support, see what is supported based on whether you're preparing for production scenarios or development and test scenarios:

* **Produzione:** per informazioni aggiornate sui sistemi operativi attualmente supportati per gli scenari di produzione, vedere [Sistemi supportati da Azure IoT Edge.](support.md#operating-systems)
* **Sviluppo e test**: per gli scenari di sviluppo e test, Azure IoT Edge con contenitori di Windows può essere installato in qualsiasi versione di Windows 10 o Windows Server 2019 che supporta la funzionalità contenitori.

I dispositivi IoT Core devono includere la funzionalità facoltativa IoT Core Windows Containers per supportare il runtime IoT Edge.IoT Core devices must include the IoT Core Windows Containers optional feature to support the IoT Edge runtime. Usare il comando seguente in una [sessione di PowerShell remota](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) per verificare che i contenitori di Windows siano supportati nel dispositivo:Use the following command in a remote PowerShell session to check that Windows containers are supported on your device:

```powershell
Get-Service vmcompute
```

Se il servizio è presente, si dovrebbe ottenere una risposta corretta con lo stato del servizio elencato come **in esecuzione**. Se `vmcompute` il servizio non viene trovato, il dispositivo non soddisfa i requisiti per IoT Edge. Contattare il provider di hardware per richiedere informazioni sul supporto per questa funzionalità.

### <a name="prepare-for-a-container-engine"></a>Preparare il dispositivo per un motore del contenitore

Azure IoT Edge si basa su un motore del contenitore [compatibile con OCI](https://www.opencontainers.org/). Per gli scenari di produzione, usare il motore Moby incluso nello script di installazione per eseguire contenitori Windows nel dispositivo Windows in uso.

## <a name="install-iot-edge-on-a-new-device"></a>Installare IoT Edge in un nuovo dispositivo

>[!NOTE]
>I pacchetti software di Azure IoT Edge sono soggetti alle condizioni di licenza riportate nei pacchetti, all'interno della directory LICENSE. Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

Uno script di PowerShell scarica e installa il daemon di sicurezza di Azure IoT Edge. Il daemon di sicurezza avvia quindi il primo di due moduli di runtime, l'agente IoT Edge, che consente distribuzioni remote di altri moduli.

>[!TIP]
>Per i dispositivi IoT Core, è consigliabile eseguire i comandi di installazione usando una sessione di RemotePowerShell.For IoT Core devices, we recommend running the installation commands using a RemotePowerShell session. Per ulteriori informazioni, vedere [Utilizzo di PowerShell per Windows IoT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Quando si installa il runtime di IoT Edge per la prima volta in un dispositivo, è necessario effettuare il provisioning del dispositivo con un'identità da un hub IoT. È possibile eseguire manualmente il provisioning di un singolo dispositivo IoT Edge usando una stringa di connessione del dispositivo fornita dall'hub IoT.IoT Edge device can be provisioned manually using a device connection string provided by IoT Hub. In alternativa, è possibile utilizzare il servizio Device Provisioning (DPS) per eseguire automaticamente il provisioning dei dispositivi, operazione utile quando si dispone di molti dispositivi da configurare. A seconda del provisioning, scegliere lo script di installazione appropriato.

Le sezioni seguenti descrivono i casi d'uso comuni e i parametri per lo script di installazione di IoT Edge in un nuovo dispositivo.

### <a name="option-1-install-and-manually-provision"></a>Opzione 1: eseguire l'installazione e ed eseguire l'installazione ed effettuare manualmente il provisioning

In questa prima opzione viene fornita una stringa di **connessione del dispositivo** generata dall'hub IoT per eseguire il provisioning del dispositivo.

In questo esempio viene illustrata un'installazione manuale con i contenitori di Windows:This example demonstrates a manual installation with Windows containers:

1. Se non è già stato fatto, registrare un nuovo dispositivo IoT Edge e recuperare la stringa di connessione del **dispositivo.** Copiare la stringa di connessione da utilizzare più avanti in questa sezione. È possibile completare questo passaggio utilizzando gli strumenti seguenti:You can complete this step using the following tools:

   * [Portale di Azure](how-to-register-device.md#register-in-the-azure-portal)
   * [Interfaccia della riga di comando di AzureAzure](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Eseguire PowerShell come amministratore.

   >[!NOTE]
   >Usare una sessione AMD64 di PowerShell per installare IoT Edge, non PowerShell (x86). Se non si conosce il tipo di sessione in uso, eseguire questo comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Il comando **Deploy-IoTEdge** controlla che il computer Windows sia in una versione supportata, attiva la funzionalità dei contenitori e quindi scarica il runtime moby e il runtime di IoT Edge. Il comando utilizza per impostazione predefinita i contenitori di Windows.The default command defaults to using Windows containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. A questo punto, i dispositivi IoT Core potrebbero riavviarsi automaticamente. Altri dispositivi Windows 10 o Windows Server potrebbero richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

5. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando richiesto, fornire la stringa di connessione del dispositivo recuperata nel passaggio 1.When prompted, provide the device connection string that you retrieved in step 1. The device connection string associates the physical device with a device ID in IoT Hub.

   La stringa di connessione del dispositivo assume il formato seguente e non deve includere virgolette:The device connection string takes the following format, and should not include quotationmarks:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Utilizzare la procedura descritta in [Verifica dell'installazione corretta](#verify-successful-installation) per verificare lo stato di Edge IoT nel dispositivo.

Quando si installa e si effettua il provisioning di un dispositivo manualmente, è possibile usare ulteriori parametri per modificare l'installazione tra cui:

* Indirizzare il traffico attraverso un server proxy
* Fare in modo che il programma di installazione punti a una directory offline
* Dichiarare un'immagine del contenitore di un agente specifico e fornire le credenziali se si trova in un registro privato

Per ulteriori informazioni su queste opzioni di installazione, passare avanti per informazioni su [tutti i parametri](#all-installation-parameters)di installazione .

### <a name="option-2-install-and-automatically-provision"></a>Opzione 2: installare ed effettuare automaticamente il provisioning

In questa seconda opzione viene effettuato il provisioning del dispositivo con il servizio Device Provisioning in hub IoT. Fornire **l'ID ambito** da un'istanza del servizio Device Provisioning insieme a qualsiasi altra informazione specifica per il meccanismo di [attestazione](../iot-dps/concepts-security.md#attestation-mechanism)preferito:

* [Creare ed eseguire il provisioning di un dispositivo IoT Edge simulato con un TPM virtuale in Windows](how-to-auto-provision-simulated-device-windows.md)
* [Creare ed eseguire il provisioning di un dispositivo IoT Edge simulato usando i certificati X.509Create and provision a simulated IoT Edge device using X.509 certificates](how-to-auto-provision-x509-certs.md)
* [Creare ed eseguire il provisioning di un dispositivo IoT Edge usando l'attestazione a chiave simmetricaCreate and provision an IoT Edge device using symmetric key attestation](how-to-auto-provision-symmetric-keys.md)

Quando si installa ed esegue il provisioning automatico di un dispositivo, è possibile utilizzare parametri aggiuntivi per modificare l'installazione, tra cui:

* Indirizzare il traffico attraverso un server proxy
* Fare in modo che il programma di installazione punti a una directory offline
* Dichiarare un'immagine del contenitore di un agente specifico e fornire le credenziali se si trova in un registro privato

Per altre informazioni su queste opzioni di installazione, continuare a leggere questo articolo oppure passare direttamente a [Tutti i parametri di installazione](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Installazione di versione offline o specifica

Durante l'installazione vengono scaricati due file:

* Cabina di Microsoft Azure IoT Edge, che contiene il daemon di sicurezza IoT Edge (iotedged), il motore contenitore Moby e Moby CLI.
* MSI del pacchetto ridistribuibile (runtime VC) di Visual C

Se il dispositivo sarà offline durante l'installazione o se si desidera installare una versione specifica di IoT Edge, è possibile scaricare uno o entrambi questi file in anticipo sul dispositivo. Quando è il momento di installare, puntare lo script di installazione alla directory che contiene i file scaricati. Il programma di installazione controlla prima tale directory e quindi scarica solo i componenti che non vengono trovati. Se tutti i file sono disponibili offline, è possibile eseguire l'installazione senza connessione internet.

Per i file di installazione di IoT Edge più recenti e versioni precedenti, vedere Versioni di [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Per eseguire l'installazione `-OfflineInstallationPath` con i componenti non in linea, utilizzare il parametro come parte del comando Deploy-IoTEdge e fornire il percorso assoluto della directory dei file. Ad esempio,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

>[!NOTE]
>Il `-OfflineInstallationPath` parametro cerca un file denominato **Microsoft-Azure-IoTEdge.cab** nella directory fornita. A partire da IoT Edge versione 1.0.9-rc4, sono disponibili due file CAB, uno per i dispositivi AMD64 e uno per ARM32. Scaricare il file corretto per il dispositivo, quindi rinominarlo per rimuovere il suffisso dell'architettura.

Il `Deploy-IoTEdge` comando installa i componenti IoT Edge e quindi `Initialize-IoTEdge` è necessario continuare con il comando per eseguire il provisioning del dispositivo con l'ID e la connessione del dispositivo Dell'hub IoT. Eseguire il comando direttamente e fornire una stringa di connessione dall'hub IoT oppure usare uno dei collegamenti nella sezione precedente per informazioni su come eseguire automaticamente il provisioning dei dispositivi con il servizio Device Provisioning.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Initialize-IoTEdge
```

È inoltre possibile utilizzare il parametro del percorso di installazione offline con il comando Update-IoTEdge.

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Verificare lo stato del servizio IoT Edge. Dovrebbe essere elencato come in esecuzione.  

```powershell
Get-Service iotedge
```

Esaminare i log del servizio generati negli ultimi cinque minuti. Se hai appena terminato l'installazione del runtime di IoT Edge, potresti visualizzare un elenco di errori dal momento tra l'esecuzione di **Deploy-IoTEdge** e **Initialize-IoTEdge**. Questi errori sono previsti, poiché il servizio sta tentando di avviarsi prima di essere configurato.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Eseguire un controllo automatico per individuare gli errori di configurazione e di rete più comuni.

```powershell
iotedge check
```

Elencare i moduli in esecuzione. Dopo una nuova installazione, l'unico modulo che dovrebbe essere visualizzato in esecuzione è **edgeAgent**. Dopo aver distribuito i [moduli IoT Edge](how-to-deploy-modules-portal.md) per la prima volta, l'altro modulo di sistema, **edgeHub**, verrà avviato anche sul dispositivo.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gestire i contenitori dei moduliManage module containers

Il servizio IoT Edge richiede un motore contenitore in esecuzione nel dispositivo. Quando si distribuisce un modulo in un dispositivo, il runtime di IoT Edge usa il motore contenitore per estrarre l'immagine del contenitore da un Registro di sistema nel cloud. Il servizio IoT Edge consente di interagire con i moduli e recuperare i log, ma a volte è possibile usare il motore contenitore per interagire con il contenitore stesso.

Per altre informazioni sui concetti relativi ai moduli, vedere [Informazioni sui moduli di Azure IoT Edge.](iot-edge-modules.md)

Se si eseguono contenitori Windows nel dispositivo Windows IoT Edge, l'installazione di IoT Edge include il motore contenitore Moby. Il motore Moby era basato sugli stessi standard di Docker, ed era progettato per funzionare in parallelo sulla stessa macchina di Docker Desktop. Per questo motivo, se si desidera impostare come destinazione i contenitori gestiti dal motore Moby, è necessario indirizzare in modo specifico tale motore anziché Docker.

Ad esempio, per elencare tutte le immagini Docker, utilizzare il comando seguente:

```powershell
docker images
```

Per elencare tutte le immagini Moby, modificare lo stesso comando con un puntatore al motore Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

L'URI del motore è elencato nell'output dello script di installazione oppure è possibile trovarlo nella sezione relativa alle impostazioni di runtime del contenitore per il file config.yaml.

![URI moby_runtime in config.yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Per ulteriori informazioni sui comandi che è possibile utilizzare per interagire con contenitori e immagini in esecuzione nel dispositivo, vedere [Interfacce della riga di comando Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="uninstall-iot-edge"></a>Disinstallare IoT Edge

Per rimuovere l'installazione di IoT Edge da un dispositivo Windows, usare il comando seguente da una finestra amministrativa di PowerShell. Questo comando rimuove il runtime di IoT Edge, insieme alla configurazione esistente e ai dati del motore Moby.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Il comando Uninstall-IoTEdge non funziona su Windows IoT Core. Per rimuovere IoT Edge dai dispositivi Windows IoT Core, devi ridistribuire l'immagine Windows IoT Core.

Per ulteriori informazioni sulle opzioni di `Get-Help Uninstall-IoTEdge -full`disinstallazione, utilizzare il comando .

## <a name="verify-installation-script"></a>Verificare lo script di installazione

I comandi di installazione forniti in questo articolo utilizzano il `aka.ms/iotedge-win`cmdlet Invoke-WebRequest per richiedere lo script di installazione da . Questo collegamento punta`IoTEdgeSecurityDaemon.ps1` allo script della versione più recente di [IoT Edge.](https://github.com/Azure/azure-iotedge/releases) È anche possibile scaricare questo script o una versione dello script da una versione specifica per eseguire i comandi di installazione nel dispositivo IoT Edge.

Lo script fornito è firmato per aumentare la sicurezza. È possibile verificare la firma scaricando lo script nel dispositivo e quindi eseguendo il comando di PowerShell seguente:You can verify the signature by downloading the script to your device then running the following PowerShell command:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Lo stato di output è **Valido** se la firma è verificata.

## <a name="all-installation-parameters"></a>Tutti i parametri di installazione

Nelle sezioni precedenti sono stati introdotti alcuni scenari di installazione comuni con esempi di come usare i parametri per modificare lo script di installazione. In questa sezione vengono fornite le tabelle di riferimento dei parametri comuni utilizzati per installare, aggiornare o disinstallare IoT Edge.

### <a name="deploy-iotedge"></a>Distribuzione-IoTEdge

Il comando Deploy-IoTEdge scarica e distribuisce il daemon di sicurezza Edge IoT e le relative dipendenze. Il comando deployment accetta questi parametri comuni, tra gli altri. Per l'elenco completo, `Get-Help Deploy-IoTEdge -full`utilizzare il comando .  

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo contenitore, Windows è il valore predefinito.<br><br>Per i contenitori windows, IoT Edge usa il motore contenitore moby incluso nell'installazione. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **Proxy** | URL proxy | Includere questo parametro se il dispositivo deve passare attraverso un server proxy per accedere a Internet. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Percorso directory | Se questo parametro è incluso, il programma di installazione controllerà la directory elencata per i file CAB IoT Edge e VC Runtime MSI necessari per l'installazione. Tutti i file non trovati nella directory vengono scaricati. Se entrambi i file si trovano nella directory, è possibile installare IoT Edge senza una connessione Internet. È inoltre possibile utilizzare questo parametro per utilizzare una versione specifica. |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **RiavvioIfNeeded** | none | Questo flag consente allo script di distribuzione di riavviare il computer senza chiedere conferma, se necessario. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Il comando Initialize-IoTEdge configura IoT Edge con la stringa di connessione del dispositivo e i dettagli operativi. Gran parte delle informazioni generate da questo comando viene quindi memorizzata nel file iotedge.config.yaml. Il comando di inizializzazione accetta questi parametri comuni, tra gli altri. Per l'elenco completo, `Get-Help Initialize-IoTEdge -full`utilizzare il comando .

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **Manuale** | nessuno | **Parametro switch**. Se non viene specificato alcun tipo di provisioning, manuale è il valore predefinito.<br><br>Dichiara che si fornirà una stringa di connessione del dispositivo per effettuare il provisioning del dispositivo manualmente. |
| **Dps** | nessuno | **Parametro switch**. Se non viene specificato alcun tipo di provisioning, manuale è il valore predefinito.<br><br>Dichiara che si forniranno un ID ambito del servizio Device Provisioning (DPS) e l'ID registrazione del dispositivo per effettuare il provisioning tramite DPS.  |
| **DeviceConnectionString** | Una stringa di connessione da un dispositivo IoT Edge registrato in un hub IoT, tra virgolette singole | **Obbligatorio** per il provisioning manuale. Se non si specifica una stringa di connessione nei parametri dello script, ne verrà richiesta una. |
| **ScopeId** | Un ID ambito da un'istanza del servizio Device Provisioning associata all'hub IoT. | **Obbligatorio** per il provisioning DPS. Se non si specifica un ID ambito nei parametri dello script, ne verrà richiesto uno. |
| **ID registrazione** | Un ID registrazione generato dal dispositivo | **Obbligatorio** per il provisioning DPS se si utilizza il TPM o l'attestazione della chiave simmetrica. **Facoltativo** se si utilizza l'attestazione del certificato X.509.Optional if using X.509 certificate attestation. |
| **X509IdentityCertificate** | Percorso URI del certificato di identità del dispositivo X.509 nel dispositivo. | **Obbligatorio** per il provisioning DPS se si utilizza l'attestazione del certificato X.509. |
| **X509IdentityPrivateKey (chiave X509IdentityPrivateKey)** | Percorso URI della chiave del certificato di identità del dispositivo X.509 nel dispositivo. | **Obbligatorio** per il provisioning DPS se si utilizza l'attestazione del certificato X.509. |
| **SymmetricKey** | Chiave simmetrica utilizzata per eseguire il provisioning dell'identità del dispositivo IoT Edge quando si usa DPS | **Obbligatorio** per il provisioning DPS se si utilizza l'attestazione della chiave simmetrica. |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo contenitore, Windows è il valore predefinito.<br><br>Per i contenitori windows, IoT Edge usa il motore contenitore moby incluso nell'installazione. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI dell'immagine dell'agente IoT Edge | Per impostazione predefinita, una nuova installazione di IoT Edge usa il tag di versioni più recente per l'immagine dell'agente IoT Edge. Usare questo parametro per impostare un tag specifico per la versione dell'immagine o per fornire la propria immagine dell'agente. Per altre informazioni, vedere [Informazioni sui tag di IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome utente** | Nome utente del registro contenitori | Usare questo parametro solo se si imposta il parametro -AgentImage su un contenitore in un registro privato. Specificare un nome utente con accesso al registro. |
| **Password** | Stringa della password di protezione | Usare questo parametro solo se si imposta il parametro -AgentImage su un contenitore in un registro privato. Specificare la password per accedere al registro. |

### <a name="update-iotedge"></a>Aggiornamento-IoTEdge

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo contenitore, Windows è il valore predefinito. Per i contenitori Windows, nell'installazione verrà incluso un motore del contenitore. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **Proxy** | URL proxy | Includere questo parametro se il dispositivo deve passare attraverso un server proxy per accedere a Internet. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Percorso directory | Se questo parametro è incluso, il programma di installazione controllerà la directory elencata per i file CAB IoT Edge e VC Runtime MSI necessari per l'installazione. Tutti i file non trovati nella directory vengono scaricati. Se entrambi i file si trovano nella directory, è possibile installare IoT Edge senza una connessione Internet. È inoltre possibile utilizzare questo parametro per utilizzare una versione specifica. |
| **RiavvioIfNeeded** | none | Questo flag consente allo script di distribuzione di riavviare il computer senza chiedere conferma, se necessario. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **Forza** | none | Questo flag forza la disinstallazione nel caso in cui il tentativo precedente di disinstallazione non sia riuscito.
| **RiavvioIfNeeded** | none | Questo flag consente allo script di disinstallazione di riavviare il computer senza chiedere conferma, se necessario. |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

In caso di problemi che impediscono di installare correttamente IoT Edge, vedere la pagina relativa alla [risoluzione dei problemi](troubleshoot.md).

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
