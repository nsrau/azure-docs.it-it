---
title: Installare Azure IoT Edge in Windows | Microsoft Docs
description: Istruzioni per l'installazione di Azure IoT Edge in Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: e5b99bba3c3b21ea9662845928c523c329695bf8
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877246"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Installare il runtime di Azure IoT Edge in Windows

Il runtime di Azure IoT Edge è ciò che trasforma un dispositivo in un dispositivo IoT Edge. Il runtime può essere distribuito nei dispositivi di dimensioni pari a un server industriale o a un dispositivo Raspberry Pi. Dopo aver configurato un dispositivo con il runtime di IoT Edge, è possibile avviare la distribuzione della logica di business dal cloud. 

Per altre informazioni sul funzionamento del runtime di IoT Edge, vedere [Informazioni sul runtime di Azure IoT Edge e sulla relativa architettura](iot-edge-runtime.md).

Questo articolo elenca i passaggi per installare il runtime di Azure IoT Edge nel sistema Windows x64 (AMD/Intel) usando i contenitori di Windows.

> [!NOTE]
> Un problema noto del sistema operativo Windows impedisce alla transizione di sospendere e ibernare gli Stati di alimentazione quando vengono eseguiti IoT Edge moduli (contenitori di Windows nano server con isolamento del processo). Questo problema influisca sulla durata della batteria sul dispositivo.
>
> Come soluzione alternativa, usare il comando `Stop-Service iotedge` per arrestare i moduli IOT Edge in esecuzione prima di usare questi stati di alimentazione. 

Una configurazione con contenitori Linux su sistemi Windows non è una configurazione di produzione consigliata o supportata per Azure IoT Edge. È tuttavia possibile usarla a scopo di sviluppo e test. Per altre informazioni, vedere [usare IOT Edge in Windows per eseguire i contenitori Linux](how-to-install-iot-edge-windows-with-linux.md).

