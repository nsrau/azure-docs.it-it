---
title: 'Avvio rapido: Creare un Firewall di Azure e i gruppi IP - Modello di Resource Manager'
description: Informazioni su come usare un modello di Azure Resource Manager per creare un'istanza di Firewall di Azure e gruppi di indirizzi IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 97d1c14a61e98f98e45e25668841cee1ad9ce0d1
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705470"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---arm-template"></a>Avvio rapido: Creare un'istanza di Firewall di Azure e gruppi di indirizzi IP - Modello di Resource Manager

In questo argomento di avvio rapido si usa un modello di Azure Resource Manager per distribuire un'istanza di Firewall di Azure con gruppi di indirizzi IP di esempio usati in una regola di rete e in una regola dell'applicazione. Un gruppo IP è una risorsa di primo livello che consente di definire e raggruppare indirizzi IP, intervalli e subnet in un singolo oggetto. Questa opzione è utile per la gestione degli indirizzi IP nelle regole del Firewall di Azure. È possibile immettere manualmente gli indirizzi IP o importarli da un file.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Rivedere il modello

Questo modello crea un Firewall di Azure e i gruppi IP, insieme alle risorse necessarie per supportare il Firewall di Azure.

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json":::

Nel modello sono definite più risorse di Azure:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello di Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea un'istanza di Firewall di Azure, l'infrastruttura di rete e due macchine virtuali.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. Nella pagina **Create an Azure Firewall with IpGroups** (Creare un Firewall di Azure con gruppi IP) del portale di Azure digitare o selezionare i valori seguenti:
   - Sottoscrizione: selezionare una delle sottoscrizioni esistenti. 
   - Gruppo di risorse:  selezionare un gruppo di risorse esistente oppure selezionare **Crea nuovo** e quindi **OK**.
   - Percorso: Selezionare una località
   - Nome rete virtuale: digitare un nome per la nuova rete virtuale 
   - Nome gruppo IP 1: digitare un nome per il gruppo IP 1 
   - Nome gruppo IP 2: digitare un nome per il gruppo IP 2 
   - Nome utente amministratore: digitare il nome utente dell'account utente amministratore 
   - Autenticazione: selezionare sshPublicKey o password 
   - Password amministratore: digitare una password o una chiave dell'amministratore.

3. Selezionare **Accetto le condizioni riportate sopra** e quindi **Acquista**. La distribuzione può richiedere almeno 10 minuti.

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

Nel portale di Azure esaminare le risorse distribuite, in particolare le regole del firewall che usano i gruppi IP.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="Gruppi IP.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Regole di rete.":::

Per informazioni sulla sintassi JSON e sulle proprietà di un firewall in un modello, vedere le [informazioni di riferimento sul modello Microsoft.Network azureFirewalls](/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il firewall non sono più necessarie, eliminare il gruppo di risorse. Oltre al firewall verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Distribuire e configurare Firewall di Azure in una rete ibrida con il portale di Azure](tutorial-hybrid-portal.md)