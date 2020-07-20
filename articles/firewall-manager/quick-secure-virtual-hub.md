---
title: "Avvio rapido: Proteggere l'hub virtuale usando Gestione firewall di Azure - Modello di Resource Manager"
description: Informazioni su come proteggere l'hub virtuale con Gestione firewall di Azure.
services: firewall-manager
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 1917bed7727b97de27e560a0f2d8032a8ae08996
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242016"
---
# <a name="quickstart-secure-your-virtual-hub-using-azure-firewall-manager---arm-template"></a>Avvio rapido: Proteggere l'hub virtuale con Gestione firewall di Azure - Modello di Resource Manager

Questa guida di avvio rapido illustra come usare un modello di Azure Resource Manager per proteggere l'hub virtuale usando Gestione firewall di Azure. Il firewall distribuito ha una regola dell'applicazione che consente le connessioni a `www.microsoft.com`. Per testare il firewall sono state distribuite due macchine virtuali Windows Server 2019. Per la connessione al server del carico di lavoro viene utilizzato un jump server. Dal server del carico di lavoro è possibile connettersi solo a `www.microsoft.com`.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Per altre informazioni su Gestione firewall di Azure, vedere [Che cos'è Gestione firewall di Azure?](overview.md).

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Rivedere il modello

Questo modello crea un hub virtuale protetto usando Gestione firewall di Azure, insieme alle risorse necessarie per supportare lo scenario.

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/fwm-docs-qs/).

:::code language="json" source="~/quickstart-templates/fwm-docs-qs/azuredeploy.json" range="001-477" highlight="47-76":::

Nel modello sono definite più risorse di Azure:

- [**Microsoft.Network/virtualWans**](/azure/templates/microsoft.network/virtualWans)
- [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualHubs)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

## <a name="deploy-the-template"></a>Distribuire il modello

Distribuire il modello di Resource Manager in Azure:

1. Selezionare **Distribuisci in Azure** per accedere ad Azure e aprire il modello. Il modello crea un'istanza di Firewall di Azure, una WAN e un hub virtuali, l'infrastruttura di rete e due macchine virtuali.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffwm-docs-qs%2Fazuredeploy.json)

2. Nella pagina **Hub virtuali protetti** del portale digitare o selezionare i valori seguenti:
   - Sottoscrizione: selezionare una delle sottoscrizioni esistenti. 
   - Gruppo di risorse:  selezionare un gruppo di risorse esistente oppure selezionare **Crea nuovo** e quindi **OK**.
   - Percorso: Selezionare una località
   - Nome utente amministratore: digitare il nome utente dell'account utente amministratore. 
   - Password amministratore: digitare una password o una chiave dell'amministratore.

3. Selezionare **Rivedi e crea** e quindi **Crea**. La distribuzione può richiedere almeno 10 minuti.

## <a name="validate-the-deployment"></a>Convalidare la distribuzione

A questo punto testare le regole del firewall per verificare che tutto funzioni come previsto.

1. Nel portale di Azure rivedere le impostazioni di rete per la macchina virtuale **Workload-Srv** e annotare l'indirizzo IP privato.
2. Connettere una sessione di Desktop remoto alla macchina virtuale **Jump-Srv** e accedere. Da qui aprire una connessione Desktop remoto all'indirizzo IP privato di **Workload-Srv**.

3. Aprire Internet Explorer e passare a `www.microsoft.com`.
4. Selezionare **OK** > **Close** negli avvisi di sicurezza di Internet Explorer.

   Verrà visualizzata la home page Microsoft.

5. Passare a `www.google.com`.

   Si verrà bloccati dal firewall.

A questo punto si è verificato che le regole del firewall funzionano:

- È possibile passare al nome di dominio completo consentito ma non agli altri.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con il firewall non sono più necessarie, eliminare il gruppo di risorse. Oltre al firewall verranno rimosse tutte le risorse correlate.

Per eliminare il gruppo di risorse, chiamare il cmdlet `Remove-AzResourceGroup`:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sui i provider partner di sicurezza](trusted-security-partners.md)
