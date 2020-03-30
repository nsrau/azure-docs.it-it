---
title: Effettuare il provisioning del dispositivo usando l'attestazione a chiave simmetrica - Azure IoT Edge
description: Usare l'attestazione a chiave simmetrica per testare il provisioning automatico dei dispositivi per Azure IoT Edge con il servizio Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 10/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d9944308d00c9cfecbd38a6443efb49913148806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535919"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Creare ed eseguire il provisioning di un dispositivo IoT Edge usando l'attestazione a chiave simmetricaCreate and provision an IoT Edge device using symmetric key attestation

I dispositivi Azure IoT Edge possono essere sottoposti a provisioning automatico usando il [servizio Device Provisioning](../iot-dps/index.yml) proprio come i dispositivi non abilitati per l'edge. Se non si ha familiarità con il processo di provisioning automatico, vedere [Concetti relativi al provisioning automatico](../iot-dps/concepts-auto-provisioning.md) prima di continuare.

Questo articolo illustra come creare una registrazione individuale del servizio Provisioning dispositivi usando l'attestazione della chiave simmetrica in un dispositivo Edge IoT con la procedura seguente:This article shows you how to create a Device Provisioning Service individual enrollment using symmetric key attestation on an IoT Edge device with the following steps:

* Creare un'istanza del servizio Device Provisioning in hub IoT.
* Creare una singola registrazione per il dispositivo.
* Installare il runtime IoT Edge e connettersi all'hub IoT.

L'attestazione con chiave simmetrica costituisce un approccio semplice per autenticare un dispositivo con un'istanza del servizio Device Provisioning. Questo metodo di attestazione rappresenta un'esperienza di "Hello World" per gli sviluppatori che non hanno familiarità con il provisioning dei dispositivi o che non possiedono requisiti di sicurezza restrittivi. L'attestazione del dispositivo che utilizza un certificato [TPM](../iot-dps/concepts-tpm-attestation.md) o [X.509](../iot-dps/concepts-security.md#x509-certificates) è più sicura e deve essere utilizzata per requisiti di sicurezza più rigorosi.

## <a name="prerequisites"></a>Prerequisiti

* Un hub IoT attivo
* Un dispositivo fisico o virtuale

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurare il servizio Device Provisioning in hub IoT di Azure

Creare una nuova istanza del servizio Device Provisioning in hub IoT in Azure e collegarla all'hub IoT. È possibile seguire le istruzioni riportate in [Configurare il servizio Device Provisioning in hub IoT](../iot-dps/quick-setup-auto-provision.md).

Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Scegliere un ID di registrazione univoco per il dispositivo

È necessario definire un ID di registrazione univoco per identificare ogni dispositivo. È possibile usare l'indirizzo MAC, il numero di serie o informazioni univoche dal dispositivo.

In questo esempio viene utilizzata una combinazione di indirizzo MAC e numero `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`di serie che forma la stringa seguente per un ID di registrazione: .

Creare un ID di registrazione univoco per il dispositivo. I caratteri validi sono i caratteri alfanumerici minuscoli e il trattino ("-").

## <a name="create-a-dps-enrollment"></a>Creare una registrazione nel servizio Device Provisioning

Utilizzare l'ID di registrazione del dispositivo per creare una singola registrazione in DPS.

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-monitor.md).

> [!TIP]
> Le iscrizioni ai gruppi sono possibili anche quando si usa l'attestazione a chiave simmetrica e si implicano le stesse decisioni delle singole iscrizioni.

