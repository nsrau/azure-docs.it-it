---
title: Errori comuni-Azure IoT Edge | Microsoft Docs
description: Usare questo articolo per risolvere i problemi comuni riscontrati durante la distribuzione di una soluzione di IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 98ee865a3ddf6c26ffe9cb77767f3872b42018d8
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442362"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemi comuni e soluzioni per Azure IoT Edge

Usare questo articolo per trovare i passaggi per risolvere i problemi comuni che si possono verificare durante la distribuzione di soluzioni IoT Edge. Per informazioni su come individuare log ed errori dal dispositivo IoT Edge, vedere [risolvere i problemi relativi al dispositivo IOT Edge](troubleshoot.md).

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agente si interrompe dopo circa un minuto

**Comportamento osservato:**

Il modulo edgeAgent viene avviato e viene eseguito correttamente per circa un minuto, quindi viene arrestato. I log indicano che l'agente di IoT Edge tenta di connettersi all'hub degli eventi su AMQP, quindi tenta di connettersi usando AMQP tramite WebSocket. In caso di errore, l'agente IoT Edge viene chiuso.

Log edgeAgent di esempio:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Causa radice:**

Una configurazione di rete nella rete host impedisce all'agente di IoT Edge di raggiungere la rete. L'agente prova prima a connettersi tramite AMQP (porta 5671). Se la connessione non riesce, prova WebSocket (porta 443).

Il runtime di IoT Edge configura una rete per ognuno dei moduli usati per la comunicazione. In Linux questa rete è una rete di bridge. In Windows si usa la rete NAT. Questo problema è più comune nei dispositivi Windows con contenitori Windows che usano la rete NAT.

**Risoluzione:**

Verificare che sia presente un instradamento a Internet per gli indirizzi IP assegnati a questa rete di bridge/NAT. Talvolta una configurazione VPN nell'host esegue l'override della rete di IoT Edge.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge Agent non è in grado di accedere all'immagine di un modulo (403)

**Comportamento osservato:**

Non è possibile eseguire un contenitore e nei log di edgeAgent viene visualizzato un errore 403.

**Causa radice:**

L'agente di IoT Edge non dispone delle autorizzazioni necessarie per accedere all'immagine di un modulo.

**Risoluzione:**

Verificare che le credenziali del registro di sistema siano specificate correttamente nel manifesto della distribuzione.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Il modulo dell'agente Edge segnala il file di configurazione vuoto e non vengono avviati moduli nel dispositivo

**Comportamento osservato:**

Il dispositivo ha problemi di avvio dei moduli definiti nella distribuzione. Solo il edgeAgent è in esecuzione, ma segnala continuamente "file di configurazione vuoto...".

**Causa radice:**

Per impostazione predefinita, IoT Edge avvia i moduli nella propria rete di contenitori isolata. Il dispositivo potrebbe avere problemi con la risoluzione dei nomi DNS all'interno della rete privata.

**Risoluzione:**

**Opzione 1: impostare il server DNS nelle impostazioni del motore del contenitore**

Specificare il server DNS per l'ambiente nelle impostazioni del motore di contenitori, che verranno applicate a tutti i moduli contenitore avviati dal motore. Creare un file denominato `daemon.json` specificando il server DNS da usare. Esempio:

```json
{
    "dns": ["1.1.1.1"]
}
```

Nell'esempio precedente il server DNS viene impostato su un servizio DNS accessibile pubblicamente. Se il dispositivo perimetrale non è in grado di accedere a questo IP dal relativo ambiente, sostituirlo con l'indirizzo del server DNS accessibile.

Posizionare `daemon.json` nella posizione corretta per la piattaforma:

| Piattaforma | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows host con i contenitori di Windows | `C:\ProgramData\iotedge-moby\config` |

Se il percorso contiene già un `daemon.json` file, aggiungervi la chiave **DNS** e salvare il file.

Riavviare il motore di gestione dei contenitori per rendere effettivi gli aggiornamenti.

| Piattaforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (amministrazione PowerShell) | `Restart-Service iotedge-moby -Force` |

**Opzione 2: impostare il server DNS nella distribuzione IoT Edge per modulo**

È possibile impostare il server DNS per *createOptions* di ogni modulo nella distribuzione di IOT Edge. Esempio:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Assicurarsi di impostare questa configurazione anche per i moduli *edgeAgent* e *edgeHub* .

## <a name="iot-edge-hub-fails-to-start"></a>Non è possibile avviare l'hub IoT Edge

**Comportamento osservato:**

Non è possibile avviare il modulo edgeHub. È possibile che venga visualizzato un messaggio simile a uno dei seguenti errori nei log:

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

Or

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**Causa radice:**

Un altro processo nel computer host ha associato una porta che il modulo edgeHub sta tentando di associare. L'hub IoT Edge esegue il mapping delle porte 443, 5671 e 8883 da usare negli scenari di gateway. Il modulo non viene avviato se un altro processo ha già associato una di queste porte.

