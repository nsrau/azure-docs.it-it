---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/21/2018
ms.author: wesmc
ms.openlocfilehash: dd9700c9472e07daf294eca12b766e3dc4832955
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111869"
---
### <a name="cacheskuname"></a>cacheSKUName
Piano tariffario del nuovo servizio Cache Redis di Azure.

    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },

Il modello definisce i valori consentiti per questo parametro (Basic o Standard) e assegna un valore predefinito (Basic) se non viene specificato alcun valore. Basic fornisce un singolo nodo con più dimensioni disponibili fino a 53 GB.
Standard fornisce due nodi di replica/primario con più dimensioni disponibili fino a 53 GB e contratto di servizio con disponibilità del 99,9%.

### <a name="cacheskufamily"></a>cacheSKUFamily
Famiglia dello SKU.

    "cacheSKUFamily": {
      "type": "string",
      "allowedValues": [
        "C"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },


### <a name="cacheskucapacity"></a>cacheSKUCapacity
Dimensione della nuova istanza di Cache Redis di Azure. 

    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }


Il modello definisce i valori consentiti per questo parametro (0, 1, 2, 3, 4, 5 o 6) e assegna un valore predefinito (0) se non viene specificato alcun valore. Questi numeri corrispondono alle dimensioni della cache seguenti: 0 = 250 MB, 1 = 1 GB, 2 = 2.5 GB, 3 = 6 GB, 4 = 13 GB, 5 = 26 GB, 6 = 53 GB

