---
title: Aggiornare la configurazione di un cluster di Azure Service Fabric
description: Informazioni su come aggiornare la configurazione di un cluster di Service Fabric in Azure tramite un modello di Resource Manager.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: c2973428354f101b5b546128b08bf67587923a8e
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793075"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Aggiornare la configurazione di un cluster in Azure 

Questo articolo illustra come personalizzare le varie impostazioni dell'infrastruttura per il cluster di Service Fabric. Per i cluster ospitati in Azure, è possibile personalizzare le impostazioni tramite il [portale di Azure](https://portal.azure.com) o con un modello di Azure Resource Manager.

> [!NOTE]
> Non tutte le impostazioni sono disponibili nel portale. Una [procedura consigliata consiste nel personalizzarlo usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code); il portale è destinato soltanto agli scenari di sviluppo/test di Service Fabric.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalizzare le impostazioni del cluster usando i modelli di Gestione risorse
È possibile configurare i cluster di Azure tramite il modello di Resource Manager JSON. Per altre informazioni sulle diverse impostazioni, vedere [Impostazioni di configurazione per i cluster](service-fabric-cluster-fabric-settings.md). La procedura di esempio riportata di seguito illustra come aggiungere una nuova impostazione *MaxDiskQuotaInMB* alla sezione *Diagnostics* tramite Azure Resource Explorer.

1. Passare a https://resources.azure.com
2. Passare alla sottoscrizione espandendo **sottoscrizioni** -> **\<la sottoscrizione>**  ->  **resourceGroups** -> **\<il gruppo di risorse>**  ->  **provider** -> **Microsoft. ServiceFabric** -> **raggruppa** -> **\<il nome del cluster>**
3. Nell'angolo in alto a destra selezionare **lettura/scrittura.**
4. Selezionare **modifica** e aggiornare l' `fabricSettings` elemento JSON e aggiungere un nuovo elemento:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

È anche possibile personalizzare le impostazioni del cluster in uno dei modi seguenti con Azure Resource Manager:

- Usare il [portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) per esportare e aggiornare il modello di Resource Manager.
- Usare [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) per esportare e aggiornare il modello di Resource Manager.
- Usare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) per esportare e aggiornare il modello di Resource Manager.
- Usare i comandi Azure PowerShell [set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) e [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) per modificare direttamente l'impostazione.
- Usare i comandi [az sf cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) dell'interfaccia della riga di comando di Azure per modificare l'impostazione direttamente.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md).
* Informazioni su come [ridimensionare il cluster](service-fabric-cluster-scale-in-out.md).