Per informazioni su ciò che è incluso nella versione più recente di IoT Edge, vedere [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Prerequisiti

Usare questa sezione per verificare se il dispositivo Windows in uso può supportare IoT Edge e per prepararlo per un motore del contenitore prima dell'installazione. 

### <a name="supported-windows-versions"></a>Versioni supportate di Windows

Per gli scenari di sviluppo e test, è possibile installare Azure IoT Edge con i contenitori di Windows in qualsiasi versione di Windows 10 o Windows Server 2019 (Build 17763) che supporta la funzionalità dei contenitori. Per informazioni sui sistemi operativi attualmente supportati per gli scenari di produzione, vedere [Azure IOT Edge sistemi supportati](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Preparare il dispositivo per un motore del contenitore 

Azure IoT Edge si basa su un motore del contenitore [compatibile con OCI](https://www.opencontainers.org/). Per gli scenari di produzione, usare il motore Moby incluso nello script di installazione per eseguire contenitori Windows nel dispositivo Windows in uso. 

## <a name="install-iot-edge-on-a-new-device"></a>Installare IoT Edge in un nuovo dispositivo

>[!NOTE]
>I pacchetti software di Azure IoT Edge sono soggetti alle condizioni di licenza riportate nei pacchetti, all'interno della directory LICENSE. Prima di usare il pacchetto, leggere le condizioni di licenza. L'installazione e l'uso del pacchetto costituiscono accettazione di tali condizioni. Se non si accettano le condizioni di licenza, non usare il pacchetto.

Uno script di PowerShell scarica e installa il daemon di sicurezza di Azure IoT Edge. Il daemon di sicurezza avvia quindi il primo di due moduli di runtime, l'agente IoT Edge, che consente distribuzioni remote di altri moduli. 

>[!TIP]
>Per i dispositivi principali, è consigliabile eseguire i comandi di installazione usando una sessione RemotePowerShell. Per altre informazioni, vedere [uso di PowerShell per Windows](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell).

Quando si installa il runtime di IoT Edge per la prima volta in un dispositivo, è necessario effettuare il provisioning del dispositivo con un'identità da un hub IoT. È possibile eseguire manualmente il provisioning di un singolo dispositivo IoT Edge usando una stringa di connessione del dispositivo fornita dall'hub. In alternativa, è possibile usare il servizio Device provisioning (DPS) per eseguire automaticamente il provisioning dei dispositivi, che risulta utile quando si dispone di molti dispositivi da configurare. A seconda del provisioning, scegliere lo script di installazione appropriato. 

Le sezioni seguenti descrivono i casi d'uso comuni e i parametri per lo script di installazione di IoT Edge in un nuovo dispositivo. 

### <a name="option-1-install-and-manually-provision"></a>Opzione 1: Effettuare il provisioning e l'installazione manualmente

In questa prima opzione è possibile specificare una **stringa di connessione del dispositivo** generata dall'hub Internet per eseguire il provisioning del dispositivo. 

In questo esempio viene illustrata un'installazione manuale con i contenitori di Windows:

1. Se non è già stato fatto, registrare un nuovo dispositivo IoT Edge e recuperare la **stringa di connessione del dispositivo**. Copiare la stringa di connessione da usare più avanti in questa sezione. Per completare questo passaggio, è possibile usare gli strumenti seguenti:

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

3. Il comando **deploy-IoTEdge** verifica che il computer Windows si trovi in una versione supportata, attiva la funzionalità contenitori e quindi Scarica il runtime di Moby e il IOT Edge Runtime. Per impostazione predefinita, il comando usa i contenitori di Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. A questo punto, i dispositivi core per le cose possono riavviarsi automaticamente. Altri dispositivi Windows 10 o Windows Server possono richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

5. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando richiesto, specificare la stringa di connessione del dispositivo recuperata nel passaggio 1. La stringa di connessione del dispositivo associa il dispositivo fisico a un ID dispositivo nell'hub. 

   La stringa di connessione del dispositivo ha il formato seguente e non deve includere le virgolette:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Usare la procedura descritta in verificare che l'installazione sia stata [eseguita correttamente](#verify-successful-installation) per verificare lo stato del IOT Edge nel dispositivo. 

Quando si installa e si effettua il provisioning di un dispositivo manualmente, è possibile usare ulteriori parametri per modificare l'installazione tra cui:

* Indirizzare il traffico attraverso un server proxy
* Fare in modo che il programma di installazione punti a una directory offline
* Dichiarare un'immagine del contenitore di un agente specifico e fornire le credenziali se si trova in un registro privato

Per ulteriori informazioni su queste opzioni di installazione, passare a informazioni su [tutti i parametri di installazione](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opzione 2: Installare ed effettuare automaticamente il provisioning

In questa seconda opzione viene effettuato il provisioning del dispositivo con il servizio Device Provisioning in hub IoT. Fornire l'**ID ambito** da un'istanza del servizio Device Provisioning e l'**ID registrazione** dal dispositivo. È possibile che vengano richiesti valori aggiuntivi in base al meccanismo di attestazione durante il provisioning con DPS, ad esempio quando si usano [chiavi simmetriche](how-to-auto-provision-symmetric-keys.md).

Nell'esempio seguente viene illustrata un'installazione automatica con i contenitori di Windows e l'attestazione TPM:

1. Seguire la procedura descritta in creare ed effettuare il provisioning di [un TPM simulato IOT Edge dispositivo in Windows](how-to-auto-provision-simulated-device-windows.md) per configurare il servizio Device provisioning e recuperare il relativo **ID ambito**, simulare un dispositivo TPM e recuperare il relativo **ID di registrazione**, quindi creare un utente singolo iscrizione. Dopo aver registrato il dispositivo nell'hub Internet, continuare con questi passaggi di installazione.  

   >[!TIP]
   >Mantenere aperta la finestra di esecuzione del simulatore TPM durante l'installazione e il test. 

1. Eseguire PowerShell come amministratore.

   >[!NOTE]
   >Usare una sessione AMD64 di PowerShell per installare IoT Edge, non PowerShell (x86). Se non si conosce il tipo di sessione in uso, eseguire questo comando:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

1. Il comando **deploy-IoTEdge** verifica che il computer Windows si trovi in una versione supportata, attiva la funzionalità contenitori e quindi Scarica il runtime di Moby e il IOT Edge Runtime. Per impostazione predefinita, il comando usa i contenitori di Windows. 

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. A questo punto, i dispositivi core per le cose possono riavviarsi automaticamente. Altri dispositivi Windows 10 o Windows Server possono richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. L'impostazione predefinita del comando è provisioning manuale con i contenitori di Windows. Usare il `-Dps` flag per usare il servizio Device provisioning anziché il provisioning manuale. Sostituire `{scope ID}` con l'ID ambito del servizio Device provisioning e `{registration ID}` con l'ID registrazione del dispositivo, entrambi recuperati nel passaggio 1.

   Usare il comando **Initialize-IoTEdge** per usare DPS con attestazione TPM:

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID}
   ```

   Usare il comando **Initialize-IoTEdge** per usare DPS con attestazione della chiave simmetrica. Sostituire `{symmetric key}` con una chiave del dispositivo.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

1. Usare la procedura descritta in verificare che l'installazione sia stata [eseguita correttamente](#verify-successful-installation) per verificare lo stato del IOT Edge nel dispositivo. 

Quando si installa e si effettua il provisioning di un dispositivo manualmente, è possibile usare ulteriori parametri per modificare l'installazione tra cui:

* Indirizzare il traffico attraverso un server proxy
* Fare in modo che il programma di installazione punti a una directory offline
* Dichiarare un'immagine del contenitore di un agente specifico e fornire le credenziali se si trova in un registro privato

Per altre informazioni su queste opzioni di installazione, continuare a leggere questo articolo oppure passare direttamente a [Tutti i parametri di installazione](#all-installation-parameters).

## <a name="offline-installation"></a>Installazione offline

Durante l'installazione vengono scaricati due file:

* Microsoft Azure IoT Edge CAB, che contiene il daemon di sicurezza IoT Edge (iotedged), il motore di contenitori di Moby e l'interfaccia della riga di comando di Moby.
* MSI di Visual C++ Redistributable Package (runtime di VC)

È possibile scaricare uno o entrambi i file in anticipo nel dispositivo, quindi puntare lo script di installazione nella directory che contiene i file. Il programma di installazione verifica innanzitutto il contenuto della directory e quindi scarica solo i componenti che non sono già presenti. Se tutti i file sono disponibili offline, è possibile eseguire l'installazione senza connessione Internet. È anche possibile usare questa funzionalità per installare una versione specifica dei componenti.  

Per i file di installazione IoT Edge più recenti insieme alle versioni precedenti, vedere [Azure IOT Edge versioni](https://github.com/Azure/azure-iotedge/releases).

Per eseguire l'installazione con componenti offline, `-OfflineInstallationPath` usare il parametro come parte del comando deploy-IoTEdge e fornire il percorso assoluto alla directory di file. Ad esempio,

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

È anche possibile usare il parametro percorso di installazione offline con il comando Update-IoTEdge, introdotto più avanti in questo articolo. 

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Verificare lo stato del servizio IoT Edge. Deve essere indicato come in esecuzione.  

```powershell
Get-Service iotedge
```

Esaminare i log del servizio generati negli ultimi cinque minuti. Se è stata appena completata l'installazione del runtime di IoT Edge, è possibile che venga visualizzato un elenco di errori dal tempo tra l'esecuzione di **deploy-IoTEdge** e **Initialize-IoTEdge**. Questi errori sono previsti, perché il servizio sta provando ad avviarsi prima di essere configurato. 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Elencare i moduli in esecuzione. Dopo una nuova installazione, l'unico modulo che dovrebbe essere in esecuzione è **edgeAgent**. Dopo aver [distribuito i moduli IOT Edge](how-to-deploy-modules-portal.md) per la prima volta, l'altro modulo di sistema, **edgeHub**, si avvierà anche sul dispositivo. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gestire i contenitori dei moduli

Il servizio IoT Edge richiede un motore di contenitori in esecuzione nel dispositivo. Quando si distribuisce un modulo in un dispositivo, il runtime di IoT Edge usa il motore di contenitori per eseguire il pull dell'immagine del contenitore da un registro nel cloud. Il servizio IoT Edge consente di interagire con i moduli e recuperare i log, ma a volte può essere utile usare il motore di contenitori per interagire con il contenitore stesso. 

Per ulteriori informazioni sui concetti relativi ai moduli, vedere informazioni sui [moduli Azure IOT Edge](iot-edge-modules.md). 

Se si eseguono contenitori di Windows nel dispositivo Windows IoT Edge, l'installazione di IoT Edge include il motore di contenitori di Moby. Il motore Moby si basa sugli stessi standard di Docker ed è stato progettato per essere eseguito in parallelo nello stesso computer del desktop docker. Per questo motivo, se si desidera destinare i contenitori gestiti dal motore di Moby, è necessario specificare in modo specifico il motore anziché docker. 

Per elencare tutte le immagini Docker, ad esempio, usare il comando seguente:

```powershell
docker images
```

Per elencare tutte le immagini di Moby, modificare lo stesso comando con un puntatore al motore di Moby: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

L'URI del motore è elencato nell'output dello script di installazione, oppure è possibile trovarlo nella sezione delle impostazioni di runtime del contenitore per il file config. yaml. 

![URI moby_runtime in config. YAML](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Per altre informazioni sui comandi che è possibile usare per interagire con i contenitori e le immagini in esecuzione nel dispositivo, vedere [interfacce della riga di comando](https://docs.docker.com/engine/reference/commandline/docker/)di Docker.

## <a name="update-an-existing-installation"></a>Aggiornare un'installazione esistente

Se è già stato installato il runtime di IoT Edge in un dispositivo prima di e ne è stato effettuato il provisioning con un'identità dall'hub Internet, è possibile aggiornare il runtime senza dover immettere nuovamente le informazioni sul dispositivo. 

Per altre informazioni, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).

Questo esempio mostra un'installazione che punta a un file di configurazione esistente e usa i contenitori Windows: 

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Quando si aggiorna IoT Edge, è possibile usare parametri aggiuntivi per modificare l'aggiornamento, tra cui:

* Indirizzare il traffico per passare attraverso un server proxy o
* Fare in modo che il programma di installazione punti a una directory offline 
* Riavvio senza richiesta, se necessario

Non è possibile dichiarare un'immagine del contenitore di IoT Edge Agent con parametri di script perché tali informazioni sono già impostate nel file di configurazione dell'installazione precedente. Se si vuole modificare l'immagine del contenitore dell'agente, eseguire questa operazione nel file config.yaml. 

Per ulteriori informazioni su queste opzioni di aggiornamento, utilizzare il `Get-Help Update-IoTEdge -full` comando o fare riferimento a [tutti i parametri di installazione](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Disinstallare IoT Edge

Per rimuovere l'installazione di IoT Edge da un dispositivo Windows, usare il comando seguente da una finestra amministrativa di PowerShell. Questo comando rimuove il runtime di IoT Edge, insieme alla configurazione esistente e ai dati del motore Moby. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

Il comando Uninstall-IoTEdge non funziona in Windows Internet core. Per rimuovere IoT Edge dai dispositivi principali di Windows, è necessario ridistribuire l'immagine principale di Windows. 

Per ulteriori informazioni sulle opzioni di disinstallazione, utilizzare il comando `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Tutti i parametri di installazione

Nelle sezioni precedenti sono stati introdotti alcuni scenari di installazione comuni con esempi di come usare i parametri per modificare lo script di installazione. In questa sezione vengono fornite le tabelle di riferimento dei parametri comuni utilizzati per installare, aggiornare o disinstallare IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

Il comando deploy-IoTEdge Scarica e distribuisce il daemon di sicurezza IoT Edge e le relative dipendenze. Il comando di distribuzione accetta questi parametri comuni, tra gli altri. Per l'elenco completo, usare il comando `Get-Help Deploy-IoTEdge -full`.  

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo contenitore, il valore predefinito è Windows.<br><br>Per i contenitori di Windows, IoT Edge usa il motore di contenitori di Moby incluso nell'installazione. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **Proxy** | URL proxy | Includere questo parametro se il dispositivo deve passare attraverso un server proxy per accedere a Internet. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Percorso directory | Se questo parametro è incluso, il programma di installazione controllerà la directory elencata per i file MSI IoT Edge CAB e VC runtime necessari per l'installazione. Tutti i file non trovati nella directory vengono scaricati. Se entrambi i file si trovano nella directory, è possibile installare IoT Edge senza una connessione Internet. È anche possibile usare questo parametro per usare una versione specifica. |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | none | Questo flag consente allo script di distribuzione di riavviare il computer senza chiedere conferma, se necessario. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Il comando Initialize-IoTEdge configura IoT Edge con la stringa di connessione del dispositivo e i dettagli operativi. Gran parte delle informazioni generate da questo comando viene quindi archiviata nel file iotedge\config.yaml. Il comando di inizializzazione accetta questi parametri comuni, tra gli altri. Per l'elenco completo, usare il comando `Get-Help Initialize-IoTEdge -full`. 

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **Manuale** | Nessuna | **Parametro opzionale**. Se non viene specificato alcun tipo di provisioning, Manual è il valore predefinito.<br><br>Dichiara che si fornirà una stringa di connessione del dispositivo per effettuare il provisioning del dispositivo manualmente. |
| **Dps** | Nessuna | **Parametro opzionale**. Se non viene specificato alcun tipo di provisioning, Manual è il valore predefinito.<br><br>Dichiara che si forniranno un ID ambito del servizio Device Provisioning (DPS) e l'ID registrazione del dispositivo per effettuare il provisioning tramite DPS.  |
| **DeviceConnectionString** | Una stringa di connessione da un dispositivo IoT Edge registrato in un hub IoT, tra virgolette singole | **Obbligatorio** per l'installazione manuale. Se non si specifica una stringa di connessione nei parametri di script, ne verrà richiesta una durante l'installazione. |
| **ScopeId** | Un ID ambito da un'istanza del servizio Device Provisioning associata all'hub IoT. | **Obbligatorio** per l'installazione di DPS. Se non si specifica un ID ambito nei parametri di script, ne verrà richiesto uno durante l'installazione. |
| **RegistrationId** | Un ID registrazione generato dal dispositivo | **Obbligatorio** per l'installazione di DPS. |
| **SymmetricKey** | Chiave simmetrica usata per eseguire il provisioning dell'identità del dispositivo IoT Edge quando si usa DPS | **Obbligatorio** per l'installazione di DPS se si usa l'attestazione della chiave simmetrica. |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo contenitore, il valore predefinito è Windows.<br><br>Per i contenitori di Windows, IoT Edge usa il motore di contenitori di Moby incluso nell'installazione. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI dell'immagine dell'agente IoT Edge | Per impostazione predefinita, una nuova installazione di IoT Edge usa il tag di versioni più recente per l'immagine dell'agente IoT Edge. Usare questo parametro per impostare un tag specifico per la versione dell'immagine o per fornire la propria immagine dell'agente. Per altre informazioni, vedere [Informazioni sui tag di IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome utente** | Nome utente del registro contenitori | Usare questo parametro solo se si imposta il parametro -AgentImage su un contenitore in un registro privato. Specificare un nome utente con accesso al registro. |
| **Password** | Stringa della password di protezione | Usare questo parametro solo se si imposta il parametro -AgentImage su un contenitore in un registro privato. Specificare la password per accedere al registro. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo del contenitore, il valore predefinito è Windows. Per i contenitori Windows, nell'installazione verrà incluso un motore del contenitore. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **Proxy** | URL proxy | Includere questo parametro se il dispositivo deve passare attraverso un server proxy per accedere a Internet. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Percorso directory | Se questo parametro è incluso, il programma di installazione controllerà la directory elencata per i file MSI IoT Edge CAB e VC runtime necessari per l'installazione. Tutti i file non trovati nella directory vengono scaricati. Se entrambi i file si trovano nella directory, è possibile installare IoT Edge senza una connessione Internet. È anche possibile usare questo parametro per usare una versione specifica. |
| **RestartIfNeeded** | none | Questo flag consente allo script di distribuzione di riavviare il computer senza chiedere conferma, se necessario. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **Forzare** | none | Questo flag forza la disinstallazione nel caso in cui il tentativo precedente di disinstallazione non abbia avuto esito positivo. 
| **RestartIfNeeded** | none | Questo flag consente allo script di disinstallazione di riavviare il computer senza chiedere conferma, se necessario. |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver eseguito il provisioning del dispositivo IoT Edge con il runtime installato, è possibile [distribuire moduli IoT Edge](how-to-deploy-modules-portal.md).

In caso di problemi che impediscono di installare correttamente IoT Edge, vedere la pagina relativa alla [risoluzione dei problemi](troubleshoot.md).

Per aggiornare un'installazione esistente alla versione più recente di IoT Edge, vedere [Aggiornare il daemon di sicurezza e il runtime di IoT Edge](how-to-update-iot-edge.md).
