---
title: Abilitare l'anteprima pubblica di Firewall di Azure
description: Usare Azure PowerShell per abilitare l'anteprima pubblica di Firewall di Azure
author: vhorne
ms.service: firewall
services: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: fe1b8f9d56b0f4faa0baa25463b2aa29a59715cb
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499591"
---
# <a name="enable-the-azure-firewall-public-preview"></a>Abilitare l'anteprima pubblica di Firewall di Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

## <a name="enable-using-azure-powershell"></a>Abilitare con Azure PowerShell

Per abilitare l'anteprima pubblica di Firewall di Azure, usare i seguenti comandi di Azure PowerShell:

```PowerShell
Register-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```

Sono necessari fino a 30 minuti per completare la registrazione della funzionalità. Per controllare lo stato della registrazione, usare i seguenti comandi di Azure PowerShell:

```powershell

Get-AzProviderFeature -FeatureName AllowRegionalGatewayManagerForSecureGateway -ProviderNamespace Microsoft.Network

Get-AzProviderFeature -FeatureName AllowAzureFirewall -ProviderNamespace Microsoft.Network
```
Al termine della registrazione eseguire questo comando:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md)

