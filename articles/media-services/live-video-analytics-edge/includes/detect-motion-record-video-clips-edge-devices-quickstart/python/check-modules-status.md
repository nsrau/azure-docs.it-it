---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682301"
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