**Risoluzione:**

È possibile risolvere il problema in due modi:

Se il dispositivo IoT Edge funziona come dispositivo gateway, è necessario trovare e arrestare il processo che usa la porta 443, 5671 o 8883. Un errore per la porta 443 indica in genere che l'altro processo è un server Web.

Se non è necessario usare il dispositivo IoT Edge come gateway, è possibile rimuovere i binding della porta dalle opzioni di creazione del modulo di edgeHub. È possibile modificare le opzioni di creazione nel portale di Azure o direttamente nella deployment.jssul file.

Nel portale di Azure:

1. Passare all'hub Internet e selezionare **IOT Edge**.

2. Selezionare il dispositivo IoT Edge che si desidera aggiornare.

3. Selezionare **imposta moduli**.

4. Selezionare **le impostazioni di runtime**.

5. Nelle impostazioni del modulo dell' **Hub Edge** eliminare tutti gli elementi dalla casella di testo **Crea opzioni** .

6. Salvare le modifiche e creare la distribuzione.

Nel file deployment.js:

1. Aprire il deployment.jsnel file che è stato applicato al dispositivo IoT Edge.

2. Trovare le `edgeHub` Impostazioni nella sezione edgeAgent desired Properties:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. Rimuovere la `createOptions` riga e la virgola finale alla fine della `image` riga prima di:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. Salvare il file e applicarlo nuovamente al dispositivo IoT Edge.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Se il nome host non è valido, l'esecuzione del daemon di sicurezza di IoT Edge ha esito negativo.

**Comportamento osservato:**

Il tentativo di [controllare i log di IOT Edge Security Manager](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) ha esito negativo e stampa il messaggio seguente:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Causa radice:**

Il runtime di IoT Edge può supportare solo nomi host contenenti meno di 64 caratteri. I computer fisici non hanno in genere nomi host lunghi, ma il problema è più comune in una macchina virtuale. I nomi host generati automaticamente per le macchine virtuali Windows ospitate in Azure, in particolare, tendono a essere lunghi.

**Risoluzione:**

Quando viene visualizzato questo errore, è possibile risolvere il problema configurando il nome DNS della macchina virtuale e quindi impostando il nome DNS come nome host nel comando di configurazione.

1. Nel portale di Azure passare alla pagina di panoramica della macchina virtuale.
2. Selezionare **Configura** sotto Nome DNS. Se la macchina virtuale dispone già di un nome DNS configurato, non è necessario configurarne uno nuovo.

   ![Configurare il nome DNS della macchina virtuale](./media/troubleshoot/configure-dns.png)

3. Specificare un valore per **Etichetta del nome DNS** e selezionare **Salva**.
4. Copiare il nuovo nome DNS, che deve essere nel formato **\<DNSnamelabel\> . \<vmlocation\> . cloudapp.azure.com**.
5. All'interno della macchina virtuale, usare il comando seguente per configurare il runtime di IoT Edge con il nome DNS:

   * In Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * In Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Non è possibile ottenere i log del daemon di IoT Edge su Windows

**Comportamento osservato:**

Si ottiene un EventLogexception quando si utilizza `Get-WinEvent` in Windows.

**Causa radice:**

Il comando di PowerShell `Get-WinEvent` si basa su una voce del Registro di sistema che deve essere presente per trovare i log da uno specifico `ProviderName`.

**Risoluzione:**

Impostare una voce del Registro di sistema per il daemon di IoT Edge. Creare un file **iotedge.reg** con il contenuto seguente e importarlo nel Registro di sistema di Windows facendovi doppio clic o usando il comando `reg import iotedge.reg`:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>Problemi di stabilità nei dispositivi più piccoli

**Comportamento osservato:**

È possibile che si verifichino problemi di stabilità nei dispositivi con vincoli di risorse come Raspberry Pi, soprattutto se usati come gateway. I sintomi includono eccezioni di memoria insufficiente nel modulo Hub IoT Edge, i dispositivi downstream non riescono a connettersi o il dispositivo non riesce a inviare messaggi di telemetria dopo alcune ore.

**Causa radice:**

L'hub IoT Edge, che fa parte del runtime di IoT Edge, è ottimizzato per le prestazioni per impostazione predefinita e tenta di allocare blocchi di memoria di grandi dimensioni. Questa ottimizzazione non è ideale per i dispositivi Edge con limiti e può causare problemi di stabilità.

**Risoluzione:**

Per l'hub IoT Edge, impostare una variabile di ambiente **OptimizeForPerformance** su **false**. Esistono due modi per impostare le variabili di ambiente:

Nel portale di Azure:

Nell'hub Internet delle cose selezionare il dispositivo IOT Edge e nella pagina dei dettagli del dispositivo e selezionare **set Modules**  >  **Runtime Settings**. Creare una variabile di ambiente per il modulo Hub IoT Edge denominato *OptimizeForPerformance* , che è impostato su *false*.

