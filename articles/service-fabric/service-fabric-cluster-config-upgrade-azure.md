---
title: Aggiornare la configurazione di un cluster di Azure Service Fabric | Microsoft Docs
description: Informazioni su come aggiornare la configurazione di un cluster di Service Fabric in Azure tramite un modello di Resource Manager.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 6ddd246057b178119d520bee3bbe58117fbae610
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820978"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Aggiornare la configurazione di un cluster in Azure 

Questo articolo illustra come personalizzare le varie impostazioni dell'infrastruttura per il cluster di Service Fabric. Per i cluster ospitati in Azure, è possibile personalizzare le impostazioni tramite il [portale di Azure](https://portal.azure.com) o con un modello di Azure Resource Manager.

> [!NOTE]
> Non tutte le impostazioni sono disponibili nel portale ed è un [procedure consigliate per personalizzarlo usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code); Portale è per Service Fabric Dev\Test dello scenario solo.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalizzare le impostazioni del cluster usando i modelli di Gestione risorse
È possibile configurare i cluster di Azure tramite il modello di Resource Manager JSON. Per altre informazioni sulle diverse impostazioni, vedere [Impostazioni di configurazione per i cluster](service-fabric-cluster-fabric-settings.md). La procedura di esempio riportata di seguito illustra come aggiungere una nuova impostazione *MaxDiskQuotaInMB* alla sezione *Diagnostics* tramite Azure Resource Explorer.

1. Passare a https://resources.azure.com.
2. Passare alla sottoscrizione espandendo **Sottoscrizioni** -> **\<sottoscrizione>** -> **resourceGroups** -> **\<gruppo risorse>** -> **providers** -> **Microsoft.ServiceFabric** -> **clusters** -> **\<nome cluster>**
3. Nell'angolo in alto a destra selezionare **Lettura/Scrittura**.
4. Selezionare **Modifica**, aggiornare l'elemento JSON `fabricSettings` e aggiungere un nuovo elemento:

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
- Usare i comandi PowerShell [Set-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) e [Remove-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) di Azure Resource Manager per modificare l'impostazione direttamente.
- Usare i comandi [az sf cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) dell'interfaccia della riga di comando di Azure per modificare l'impostazione direttamente.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle [impostazioni dei cluster di Service Fabric](service-fabric-cluster-fabric-settings.md).
* Informazioni su come [aumentare o ridurre le istanze del cluster](service-fabric-cluster-scale-up-down.md).
