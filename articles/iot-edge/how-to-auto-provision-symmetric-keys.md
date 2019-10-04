---
title: Eseguire automaticamente il provisioning dei dispositivi con DPS usando l'attestazione della chiave simmetrica-Azure IoT Edge | Microsoft Docs
description: Usare l'attestazione della chiave simmetrica per testare il provisioning automatico dei dispositivi per Azure IoT Edge con il servizio Device provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 10/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 53b1abca25119f4168aaf12a66c4347c53ed0a62
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828070"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Creare ed effettuare il provisioning di un dispositivo IoT Edge usando l'attestazione della chiave simmetrica

È possibile effettuare il provisioning automatico di dispositivi Azure IoT Edge tramite il [servizio Device Provisioning](../iot-dps/index.yml) esattamente come per i dispositivi non abilitati per Edge. Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare.

Questo articolo illustra come creare un servizio Device provisioning singola registrazione con l'attestazione della chiave simmetrica in un dispositivo IoT Edge con i passaggi seguenti:

* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare una singola registrazione per il dispositivo.
* Installare il runtime di IoT Edge e connettersi all'hub Internet.

L'attestazione con chiave simmetrica costituisce un approccio semplice per autenticare un dispositivo con un'istanza del servizio Device Provisioning. Questo metodo di attestazione rappresenta un'esperienza di "Hello World" per gli sviluppatori che non hanno familiarità con il provisioning dei dispositivi o che non possiedono requisiti di sicurezza restrittivi. L'attestazione del dispositivo che usa un [TPM](../iot-dps/concepts-tpm-attestation.md) o [certificati X. 509](../iot-dps/concepts-security.md#x509-certificates) è più sicura e deve essere usata per requisiti di sicurezza più rigorosi.

## <a name="prerequisites"></a>Prerequisiti

* Un hub tutto attivo
* Un dispositivo fisico o virtuale

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Scegliere un ID di registrazione univoco per il dispositivo

È necessario definire un ID di registrazione univoco per identificare ogni dispositivo. È possibile usare l'indirizzo MAC, il numero di serie o informazioni univoche dal dispositivo.

In questo esempio si usa una combinazione di indirizzo MAC e numero di serie che formano la stringa seguente per un ID di registrazione.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Creare un ID di registrazione univoco per il dispositivo. I caratteri validi sono i caratteri alfanumerici minuscoli e il trattino ("-").

## <a name="create-a-dps-enrollment"></a>Creare una registrazione nel servizio Device Provisioning

Usare l'ID di registrazione del dispositivo per creare una registrazione singola in DPS.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md).

> [!TIP]
> Le registrazioni di gruppo sono possibili anche quando si usa l'attestazione della chiave simmetrica e si prevedono le stesse decisioni delle singole registrazioni.

1. Nella [portale di Azure](https://portal.azure.com)passare all'istanza del servizio Device provisioning in hub Internet.

1. In **le impostazioni** selezionare **Gestisci registrazioni**.

1. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   1. Per **meccanismo**selezionare **chiave simmetrica**.

   1. Selezionare la casella **di controllo genera chiavi automaticamente** .

   1. Fornire l' **ID di registrazione** creato per il dispositivo.

   1. Se lo si desidera, specificare un **ID dispositivo dell'hub** Internet per il dispositivo. È possibile usare gli ID dispositivo per identificare come destinazione un singolo dispositivo per la distribuzione di moduli. Se non si specifica un ID dispositivo, viene usato l'ID di registrazione.

   1. Selezionare **true** per dichiarare che la registrazione è per un dispositivo IOT Edge. Per la registrazione di un gruppo, tutti i dispositivi devono essere IoT Edge dispositivi o nessuno di essi può essere.

   1. Accettare il valore predefinito dal criterio di allocazione del servizio Device provisioning per la **modalità di assegnazione dei dispositivi agli hub** o scegliere un valore diverso specifico per questa registrazione.

   1. Scegliere l'**hub IoT** collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di allocazione selezionati.

   1. Scegliere il modo in cui **si vogliono gestire i dati del dispositivo** durante il nuovo provisioning quando i dispositivi richiedono il provisioning dopo la prima volta.

   1. Aggiungere un valore di tag allo **stato dispositivo gemello iniziale**, se si desidera. È possibile usare tag per identificare come destinazione gruppi di dispositivi per la distribuzione di moduli. Esempio:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Assicurarsi che **Abilita voce** sia impostato su **Abilita**.

   1. Selezionare **Salva**.

Ora che è presente una registrazione per questo dispositivo, il runtime di IoT Edge può effettuare automaticamente il provisioning del dispositivo durante l'installazione. Assicurarsi di copiare il valore della **chiave primaria** di registrazione da usare quando si installa il runtime di IOT Edge o se si intende creare chiavi di dispositivo da usare con la registrazione di un gruppo.

## <a name="derive-a-device-key"></a>Derivare una chiave di dispositivo

> [!NOTE]
> Questa sezione è obbligatoria solo se si usa una registrazione di gruppo.

Ogni dispositivo usa la chiave del dispositivo derivata con l'ID di registrazione univoco per eseguire l'attestazione della chiave simmetrica con la registrazione durante il provisioning. Per generare la chiave del dispositivo, usare la chiave copiata dalla registrazione di DPS per calcolare un [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dell'ID registrazione univoco per il dispositivo e convertire il risultato in formato Base64.

Non includere la chiave primaria o secondaria della registrazione nel codice del dispositivo.

### <a name="linux-workstations"></a>Workstation di Linux

Se si usa una workstation di Linux, è possibile usare openssl per generare la chiave di dispositivo derivata come illustrato nell'esempio seguente.

Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

Sostituire il valore di **REG_ID** con l'ID registrazione del dispositivo.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Workstation basate su Windows

Se si usan una workstation basata su Windows, è possibile usare PowerShell per generare le chiavi di dispositivo derivate come illustrato nell'esempio seguente.

Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

Sostituire il valore di **REG_ID** con l'ID registrazione del dispositivo.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale.

Quando si esegue il provisioning del dispositivo, sono necessarie le informazioni seguenti:

* Valore dell' **ambito dell'ID** DPS
* ID di **registrazione** del dispositivo creato
* **Chiave primaria** copiata dalla registrazione DPS

> [!TIP]
> Per le registrazioni di gruppo, è necessaria la [chiave derivata](#derive-a-device-key) di ogni dispositivo invece della chiave di registrazione DPS.

### <a name="linux-device"></a>Dispositivo Linux

Seguire le istruzioni per l'architettura del dispositivo. Assicurarsi di configurare il runtime IoT Edge per il provisioning automatico, non manuale.

[Installare il runtime di Azure IoT Edge in Linux](how-to-install-iot-edge-linux.md)

La sezione nel file di configurazione per il provisioning di chiavi simmetriche è simile alla seguente:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

Sostituire i valori segnaposto `{scope_id}`per `{registration_id}`, e `{symmetric_key}` con i dati raccolti in precedenza.

### <a name="windows-device"></a>Dispositivo Windows

Installare il runtime di IoT Edge nel dispositivo per cui è stata generata una chiave del dispositivo derivata. Il runtime di IoT Edge verrà configurato per il provisioning automatico, non manuale.

Per informazioni più dettagliate sull'installazione di IoT Edge in Windows, inclusi i prerequisiti e le istruzioni per attività come la gestione dei contenitori e l'aggiornamento IoT Edge, vedere [installare il Azure IOT Edge Runtime in Windows](how-to-install-iot-edge-windows.md).

1. Aprire una finestra di PowerShell in modalità amministratore. Assicurarsi di usare una sessione AMD64 di PowerShell durante l'installazione di IoT Edge, non di PowerShell (x86).

1. Il comando **deploy-IoTEdge** verifica che il computer Windows si trovi in una versione supportata, attiva la funzionalità contenitori e quindi Scarica il runtime di Moby e il IOT Edge Runtime. Per impostazione predefinita, il comando usa i contenitori di Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. A questo punto, i dispositivi core per le cose possono riavviarsi automaticamente. Altri dispositivi Windows 10 o Windows Server possono richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. Per impostazione predefinita, il comando esegue il provisioning manuale con i contenitori di Windows a meno che non si usi il flag `-Dps` per usare il provisioning automatico.

   Sostituire i valori segnaposto `{scope_id}`per `{registration_id}`, e `{symmetric_key}` con i dati raccolti in precedenza.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se il runtime è stato avviato correttamente, è possibile passare all'hub IoT e iniziare la distribuzione di moduli IoT Edge nel dispositivo. Usare i comandi seguenti sul dispositivo per verificare che il runtime sia stato installato e avviato correttamente.

### <a name="linux-device"></a>Dispositivo Linux

Verificare lo stato del servizio IoT Edge.

```cmd/sh
systemctl status iotedge
```

Esaminare i log del servizio.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Elencare i moduli in esecuzione.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Dispositivo Windows

Verificare lo stato del servizio IoT Edge.

```powershell
Get-Service iotedge
```

Esaminare i log del servizio.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Elencare i moduli in esecuzione.

```powershell
iotedge list
```

È possibile verificare che sia stata usata la registrazione singola creata nel servizio Device provisioning. Passare all'istanza del servizio Device provisioning nel portale di Azure. Aprire i dettagli di registrazione per la registrazione singola creata. Si noti che lo stato della registrazione viene **assegnato** e viene elencato l'ID del dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come [Distribuire e monitorare i moduli IoT Edge su larga scala tramite il portale di Azure](how-to-deploy-monitor.md) o usando l'[interfaccia della riga di comando di Azure](how-to-deploy-monitor-cli.md).
