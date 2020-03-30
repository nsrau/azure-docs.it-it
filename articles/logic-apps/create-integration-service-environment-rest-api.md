---
title: Creare ambienti del servizio di integrazione (ISE) con l'API REST delle app per la logicaCreate integration service environments (ISEs) with Logic Apps REST API
description: Creare un ambiente del servizio di integrazione (ISE) usando l'API REST delle app per la logica in modo da poter accedere alle reti virtuali di Azure da app per la logica di AzureCreate an integration service environment (ISE) by using the Logic Apps REST API so you can access Azure virtual networks (VNETs) from Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 2c6e35b1e7d160064998004f87c5b14d0eaeac5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127666"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Creare un ambiente del servizio di integrazione (ISE) usando l'API REST delle app per la logicaCreate an integration service environment (ISE) by using the Logic Apps REST API

Questo articolo illustra come creare un ambiente del servizio di [ *integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) tramite l'API REST delle app per la logica per gli scenari in cui le app per la logica e gli account di integrazione devono accedere a una rete virtuale di Azure.This article shows how to create an integration service environment (ISE) through the Logic Apps REST API for scenarios where your logic apps and integration accounts need access to an [Azure virtual network](../virtual-network/virtual-networks-overview.md). Un ISE è un ambiente isolato che utilizza l'archiviazione dedicata e altre risorse che vengono mantenute separate dal servizio "globale" app per la logica multi-tenant. Questa separazione riduce anche qualsiasi impatto che altri tenant di Azure possono avere sulle prestazioni delle app create. Un ISE fornisce anche i propri indirizzi IP statici. Questi indirizzi IP sono separati dagli indirizzi IP statici condivisi dalle app per la logica nel servizio pubblico multi-tenant.

Per creare un ISE usando invece il portale di Azure, vedere [Connettersi alle reti virtuali](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)di Azure da App per la logica di Azure.

> [!IMPORTANT]
> Le app per la logica, i trigger predefiniti, le azioni predefinite e i connettori eseguiti in ISE usano un piano tariffario diverso dal piano tariffario basato sul consumo. Per informazioni sul funzionamento dei prezzi e della fatturazione per ISE, vedere il modello di determinazione dei prezzi app per la [logica.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Per i prezzi, vedere [Prezzi di App per la logica.](../logic-apps/logic-apps-pricing.md)

## <a name="prerequisites"></a>Prerequisiti

* Gli stessi [prerequisiti](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisiti per abilitare l'accesso per ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) come quando si crea un ISE nel portale di Azure

* Strumento che è possibile usare per creare ISE chiamando l'API REST app per la logica con una richiesta HTTPS PUT. Ad esempio, è possibile usare [Postman](https://www.getpostman.com/downloads/)oppure creare un'app per la logica che esegue questa attività.

## <a name="send-the-request"></a>Inviare la richiesta

Per creare ISE chiamando l'API REST app per la logica, effettuare questa richiesta HTTPS PUT:To create your ISE by calling the Logic Apps REST API, make this HTTPS PUT request:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> La versione dell'API REST App per la logica 2019-05-01 richiede di effettuare la propria richiesta HTTP PUT per i connettori ISE.

Il completamento della distribuzione richiede in genere entro due ore. In alcuni casi, la distribuzione potrebbe richiedere fino a quattro ore. Per controllare lo stato della distribuzione, nel portale di [Azure](https://portal.azure.com), sulla barra degli strumenti di Azure, selezionare l'icona delle notifiche, che apre il riquadro delle notifiche.

> [!NOTE]
> Se si verifica un errore di distribuzione o si elimina l'ISE, Azure potrebbe richiedere fino a un'ora prima di rilasciare le subnet. Questo ritardo significa che potrebbe essere necessario attendere prima di riutilizzare tali subnet in un altro ISE.
>
> Se si elimina la rete virtuale, Azure richiede in genere fino a due ore prima di rilasciare le subnet, ma questa operazione potrebbe richiedere più tempo. 
> Quando si eliminano reti virtuali, assicurarsi che nessuna risorsa sia ancora connessa. 
> Vedere [Eliminare rete virtuale](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Intestazione della richiesta

Nell'intestazione della richiesta includere queste proprietà:In the request header, include these properties:

* `Content-type`: imposta il `application/json`valore di questa proprietà su .

* `Authorization`: impostare il valore di questa proprietà sul token di connessione per il cliente che ha accesso alla sottoscrizione o al gruppo di risorse di Azure che si vuole usare.

### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta

Di seguito è riportata la sintassi del corpo della richiesta, che descrive le proprietà da utilizzare quando si crea ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Esempio di corpo della richiesta

Questo corpo della richiesta di esempio mostra i valori di esempio:This example request body shows the sample values:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere risorse agli ambienti del servizio di integrazioneAdd resources to integration service environments](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gestire gli ambienti del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