1. Nel [portale di Azure](https://portal.azure.com)passare all'istanza del servizio di provisioning dei dispositivi dell'hub IoT.In the Azure portal , navigate to your instance of IoT Hub Device Provisioning Service.

1. In **le impostazioni** selezionare **Gestisci registrazioni**.

1. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   1. Per **Meccanismo**, selezionare **Chiave simmetrica**.

   1. Selezionare la casella di controllo **Genera automaticamente chiavi.**

   1. Specificare **l'ID di registrazione** creato per il dispositivo.

   1. Se lo desideri, fornisci un **ID dispositivo hub IoT** per il tuo dispositivo. È possibile usare gli ID dispositivo per identificare come destinazione un singolo dispositivo per la distribuzione di moduli. Se non fornisci un ID dispositivo, viene utilizzato l'ID di registrazione.

   1. Selezionare True per dichiarare che la registrazione è per un dispositivo IoT Edge.Select **True** to declare that the enrollment is for an IoT Edge device. Per una registrazione di gruppo, tutti i dispositivi devono essere dispositivi IoT Edge o nessuno di essi può essere.

   1. Accettare il valore predefinito dai criteri di allocazione del servizio Device Provisioning per **la modalità** di assegnazione dei dispositivi agli hub o scegliere un valore diverso specifico per questa registrazione.

   1. Scegliere l'**hub IoT** collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di allocazione selezionati.

   1. Scegli come vuoi che i **dati del dispositivo vengano gestiti durante** il nuovo provisioning quando i dispositivi richiedono il provisioning dopo la prima volta.

   1. Aggiungere un valore di tag allo **stato dispositivo gemello iniziale**, se si desidera. È possibile usare tag per identificare come destinazione gruppi di dispositivi per la distribuzione di moduli. Ad esempio:

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

Ora che esiste una registrazione per questo dispositivo, il runtime di IoT Edge può eseguire automaticamente il provisioning del dispositivo durante l'installazione. Assicurarsi di copiare il valore **della chiave primaria** della registrazione da usare durante l'installazione del runtime di IoT Edge o se si intende creare chiavi del dispositivo da usare con una registrazione di gruppo.

## <a name="derive-a-device-key"></a>Derivare una chiave di dispositivo

> [!NOTE]
> Questa sezione è necessaria solo se si usa una registrazione di gruppo.

Ogni dispositivo usa la chiave del dispositivo derivata con l'ID di registrazione univoco per eseguire l'attestazione della chiave simmetrica con la registrazione durante il provisioning. Per generare la chiave del dispositivo, utilizzare la chiave copiata dalla registrazione DPS per calcolare un [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) dell'ID di registrazione univoco per il dispositivo e convertire il risultato nel formato Base64.

Non includere la chiave primaria o secondaria della registrazione nel codice del dispositivo.

### <a name="linux-workstations"></a>Workstation di Linux

Se si usa una workstation di Linux, è possibile usare openssl per generare la chiave di dispositivo derivata come illustrato nell'esempio seguente.

Sostituire il valore della **CHIAVE** con la **chiave primaria** annotata in precedenza.

Sostituire il valore di **REG_ID** con l'ID di registrazione del dispositivo.

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

Sostituire il valore di **REG_ID** con l'ID di registrazione del dispositivo.

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

Quando si esegue il provisioning del dispositivo, sono necessarie le informazioni seguenti:You'll need the following information when provisioning your device:

* Il valore **dell'ambito ID** DPS
* ID **di registrazione** del dispositivo creato
* Chiave **primaria** copiata dalla registrazione DPS

> [!TIP]
> Per le registrazioni di gruppo, è necessaria la [chiave derivata](#derive-a-device-key) di ogni dispositivo anziché la chiave di registrazione DPS.

### <a name="linux-device"></a>Dispositivo Linux

Seguire le istruzioni per l'architettura del dispositivo. Assicurarsi di configurare il runtime IoT Edge per il provisioning automatico, non manuale.

[Installare il runtime di Azure IoT Edge in LinuxInstall the Azure IoT Edge runtime on Linux](how-to-install-iot-edge-linux.md)

La sezione nel file di configurazione per il provisioning della chiave simmetrica è simile alla seguente:The section in the configuration file for symmetric key provisioning looks like this:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "<SCOPE_ID>"
   attestation:
      method: "symmetric_key"
      registration_id: "<REGISTRATION_ID>"
      symmetric_key: "<SYMMETRIC_KEY>"
```

Sostituire i valori `<SCOPE_ID>` `<REGISTRATION_ID>`segnaposto `<SYMMETRIC_KEY>` per , e con i dati raccolti in precedenza. Assicurati che la riga **provisioning:** non contengono spazi vuoti precedenti e che gli elementi nidificati siano rientrati di due spazi.

### <a name="windows-device"></a>Dispositivo Windows

Installare il runtime IoT Edge nel dispositivo per il quale è stata generata una chiave del dispositivo derivata. Il runtime Di IoT Edge verrà configurato per il provisioning automatico, non manuale.

Per informazioni più dettagliate sull'installazione di Edge In Windows, inclusi prerequisiti e istruzioni per attività quali la gestione dei contenitori e l'aggiornamento di IoT Edge, vedere Installare il runtime di [Azure IoT Edge in Windows.](how-to-install-iot-edge-windows.md)

1. Aprire una finestra di PowerShell in modalità amministratore. Assicurarsi di usare una sessione AMD64 di PowerShell durante l'installazione di IoT Edge, non powerShell (x86).

1. Il comando **Deploy-IoTEdge** controlla che il computer Windows sia in una versione supportata, attiva la funzionalità dei contenitori e quindi scarica il runtime moby e il runtime di IoT Edge. Il comando utilizza per impostazione predefinita i contenitori di Windows.The default command defaults to using Windows containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. A questo punto, i dispositivi IoT Core potrebbero riavviarsi automaticamente. Altri dispositivi Windows 10 o Windows Server potrebbero richiedere il riavvio. In tal caso, riavviare il dispositivo ora. Quando il dispositivo è pronto, eseguire di nuovo PowerShell come amministratore.

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. Il comando per impostazione predefinita è il `-Dps` provisioning manuale con i contenitori di Windows, a meno che non si utilizzi il flag per usare il provisioning automatico.

   Sostituire i valori `{scope_id}` `{registration_id}`segnaposto `{symmetric_key}` per , e con i dati raccolti in precedenza.

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

Esaminare i registri dei servizi.

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

Esaminare i registri dei servizi.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Elencare i moduli in esecuzione.

```powershell
iotedge list
```

È possibile verificare che sia stata utilizzata la singola registrazione creata in Device Provisioning Service. Passare all'istanza del servizio Device Provisioning nel portale di Azure.Navigate to your Device Provisioning Service instance in the Azure portal. Aprire i dettagli della registrazione per la singola registrazione creata. Si noti che lo stato della registrazione è **assegnato** e l'ID dispositivo è elencato.

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come distribuire e monitorare i [moduli di IoT Edge su larga scala usando il portale](how-to-deploy-monitor.md) di Azure o l'interfaccia della riga di comando di [Azure.](how-to-deploy-monitor-cli.md)
