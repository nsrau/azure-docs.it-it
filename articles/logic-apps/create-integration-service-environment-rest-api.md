---
title: Creare ambienti di Integration Services (ISEs) con l'API REST di app per la logica
description: Creare un ambiente del servizio di integrazione con l'API REST di app per la logica in modo che sia possibile accedere alle reti virtuali di Azure (reti virtuali) da app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478840"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Creare un ambiente del servizio di integrazione (ISE) usando l'API REST di app per la logica

Questo articolo illustra come creare un [ *ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) tramite l'API REST delle app per la logica per gli scenari in cui le app per la logica e gli account di integrazione devono accedere a una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). ISE è un ambiente isolato che usa risorse di archiviazione dedicate e altre risorse che vengono mantenute separate dal servizio "globale" di app per la logica multi-tenant. Questa separazione riduce anche qualsiasi impatto che altri tenant di Azure possono avere sulle prestazioni delle app create. Un ISE fornisce anche indirizzi IP statici. Questi indirizzi IP sono distinti dagli indirizzi IP statici condivisi dalle app per la logica nel servizio pubblico multi-tenant.

È anche possibile creare un ISE usando il [modello di avvio rapido di esempio Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) o usando il [portale di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Le app per la logica, i trigger incorporati, le azioni predefinite e i connettori eseguiti in ISE usano un piano tariffario diverso dal piano tariffario in base al consumo. Per informazioni sul funzionamento dei prezzi e della fatturazione per ISEs, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md#fixed-pricing)per la logica. Per informazioni sui prezzi, vedere [prezzi di app](../logic-apps/logic-apps-pricing.md)per la logica.

## <a name="prerequisites"></a>Prerequisiti

* Gli stessi [prerequisiti](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e [requisiti per abilitare l'accesso per ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) come quando si crea un ISE nel portale di Azure

* Uno strumento che è possibile usare per creare ISE chiamando l'API REST di app per la logica con una richiesta PUT HTTPS. Ad esempio, è possibile usare il [post](https://www.getpostman.com/downloads/), oppure è possibile compilare un'app per la logica che esegue questa attività.

## <a name="send-the-request"></a>Inviare la richiesta

Per creare ISE chiamando l'API REST di app per la logica, effettuare questa richiesta HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Per la versione dell'API REST di app per la logica 2019-05-01 è necessario effettuare una richiesta HTTP PUT per i connettori ISE.

Il completamento della distribuzione richiede in genere entro due ore. In alcuni casi, la distribuzione potrebbe richiedere fino a quattro ore. Per controllare lo stato della distribuzione, nella [portale di Azure](https://portal.azure.com)della barra degli strumenti di Azure selezionare l'icona notifiche, che consente di aprire il riquadro notifiche.

> [!NOTE]
> Se si verifica un errore di distribuzione o si elimina l'ISE, Azure potrebbe richiedere fino a un'ora prima di rilasciare le subnet. Questo ritardo significa che potrebbe essere necessario attendere prima di riutilizzare tali subnet in un altro ISE.
>
> Se si elimina la rete virtuale, Azure richiede in genere fino a due ore prima di rilasciare le subnet, ma questa operazione potrebbe richiedere più tempo. 
> Quando si eliminano le reti virtuali, assicurarsi che non ci siano risorse ancora connesse. 
> Vedere [eliminare la rete virtuale](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Intestazione della richiesta

Nell'intestazione della richiesta includere le proprietà seguenti:

* `Content-type`: Impostare il valore della proprietà `application/json`su.

* `Authorization`: Impostare questo valore della proprietà sul bearer token per il cliente che ha accesso alla sottoscrizione o al gruppo di risorse di Azure che si vuole usare.

### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta

Ecco la sintassi del corpo della richiesta, che descrive le proprietà da usare quando si crea ISE:

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

Questo corpo della richiesta di esempio mostra i valori di esempio:

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

* [Aggiungere risorse agli ambienti del servizio di integrazione](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gestire gli ambienti del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

