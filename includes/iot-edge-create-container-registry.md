---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75564794"
---
## <a name="create-a-container-registry"></a>Creare un registro contenitori

In questa esercitazione viene usata l'estensione Azure IoT Tools per compilare un modulo e creare un'**immagine del contenitore** dai file. Eseguire quindi il push dell'immagine in un **registro** che archivia e gestisce le immagini. Distribuire infine l'immagine dal registro nel dispositivo IoT Edge.

È possibile usare qualsiasi registro compatibile con Docker per inserire le immagini dei contenitori. Due servizi di registro Docker molto diffusi sono [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa esercitazione usa il Registro Azure Container.

Se non è ancora disponibile alcun registro contenitori, seguire questa procedura per crearne uno nuovo in Azure:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.

2. Specificare i valori seguenti per creare il registro contenitori:

   | Campo | valore |
   | ----- | ----- |
   | Nome registro | Specificare un nome univoco. |
   | Subscription | Selezionare una sottoscrizione nell'elenco a discesa. |
   | Resource group | È consigliabile usare lo stesso gruppo di risorse per tutte le risorse di test create durante le esercitazioni e le guide introduttive di IoT Edge. Ad esempio, **IoTEdgeResources**. |
   | Location | Scegliere una località vicina. |
   | Utente amministratore | Impostare su **Abilita**. |
   | SKU | Selezionare **Basic**. |

3. Selezionare **Create** (Crea).

4. Dopo aver creato il registro contenitori, selezionarlo, quindi nel riquadro sinistro scegliere **Chiavi di accesso** dal menu disponibile in **Impostazioni**.

5. Copiare i valori di **Server di accesso**, **Nome utente** e **Password** e conservarli in una posizione pratica. Questi valori verranno usati durante l'esercitazione per fornire l'accesso al registro contenitori.

   ![Copiare nome del server, nome utente e password per il registro contenitori](./media/iot-edge-create-container-registry/registry-access-key.png)