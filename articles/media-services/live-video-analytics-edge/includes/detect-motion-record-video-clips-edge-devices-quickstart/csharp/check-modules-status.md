---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682229"
---
Nel passaggio di [generazione e distribuzione del manifesto della distribuzione di IoT Edge](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) in Visual Studio Code espandere il nodo **lva-sample-device** in **HUB IOT DI AZURE** (sezione in basso a sinistra). Si noterà che sono distribuiti i moduli seguenti:

* Il modulo Analisi video live, denominato `lvaEdge`
* Il modulo `rtspsim`, che simula un server RTSP che funge da origine di un feed di video live

  ![Moduli](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> Se si usa un dispositivo perimetrale personalizzato invece del dispositivo sottoposto a provisioning dallo script di configurazione, passare al dispositivo perimetrale ed eseguire i comandi seguenti con **diritti di amministratore** per eseguire il pull e archiviare il file del video di esempio usato per questo avvio rapido:  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
