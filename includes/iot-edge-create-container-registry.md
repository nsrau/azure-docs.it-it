---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: 0c43c6dcced94225e9ab9ae903535ce74286ad9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87406716"
---
## <a name="create-a-container-registry"></a>Creare un registro contenitori

In questa esercitazione viene usata l'estensione Azure IoT Tools per compilare un modulo e creare un'**immagine del contenitore** dai file. Eseguire quindi il push dell'immagine in un **registro** che archivia e gestisce le immagini. Distribuire infine l'immagine dal registro nel dispositivo IoT Edge.

È possibile usare qualsiasi registro compatibile con Docker per inserire le immagini dei contenitori. Due servizi di registro Docker molto diffusi sono [Registro Azure Container](https://docs.microsoft.com/azure/container-registry/) e [Hub Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Questa esercitazione usa il Registro Azure Container.

Se non è ancora disponibile alcun registro contenitori, seguire questa procedura per crearne uno nuovo in Azure:

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.

2. Specificare i valori seguenti per creare il registro contenitori:

   | Campo | valore |
   | ----- | ----- |
   | Sottoscrizione | Selezionare una sottoscrizione nell'elenco a discesa. |
   | Resource group | È consigliabile usare lo stesso gruppo di risorse per tutte le risorse di test create durante le esercitazioni e le guide introduttive di IoT Edge. Ad esempio, **IoTEdgeResources**. |
   | Nome registro | Specificare un nome univoco. |
   | Location | Scegliere una località vicina. |
   | SKU | Selezionare **Basic**. |

3. Selezionare **Crea**.

4. Dopo aver creato il registro contenitori, selezionarlo, quindi nel riquadro sinistro scegliere **Chiavi di accesso** dal menu disponibile in **Impostazioni**. 

5. Fare clic per consentire all'utente amministratore di visualizzare il **nome utente** e la **password** del registro contenitori.

6. Copiare i valori di **Server di accesso**, **Nome utente** e **Password** e conservarli in una posizione pratica. Questi valori verranno usati durante l'esercitazione per fornire l'accesso al registro contenitori.

   ![Copiare nome del server, nome utente e password per il registro contenitori](./media/iot-edge-create-container-registry/registry-access-key.png)