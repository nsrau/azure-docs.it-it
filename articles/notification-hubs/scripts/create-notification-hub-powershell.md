---
title: Creare un hub di notifica di Azure tramite PowerShell | Microsoft Docs
description: Informazioni su come usare uno script di PowerShell per creare un hub di notifica di Azure.
author: dimazaid
manager: femila
services: notification-hubs
editor: sethmanheim
ms.service: notification-hubs
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2020
ms.author: dimazaid
ms.openlocfilehash: 857210efdc5740defcde4187b7b04cce55215325
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87071611"
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
| [New-AzNotificationHubsNamespace](/powershell/module/az.notificationhubs/new-aznotificationhubsnamespace) | Crea uno spazio dei nomi per l'hub di notifica. |
| [New-AzNotificationHub](/powershell/module/az.notificationhubs/new-aznotificationhub) | Crea un hub di notifica. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).
