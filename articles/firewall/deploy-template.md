---
title: Distribuire Firewall di Azure con un modello
description: Distribuire Firewall di Azure con un modello
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: 1a732e22d72c36afe11030e42bae529baa35df1a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992510"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Distribuire Firewall di Azure con un modello

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Gli esempi negli articoli di Firewall di Azure presuppongono che sia già stata abilitata l'anteprima pubblica di Firewall di Azure. Per altre informazioni, vedere [Abilitare l'anteprima pubblica di Firewall di Azure](public-preview.md).

Questo modello crea un firewall e un ambiente di rete di test. La rete contiene una rete virtuale con tre subnet: *AzureFirewallSubnet*, *ServersSubnet* e *JumpboxSubnet*. ServersSubnet e JumpboxSubnet contengono ognuna un'istanza di Windows Server con 2 core.

Il firewall si trova in AzureFirewallSubnet ed è configurato con una raccolta di regole di applicazione con una singola regola che consente l'accesso a www.microsoft.com.

Viene creata una route definita dall'utente che punta al traffico di rete da ServersSubnet attraverso il firewall, in cui vengono applicate le regole del firewall.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="template-location"></a>Percorso del modello

Il modello si trova in:

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)

Leggere l'introduzione e, quando si è pronti per la distribuzione, fare clic su **Distribuzione in Azure**.

## <a name="clean-up-resources"></a>Pulire le risorse

Per prima cosa esplorare le risorse create con il firewall e poi, quando non sono più necessari, è possibile usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per eliminare il gruppo di risorse, il firewall e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>Passaggi successivi

È ora possibile monitorare i log di Firewall di Azure:

- [Esercitazione: Monitorare i log di Firewall di Azure](./tutorial-diagnostics.md)

