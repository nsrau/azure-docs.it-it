---
title: Dopo l'installazione e il provisioning, verificare la riuscita e la risoluzione dei problemi
description: includere file
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 10/06/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 295769e5d4924e529b296dbb0b9d405ee197c1db
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979720"
---
## <a name="verify-successful-setup"></a>Verificare la corretta installazione

Verificare lo stato del servizio IoT Edge. Deve essere indicato come in esecuzione.  

# <a name="linux"></a>[Linux](#tab/linux)

```bash
systemctl status iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

```powershell
Get-Service iotedge
```

---

Esaminare i log del servizio.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
journalctl -u iotedge --no-pager --no-full
```

# <a name="windows"></a>[Windows](#tab/windows)

Se è stata appena completata l'installazione del runtime di IoT Edge, è possibile che venga visualizzato un elenco di errori dal tempo tra l'esecuzione di **deploy-IoTEdge** e **Initialize-IoTEdge**. Questi errori sono previsti, perché il servizio sta provando ad avviarsi prima di essere configurato.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

---

Eseguire lo [strumento di risoluzione dei problemi](../articles/iot-edge/troubleshoot.md#run-the-check-command) per verificare la configurazione e gli errori di rete più comuni.

```powershell
iotedge check
```

Fino a quando non si distribuisce il primo modulo per IoT Edge nel dispositivo, il modulo del sistema **$edgeHub** non verrà distribuito nel dispositivo. Di conseguenza, il controllo automatico restituirà un errore per il `Edge Hub can bind to ports on host` controllo della connettività. Questo errore può essere ignorato a meno che non avvenga dopo la distribuzione di un modulo nel dispositivo.

Infine, elencare i moduli in esecuzione:

```powershell
iotedge list
```

Dopo una nuova installazione, l'unico modulo che dovrebbe essere in esecuzione è **edgeAgent**.

## <a name="tips-and-troubleshooting"></a>Suggerimenti e risoluzione dei problemi

Nei dispositivi con risorse vincolate, si consiglia vivamente di impostare la variabile di ambiente *OptimizeForPerformance* su *false*, come indicato nelle istruzioni della [Guida alla risoluzione dei problemi](../articles/iot-edge/troubleshoot.md).

Se il dispositivo non è in grado di connettersi all'hub Internet e la rete ha un server proxy, seguire i passaggi descritti in [configurare il dispositivo IOT Edge per la comunicazione tramite un server proxy](../articles/iot-edge/how-to-configure-proxy-support.md).

# <a name="linux"></a>[Linux](#tab/linux)

Nei dispositivi Linux sono necessari privilegi elevati per eseguire i `iotedge` comandi. Dopo aver installato il runtime, scollegarsi e ricollegarsi al computer per aggiornare automaticamente le autorizzazioni. Fino a quel momento, utilizzare `sudo` per eseguire comandi con privilegi elevati.

# <a name="windows"></a>[Windows](#tab/windows)

Nei dispositivi Windows che eseguono i contenitori di Windows, il motore di contenitori di Moby è stato installato come parte di IoT Edge. Il motore Moby è stato progettato per essere eseguito in parallelo con Docker desktop. È possibile usare `docker` i comandi se si vuole interagire direttamente con i contenitori nel dispositivo. Tuttavia, è necessario specificare specificamente come destinazione il motore Moby nel caso in cui Docker desktop sia installato anche nel dispositivo.

Per elencare tutte le immagini Docker, ad esempio, usare il comando seguente:

```powershell
docker images
```

Per elencare tutte le immagini di Moby, modificare lo stesso comando con un puntatore al motore di Moby:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

L'URI del motore è elencato nell'output dello script di installazione, oppure è possibile trovarlo nella sezione delle impostazioni di runtime del contenitore per il file config. yaml.

![moby_runtime URI in config. YAML](./media/iot-edge-verify-troubleshoot-install/moby-runtime-uri.png)

---