![OptimizeForPerformance impostato su false](./media/troubleshoot/optimizeforperformance-false.png)

Nel manifesto della distribuzione:

```json
"edgeHub": {
  "type": "docker",
  "settings": {
    "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
    "createOptions": <snipped>
  },
  "env": {
    "OptimizeForPerformance": {
      "value": "false"
    }
  },
```

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge modulo non è in grado di inviare un messaggio a edgeHub con 404 errore

**Comportamento osservato:**

Un modulo di IoT Edge personalizzato non riesce a inviare un messaggio all'hub IoT Edge con un `Module not found` errore 404. Il daemon di IoT Edge stampa il messaggio seguente nei log:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Causa radice:**

Per motivi di sicurezza, il daemon di IoT Edge impone l'identificazione del processo a tutti i moduli che si connettono a edgeHub. Verifica che tutti i messaggi inviati da un modulo provengano dall'ID del processo principale del modulo. Se viene inviato un messaggio da un modulo da un ID di processo diverso rispetto a quello inizialmente stabilito, rifiuterà il messaggio con un messaggio di errore 404.

**Risoluzione:**

A partire dalla versione 1.0.7, tutti i processi dei moduli sono autorizzati a connettersi. Per altre informazioni, vedere il log delle modifiche della [versione 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Se non è possibile eseguire l'aggiornamento a 1.0.7, seguire questa procedura. Verificare che il modulo IoT Edge personalizzato usi sempre lo stesso ID di processo per inviare messaggi a edgeHub. Ad esempio, assicurarsi di eseguire `ENTRYPOINT` `CMD` il comando anziché nel file docker. Il `CMD` comando conduce a un ID di processo per il modulo e un altro ID processo per il comando bash che esegue il programma principale, ma `ENTRYPOINT` conduce a un singolo ID processo.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>Il modulo IoT Edge viene distribuito correttamente, quindi scompare dal dispositivo

**Comportamento osservato:**

Dopo aver impostato i moduli per un dispositivo IoT Edge, i moduli vengono distribuiti correttamente, ma dopo alcuni minuti scompaiono dal dispositivo e dai dettagli del dispositivo nel portale di Azure. Altri moduli di quelli definiti potrebbero essere visualizzati anche sul dispositivo.

**Causa radice:**

Se una distribuzione automatica è destinata a un dispositivo, ha la priorità rispetto all'impostazione manuale dei moduli per un singolo dispositivo. La funzionalità **imposta moduli** in portale di Azure o **Crea distribuzione per** la funzionalità del singolo dispositivo in Visual Studio Code diverrà effettiva per un momento. Vengono visualizzati i moduli definiti per iniziare nel dispositivo. Quindi, la priorità della distribuzione automatica avvia e sovrascrive le proprietà desiderate del dispositivo.

**Risoluzione:**

Usare solo un tipo di meccanismo di distribuzione per dispositivo, ovvero una distribuzione automatica o distribuzioni di singoli dispositivi. Se sono presenti più distribuzioni automatiche destinate a un dispositivo, è possibile modificare le descrizioni della priorità o della destinazione per assicurarsi che la correzione venga applicata a un determinato dispositivo. È anche possibile aggiornare i dispositivi gemelli in modo che non corrispondano più alla descrizione di destinazione della distribuzione automatica.

Per altre informazioni, vedere [Informazioni sulle distribuzioni automatiche di IoT Edge per singoli dispositivi o su vasta scala](module-deployment-monitoring.md).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>IoT Edge dietro un gateway non può eseguire richieste HTTP e avviare il modulo edgeAgent

**Comportamento osservato:**

Il daemon IoT Edge è attivo con un file di configurazione valido, ma non può avviare il modulo edgeAgent. Il comando `iotedge list` restituisce un elenco vuoto. Il report dei log del daemon IoT Edge `Could not perform HTTP request` .

**Causa radice:**

IoT Edge i dispositivi protetti da un gateway ottengono le immagini del modulo dal dispositivo padre IoT Edge specificato nel `parent_hostname` campo del file config. yaml. L' `Could not perform HTTP request` errore indica che il dispositivo figlio non riesce a raggiungere il dispositivo padre tramite http.

**Risoluzione:**

Verificare che il dispositivo IoT Edge padre possa ricevere le richieste in ingresso dal dispositivo IoT Edge figlio. Aprire il traffico di rete sulle porte 443 e 6617 per le richieste provenienti dal dispositivo figlio.

:::moniker-end

## <a name="next-steps"></a>Passaggi successivi

Se si ritiene di aver rilevato un bug nella piattaforma di IoT Edge, [Inviare un problema](https://github.com/Azure/iotedge/issues) in modo da poter migliorare l'esperienza.

In caso di altre domande, creare una [Richiesta di supporto](https://portal.azure.com/#create/Microsoft.Support) per assistenza.
