---
title: 'Script di PowerShell: Creare un hub di notifica di Azure | Microsoft Docs'
description: Questo script di PowerShell crea un hub di notifica di Azure.
services: notification-hubs
author: dimazaid
manager: kpiteira
editor: spelluru
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: d1d9628c073b298b6e01f044abfde8b1d40ece2d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245678"
---
# <a name="use-powershell-to-create-an-azure-notification-hub"></a>Usare PowerShell per creare un hub di notifica di Azure

Questo script di PowerShell crea un esempio di hub di notifica di Azure. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure** - Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/notification-hubs/create-notification-hub/create-notification-hub.ps1 "Create a notification hub")]


## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo aver eseguito lo script di esempio, è possibile eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse associate a questo:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzNotificationHubsNamespace](/powershell/module//azurerm.notificationhubs/new-Aznotificationhubsnamespace) | Crea uno spazio dei nomi per l'hub di notifica. |
| [New-AzNotificationHub](/powershell/module//azurerm.notificationhubs/new-Aznotificationhubsnamespace) | Crea un hub di notifica. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).
