---
title: Script di Windows per Azure IoT Edge | Microsoft Docs
description: Informazioni di riferimento per gli script di IoT Edge PowerShell per l'installazione, la disinstallazione o l'aggiornamento nei dispositivi Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a082ccb62103ab5bd027bf49b9ee05bc48c63115
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979672"
---
# <a name="powershell-scripts-for-iot-edge-on-windows"></a>Script di PowerShell per IoT Edge in Windows

Informazioni sugli script di PowerShell per l'installazione, l'aggiornamento o la disinstallazione di IoT Edge nei dispositivi Windows.

I comandi descritti in questo articolo si trovano nel `IoTEdgeSecurityDaemon.ps1` file rilasciato con ogni [IOT Edge versione](https://github.com/Azure/azure-iotedge/releases). La versione più recente dello script è sempre disponibile in aka.ms/iotedge-win.

È possibile eseguire qualsiasi comando utilizzando il `Invoke-WebRequest` cmdlet per accedere alla versione più recente dello script. Ad esempio:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

È anche possibile scaricare questo script o una versione dello script da una versione specifica per eseguire i comandi. Ad esempio:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

Lo script specificato è firmato per aumentare la sicurezza. Per verificare la firma, scaricare lo script nel dispositivo e quindi eseguire il comando PowerShell seguente:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

Lo stato dell'output è **valido** se la firma viene verificata.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Il Deploy-IoTEdge comando Scarica e distribuisce il daemon di sicurezza IoT Edge e le relative dipendenze. Il comando di distribuzione accetta questi parametri comuni, tra gli altri. Per l'elenco completo, usare il comando `Get-Help Deploy-IoTEdge -full` .  

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo contenitore, il valore predefinito è Windows.<br><br>Per i contenitori di Windows, IoT Edge usa il motore di contenitori di Moby incluso nell'installazione. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **Proxy** | URL proxy | Includere questo parametro se il dispositivo deve passare attraverso un server proxy per accedere a Internet. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Percorso directory | Se questo parametro è incluso, il programma di installazione controllerà la directory elencata per i file MSI IoT Edge CAB e VC runtime necessari per l'installazione. Tutti i file non trovati nella directory vengono scaricati. Se entrambi i file si trovano nella directory, è possibile installare IoT Edge senza una connessione Internet. È anche possibile usare questo parametro per usare una versione specifica. |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | none | Questo flag consente allo script di distribuzione di riavviare il computer senza chiedere conferma, se necessario. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Il comando Initialize-IoTEdge configura IoT Edge con la stringa di connessione del dispositivo e i dettagli operativi. Gran parte delle informazioni generate da questo comando viene quindi archiviata nel file iotedge\config.yaml. Il comando di inizializzazione accetta questi parametri comuni, tra gli altri. Per l'elenco completo, usare il comando `Get-Help Initialize-IoTEdge -full` .

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ManualConnectionString** | nessuno | **Parametro switch**. **Valore predefinito**. Se non viene specificato alcun tipo di provisioning, il provisioning manuale con una stringa di connessione corrisponde al valore predefinito.<br><br>Dichiara che si fornirà una stringa di connessione del dispositivo per eseguire il provisioning manuale del dispositivo. |
| **ManualX509** | nessuno | **Parametro switch**. Se non viene specificato alcun tipo di provisioning, il provisioning manuale con una stringa di connessione corrisponde al valore predefinito.<br><br>Dichiara che si fornirà un certificato di identità e una chiave privata per eseguire il provisioning manuale del dispositivo.
| **DpsTpm** | nessuno | **Parametro switch**. Se non viene specificato alcun tipo di provisioning, il provisioning manuale con una stringa di connessione corrisponde al valore predefinito.<br><br>Dichiara che si forniranno un ID ambito del servizio Device Provisioning (DPS) e l'ID registrazione del dispositivo per effettuare il provisioning tramite DPS.  |
| **DpsSymmetricKey** | nessuno | **Parametro switch**. Se non viene specificato alcun tipo di provisioning, il provisioning manuale con una stringa di connessione corrisponde al valore predefinito.<br><br>Dichiara che si fornirà un ID ambito del servizio Device provisioning (DPS) e l'ID di registrazione del dispositivo per il provisioning tramite DPS, insieme a una chiave simmetrica per l'attestazione. |
| **DpsX509** | nessuno | **Parametro switch**. Se non viene specificato alcun tipo di provisioning, il provisioning manuale con una stringa di connessione corrisponde al valore predefinito.<br><br>Dichiara che si fornirà un ID ambito del servizio Device provisioning (DPS) e l'ID di registrazione del dispositivo per il provisioning tramite DPS, insieme a un certificato di identità X. 509 e a una chiave privata per l'attestazione.  |
| **DeviceConnectionString** | Una stringa di connessione da un dispositivo IoT Edge registrato in un hub IoT, tra virgolette singole | **Obbligatorio** per il provisioning manuale con una stringa di connessione. Se non si specifica una stringa di connessione nei parametri dello script, ne verrà richiesto uno. |
| **IotHubHostName** | Nome host dell'hub delle cose a cui si connette un dispositivo. | **Obbligatorio** per il provisioning manuale con certificati X. 509. Accetta il formato *{Hub Name}. Azure-Devices.NET*. |
| **DeviceId** | ID del dispositivo da un'identità del dispositivo registrato nell'hub Internet. | **Obbligatorio** per il provisioning manuale con certificati X. 509. |
| **ScopeId** | Un ID ambito da un'istanza del servizio Device Provisioning associata all'hub IoT. | **Obbligatorio** per il provisioning DPS. Se non si specifica un ID ambito nei parametri dello script, ne verrà richiesto uno. |
| **ID registrazione** | Un ID registrazione generato dal dispositivo | **Obbligatorio** per il provisioning di DPS se si usa il TPM o l'attestazione della chiave simmetrica. **Facoltativo** se si utilizza l'attestazione del certificato X. 509. |
| **X509IdentityCertificate** | Percorso URI del certificato di identità del dispositivo X. 509 nel dispositivo. | **Obbligatorio** per il provisioning manuale o DPS se si usa l'attestazione del certificato X. 509. |
| **X509IdentityPrivateKey** | Percorso URI della chiave del certificato di identità del dispositivo X. 509 nel dispositivo. | **Obbligatorio** per il provisioning manuale o DPS se si usa l'attestazione del certificato X. 509. |
| **SymmetricKey** | Chiave simmetrica usata per eseguire il provisioning dell'identità del dispositivo IoT Edge quando si usa DPS | **Obbligatorio** per il provisioning di DPS se si usa l'attestazione della chiave simmetrica. |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo contenitore, il valore predefinito è Windows.<br><br>Per i contenitori di Windows, IoT Edge usa il motore di contenitori di Moby incluso nell'installazione. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **DeviceCACertificate** | Percorso URI del certificato della CA del dispositivo X. 509 nel dispositivo. | Può anche essere configurato nel `C:\ProgramData\iotedge\config.yaml` file. Per altre informazioni, vedere [gestire i certificati in un dispositivo IOT Edge](how-to-manage-device-certificates.md). |
| **DeviceCAPrivateKey** | Percorso URI della chiave privata della CA del dispositivo X. 509 nel dispositivo. | Può anche essere configurato nel `C:\ProgramData\iotedge\config.yaml` file. Per altre informazioni, vedere [gestire i certificati in un dispositivo IOT Edge](how-to-manage-device-certificates.md). |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI dell'immagine dell'agente IoT Edge | Per impostazione predefinita, una nuova installazione di IoT Edge usa il tag di versioni più recente per l'immagine dell'agente IoT Edge. Usare questo parametro per impostare un tag specifico per la versione dell'immagine o per fornire la propria immagine dell'agente. Per altre informazioni, vedere [Informazioni sui tag di IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome utente** | Nome utente del registro contenitori | Usare questo parametro solo se si imposta il parametro -AgentImage su un contenitore in un registro privato. Specificare un nome utente con accesso al registro. |
| **Password** | Stringa della password di protezione | Usare questo parametro solo se si imposta il parametro -AgentImage su un contenitore in un registro privato. Specificare la password per accedere al registro. |

## <a name="update-iotedge"></a>Update-IoTEdge

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** o **Linux** | Se non viene specificato alcun sistema operativo del contenitore, il valore predefinito è Windows. Per i contenitori Windows, nell'installazione verrà incluso un motore del contenitore. Per i contenitori Linux, è necessario installare un motore del contenitore prima di avviare l'installazione. |
| **Proxy** | URL proxy | Includere questo parametro se il dispositivo deve passare attraverso un server proxy per accedere a Internet. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabella hash dei parametri e dei valori | Durante l'installazione vengono effettuate più richieste Web. Usare questo campo per impostare i parametri per queste richieste Web. Questo parametro è utile per configurare le credenziali per i server proxy. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Percorso directory | Se questo parametro è incluso, il programma di installazione controllerà la directory elencata per i file MSI IoT Edge CAB e VC runtime necessari per l'installazione. Tutti i file non trovati nella directory vengono scaricati. Se entrambi i file si trovano nella directory, è possibile installare IoT Edge senza una connessione Internet. È anche possibile usare questo parametro per usare una versione specifica. |
| **RestartIfNeeded** | none | Questo flag consente allo script di distribuzione di riavviare il computer senza chiedere conferma, se necessario. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parametro | Valori accettati | Commenti |
| --------- | --------------- | -------- |
| **Forzare** | none | Questo flag forza la disinstallazione nel caso in cui il tentativo precedente di disinstallazione non abbia avuto esito positivo.
| **RestartIfNeeded** | none | Questo flag consente allo script di disinstallazione di riavviare il computer senza chiedere conferma, se necessario. |

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare questi comandi negli articoli seguenti:

* [Installare o disinstallare il runtime di Azure IoT Edge](how-to-install-iot-edge.md)
* [Effettuare il provisioning di un dispositivo Azure IoT Edge con autenticazione con chiave simmetrica](how-to-manual-provision-symmetric-key.md)
* [Effettuare il provisioning di un dispositivo Azure IoT Edge con l'autenticazione del certificato X. 509](how-to-manual-provision-x509.md)
