---
title: Distribuire Firewall di Azure con un modello
description: Distribuire Firewall di Azure con un modello
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 12/01/2018
ms.author: victorh
ms.openlocfilehash: e0fbec8b22993345114d8d6642e42095191d0b37
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046275"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Distribuire Firewall di Azure con un modello

[Creare il modello di configurazione sandbox di AzureFirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox) crea un ambiente di rete di test con un firewall. La rete ha una rete virtuale (VNet) con tre subnet: *AzureFirewallSubnet*, *ServersSubnet*, e *JumpboxSubnet*. Ciascuna delle subnet *ServersSubnet* e *JumpboxSubnet* dispone di una macchina singola virtuale Windows Server a due core.

Il firewall si trova nella subnet *AzureFirewallSubnet* e dispone di una raccolta di regole dell'applicazione con una singola regola che consente l'accesso a *www.microsoft.com*.

Una route definita dall'utente punta al traffico di rete dalla subnet *ServersSubnet* attraverso il firewall, in cui vengono applicate le regole del firewall.

Per altre informazioni su Firewall di Azure, vedere [Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Usare il modello per distribuire Firewall di Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

**Per installare e distribuire Firewall di Azure usando il modello:**

1. Accedere al modello all'indirizzo [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox).
   
1. Leggere l'introduzione e, quando si è pronti per la distribuzione, selezionare **Distribuisci in Azure**.
   
1. Se necessario, accedere al portale di Azure. 

1. Nel portale, alla pagina **Creare una configurazione sandbox di AzureFirewall**, digitare o selezionare i seguenti valori:
   
   - **Gruppo di risorse**: selezionare **Crea nuovo**, digitare un nome per il gruppo di risorse e selezionare **OK**. 
   - **Nome della rete virtuale**: Digitare un nome per la nuova rete virtuale. 
   - **Nome utente amministratore**: Digitare un nome utente per l'account utente amministratore.
   - **Password amministratore**: Digitare una password dell'amministratore. 
   
1. Leggere le condizioni, quindi selezionare **Accetto le condizioni riportate sopra**.
   
1. Selezionare **Acquisto**.
   
   La creazione delle risorse richiederà alcuni minuti. 
   
1. Esplorare le risorse create con il firewall. 

Per altre informazioni sulla sintassi JSON e sulle proprietà di un firewall in un modello, vedere [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, è possibile rimuovere il gruppo di risorse, firewall e tutte le relative risorse eseguendo il [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) comando di PowerShell. Per rimuovere un gruppo di risorse denominato *MyResourceGroup*, eseguire: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Non rimuovere ancora il gruppo di risorse e il firewall se si prevede di continuare con l'esercitazione sul monitoraggio del firewall. 

## <a name="next-steps"></a>Passaggi successivi

È ora possibile monitorare i log di Firewall di Azure:

> [!div class="nextstepaction"]
> [Esercitazione: Controllare i log del Firewall di Azure](./tutorial-diagnostics.md)
