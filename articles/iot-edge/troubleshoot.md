---
title: Risolvere i problemi relativi a Azure IoT Edge | Documenti Microsoft
description: Risolvere i problemi comuni e acquisire le competenze di risoluzione dei problemi per Azure IoT Edge
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/15/2017
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 3f61f0bf8234e747ae38146d1a5ea030e3163fa3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemi comuni e soluzioni per Azure IoT Edge

Se si verificano problemi durante l'esecuzione di Azure IoT Edge nel proprio ambiente, usare questo articolo come guida per la risoluzione dei problemi e la risoluzione. 

## <a name="standard-diagnostic-steps"></a>Passaggi di diagnostica standard 

Quando si verifica un problema, informazioni sullo stato del dispositivo IoT Edge esaminando i registri di contenitore e i messaggi che passano da e verso il dispositivo. Usare gli strumenti e comandi in questa sezione per raccogliere informazioni. 

* Esaminare i registri dei contenitori di docker per rilevare i problemi. Iniziare con i contenitori distribuiti, quindi esaminare i contenitori che costituiscono il runtime IoT Edge: agente Edge e Hub di bordo. In genere, i registri dell'agente di Edge forniscono informazioni sul ciclo di vita di ogni contenitore. I registri di Edge Hub forniscono informazioni su routing e di messaggistica. 

   ```cmd
   docker logs <container name>
   ```

* Visualizzare i messaggi inviati tramite l'Hub di Edge e raccogliere informazioni dettagliate sugli aggiornamenti di proprietà dispositivo con log dettagliati dai contenitori di runtime.

   ```cmd
   iotedgectl setup --runtime-log-level DEBUG
   ```

* Se si verificano problemi di connettività, esaminare le variabili di ambiente del dispositivo edge come stringa di connessione del dispositivo:

   ```cmd
   docker exec edgeAgent printenv
   ```

È inoltre possibile verificare i messaggi inviati tra l'IoT Hub e i dispositivi IoT Edge. Visualizzare i messaggi utilizzando il [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) estensione per il codice di Visual Studio. Per ulteriori informazioni, vedere [strumento utile quando si sviluppa con Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

Dopo avere esaminato i registri e i messaggi per informazioni, è anche possibile provare il riavvio il runtime di Azure IoT Edge:

   ```cmd
   iotedgectl restart
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Bordo agente si arresta dopo circa un minuto

L'agente di bordo viene avviato e viene eseguito correttamente per circa un minuto, quindi si arresta. I log indicano che l'agente di Edge sta tentando di connettersi all'IoT Hub tramite AMQP, e quindi si tenta di connettersi tramite AMQP su websocket circa 30 secondi. Operazione non riesce, viene chiuso l'agente di bordo. 

Registri dell'agente di Edge di esempio:

```
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Causa radice
Una configurazione di rete nella rete host impedisce di raggiungere la rete dell'agente di bordo. L'agente tenta di connettersi tramite AMQP (porta 5671). Se l'operazione non riesce, ritenta WebSocket (porta 443).

Runtime IoT Edge imposta per comunicare in una rete per ciascuno dei moduli. In Linux, la rete si trova un bridge di rete. In Windows, Usa NAT. Questo problema è più comune nei dispositivi Windows con i contenitori di Windows che utilizzano la rete NAT. 

### <a name="resolution"></a>Risoluzione
Verificare che sia presente una route a internet per gli indirizzi IP assegnati a questa rete bridge/NAT. Talvolta una configurazione VPN nell'host esegue l'override della rete perimetrale IoT. 

## <a name="edge-hub-fails-to-start"></a>Non è possibile avviare Edge Hub

Ha esito negativo Edge Hub per avviare e viene stampato il seguente messaggio di log: 

```
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Causa radice
Un altro processo nel computer host è associata la porta 443. L'Hub di Edge esegue il mapping di porte 5671 e 443 per usare in scenari di gateway. Questo mapping di porta non riesce se un altro processo è già associato a questa porta. 

### <a name="resolution"></a>Risoluzione
Individuare e arrestare il processo che utilizza la porta 443. Questo processo è in genere un server web.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Bordo agente non è possibile accedere all'immagine di un modulo (403)
Un contenitore non viene eseguito correttamente e i registri dell'agente di bordo mostrano un errore 403. 

### <a name="root-cause"></a>Causa radice
L'agente di Edge non dispone delle autorizzazioni per accedere all'immagine di un modulo. 

### <a name="resolution"></a>Risoluzione
Provare a eseguire il `iotedgectl login` nuovo il comando.

## <a name="next-steps"></a>Passaggi successivi
Si ritiene che è stato individuato un bug nella piattaforma IoT Edge? Il [presenta un problema](https://github.com/Azure/iot-edge/issues) in modo da potere continuare a migliorare. 
