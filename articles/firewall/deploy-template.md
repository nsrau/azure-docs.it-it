---
title: Distribuire Firewall di Azure con un modello
description: Distribuire Firewall di Azure usando un modello. La rete creata ha una rete virtuale con tre subnet. Vengono distribuite due macchine virtuali Windows Server a due core.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74169191"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Distribuire Firewall di Azure con un modello

[Creare il modello di configurazione sandbox di AzureFirewall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) crea un ambiente di rete di test con un firewall. La rete dispone di una rete virtuale (VNet) con tre subnet: *AzureFirewallSubnet*, *ServersSubnet*e *JumpboxSubnet*. Ciascuna delle subnet *ServersSubnet* e *JumpboxSubnet* dispone di una macchina singola virtuale Windows Server a due core.

Il firewall si trova nella subnet *AzureFirewallSubnet* e dispone di una `www.microsoft.com`raccolta di regole dell'applicazione con una singola regola che consente l'accesso a .

Una route definita dall'utente punta al traffico di rete dalla subnet *ServersSubnet* attraverso il firewall, in cui vengono applicate le regole del firewall.

Per altre informazioni su Firewall di Azure, vedere [Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Usare il modello per distribuire Firewall di Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

**Per installare e distribuire Firewall di Azure usando il modello:**

1. Accedere al [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox)modello all'indirizzo .
   
1. Leggere l'introduzione e, quando si è pronti per la distribuzione, selezionare **Distribuisci in Azure**.
   
1. Se necessario, accedere al portale di Azure. 

1. Nel portale, alla pagina **Creare una configurazione sandbox di AzureFirewall**, digitare o selezionare i seguenti valori:
   
   - **Gruppo di risorse**: selezionare **Crea nuovo**, digitare un nome per il gruppo di risorse e scegliere **OK**. 
   - **Nome rete virtuale:** digitare un nome per la nuova rete virtuale. 
   - **Nome utente amministratore**: digitare un nome utente per l'account utente amministratore.
   - **Password amministratore**: Digitare una password di amministratore. 
   
1. Leggere i termini e le condizioni, quindi selezionare **Accetto i termini e le condizioni sopra indicati**.
   
1. Selezionare **Acquista**.
   
   La creazione delle risorse richiederà alcuni minuti. 
   
1. Esplorare le risorse create con il firewall. 

Per altre informazioni sulla sintassi JSON e sulle proprietà di un firewall in un modello, vedere [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse, il firewall e tutte le risorse correlate eseguendo il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) PowerShell. Per rimuovere un gruppo di risorse denominato *MyResourceGroup*, eseguire: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Non rimuovere ancora il gruppo di risorse e il firewall se si prevede di continuare con l'esercitazione sul monitoraggio del firewall. 

## <a name="next-steps"></a>Passaggi successivi

È ora possibile monitorare i log di Firewall di Azure:

> [!div class="nextstepaction"]
> [Esercitazione: Monitorare i log di Firewall di Azure](./tutorial-diagnostics.md)
