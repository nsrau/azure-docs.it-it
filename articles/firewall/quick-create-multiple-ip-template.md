---
title: "Guida introduttiva: Creare un'istanza di Firewall di Azure con più indirizzi IP pubblici - Modello di Resource Manager"
description: Informazioni su come usare un modello di Resource Manager per creare un'istanza di Firewall di Azure con più indirizzi IP pubblici.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: b81362f191e6317aa7ffa974a6d432b0c7514d8f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680543"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Guida introduttiva: Creare un'istanza di Firewall di Azure con più indirizzi IP pubblici - Modello di Resource Manager

In questa guida di avvio rapido si usa un modello di Resource Manager per distribuire un'istanza di Firewall di Azure con più indirizzi IP pubblici. Il firewall distribuito include regole della raccolta regole NAT che consentono connessioni RDP a due macchine virtuali Windows Server 2019.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Per altre informazioni su Firewall di Azure con più indirizzi IP pubblici, vedere [Distribuire un Firewall di Azure con più indirizzi IP pubblici usando Azure PowerShell](deploy-multi-public-ip-powershell.md).

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall"></a>Creare un'istanza di Firewall di Azure

Questo modello crea un'istanza di Firewall di Azure con due indirizzi IP pubblici, unitamente alle risorse necessarie per supportare Firewall di Azure.

### <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/fw-docs-qs).

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

Nel modello sono definite più risorse di Azure:

- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

### <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello di Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea un'istanza di Firewall di Azure, l'infrastruttura di rete e due macchine virtuali.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. Nella pagina **Create an Azure Firewall with multiple IP public addresses** (Crea Firewall di Azure con più indirizzi IP pubblici) del portale digitare o selezionare i valori seguenti:
   - Sottoscrizione: selezionare una delle sottoscrizioni esistenti. 
   - Gruppo di risorse:  selezionare un gruppo di risorse esistente oppure selezionare **Crea nuovo** e quindi **OK**.
   - Percorso: Selezionare una località
   - Nome utente amministratore: digitare il nome utente dell'account utente amministratore. 
   - Password amministratore: digitare una password o una chiave dell'amministratore.

3. Selezionare **Accetto le condizioni riportate sopra** e quindi **Acquista**. La distribuzione può richiedere almeno 10 minuti.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

Nel portale di Azure esaminare le risorse distribuite. Prendere nota degli indirizzi IP pubblici del firewall.  

Usare Connessione Desktop remoto per connettersi agli indirizzi IP pubblici del firewall. In Connessioni riuscite sono indicate le regole NAT del firewall che consentono la connessione ai server back-end.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il firewall non sono più necessarie, eliminare il gruppo di risorse. Oltre al firewall verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Distribuire e configurare Firewall di Azure in una rete ibrida con il portale di Azure](tutorial-hybrid-portal.md)