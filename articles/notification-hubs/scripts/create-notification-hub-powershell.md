---
title: 'Script di PowerShell: Creare un hub di notifica di Azure | Microsoft Docs'
description: Questo script di PowerShell crea un hub di notifica di Azure.
services: data-factory
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 747d743a0573bd959b4d3c7100be8ae9451c5ed5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786730"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>Usare PowerShell per creare un hub di notifica di Azure

Questo script di PowerShell crea un esempio di hub di notifica di Azure. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>prerequisiti
* **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]


## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo aver eseguito lo script di esempio, è possibile eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse associate a questo:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmNotificationHubsNamespace](/powershell/module//azurerm.notificationhubs/new-azurermnotificationhubsnamespace) | Crea uno spazio dei nomi per l'hub di notifica. |
| [New-AzureRmNotificationHub](/powershell/module//azurerm.notificationhubs/new-azurermnotificationhubsnamespace) | Crea un hub di notifica. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).
